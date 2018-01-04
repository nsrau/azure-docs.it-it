---
title: 'Aggiungere l''accesso a un''app web Node. js: Azure Active Directory B2C'
description: Come creare un'app web Node. js che esegue l'accesso agli utenti con Azure Active Directory B2C.
services: active-directory-b2c
author: PatAltimore
manager: mtillman
editor: dstrockis
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: b4a5db7e6769d7ebb0bcf0287b3a1bfb7932984a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: aggiungere l'accesso a un'app Web Node.js

**Passport** è il middleware di autenticazione per Node.js. Estremamente flessibile e modulare, Passport può essere installato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. Un set completo di strategie supporta l'autenticazione tramite nome utente e password, Facebook, Twitter e altro ancora.

Per Azure Active Directory (Azure AD), possibile installare il modulo e quindi aggiungere Azure AD `passport-azure-ad` plug-in.

È necessario:

1. Registrare un'applicazione usando Azure AD.
2. Configurare l'app per usare il plug-in `passport-azure-ad`.
3. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD.
4. Stampare i dati utente.

Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Per seguire la procedura, è possibile [scaricare la struttura dell'app come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). È anche possibile clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Al termine dell'esercitazione, verrà fornita l'applicazione completata.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant.  Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

## <a name="create-an-application"></a>Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app client che l'API Web saranno rappresentate da un unico **ID applicazione**, perché includono un'app per la logica. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un'**app Web**/**API Web** nell'applicazione.
- Immettere `http://localhost:3000/auth/openid/return` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e prenderne nota. Sarà necessario più avanti. Si noti che prima di usare questo valore è necessario [inserire un carattere di escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) .
- Copiare l' **ID applicazione** assegnato all'app. Sarà necessario più avanti.

## <a name="create-your-policies"></a>Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. È necessario creare i criteri per ogni tipo, come descritto nell' [articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Durante la creazione dei tre criteri assicurarsi di:

- Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
- Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`.  I nomi dei criteri saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall' [esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Aggiungere prerequisiti alla directory

Dalla riga di comando passare alla cartella radice, se necessario. Eseguire i comandi seguenti:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

È stato anche usato `passport-azure-ad` per l'anteprima nella struttura di avvio rapido.

- `npm install passport-azure-ad`

Verranno installate le librerie da cui dipende `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Configurare l'app per l'uso della strategia Passport-Node.js
Configurare il middleware Express per l'uso del protocollo di autenticazione OpenID Connect. Passport verrà usato, tra le altre azioni, per inviare le richieste di accesso e disconnessione, gestire le sessioni degli utenti e ottenere informazioni sugli utenti.

Aprire il file `config.js` nella radice del progetto e immettere i valori di configurazione dell'app nella sezione `exports.creds`.
- `clientID`: **ID applicazione** assegnato all'app nel portale di registrazione.
- `returnURL`: **URI di reindirizzamento** immesso nel portale.
- `tenantName`: nome del tenant dell'app, ad esempio, **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Aprire il file `app.js` nella radice del progetto. Aggiungere la chiamata seguente per richiamare la strategia `OIDCStrategy` fornita con `passport-azure-ad`.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Usare la strategia a cui si è appena fatto riferimento per gestire le richieste di accesso.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Passport usa un modello simile per tutte le strategie (inclusi Twitter e Facebook). Tutti i writer di strategie rispettano questo modello. Quando si esamina la strategia, è possibile osservare che le si passa un oggetto `function()` che ha come parametri un token e un oggetto `done`. La strategia risponde dopo avere eseguito tutte le relative operazioni. Archiviare l'utente e accantonare il token per non doverlo richiedere nuovamente.

> [!IMPORTANT]
Il codice precedente accetta tutti gli utenti autenticati dal server. Questa operazione è la registrazione automatica. Quando si usano server di produzione, si consente l'accesso solo agli utenti che abbiano eseguito un processo di registrazione configurato. Questo modello è frequente nelle app consumer che consentono di eseguire la registrazione usando Facebook, ma che chiedono anche di inserire altre informazioni. Se non si trattasse di un'applicazione di esempio, si estrarrebbe un indirizzo di posta elettronica dall'oggetto token restituito e si chiederebbe all'utente di immettere informazioni aggiuntive. Trattandosi di un server di test, è sufficiente aggiungere gli utenti al database in memoria.

Aggiungere i metodi che consentono di tenere traccia degli utenti che hanno eseguito l'accesso, come richiesto da Passport. Questa operazione include la serializzazione e la deserializzazione delle informazioni dell'utente:

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Aggiungere il codice per caricare il motore di Express. Nel codice seguente è possibile notare che vengono usati i modelli `/views` e `/routes` predefiniti forniti da Express.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Aggiungere le route `POST` che trasferiscono le richieste di accesso effettive al motore `passport-azure-ad`:

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD

L'app ora è configurata correttamente per comunicare con l'endpoint 2.0 mediante il protocollo di autenticazione OpenID Connect. `passport-azure-ad` ha gestito i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione utente. A questo punto, è sufficiente offrire agli utenti un modo per accedere e disconnettersi e per raccogliere informazioni aggiuntive sugli utenti connessi.

Aggiungere prima di tutto i metodi predefinito, di accesso, account e disconnessione al file `app.js`:

```javascript

//Routes (Section 4)

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

Per esaminare questi metodi in dettaglio:
- La route `/` viene reindirizzata alla visualizzazione `index.ejs` passando l'utente nella richiesta (se presente).
- La route `/account` verifica prima di tutto che l'utente sia autenticato. L'implementazione per questo passaggio è disponibile più avanti. quindi passa l'utente nella richiesta per poter ottenere altre informazioni sull'utente.
- La route `/login` chiama l'autenticatore `azuread-openidconnect` da `passport-azure-ad`. Se l'esito è negativo, la route reindirizza di nuovo l'utente a `/login`.
- `/logout` chiama semplicemente `logout.ejs` e la rispettiva route. I cookie vengono cancellati e quindi l'utente torna a `index.ejs`.


Per l'ultima parte di `app.js`, aggiungere il metodo `EnsureAuthenticated` usato nella route `/account`.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Creare infine il server stesso in `app.js`.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Creare le visualizzazioni e le route in Express per chiamare i criteri

`app.js` è stato completato. È sufficiente aggiungere le route e le visualizzazioni che consentono di chiamare i criteri di accesso e di iscrizione. In questo modo vengono gestite anche le route `/logout` e `/login` create.

Creare la route `/routes/index.js` nella directory radice.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Creare la route `/routes/user.js` nella directory radice.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Queste semplici route passano le richieste alle visualizzazioni. Includono l'utente, se è presente.

Creare la vista `/views/index.ejs` nella directory radice. Si tratta di una semplice pagina che chiama i criteri per l'accesso e la disconnessione. È possibile usarla anche per ottenere informazioni account. Si noti che è possibile usare `if (!user)` condizionale quando l'utente viene passato nella richiesta per fornire la prova che l'utente ha eseguito l'accesso.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Creare la visualizzazione `/views/account.ejs` nella directory radice per poter visualizzare informazioni aggiuntive che `passport-azure-ad` ha inserito nella richiesta dell'utente.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Ora è possibile compilare ed eseguire l'app.

Eseguire `node app.js` e passare a `http://localhost:3000`.


Iscriversi o accedere all'app usando la posta elettronica o Facebook. Disconnettersi e accedere nuovamente come un utente diverso.

##<a name="next-steps"></a>Passaggi successivi

Come riferimento viene fornito l'esempio completato, senza i valori di configurazione, [come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Ora è possibile passare ad argomenti più avanzati. È possibile provare a:

[Proteggere un'API Web usando il modello B2C in Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
