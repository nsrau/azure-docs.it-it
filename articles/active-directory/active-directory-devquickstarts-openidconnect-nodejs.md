<properties
	pageTitle="Introduzione alle procedure di accesso e disconnessione di Azure AD con Node.js"
	description="Come creare un'app Web Express MVC di Node.js che si integra con Azure AD per l'accesso."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# Accedere e disconnettersi dalle app Web con Azure AD


Passport viene usato per:

- Connettere l'utente all'app con Azure AD e Modello app 2.0.
- Visualizzare alcune informazioni relative all'utente.
- Disconnettere l'utente dall'app.

**Passport** è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

A questo scopo è necessario:

1. Registrare un'app.
2. Configurare l'app per l'uso della strategia Passport-azure-ad.
3. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD.
4. Visualizzare dati relativi all'utente.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Al termine dell'esercitazione, verrà fornita anche l'applicazione completata.

## 1. Registrare un'app
- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **Applicazioni**, quindi fare clic su Aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
    - Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    -	L' **URL accesso** è l'URL di base dell'app.  Il valore predefinito della struttura è `http://localhost:3000/auth/openid/return``.
    - L' **URI ID app** è un identificatore univoco dell'applicazione.  Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`
- Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco.  Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda Configura.

## 2. Aggiungere prerequisiti alla directory

Dalla riga di comando passare alla directory della cartella radice, se non è già stato fatto, ed eseguire i comandi seguenti:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Inoltre, è necessario anche `passport-azure-ad` :

- `npm install passport-azure-ad`

Verranno installate le librerie da cui dipende passport-azure-ad.

## 3. Configurare l'app per l'uso della strategia passport-node-js
In questo caso, verrà configurato il middleware Express per l'uso del protocollo di autenticazione OpenID Connect.  Passport verrà usato, tra le altre cose, per inviare le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente.

-	Per iniziare, aprire il file `web.config` nella radice del progetto e immettere i valori di configurazione dell'app nella sezione `<appSettings>`.
    -	`clientID`: rappresenta l'**ID applicazione** assegnato all'app nel portale di registrazione.
    -	`returnURL` rappresenta l'**URI di reindirizzamento** immesso nel portale.
    - `clientSecret` rappresenta il segreto generato nel portale.

- Aprire quindi il file `app.js`  nella radice del progetto e aggiungere la chiamata seguente per richiamare la strategia `OIDCStrategy` fornita con `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// aggiunge un logger

var log = bunyan.createLogger({ name: 'Microsoft OIDC Example Web Application' }); ```

- Successivamente, usare la strategia a cui è stato fatto riferimento per gestire le richieste di accesso.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
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
Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via) che soddisfano i requisiti degli scrittori della strategia. Osservando la strategia, è possibile notare che a quest'ultima è stata passata una funzione() con parametri token e done. La strategia verrà restituita al termine dell'esecuzione. Una volta restituita, è opportuno archiviare l'utente e mettere da parte il token in modo che non sia necessario richiederlo nuovamente.


> [AZURE.IMPORTANT]Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione. Questo è il modello in genere adottato per le app consumer che consentono di eseguire la registrazione con Facebook, ma che chiedono di immettere informazioni aggiuntive. Se non si trattasse di un'applicazione di esempio, si sarebbe estratto il messaggio di posta elettronica dall'oggetto token restituito e si sarebbe chiesto di immettere informazioni aggiuntive. Poiché si tratta di un server di test, è sufficiente aggiungere le informazioni al database in memoria.

- Successivamente, aggiungere i metodi che consentiranno di tenere traccia degli utenti connessi come richiesto da Passport. Questa operazione include la serializzazione e deserializzazione delle informazioni dell'utente:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
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

- Successivamente, aggiungere il codice per caricare il motore di Express. Di seguito è possibile visualizzare i modelli /views e /routes predefiniti forniti da Express.

```JavaScript

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
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Infine, aggiungere le route che trasferiranno le richieste di accesso correnti al motore `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## 4. Use Passport to issue sign-in and sign-out requests to Azure AD

Your app is now properly configured to communicate with the v2.0 endpoint using the OpenID Connect authentication protocol.  `passport-azure-ad` has taken care of all of the ugly details of crafting authentication messages, validating tokens from Azure AD, and maintaining user session.  All that remains is to give your users a way to sign in, sign out, and gather additional info on the logged in user.

- First, lets add the default, login, account, and logout methods to our `app.js` file:

```JavaScript

//Route (Section 4)

app.get('/', function(req, res){ res.render('index', { user: req.user }); });

app.get('/account', ensureAuthenticated, function(req, res){ res.render('account', { user: req.user }); });

app.get('/login', passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }), function(req, res) { log.info('Login was called in the Sample'); res.redirect('/'); });

app.get('/logout', function(req, res){ req.logout(); res.redirect('/'); });

```

-	Let's review these in detail:
    -	The `/` route will redirect to the index.ejs view passing the user in the request (if it exists)
    - The `/account` route will first ***ensure we are authenticated*** (we implement that below) and then pass the user in the request so that we can get additional information about the user.
    - The `/login` route will call our azuread-openidconnect authenticator from `passport-azuread` and if that doesn't succeed will redirect the user back to /login
    - The `/logout` will simply call the logout.ejs (and route) which clears cookies and then return the user back to index.ejs


- For the last part of `app.js`, let's add the EnsureAuthenticated method that is used in `/account` above.

```JavaScript

// Route middleware semplice per garantire che l'utente sia autenticato. (Section 4)

// Utilizzare questa route middleware sulle risorse che devono essere protette. Se // la richiesta è autenticata (in genere tramite una sessione di accesso permanente), // la richiesta procederà. In caso contrario, l'utente verrà reindirizzato alla // pagina di accesso. function ensureAuthenticated(req, res, next) { if (req.isAuthenticated()) { return next(); } res.redirect('/login') } ```

- Infine, creare il server in `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Creare le viste e le route in Express per visualizzare l'utente nel sito Web

Il file `app.js` è stato completato. Ora è sufficiente aggiungere le route e le viste che mostrano le informazioni ottenute dall'utente e gestire le route `/logout` e `/login` create.

- Creare la route `/routes/index.js` nella directory radice.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Creare la route `/routes/user.js` nella directory radice

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Queste semplici route passeranno la richiesta alle viste, incluso l'utente se presente.

- Creare la vista `/views/index.ejs` sotto la directory radice. Si tratta di una pagina semplice che chiama i metodi di accesso e disconnessione e consente di ottenere informazioni sull'account. Si noti che è possibile usare il parametro condizionale `if (!user)` poiché l'utente passato nella richiesta dimostra che si dispone di un utente registrato.

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

- Creare la vista `/views/account.ejs` nella directory radice affinché sia possibile visualizzare informazioni aggiuntive che `passport-azuread` ha inserito nella richiesta dell'utente.

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
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>

```

- Infine, aggiungere un layout per modificarne l'aspetto. Creare la vista '/views/layout.ejs' nella directory radice.

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
</html>```

Finally, build and run your app! 

Run `node app.js` and navigate to `http://localhost:3000`


Sign in with either a personal Microsoft Account or a work or school account, and notice how the user's identity is reflected in the /account list.  You now have a web app secured using industry standard protocols that can authenticate users with both their personal and work/school accounts.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


È ora possibile passare ad argomenti più avanzati. È possibile consultare:

[Proteggere un'API Web con Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!----HONumber=September15_HO1-->