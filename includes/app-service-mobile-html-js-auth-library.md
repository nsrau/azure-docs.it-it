### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso server)
Per consentire alle app per dispositivi mobili di gestire il processo di autenticazione nella propria app, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio app di Azure è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider.
Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](../articles/app-service-mobile/app-service-mobile-ios-get-started-users.md).

Dopo la registrazione del provider di identità, è sufficiente chiamare il metodo .login() con il nome del provider. Per accedere ad esempio con Facebook, utilizzare il codice seguente.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});app-service-mobile
app-service-mobile-ios-get-started-users value passed to the login method above to one of
the following: `microsoftaccount`, `facebook`, `twitter`, `google`, or `aad`.

In this case, Azure App Service manages the OAuth 2.0 authentication flow by displaying the login page of the selected
provider and generating a App Service authentication token after successful login with the identity provider. The login
function, when complete, returns a JSON object (user) that exposes both the user ID and App Service authentication token
in the userId and authenticationToken fields, respectively. This token can be cached and re-used until it expires.

###<a name="client-auth"></a>How to: Authenticate with a Provider (Client Flow)

Your app can also independently contact the identity provider and then provide the returned token to your App Service for
authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional
user data from the identity provider.

#### Social Authentication basic example

This example uses Facebook client SDK for authentication:

```
client.login( "facebook", {"access_token": token}) .done(function (results) { alert("You are now logged in as: " + results.userId); }, function (err) { alert("Error: " + err); });

```
This example assumes that the token provided by the respective provider SDK is stored in the token variable.

#### Microsoft Account example

The following example uses the Live SDK, which supports single-sign-on for Windows Store apps by using Microsoft Account:

```
WL.login({ scope: "wl.basic"}).then(function (result) { client.login( "microsoftaccount", {"authenticationToken": result.session.authentication_token}) .done(function(results){ alert("You are now logged in as: " + results.userId); }, function(error){ alert("Error: " + err); }); });

```

This example gets a token from Live Connect, which is supplied to your App Service by calling the login function.

###<a name="auth-getinfo"></a>How to: Obtain information about the authenticated user

The authentication information for the current user can be retrieved from the `/.auth/me` endpoint using any
AJAX method.  Ensure you set the `X-ZUMO-AUTH` header to your authentication token.  The authentication token
is stored in `client.currentUser.mobileServiceAuthenticationToken`.  For example, to use the fetch API:

```
var url = client.applicationUrl + '/.auth/me'; var headers = new Headers(); headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken); fetch(url, { headers: headers }) .then(function (data) { return data.json() }).then(function (user) { // The user object contains the claims for the authenticated user });
```

Fetch is available as an npm package or for browser download from CDNJS. You could also use
jQuery or another AJAX API to fetch the information.  Data will be received as a JSON object.



<!--HONumber=Nov16_HO3-->


