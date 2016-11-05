### <a name="server-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso server)
Per consentire a Servizi mobili di gestire il processo di autenticazione nella propria app, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio app di Azure è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app].

Dopo la registrazione del provider di identità, è sufficiente chiamare il metodo .login() con il nome del provider. Per accedere ad esempio con Facebook, utilizzare il codice seguente.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo di accesso precedente con uno dei seguenti: `microsoftaccount`, `facebook`, `twitter`, `google` o `aad`

In questo caso, il servizio app di Azure gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione del servizio app una volta eseguito correttamente l'accesso con il provider di identità. La funzione login, una volta completata, restituisce un oggetto JSON (user) che espone l'ID utente e il token di autenticazione del servizio app rispettivamente nei campi userId e authenticationToken. È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza.

### <a name="client-auth"></a>Procedura: Eseguire l'autenticazione con un provider (flusso client)
L'app può anche contattare il provider di identità in modo indipendente e quindi fornire il token restituito al servizio app per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

#### Esempio di base di autenticazione tramite social network
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

#### Esempio di account Microsoft
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

### <a name="auth-getinfo"></a>Procedura: Ottenere informazioni relative all'utente autenticato
Le informazioni di autenticazione per l'utente corrente possono essere recuperate dall'endpoint `/.auth/me` usando un metodo AJAX qualsiasi. Assicurarsi di impostare l'intestazione `X-ZUMO-AUTH` sul token di autenticazione. Il token di autenticazione è memorizzato in `client.currentUser.mobileServiceAuthenticationToken`. Ad esempio, per usare l'API fetch:

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

L'operazione di recupero è disponibile come pacchetto npm oppure può essere scaricato tramite il browser da CDNJS. È anche possibile usare jQuery o un'altra API AJAX per recuperare le informazioni. I dati saranno ricevuti come oggetto JSON.

<!---HONumber=AcomDC_0615_2016-->