collection of key/value pairs

Ex: iisSettings is the key and inside {} is the value

we can change IISExpress port number here

```C#
"iisSettings": {
	"windowsAuthentication": false,
	"anonymousAuthentication": true,
	"iisExpress": {
	"applicationUrl": "http://localhost:11087",
	"sslPort": 0
}
```

  

there 2 by default 2 profiles:

```C#
"profiles": {
	"http": {
		"commandName": "Project",
		"dotnetRunMessages": true,
		"launchBrowser": true,
		"applicationUrl": "http://localhost:5194",
		"environmentVariables": {
		"ASPNETCORE_ENVIRONMENT": "Development"
			}
		},
			"IIS Express": {
			"commandName": "IISExpress",
			"launchBrowser": true,
			"environmentVariables": {
			"ASPNETCORE_ENVIRONMENT": "Development"
		}
	}
}
```

“profiles” is default kestrel server configuration which can be changed to other names and ==**IIS Express**== is ==**dummy reverse proxy**== ==server== and can changed name too

commandName: 2 values which is Project(Kestrel server) or IISExpress(dummy rps)

dotnetRunMessages: allow to use dotnet commands in terminal

applicationUrl: the url of application and we can change the port number from 1024 to 65535

ASPNETCORE_ENVIRONMENT can be changed to Development, Staging or Product