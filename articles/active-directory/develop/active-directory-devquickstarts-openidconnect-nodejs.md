---
title: Introduzione alle procedure di accesso e disconnessione di Azure AD con Node.js | Microsoft Docs
description: Informazioni sulla compilazione di un'app Web Express MVC di Node.js che si integra con Azure AD per l'accesso.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13317b016f9ff3955f376b858645c42668b0de42
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>Accesso e disconnessione all'app Web di Node. js con Azure AD
Passport viene usato per:

* Far accedere l'utente all'app con Azure Active Directory (Azure AD).
* Visualizzare informazioni relative all'utente.
* Disconnettere l'utente dall'app.

Passport è il middleware di autenticazione per Node.js. Passport, flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o restify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

Per effettuare questa operazione, eseguire i passaggi seguenti:

1. Registrare un'app.
2. Configurare l'app in modo che usi la strategia `passport-azure-ad`.
3. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD.
4. Stampare dati relativi all'utente.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Per seguire la procedura, è possibile [scaricare la struttura dell'app come file .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Al termine dell'esercitazione, verrà fornita anche l'applicazione completata.

## <a name="step-1-register-an-app"></a>Passaggio 1: Registrare un'app
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu nella parte superiore della pagina selezionare il proprio account. Nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.

3. Selezionare **More services** (Altri servizi) nel menu a sinistra della schermata, quindi scegliere **Azure Active Directory**.

4. Selezionare **Registrazioni per l'app**, quindi scegliere **Aggiungi**.

5. Seguire le istruzioni e creare una nuova **applicazione Web** e/o **API Web**.
  * Il **nome** dell'applicazione descrive l'applicazione agli utenti.

  * L' **URL accesso** è l'URL di base dell'app.  Il valore predefinito della struttura è `http://localhost:3000/auth/openid/return``.

6. Dopo la registrazione, Azure AD assegna all'app un ID applicazione univoco. Poiché questo valore sarà necessario nelle sezioni successive, è necessario copiarlo dalla pagina dell'applicazione.
7. Dalla pagina **Impostazioni** -> **Proprietà** dell'applicazione aggiornare l'URI dell'ID app. L' **URI ID app** è un identificatore univoco dell'applicazione. La convenzione consiste nell'usare il formato `https://<tenant-domain>/<app-name>`, ad esempio: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Passaggio 2: Aggiungere prerequisiti alla directory
1. Dalla riga di comando passare dalle directory alla cartella radice, se non è già stato fatto, ed eseguire i comandi seguenti:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Inoltre, è necessario `passport-azure-ad`:
    * `npm install passport-azure-ad`

Verranno installate le librerie da cui dipende `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Passaggio 3: Configurare l'app in modo che usi la strategia passport-node-js
In questo caso, verrà configurato Express in modo che usi il protocollo di autenticazione OpenID Connect.  Passport verrà usato, tra le altre cose, per inviare richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

1. Per iniziare, aprire il file `config.js` nella radice del progetto e immettere i valori di configurazione dell'app nella sezione `exports.creds`.

  * `clientID` rappresenta l'**ID applicazione** assegnato all'app nel portale di registrazione.

  * `returnURL` rappresenta l'**URI di reindirizzamento** immesso nel portale.

  * `clientSecret` rappresenta il segreto generato nel portale.

2. In seguito, aprire il file `app.js` nella radice del progetto. Quindi, aggiungere la chiamata seguente per richiamare la strategia `OIDCStrategy` fornita con `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Successivamente, usare la strategia a cui è stato fatto riferimento per gestire le richieste di accesso.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via) seguite dagli scrittori della strategia. Osservando la strategia, è possibile notare che a quest'ultima è stata passata una funzione con parametri token e done. La strategia torna indietro dopo eseguito il suo lavoro. A questo punto è necessario archiviare l'utente e accantonare il token in modo che non sia necessario richiederlo nuovamente.

> [!IMPORTANT]
Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Si consiglia di non consentire agli utenti di eseguire l'autenticazione per un server di produzione senza prima prevedere un processo di registrazione. Questo è il criterio usato in genere per le app consumer, che consentono di eseguire la registrazione con Facebook ma poi chiedono di immettere informazioni aggiuntive. Se non si trattasse di un'applicazione di esempio, sarebbe stato possibile estrarre l'indirizzo e-mail dell'utente dall'oggetto token restituito e chiedere all'utente di immettere le informazioni aggiuntive. Poiché si tratta di un server di test, le informazioni vengono aggiunte al database in memoria.


4. Successivamente, aggiungere i metodi che consentiranno di tenere traccia degli utenti connessi come richiesto da Passport. Questi metodi includono la serializzazione e deserializzazione delle informazioni dell'utente.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
            var users = [];

            var findByEmail = function(email, fn) {
            for (var i = 0, len = users.length; i < len; i++) {
                var user = users[i];
            log.info('we are using user: ', user);
                if (user.email === email) {
                return fn(null, user);
                }
            }
            return fn(null, null);
            };
    ```

5.  Quindi aggiungere il codice per caricare il motore Express. Qui usiamo i modelli /views e /routes predefiniti forniti da Express.

    ```JavaScript

        // configure Express (section 2)

            var app = express();
            app.configure(function() {
          app.set('views', __dirname + '/views');
          app.set('view engine', 'ejs');
          app.use(express.logger());
          app.use(express.methodOverride());
          app.use(cookieParser());
          app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
          app.use(bodyParser.urlencoded({ extended : true }));
          // Initialize Passport!  Also use passport.session() middleware, to support
          // persistent login sessions (recommended).
          app.use(passport.initialize());
          app.use(passport.session());
          app.use(app.router);
          app.use(express.static(__dirname + '/../../public'));
        });

    ```

6. Infine aggiungere le route che trasferiscono le richieste di accesso effettive al motore `passport-azure-ad`:


       ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
       ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passaggio 4: Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD
L'app ora è configurata correttamente per comunicare con l'endpoint mediante il protocollo di autenticazione OpenID Connect.  `passport-azure-ad` ha gestito tutti i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione delle sessioni utente. A questo punto è sufficiente offrire agli utenti un modo per accedere e disconnettersi e per raccogliere informazioni aggiuntive sugli utenti connessi.

1. Aggiungere prima di tutto i metodi predefinito, di accesso, account e disconnessione al file `app.js`:

    ```JavaScript

        //Routes (section 4)

        app.get('/', function(req, res){
          res.render('index', { user: req.user });
        });

        app.get('/account', ensureAuthenticated, function(req, res){
          res.render('account', { user: req.user });
        });

        app.get('/login',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  Esaminare nel dettaglio questi aspetti:

  * Il percorso `/` reindirizza alla vista index.ejs passando l'utente nella richiesta (se presente).
  * Il percorso `/account` prima di tutto *verifica che l'autenticazione sia stata eseguita* (l'implementazione nell'esempio di seguito), quindi passa l'utente nella richiesta in modo da ottenere informazioni aggiuntive su quest'ultimo.
  * Il percorso `/login` richiama l'autenticatore azuread-openidconnect da `passport-azuread`. Se l'operazione ha esito negativo, il percorso reindirizza di nuovo l'utente a /login.
  * Il percorso `/logout` chiama logout.ejs (e il percorso), consentendo di cancellare i cookie e riportando quindi l'utente a index.ejs.

3. Per l'ultima parte di `app.js`, aggiungere il metodo **EnsureAuthenticated** usato in `/account`, come illustrato in precedenza.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Infine, creare il server stesso in `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Passaggio 5: Creare le viste e i percorsi in Express per visualizzare l'utente nel sito Web
Ora il file `app.js` è completo. È sufficiente aggiungere i percorsi e le viste che mostrano all'utente le informazioni ottenute e gestire i percorsi `/logout` e `/login` creati.

1. Creare la route `/routes/index.js` nella directory radice.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Creare la route `/routes/user.js` nella directory radice.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Queste passeranno la richiesta alle viste, incluso l'utente se presente.

3. Creare la vista `/views/index.ejs` nella directory radice. Si tratta di una pagina semplice che chiama i metodi di accesso e disconnessione e consente di ottenere informazioni sull'account. Si noti che è possibile usare il parametro condizionale `if (!user)` in quanto l'utente passato nella richiesta dimostra che c'è un utente che ha eseguito l'accesso.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Creare la vista `/views/account.ejs` nella directory radice affinché sia possibile visualizzare informazioni aggiuntive che `passport-azuread` ha inserito nella richiesta dell'utente.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Ora viene aggiunto un layout per modificare l'aspetto. Creare la vista '/views/layout.ejs' nella directory radice.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>Passaggi successivi
Per finire, compilare ed eseguire l'app. Eseguire `node app.js`, quindi passare a `http://localhost:3000`.

Accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione e osservare come l'identità dell'utente è indicata nell'elenco /account. È ora disponibile un'app Web protetta con protocolli standard del settore in grado di autenticare gli utenti con account personali, aziendali e dell'istituto di istruzione.

Come riferimento viene fornito l'esempio completato, senza i valori di configurazione, [come file con estensione zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). In alternativa, è possibile eseguire la clonazione da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Ora è possibile passare ad argomenti più avanzati. È consigliabile provare:

[Proteggere un'API Web con Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
