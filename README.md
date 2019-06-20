[![Build Status](https://travis-ci.org/aslabicki/Zabbix.API.Core.svg?branch=master)](https://travis-ci.org/aslabicki/Zabbix.API.Core)

# Zabbix.NET.Core

Zabbix.NET.Core is a Zabbix API library based on .NET Standard 2.0. Fully asynchronous. 
Using this API you can very easy transform request and response into dynamic objects. You don't need to create special models.
This library works with .NET Core applications.

## Install Package

Package is available on NuGet. Use this command **PM> Install-Package Zabbix.API.Core**

## Configuration

Initialize Zabbix object passing user login, password and url.
```cs
Zabbix zabbix = new Zabbix("admin", "zabbix", "http://zabbix.example/api_jsonrpc.php");
```
If it's needed use basic authentication.
```cs
Zabbix zabbix = new Zabbix("admin", "zabbix", "http://zabbix.example/api_jsonrpc.php", true);
```

Before call any request use **LoginAsync()** method. After all use  **LogoutAsync()**.

## Usage examples

Create new user
```cs
Zabbix zabbix = new Zabbix("admin", "zabbix", "http://zabbix.example/api_jsonrpc.php");
zabbix.LoginAsync().Wait();
			
Task<Response> createUserResponse = zabbix.GetResponseObjectAsync("user.create", new
{
    alias = "joe.kowalsky",
    passwd = "password",
    name = "Joe",
    surname = "Kowalsky",
    autologin = 1,
    autologout = 0,
    type = 2,
    usrgrps = new[]
    {
        new
        {
            usrgrpid = 7
        }
    },
    user_medias = new[]
    {
        new
        {
            mediatypeid = 1,
            sendto = "joe.kowalsky@example.com",
            active = 0,
            severity = 63,
            period = "1-7,00:00-24:00"
        }
    }
});

if (createUserResponse.Result.error != null)
{
    var error = createUserResponse.Result.error;
    Console.WriteLine(error.GetErrorMessage());
}
else
{
    userid = createUserResponse.Result.result.userids[0];
    Console.WriteLine(userid);
}

createUserResponse.Wait();
zabbix.LogoutAsync().Wait();
```

Check user exist
```cs
Zabbix zabbix = new Zabbix("admin", "zabbix", "http://zabbix.example/api_jsonrpc.php");
zabbix.LoginAsync().Wait();

Task<Response> checkUserExistResponse = zabbix.GetResponseObjectAsync("user.get", new
{
    output = "userid",
    filter = new { userid = 32 }
});

if (checkUserExistResponse.Result.result.Count > 0)
{
    Console.WriteLine($"User id {userid} exist");
}
else
{
    Console.WriteLine($"User id {userid} not exist");
}

checkUserExistResponse.Wait();
zabbix.LogoutAsync().Wait();
```

You can get json string using **GetResponseJsonAsync()** method.

```cs
Zabbix zabbix = new Zabbix("admin", "zabbix", "http://zabbix.example/api_jsonrpc.php");
zabbix.LoginAsync().Wait();

Task<string> createUserResponseJson = zabbix.GetResponseJsonAsync("user.create", new
{
    alias = "david.kowalsky",
    passwd = "password",
    name = "David",
    surname = "Kowalsky",
    autologin = 1,
    autologout = 0,
    type = 2,
    usrgrps = new[]
    {
        new
        {
            usrgrpid = 7
        }
    },
    user_medias = new[]
    {
        new
        {
            mediatypeid = 1,
            sendto = "david.kowalsky@example.com",
            active = 0,
            severity = 63,
            period = "1-7,00:00-24:00"
        }
    }
});

createUserResponseJson.Wait();
Console.WriteLine(createUserResponseJson.Result);
zabbix.LogoutAsync().Wait();
```

## License

MIT