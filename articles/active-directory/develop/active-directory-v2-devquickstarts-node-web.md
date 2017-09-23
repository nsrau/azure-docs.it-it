---
title: Accesso all'app Web Node.js con Azure Active Directory 2.0 | Microsoft Docs
description: Informazioni su come creare un'app Web Node.js che consente agli utenti di accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Aggiungere l'accesso a un'app Web Node.js

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory usano l'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0 o l'endpoint v1.0, leggere le informazioni sulle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
> 

In questa esercitazione si userà Passport per eseguire le operazioni seguenti:

* In un'app Web, consentire l'accesso dell'utente con Azure Active Directory (Azure AD) e l'endpoint 2.0.
* Visualizzare informazioni relative all'utente.
* Disconnettere l'utente dall'app.

**Passport** è il middleware di autenticazione per Node.js. Passport, flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. In Passport una gamma completa di strategie supporta l'autenticazione usando un nome utente e password, Facebook, Twitter o altre opzioni. È stata sviluppata una strategia per Azure AD. Questo articolo illustra come installare il modulo e quindi aggiungere il plug-in `passport-azure-ad` di Azure AD.

## <a name="download"></a>Scaricare
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Per seguire l'esercitazione, è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Al termine dell'esercitazione, è anche possibile ottenere l'applicazione completata.

## <a name="1-register-an-app"></a>1: Registrare un'app
Per registrare un'app creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md). Assicurarsi di:

* Copiare l'**ID applicazione** assegnato all'app. È necessario per eseguire questa esercitazione.
* Aggiungere la piattaforma **Web** per l'app.
* Copiare l' **URI di reindirizzamento** dal portale. È necessario usare il valore URI predefinito `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>2: Aggiungere prerequisiti alla directory
Al prompt dei comandi passare alla cartella radice, se necessario. Eseguire i comandi seguenti:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

Nella struttura di avvio rapido si usa anche `passport-azure-ad`:

* `npm install passport-azure-ad`

In questo modo, vengono installate le librerie usate da `passport-azure-ad`.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: Configurare l'app per l'uso della strategia passport-node-js
Configurare il middleware Express per l'uso del protocollo di autenticazione OpenID Connect. Passport verrà usato, tra le altre cose, per inviare richieste di accesso e disconnessione, gestire la sessione utente e ottenere informazioni sull'utente.

1.  Nella radice del progetto aprire il file Config.js. Nella sezione `exports.creds` immettere i valori di configurazione dell'app.
  
  * `clientID`: l'**ID applicazione** assegnato all'app nel portale di Azure.
  * `returnURL`: l'**URI di reindirizzamento** immesso nel portale.
  * `clientSecret`: il segreto generato nel portale.

2.  Nella radice del progetto aprire il file App.js. Per richiamare la strategia OIDCStrategy, fornita con `passport-azure-ad`, aggiungere la chiamata seguente:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Per gestire le richieste di accesso usare la strategia a cui si è appena fatto riferimento:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via). Tutti i writer di strategie rispettano questo modello. Passare alla strategia un elemento `function()` che usa un token e `done` come parametri. La strategia viene restituita dopo l'esecuzione di tutte le operazioni. Archiviare l'utente e mettere da parte il token per non doverlo richiedere nuovamente.

  > [!IMPORTANT]
  > Il codice precedente accetta qualsiasi utente che può eseguire l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione a scelta. Questo è il criterio solitamente adottato per le app consumer. L'app potrebbe consentire di eseguire la registrazione con Facebook, ma poi richiede di inserire informazioni aggiuntive. Se per questa esercitazione non è stato usato un programma della riga di comando, è possibile estrarre il messaggio di posta elettronica dall'oggetto token restituito. Quindi, è possibile chiedere all'utente di inserire informazioni aggiuntive. Trattandosi di un server di test, è possibile aggiungere l'utente direttamente al database in-memory.
  > 
  > 

4.  Aggiungere i metodi che consentono di tenere traccia degli utenti che hanno eseguito l'accesso, come richiesto da Passport. Questa operazione include la serializzazione e deserializzazione delle informazioni dell'utente:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Aggiungere il codice che carica il motore di Express. Verranno usati i modelli /views e /routes predefiniti forniti da Express:

  ```JavaScript

  // Set up Express (section 2)

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

6.  Aggiungere le route POST che trasferiscono le richieste di accesso effettive al motore `passport-azure-ad`:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD
L'app è configurata ora per comunicare con l'endpoint 2.0 mediante il protocollo di autenticazione OpenID Connect. La strategia `passport-azure-ad` gestisce tutti i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione utente. A questo punto, è sufficiente offrire agli utenti un modo per accedere e disconnettersi e per raccogliere informazioni aggiuntive sull'utente connesso.

1.  Aggiungere al file App.js i metodi **default**, **login**, **account** e **logout**:

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
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Di seguito sono riportati i dettagli:
    
    * La route `/` reindirizza alla vista index.ejs e passa l'utente nella richiesta (se esistente).
    * La route `/account` prima *verifica che l'utente sia autenticato* (questo passaggio viene implementato nel codice seguente) e quindi passa l'utente nella richiesta. In questo modo è possibile ottenere altre informazioni sull'utente.
    * La route `/login` chiama l'autenticatore `azuread-openidconnect` da `passport-azuread`. Se l'operazione ha esito negativo, reindirizza di nuovo l'utente a `/login`.
    * La route `/logout` chiama la vista logout.ejs (e route). I cookie vengono cancellati e quindi l'utente torna a index.ejs.

2.  Aggiungere il metodo **EnsureAuthenticated** usato in precedenza in `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  In App.js creare il server:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: Creare le viste e le route in Express per visualizzare l'utente nel sito Web
Aggiungere le route e le viste che mostrano informazioni all'utente. Le route e le viste gestiscono anche le route `/logout` e `/login` precedentemente create.

1. Nella directory radice creare la route `/routes/index.js`.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  Nella directory radice creare la route `/routes/user.js`.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` e `/routes/user.js` sono semplici route che passeranno la richiesta alle viste, incluso l'utente se presente.

3.  Nella directory radice creare la vista `/views/index.ejs`. Questa pagina chiama i metodi **login** e **logout**. È possibile anche usare la vista `/views/index.ejs` per acquisire informazioni sull'account. È possibile usare l'espressione `if (!user)` condizionale quando l'utente viene passato nella richiesta, a prova che l'utente ha eseguito l'accesso.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  Nella directory radice creare la vista `/views/account.ejs`. La vista `/views/account.ejs` consente di visualizzare informazioni aggiuntive che `passport-azuread` inserisce nella richiesta dell'utente.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Aggiungere un layout. Nella directory radice creare la vista `/views/layout.ejs`.

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Per compilare ed eseguire l'app, eseguire `node app.js` e quindi passare a `http://localhost:3000`.

7.  Accedere con un account Microsoft personale o con un account aziendale o dell'istituto di istruzione. Osservare come l'identità dell'utente sia riflessa nell'elenco /account. 

Si dispone ora di un'app Web protetta tramite protocolli standard del settore. È possibile autenticare gli utenti nell'app usando i relativi account personali e aziendali o dell'istituto di istruzione.

## <a name="next-steps"></a>Passaggi successivi
Come riferimento viene fornito l'esempio completato, senza i valori di configurazione, [come file zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

È possibile ora passare ad argomenti più avanzati. È consigliabile provare:

[Proteggere un'API Web Node.js usando l'endpoint 2.0](active-directory-v2-devquickstarts-node-api.md)

Altre risorse:

* [Guida per sviluppatori Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [StackOverflow: tag "azure-active-directory"](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile eseguire l'iscrizione per ricevere una notifica quando si verificano problemi di protezione. Nella pagina [Notifiche sulla sicurezza Microsoft](https://technet.microsoft.com/security/dd252948) effettuare la sottoscrizione agli advisory sulla sicurezza.


