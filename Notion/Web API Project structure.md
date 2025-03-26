# Solution

This structure have 2 extension method, which is AddInfrastructureService() and AddWebAPIService() → Make Program.cs smaller

# APIs

## Application

## Common

AppConfiguration.cs

```C#
namespace Application.Commons
{
    public class AppConfiguration
    {
        public string DatabaseConnection { get; set; }
        public string JWTSecretKey { get; set; }
    }
}
```

  

Pagination.cs

```C#
namespace Application.Commons
{
    public class Pagination<T>
    {
        public int TotalItemsCount { get; set; }
        public int PageSize { get; set; }
        public int TotalPagesCount
        {
            get
            {
                var temp = TotalItemsCount / PageSize;
                if (TotalItemsCount % PageSize == 0)
                {
                    return temp;
                }
                return temp + 1;
            }
        }
        public int PageIndex { get; set; }

        /// <summary>
        /// page number start from 0
        /// </summary>
        public bool Next => PageIndex + 1 < TotalPagesCount;
        public bool Previous => PageIndex > 0;
        public ICollection<T> Items { get; set; }
    }
}
```

## Interfaces

All the Services Interfaces

  

## Repositories

All the repositories interfaces

## Services

Implementation of Service Interfaces

## Utils

GenerateJWTString.cs

```C#
using Domain.Entities;
using Microsoft.IdentityModel.Tokens;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;

namespace Application.Utils
{
    public static class GenerateJsonWebTokenString
    {
        public static string GenerateJsonWebToken(this User user, string secretKey, DateTime now)
        {
            var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));
            var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);
            var claims = new[]
            {
                new Claim(ClaimTypes.NameIdentifier ,user.UserName),
            };
            var token = new JwtSecurityToken(
                claims: claims,
                expires: now.AddMinutes(15),
                signingCredentials: credentials);


            return new JwtSecurityTokenHandler().WriteToken(token);
        }
    }
}
```

## ViewModels (it’s like DTO)

IUnitOfWork.cs

```C#
using Application.Repositories;

namespace Application
{
    public interface IUnitOfWork
    {
        public IChemicalRepository ChemicalRepository { get; }

        public IUserRepository UserRepository { get; }

        public Task<int> SaveChangeAsync();
    }
}
```

## Domain

## Entities

BaseEntity.cs

```C#
namespace Domain.Entities
{
    public abstract class BaseEntity
    {
        public Guid Id { get; set; }

        public DateTime CreationDate { get; set; }

        public Guid? CreatedBy { get; set; }

        public DateTime? ModificationDate { get; set; }

        public Guid? ModificationBy { get; set; }

        public DateTime? DeletionDate { get; set; }

        public Guid? DeleteBy { get; set; }

        public bool IsDeleted { get; set; }
    }
}
```

Other entity, such as User,…

## Enums

StatusEnum.cs

```C#
namespace Domain.Enums
{
    public enum StatusEnums
    {
        Good,
        Bad,
        Out,
        OnTrack
    }
}
```

## Infrastructure (Relate to DbContext)

## WebAPI

# Tests