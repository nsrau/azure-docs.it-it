
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare Microsoft Authentication Library (MSAL) per ottenere un token per l'API Microsoft Graph

Aggiungere il codice seguente a `index.html` all'interno del tag `<body>`:

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni
#### <a name="sign-in-the-user"></a>Connettere l'utente
Dopo che un utente ha fatto clic sul pulsante "Call Microsoft Graph API" per la prima volta, viene eseguito `loginPopup` per far accedere l'utente. Questo metodo restituisce una finestra del browser in cui si chiede all'utente di eseguire l'accesso. Dopo il completamento dell'accesso, le informazioni sull'utente vengono memorizzate nella cache da MSAL e viene restituito un token. Questo token è noto come *token ID* e contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Se si prevede di usare per qualsiasi scopo i dati forniti da questo token, è necessario verificare che il token venga convalidato dal server back-end per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'app a singola pagina generata da questa guida non usa direttamente il token ID, ma chiama `acquireTokenSilent` e/o `acquireTokenPopup` per acquisire *token di accesso* usato per eseguire una query sull'API Microsoft Graph. Se è necessario un esempio che convalida il token ID, vedere [questa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "esempio active-directory-javascript-singlepageapp-dotnet-webapi-v2 di Github") applicazione di esempio in GitHub. L'esempio usa un'API Web ASP.NET per la convalida del token.

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo
Se si chiama il metodo `acquireTokenPopup(scope)`, viene visualizzata una finestra del browser in cui si chiede all'utente di eseguire l'accesso. In genere, le applicazioni chiedono agli utenti di accedere in modo interattivo la prima volta che devono accedere a una risorsa protetta o quando un'operazione invisibile di acquisizione di un token ha esito negativo (ad esempio, perché la password dell'utente è scaduta).

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile
Il metodo ` acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito `loginPopup` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`: le chiamate per richiedere o rinnovare token vengono effettuate in modo invisibile per l'utente.
Alla fine, tuttavia, `acquireTokenSilent` avrà esito negativo perché, ad esempio, l'utente avrà modificato la password in un altro dispositivo. L'applicazione può gestire questa eccezione in due modi:

1.  Effettuare subito una chiamata ad `acquireTokenPopup`, per poter chiedere all'utente di eseguire l'accesso. Questo criterio viene usato in genere nelle applicazioni online in cui non sono disponibili contenuti offline per l'utente. L'esempio generato da questa configurazione guidata usa questo modello.
2. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questo metodo viene usato in genere quando l'utente può accedere ad altre funzionalità dell'applicazione senza essere interrotto, ad esempio quando nell'applicazione sono disponibili contenuti offline. In questo caso, l'utente può decidere quando eseguire l'accesso per accedere alla risorsa protetta o per aggiornare informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `acquireTokenSilent` se la rete viene ripristinata dopo essere stata temporaneamente non disponibile.

Un altro scenario in cui `acquireTokenSilent` avrà esito negativo è quello in cui l'utente non ha ancora dato il consenso all'ambito richiesto dalla chiamata. Ad esempio, la prima volta che un utente esegue l'applicazione generata da questa guida, viene fornito l'ambito *user.read* come parametro. Poiché l'utente connesso non ha mai dato il consenso alla lettura del proprio profilo, `acquireTokenSilent` genererà un'eccezione contenente `interaction_required`, che in genere indica che, per risolvere il problema, è consigliabile chiamare `acquireTokenPopup`. In questo caso, verrà creata un finestra popup per visualizzare la schermata di consenso per l'ambito necessario.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il codice seguente a index.html all'interno del tag `<body>`:

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

Nell'applicazione di esempio creata da questa guida viene usato il metodo `callWebApiWithToken()` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per l'applicazione di esempio creata da questa guida, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

Aggiungere il codice seguente a index.html all'interno del tag `<body>`:

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
