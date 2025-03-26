### Create ApplicationUser and ApplicationRole

```C#
using Microsoft.AspNetCore.Identity;

namespace CitiesManager.Core.Identity
{
    public class ApplicationUser : IdentityUser<Guid>
    {
        public string? PersonName { get; set; }
    }
}
```

```C#
using Microsoft.AspNetCore.Identity;

namespace CitiesManager.Core.Identity
{
	public class ApplicationRole : IdentityRole<Guid>
	{
	}
}
```

### Add Authorization and Authentication

```C#
app.UseRouting();
app.UseCors();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

### Add Identity to ApplicationDbContext

```C#
public class ApplicationDbContext : IdentityDbContext<ApplicationUser, ApplicationRole, Guid>
{
```

### RegisterDTO

```C#
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CitiesManager.Core.DTO
{
    public class RegisterDTO
    {
        [Required(ErrorMessage = "Person name is required")]
        public string PersonName { get; set; }
        [Required(ErrorMessage = "Email is required")]
        [EmailAddress(ErrorMessage = "Email should be in right format")]
        public string Email { get; set; }

        [Required(ErrorMessage = "Phone number is required")]
        [RegularExpression("^[0-9]*", ErrorMessage = "Phone number should contains digits only")]
        public string PhoneNumber { get; set; }

        [Required(ErrorMessage = "Password is required")]
        public string Password { get; set; }

        [Required(ErrorMessage = "Confirm Password is required")]
        [Compare("Password", ErrorMessage = "Password and Confirm Password do not match")]
        public string ConfirmPassword { get; set; }

    }
}
```

### AccountController

**Register action method**

```C#
using CitiesManager.Core.DTO;
using CitiesManager.Core.Identity;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using NuGet.Protocol.Plugins;

namespace CitiesManager.WebAPI.Controllers.v1
{
    [AllowAnonymous]
    [ApiVersion("1.0")]
    public class AccountController : CustomControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;
        private readonly SignInManager<ApplicationUser> _signInManager;
        private readonly RoleManager<ApplicationRole> _roleManager;

        public AccountController(UserManager<ApplicationUser> userMangager, SignInManager<ApplicationUser> signInManager, RoleManager<ApplicationRole> roleManager)
        {
            _userMangager = userMangager;
            _signInManager = signInManager;
            _roleManager = roleManager;
        }

        [HttpPost("register")]
        public async Task<ActionResult<ApplicationUser>> Register(RegisterDTO registerDTO)
        {
            //validation
            if(!ModelState.IsValid)
            {
                string errorMessage = string.Join(",", ModelState.Values.SelectMany(v => v.Errors).Select(e => e.ErrorMessage));
                return Problem(errorMessage);
            }
            var existingUser = await _userMangager.FindByEmailAsync(registerDTO.Email);
            if(existingUser != null)
            {
                return BadRequest("Email is already registered");
            }

            //Create user
            ApplicationUser user = new ApplicationUser()
            {
                Email = registerDTO.Email,
                PhoneNumber = registerDTO.PhoneNumber,
                UserName = registerDTO.Email,
                PersonName = registerDTO.PersonName
            };
            var result = await _userMangager.CreateAsync(user, registerDTO.Password);
            if(result.Succeeded)
            {
                //automatically sign-in
                await _signInManager.SignInAsync(user, isPersistent: false);
                return Ok(user);
            }
            else
            {
                string errorMessage = string.Join(",", result.Errors.Select(e => e.Description));
                return Problem(errorMessage);
            }
        }
    }
}
```

==**[AllowAnonymous]**== : so that everyone can access these methods

  

**Login action method**

```C#
[HttpPost("login")]
public async Task<IActionResult> Login(LoginDTO loginDTO)
{
    //validation
    if(!ModelState.IsValid)
    {
        string errorMessage = string.Join(",", ModelState.Values.SelectMany(v => v.Errors).Select(e => e.ErrorMessage));
        return Problem(errorMessage);
    }
    var result = await _signInManager.PasswordSignInAsync(loginDTO.Email, loginDTO.Password, isPersistent: true, lockoutOnFailure: false);
    if(result.Succeeded)
    {
        ApplicationUser user = await _userMangager.FindByEmailAsync(loginDTO.Email);
        return Ok(new { personName = user.PersonName, email = user.Email });
    }
    else
    {
        return Problem("Invalid email or password");
    }
    
}
```

PasswordSignInAsync(4 parameters): here we use PasswordSignInAsync() instead of SignInAsync(), because we use email and password to sign in instead of an ApplicationUser object

  

**Logout action method**

```C#
[HttpGet("logout")]
public async Task<IActionResult> Logout()
{
    await _signInManager.SignOutAsync();
    return NoContent();
}
```

  

  

# JWT - Basics

> A JSON Web Token (JWT) is a compact and self-contained object for securely transmitting information between parties as a JSON object.  
> (Một JSON Web Token (JWT) là một đối tượng nhỏ gọn và đóng khung, được sử dụng để truyền tải thông tin một cách an toàn giữa các bên dưới dạng một đối tượng JSON.)  

![[/Untitled 146.png|Untitled 146.png]]

- JWT is a token system, that is exchanged from server to client.
- It is a form of representation of the user data, which is also called user claims.
- Browser will receive the token and store it within the session storage or the local storage
- This is more secured than storing UserId or User Details in the browser memory

  

## Contents of JWT

![[/Untitled 1 28.png|Untitled 1 28.png]]

base 64 string: a form of data that is used to encode the binary data in a printable text format

- **Header**: contains the details of the token. Indicating what security algorithm is used in order to generate the token
- **Payload**: contains actual user details that we want to store. We shouldn’t store any sensitive data, such as password, finnancial information,…
- **Signature:** encrypted version of the same header and payload

![[/Untitled 2 23.png|Untitled 2 23.png]]

- As above picture, we can say that signature is that header and payload but encrypted
- The secret key is used to ensure that a token is generated by a particular server, since only the server knows the key. The hashedData is generated by the key, if other people try to mimic the process, they do not know the secret key.
- The process of creating the token and verifying the same token of the subsequent requests is all done by the server
    
    - How does the server verify whether the token is valid or not?: It just repeats the same process based on the header and payload and it gets the new token, so if the new token matches the token supplied by the client, it is valid.
    
      
    
    - **Audience**: in audience, we have to mention the name of the domain, which domain can have this jwt token. In this case, we are targeting to supply this token for audience of 4200(angular application, but we didn’t do it). So we must include the domain address of the domain application(front end)

## Generating JWT Tokens

To generate the token, we need to install the package: ==**Microsoft.AspNetCore.Authentication.JwtBearer**==

System.IdentityModel.Tokens.Jwt at where Service resides.

==Microsoft.IdentityModel.JsonWebTokens(this is newer package, a bit different from System.IdentityModel.Tokens.Jwt)==

We will write the token as a service so it can be reusable

First create a DTO as a response

```C#
namespace CitiesManager.Core.DTO
{
    public class AuthenticationResponse
    {
        public string? PersonName { get; set; } = string.Empty;
        public string? Email { get; set; } = string.Empty;
        public string? Token { get; set; } = string.Empty;
        public DateTime Expiration { get; set; }
    }
}
```

  

```C#
using CitiesManager.Core.DTO;
using CitiesManager.Core.Identity;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CitiesManager.Core.ServiceContracts
{
    public interface IJwtService
    {
        AuthenticationResponse CreateJwtToken(ApplicationUser user);
    }
}
```

After that create a folder called “ServiceContracts”, and inside create the interface for the service

Here it means, we supply an ApplicationUser object, based on existing user details, it has to automatically generated the Jwt token.

  

Next, we add Jwt settings to appsettings.json

```C#
"Jwt": {
  "Issuer": "http://localhost:7221",
  "Audience": "http://localhost:722",
  "EXPIRATION_MINUTES": "10",
  "Key": "ThisIsALongerSecurityKeyWithAtLeast32Characters"
}
```

- **Issuer**: while generating the jwt token as per the standards, we have to add some user claims as a part of the payload. So the important properties or claims that must be present in the payloads, one of them is issuer, that indicates the domain address of the Web API that has generated or created the JWT Token
- EXPIRATION_MINUTES: How many minutes the token will be validated for, after x minutes, the token will be expired and invalid.
- Key: a secret key uses to hash, make sure it’s at least 32 characters long

The above 3 are ==**required**==

  

Next, create “Service” folder, with JwtService class implementing IJwtService

```C#
using CitiesManager.Core.DTO;
using CitiesManager.Core.Identity;
using CitiesManager.Core.ServiceContracts;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.JsonWebTokens;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Text;
using System.Threading.Tasks;

namespace CitiesManager.Core.Services
{
    public class JwtService : IJwtService
    {
        private readonly IConfiguration _configuration;
        public JwtService(IConfiguration configuration)
        {
            _configuration = configuration;
        }
        public AuthenticationResponse CreateJwtToken(ApplicationUser user)
        {
            DateTime expiration = DateTime.UtcNow.AddMinutes(Convert.ToDouble(_configuration["Jwt:EXPIRATION_MINUTES"]));

            Claim[] claims = new Claim[]
            {
                new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString()),
                new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()), //JWT unique ID
                new Claim(JwtRegisteredClaimNames.Iat, DateTimeOffset.Now.ToUnixTimeSeconds().ToString(), //Issued at
                new Claim(ClaimTypes.NameIdentifier, user.Email), //Unique name identifier of the user (Email)
                new Claim(ClaimTypes.Name, user.PersonName), //Name of the user
                new Claim(ClaimTypes.Email, user.Email)
            };
        }
    }
}
```

Since we need to take configuration for appsettings.json, we will inject IConfiguration.

- ==**expiration**==: take the expiration duration in appsettings.json and add to time of now
- Now start preparing the Claim(Claim represents particular value, like fields or details of a particular user) that need to be added into the Payload. There are certain type of claim that must be included in the Payload of JWT token:
    
    - ==**(Required)**====**JwtRegisteredClaimNames**==.Sub (Subject): Sub(Subject) value indicates user identity(unique value of a particular user, we can use any unique value of a particular user(Email,Username,…))
    - ==**(Required)**====**JwtRegisteredClaimNames**==.Jti: Unique Id for the token
    - ==**(Required)**====**JwtRegisteredClaimNames**==.Iat (Issued at): Date and Time of token generation
    - ==**(Optional)**====**ClaimTypes**==.NameIdentifier: Indicates the unique value of a particular user, already we had added the userId, so we use Email
    - ==**(Optional)**====**ClaimTypes**==.Name, user.PersonName: Name of the user
    
      
    

Now we have the Payload, after that we have to generate a secret key, based on which the Signature can be generated

```C#
...
Claim[] claims = new Claim[]{
...
};

SymmetricSecurityKey securityKey = new SymmetricSecurityKey(
    Encoding.UTF8.GetBytes(_configuration["Jwt:Key"])
);
```

- The hashing algorithm requires the secret key
- The secret key represented as **SymmetrcSecurityKey** class
- In this object, we have to supply the key
- In the production level, we will store the security key as a part of the environment variables, the development time key may be known to the other Devs, but the production level key will not be even known the Devs
- The key value must be converted into Bytes, Byte array.

  

After the key, we need to define the algorithm that we are using(hashing algorithm):

```C#
SigningCredentials signingCredentials = new 
									 SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);
```

Create a SigningCredentials object, then pass the key and the name of the algo we are using(HmacSha256)

  

Now, we are going to create a token generator. It responsible for creating an actual token based on all these information we have provided above, and return AuthenticationResponse

```C#
var token = new JwtSecurityToken(
     _configuration["Jwt:Issuer"],
     _configuration["Jwt:Audience"],
     claims,
     expiration,
     signingCredentials: signingCredentials
     );

 return new AuthenticationResponse
 {
     Token = new JwtSecurityTokenHandler().WriteToken(token),
     Email = user.Email,
     PersonName = user.PersonName,
     Expiration = expiration,
     RefreshToken = GenerateRefreshToken(),
     RefreshTokenExpirationDateTime = DateTime.UtcNow.AddMinutes(Convert.ToInt32(_configuration["RefreshToken:EXPIRATION_MINUTES"]))
 };
```

Now we just need add the service in the Program.cs then inject and use it in the AccountController

Program.cs

```C#
builder.Services.AddTransient<IJwtService, JwtService>();
```

  

AccountController.cs

```C#
using CitiesManager.Core.DTO;
using CitiesManager.Core.Identity;
using CitiesManager.Core.ServiceContracts;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using NuGet.Protocol.Plugins;

namespace CitiesManager.WebAPI.Controllers.v1
{
    [AllowAnonymous]
    [ApiVersion("1.0")]
    public class AccountController : CustomControllerBase
    {
        private readonly UserManager<ApplicationUser> _userMangager;
        private readonly SignInManager<ApplicationUser> _signInManager;
        private readonly RoleManager<ApplicationRole> _roleManager;
        private readonly IJwtService _jwtService;

        public AccountController(UserManager<ApplicationUser> userMangager, SignInManager<ApplicationUser> signInManager, RoleManager<ApplicationRole> roleManager, IJwtService jwtService)
        {
            _userMangager = userMangager;
            _signInManager = signInManager;
            _roleManager = roleManager;
            _jwtService = jwtService;
        }

        [HttpPost("register")]
        public async Task<ActionResult<AuthenticationResponse>> Register(RegisterDTO registerDTO)
        {
            //validation
            if(!ModelState.IsValid)
            {
                string errorMessage = string.Join(",", ModelState.Values.SelectMany(v => v.Errors).Select(e => e.ErrorMessage));
                return Problem(errorMessage);
            }
            var existingUser = await _userMangager.FindByEmailAsync(registerDTO.Email);
            if(existingUser != null)
            {
                return BadRequest("Email is already registered");
            }

            //Create user
            ApplicationUser user = new ApplicationUser()
            {
                Email = registerDTO.Email,
                PhoneNumber = registerDTO.PhoneNumber,
                UserName = registerDTO.Email,
                PersonName = registerDTO.PersonName
            };
            var result = await _userMangager.CreateAsync(user, registerDTO.Password);
            if(result.Succeeded)
            {
                //automatically sign-in
                await _signInManager.SignInAsync(user, isPersistent: false);

                var authenticationResponse = _jwtService.CreateJwtToken(user);
                return Ok(authenticationResponse);
            }
            else
            {
                string errorMessage = string.Join(",", result.Errors.Select(e => e.Description));
                return Problem(errorMessage);
            }
        }

        [HttpPost("login")]
        public async Task<ActionResult<AuthenticationResponse>> Login(LoginDTO loginDTO)
        {
            //validation
            if(!ModelState.IsValid)
            {
                string errorMessage = string.Join(",", ModelState.Values.SelectMany(v => v.Errors).Select(e => e.ErrorMessage));
                return Problem(errorMessage);
            }
            var result = await _signInManager.PasswordSignInAsync(loginDTO.Email, loginDTO.Password, isPersistent: true, lockoutOnFailure: false);
            if(result.Succeeded)
            {
                ApplicationUser user = await _userMangager.FindByEmailAsync(loginDTO.Email);
                var authenticationResponse = _jwtService.CreateJwtToken(user);
                return Ok(authenticationResponse);
            }
            else
            {
                return Problem("Invalid email or password");
            }
            
        }

        [HttpGet("logout")]
        public async Task<IActionResult> Logout()
        {
            await _signInManager.SignOutAsync();
            return NoContent();
        }
    }
}
```

  

# Authorization With JWT

### **Configure the UseAuthentication for it to validate the JWT Token**

Program.cs

```C#
//JWT
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    //options.DefaultChallengeScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
});
```

- options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme: by default, it is cookie authentication
- options.DefaultChallengeScheme = CookieAuthenticationDefaults.AuthenticationScheme: whenever a request is received from the client application, first it will try to validate that request by using the default authentication scheme, that is JwtBearer. But if that authentication is failed at the default scheme, instead of redirect user to login page, it will try to validate the user by using the challenge scheme, so we would use cookie authentication scheme here, since the jwt authentication scheme failed.
- But since we don’t do Cookie authentication, switch it back to the JwtBearerDefaults

  

Now we have to specify what properties that has to be checked in the JWT Token, how the token will be processed, and what things have to be validated within the token.

```C#
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = CookieAuthenticationDefaults.AuthenticationScheme;
}).AddJwtBearer(options =>
{
    options.TokenValidationParameters = new TokenValidationParameters()
		{
	    ValidateAudience = true,
			ValidAudience = builder.Configuration["Jwt:Audience"],
			ValidateIssuer = true,
			ValidIssuer = builder.Configuration["Jwt:Issuer"],
			ValidateLifetime = true,
			ValidateIssuerSigningKey = true,
			IssuerSigningKey = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
		};
});
```

- We have received the token, and it contains the values of audience as a part of the payload. The payload contains ==**issuer, iat, jti, audience,**== so: we have to
- Validate the Audience(ValidateAudience = true), and specify who is the valid audience(ValidAudience = builder.Configuration["Jwt:Audience"])
- Validate the Issuer(ValidateIssuer = true), and specify the actual valid value for the issuer(ValidIssuer = builder.Configuration["Jwt:Issuer"])
- Validate the Expiration date(Lifetime)
- Validate the IssuerSigningKey: we have to mention the secret key value (IssuerSigningKey = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"])))
- →If the audience value or the issuer or any above value don’t match with the valid value, the token is invalid
    
      
    

### AddAuthorization

```C#
AddAuthentication(...);
AddAuthorization(options => {
});
```

By default authorization is added, but writing this explicitly will allow us to add more options, such as policy

  

To force those authentication process to perform, we have to add [Authorized] attribute to the controllers. Instead of writing the attribute tag on every controller, we can make the global policy for every controllers:

Program.cs

```C#
builder.Services.AddControllers(options =>
{
    options.Filters.Add(new ProducesAttribute("application/json"));
    options.Filters.Add(new ConsumesAttribute("application/json"));
    var policy = new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build();
    options.Filters.Add(new AuthorizeFilter(policy));
})
 .AddXmlSerializerFormatters();
```

This will apply the [Authorized] attribute to all controllers. But we don’t want to apply it to AccountController(login, register,…). So we will add [AllowAnonymous] tag on top of the AccountController, it will bypass the policy above

```C#
[AllowAnonymous]
 public class AccountController : CustomControllerBase
 {
 ...
```

Now, if the user is not logged in, or sent request doesn’t have Authorization header with token, it will be 401(Unauthorized)

  

# Refresh Token

> A refresh token is a token(base-64 string of a random number) that is used to obtain a new JWT token every time, when it is expired

If we set the expiration of the jwt token for 5-10 minutes, then every 5-10 minutes, user will have to re-login to get the new JWT Token, which is inconvenient. Refresh token is introduced for automatic regeneration of JWT Token.

![[/Untitled 3 21.png|Untitled 3 21.png]]

- Refresh token is not like JWT Token, it does not contain header, payload,… It just a base-64 string of random number.
- It is recommended to set the shortest time for expiration, for example 1 minutes instead of 10 minutes

  

Now we have to set the number of time of expiration for both JWT Token and Refresh Token in appsettings.json. We will set shorter span of time for JWT and longer span of time for refresh token

```C#
"Jwt": {
  "Issuer": "http://localhost:7221",
  "Audience": "http://localhost:7221",
  "EXPIRATION_MINUTES": 1,
  "Key": "ThisIsALongerSecurityKeyWithAtLeast32Characters"
},
"RefreshToken": {
  "EXPIRATION_MINUTES": 60
}
```

  

We will have to add an attribute to AuthenticationResponse, which is RefreshToken, and RefreshToken expired date time

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CitiesManager.Core.DTO
{
    public class AuthenticationResponse
		{
		    public string? PersonName { get; set; } = string.Empty;
		    public string? Email { get; set; } = string.Empty;
		    public string? Token { get; set; } = string.Empty;
		    public DateTime Expiration { get; set; }
		    public string? RefreshToken {  get; set; } = string.Empty;
		    public DateTime RefreshTokenExpirationDateTime { get; set; }
		}
}
```

Refresh Token is created when user logs in or registers → Whenever the user is authenticated, we will have to create a new refresh token, and later, based on the refresh token, the JWT Token has to be regenerated

  

## Creating Refresh Token

We will have to store refresh token in the User table(AspNetUsers), if it is not stored in the User table, we will be unable to validate the refresh token. If someone has submitted something called refresh token, how do you validate it? How do you validate if it is a valid refresh token, that is generated by your server but not somebody else? → For one user only one refresh token and the latest refresh token of a particular user should be stored in the AspNetUsers table

  

### Add Refresh Token to ApplicationUser

```C#
public class ApplicationUser : IdentityUser<Guid>
{
	public string? PersonName { get; set; }
	public string? RefreshToken { get; set; }
	public DateTime RefreshTokenExpirationDateTime { get; set; }
}
```

Then Add-Migration and Update-Database, we will see changes in AspNetUsers table

![[/Untitled 4 18.png|Untitled 4 18.png]]

### JwtService

We will add a new method to the JwtService to generate refresh token, we can add it as a private method because we will just call it inside the JwtService itself.

JwtService.cs

```C#
//Create a refresh token (base 64 string of random numbers)
private string GenerateRefreshToken()
{
    byte[] bytes = new byte[64];
	var randomNumberGenerator = RandomNumberGenerator.Create();
	randomNumberGenerator.GetBytes(bytes);
	return Convert.ToBase64String(bytes);
}
```

The purpose of RandomNumberGenerator is that generating a random number that is cryptographically stronger, it avoids dupplication.

randomNumberGenerator.GetBytes(bytes): so within the bytes array, it will be filled with random numbers, means random values

return Convert.ToBase64String(bytes): after that particular random number, which is in form of a byte array, should be converted into base 64 string.

  

After that, we will use the method above to generate the RefreshToken for AuthenticationResponse in CreateJwtToken() method:

```C#
public AuthenticationResponse CreateJwtToken(ApplicationUser user)
{
...
    return new AuthenticationResponse
    {
        Token = tokenString,
        Email = user.Email,
        PersonName = user.PersonName,
        Expiration = expiration,
        RefreshToken = GenerateRefreshToken(),
        RefreshTokenExpirationDateTime = DateTime.UtcNow.AddMinutes(Convert.ToInt32(_configuration["RefreshToken:EXPIRATION_MINUTES"]))
    };
}
```

  

Now we will store the refresh token in the AspNetUsers table, in Register action method

```C#
[HttpPost("register")]
public async Task<ActionResult<AuthenticationResponse>> Register(RegisterDTO registerDTO)
{
...
if(result.Succeeded)
{
    //automatically sign-in
    await _signInManager.SignInAsync(user, isPersistent: false);

    var authenticationResponse = _jwtService.CreateJwtToken(user);
    user.RefreshToken = authenticationResponse.RefreshToken;
    user.RefreshTokenExpirationDateTime = authenticationResponse.RefreshTokenExpirationDateTime;
	  await _userMangager.UpdateAsync(user);//update to database
    
    return Ok(authenticationResponse);
}
```

  

Now also add in the Login action method

```C#
[HttpPost("login")]
public async Task<ActionResult<AuthenticationResponse>> Login(LoginDTO loginDTO)
{
...
if(result.Succeeded)
{
    ApplicationUser user = await _userMangager.FindByEmailAsync(loginDTO.Email);
    var authenticationResponse = _jwtService.CreateJwtToken(user);
    user.RefreshToken = authenticationResponse.RefreshToken;
    user.RefreshTokenExpirationDateTime = authenticationResponse.RefreshTokenExpirationDateTime;
    await _userMangager.UpdateAsync(user);
    return Ok(authenticationResponse);
}
...
```

When we logged in or register, it will save to database

![[/Untitled 5 15.png|Untitled 5 15.png]]

  

So next time we can validate the refresh token based on this database data

Next, we will implement the JWT Token regeneration based on the valid Refresh Token. We should make a post request to the server, quoting the 2 tokens(AccessToken, RefreshToken), and the server will receive, verify both tokens, and generate a new AccessToken(JWT Token).

First create the DTO for both tokens

TokenModel.cs

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CitiesManager.Core.DTO
{
    public class TokenModel
    {
        public string? Token { get; set; }
        public string? RefreshToken { get; set; }
    }
}
```

Then create action method in AccountController.cs, and verify those 2 tokens

AccountController.cs

```C#
[HttpPost("generate-new-jwt-token")]
public async Task<IActionResult> GenerateNewAccessToken(TokenModel tokenModel)
{
	if(tokenModel == null)
	{
	    return BadRequest("Invalid client request");
	}
}
```

We have to verify the user identity in jwt token, because in the payload of the jwt token already contains the user id → extract the user details and verify it according to database

  

Create new method for jwt service

IJwtService.cs

```C#
public interface IJwtService
{
    AuthenticationResponse CreateJwtToken(ApplicationUser user);
    ClaimsPrincipal? GetPrincipalFromJwtToken(string? token);
}
```

The ==**ClaimsPrincipal**== object represents the user details(id, username, email address,…)

Since the token can be invalid sometimes, we will return nullable(==**?**==)

  

JwtService.cs

This method will extract the token and read the payload for user details. JwtBearer nuget packes also supply some essential classes for this.

```C#
public ClaimsPrincipal? GetPrincipalFromJwtToken(string? token)
{
	var tokenValidationParameters = new TokenValidationParameters()
	{
	    ValidateAudience = true,
	    ValidAudience = _configuration["Jwt:Audience"],
	    ValidateIssuer = true,
	    ValidIssuer = _configuration["Jwt:Issuer"],
	    ValidateIssuerSigningKey = true,
	    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["Jwt:Key"])),
	    ValidateLifetime = false,
	};
}
```

First we have to define what validations we have to perform on this token(make sure it’s valid)

- TokenValidationParameters class object represents what validations must be performed while extracting the token details
- ValidateLifetime = false: Because we will call this method with an ==**expired token**==, so we should not validate the life time of the token

  

After that, we will create a JwtSecurityTokenHandler instance to ValidateToken and get the ClaimsPrincipal

```C#
JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();
ClaimsPrincipal principal = tokenHandler.ValidateToken(token, tokenValidationParameters, out SecurityToken securityToken);
```

ValidateToken will validate the token with the tokenValidationParameters

And it returns one more thing as a “out” parameter that is security token. This out security token out parameter represents an instance of JwtSecurityToken class in case if the token is a valid token. In case if the token is ==**invalid**==, then this out parameter represents an object of SecurityToken class, or maybe null. And then we will check some conditions to check if the token is valid:

```C#
if(securityToken is not JwtSecurityToken jwtSecurityToken
   || !jwtSecurityToken.Header.Alg.Equals(SecurityAlgorithms.HmacSha256, StringComparison.InvariantCultureIgnoreCase))
{
    throw new SecurityTokenException("Invalid token");
}
```

- securityToken is not JwtSecurityToken jwtSecurityToken: If securityToken is not an object of JwtSecurityToken class, then it indicates the token is invalid
- JwtSecurityToken jwtSecurityToken: In case the securityToken is an instance of JwtSecurityToken class, then we have to type cast it into JwtSecurityToken(pattern matching syntax)
- !jwtSecurityToken.Header.Alg.Equals(SecurityAlgorithms.HmacSha256, StringComparison.InvariantCultureIgnoreCase): check if the jwtSecurityToken’s header contains the right algorithm used to hashed value for the signature

→ After checking the conditions, now we have the ClaimsPrincipal(user information details) of the user. The claimsPrincipal object represents or reflects the user details from the payload of the token

Complete:

```C#
public ClaimsPrincipal? GetPrincipalFromJwtToken(string? token)
{
    var tokenValidationParameters = new TokenValidationParameters()
    {
        ValidateAudience = true,
        ValidAudience = _configuration["Jwt:Audience"],
        ValidateIssuer = true,
        ValidIssuer = _configuration["Jwt:Issuer"],
        ValidateIssuerSigningKey = true,
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["Jwt:Key"])),
        ValidateLifetime = false
    };

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();
    ClaimsPrincipal principal = tokenHandler.ValidateToken(token, tokenValidationParameters, out SecurityToken securityToken);

    if(securityToken is not JwtSecurityToken jwtSecurityToken
       || !jwtSecurityToken.Header.Alg.Equals(SecurityAlgorithms.HmacSha256, StringComparison.InvariantCultureIgnoreCase))
    {
        throw new SecurityTokenException("Invalid token");
    }
    return principal;
}
```

  

Back to the AccountController:

```C#
[HttpPost("generate-new-jwt-token")]
public async Task<IActionResult> GenerateNewAccessToken(TokenModel tokenModel)
{
    if(tokenModel == null)
    {
        return BadRequest("Invalid client request");
    }

		//continue
    ClaimsPrincipal? principal = _jwtService.GetPrincipalFromJwtToken(tokenModel.Token);

    if(principal == null)
    {
        return BadRequest("Invalid jwt access token");
    }

    string? email = principal.FindFirstValue(ClaimTypes.Email);
}
```

Now we have to extract the email address from the ClaimsPrincipal by accessing NameIdentifer.

We have to get the corresponding data as we set in the JwtToken Claims and ==right type== to avoid ==conflict==(Like if want to get email from claims, get ClaimTypes.Email, instead of ClaimTypes.NameIdentifier even though we set ClaimTypes.NameIdentifier as user.Email):

```C#
public AuthenticationResponse CreateJwtToken(ApplicationUser user)
{
    DateTime expiration = DateTime.UtcNow.AddMinutes(Convert.ToDouble(_configuration["Jwt:EXPIRATION_MINUTES"]));

    Claim[] claims = new Claim[]
    {
        new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString()),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()), //JWT unique ID
        new Claim(JwtRegisteredClaimNames.Iat, DateTimeOffset.Now.ToUnixTimeSeconds().ToString(), //Issued at (Date and Time of token generation)
        new Claim(ClaimTypes.NameIdentifier, user.Email), //Unique name identifier of the user (Email)
        new Claim(ClaimTypes.Name, user.PersonName), //Name of the user
        new Claim(ClaimTypes.Email, user.Email)

    };
```

Since we set [user.Email](http://user.Email) is NameIdentifier, to get the email, we have to access NameIdentifier

After getting the email, we will have to validate:

```C#
string? email = principal.FindFirstValue(ClaimTypes.Email);

ApplicationUser user = await _userMangager.FindByEmailAsync(email);
if(user == null || user.RefreshToken != tokenModel.RefreshToken || user.RefreshTokenExpirationDateTime <= DateTime.Now)
{
    return BadRequest("Invalid refresh token");
}
```

- Check if the user is null(if null then invalid email)
- Compare the refresh token saved in database with the tokenModel(request)’s refresh token
- Check if the expiry date is appropriate

  

After checking conditions, we will regenerate both token and save the refresh token and expiry date of it to database.

```C#
if(...){}
AuthenticationResponse response = _jwtService.CreateJwtToken(user);
user.RefreshToken = response.RefreshToken;
user.RefreshTokenExpirationDateTime = response.RefreshTokenExpirationDateTime;
await _userMangager.UpdateAsync(user);  
```