### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso server)
Per consentire alle app per dispositivi mobili di gestire il processo di autenticazione nella propria app, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio app di Azure è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider.
Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Dopo aver effettuato la registrazione del provider di identità, chiamare il metodo `.login()` con il nome del provider. Per accedere ad esempio con Facebook, utilizzare il codice seguente:

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

I valori validi per il provider sono "aad", "facebook", "google", "microsoftaccount" e "twitter".

> [!NOTE]
> L'autenticazione di Google attualmente non funziona tramite Flusso server.  Per eseguire l'autenticazione con Google, è necessario usare un [metodo di flusso client](#client-auth).

In questo caso, il Servizio app di Azure gestisce il flusso di autenticazione OAuth 2.0.  Viene visualizzata la pagina di accesso al provider selezionato e viene generato un token di autenticazione del Servizio app dopo aver eseguito correttamente l'accesso con il provider di identità. La funzione login, una volta completata, restituisce un oggetto JSON che espone l'ID utente e il token di autenticazione del servizio app rispettivamente nei campi userId e authenticationToken. È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza.

###<a name="a-nameclient-authahow-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso client)

L'app può anche contattare il provider di identità in modo indipendente e quindi fornire il token restituito al servizio app per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

#### <a name="social-authentication-basic-example"></a>Esempio di base di autenticazione tramite social network

Questo esempio utilizza il client SDK di Facebook per l'autenticazione:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
In questo esempio si presuppone che il token fornito dall'SDK del rispettivo provider sia archiviato nella variabile token.

#### <a name="microsoft-account-example"></a>Esempio di account Microsoft

Nell'esempio seguente viene utilizzato Live SDK, che supporta Single-Sign-On per le app di Windows Store tramite un account Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

Questo esempio ottiene un token da Live Connect, che viene fornito al servizio app chiamando la funzione login.

###<a name="a-nameauth-getinfoahow-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Procedura: Ottenere informazioni relative all'utente autenticato

Le informazioni di autenticazione possono essere recuperate dall'endpoint `/.auth/me` usando una chiamata HTTP con una libreria AJAX qualsiasi.  Assicurarsi di impostare l'intestazione `X-ZUMO-AUTH` sul token di autenticazione.  Il token di autenticazione è memorizzato in `client.currentUser.mobileServiceAuthenticationToken`.  Ad esempio, per usare l'API fetch:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

L'operazione di recupero è disponibile come [pacchetto npm](https://www.npmjs.com/package/whatwg-fetch) oppure può essere scaricato tramite il browser da [CDNJS](https://cdnjs.com/libraries/fetch). È anche possibile usare jQuery o un'altra API AJAX per recuperare le informazioni.  I dati saranno ricevuti come oggetto JSON.


<!--HONumber=Dec16_HO1-->


