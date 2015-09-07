<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Come creare un'app Web NodeJS che consente agli utenti di accedere con un account Microsoft personale, aziendale e dell'istituto di istruzione."
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

# Anteprima di Modello app 2.0: Aggiungere l'accesso a un'app Web NodeJS


  >[AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).


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

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

The completed application is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect URI indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `http://localhost:3000/auth/openid/return`.

## 2. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- In addition, we've included a special `passport-azure-ad` for our Preview in the skeleton of the quickstart. You'll need to install the pre-requisites in there as well.

- `cd /lib/passport-azure-ad`
- `npm install`

This will install the libraries that passport-azure-ad depend on.

## 3. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `clientSecret` is the secret you generated in the portal
    - The `realm` is the **Redirect URI** you entered in the portal without the route. (example: http//localhost:3000)
    - The `issuer` is where you append the **Application Id** after, as an example: https://sts.windows.net/96702724-991f-4576-bc90-be9862749ac5/

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy; ```

- Successivamente, usare la strategia a cui è stato fatto riferimento per gestire le richieste di accesso.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 

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
    skipUserProfile: true // doesn't fetch user profile
  },
  function(iss, sub, email, claims, profile, accessToken, refreshToken, done) {
    log.info('We received claims of: ', claims);
    log.info('Example: Email address we received was: ', claims.preferred_username);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(claims.preferred_username, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(claims);
          return done(null, claims);
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
  done(null, user.preferred_username);
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
    if (user.preferred_username === email) {
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

- Infine, aggiungere le route POST che trasferiranno le richieste di accesso correnti al motore `passport-azure-ad`:

```JavaScript

// Our POST routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.post('/auth/openid',
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

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});
```

## 4\. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD

L'app è ora configurata correttamente per comunicare con l'endpoint 2.0 mediante il protocollo di autenticazione OpenID Connect. `passport-azure-ad` ha preso in considerazione tutti i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione dell'utente. A questo punto, è sufficiente offrire agli utenti un modo per accedere, disconnettersi e raccogliere informazioni aggiuntive sull'utente connesso.

- Innanzitutto, aggiungere i metodi di accesso, account e disconnessione predefiniti al file `app.js`:

```JavaScript

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

-	Esaminare nel dettaglio questi aspetti:
    -	La route `/` viene reindirizzata alla vista index.ejs passando l'utente alla richiesta (se presente).
    - La route `/account` si ***assicurerà che l'autenticazione sia stata eseguita*** (vedere di seguito l'implementazione), quindi passerà l'utente nella richiesta in modo da ottenere informazioni aggiuntive sul quest'ultimo.
    - La route `/login` chiamerà l'autenticatore azuread-openidconnect da `passport-azuread` e, se l'operazione non ha esito positivo, l'utente verrà reindirizzato a /login.
    - La route `/logout` chiamerà logout.ejs (e la route) che consente di cancellare i cookie e restituisce quindi l'utente a index.ejs.


- Per l'ultima parte di `app.js`, aggiungere il metodo EnsureAuthenticated usato precedentemente in `/account`.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

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

- Creare la route `/routes/user.js` nella directory radice.

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
	<h2>Hello, <%= user.name %>.</h2>
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
<p>displayName: <%= user.name %></p>
<p>Profile ID: <%= user.sub %></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Infine, aggiungere un layout per modificarne l'aspetto. Creare la vista '/views/layout.ejs' nella directory radice.

```JavaScript

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

Infine compilare ed eseguire l'app.

Eseguire `node app.js` e passare a `http://localhost:3000`.


Accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione e osservare come l'identità dell'utente è indicata nell'elenco /account. È ora disponibile un'app Web protetta usando protocolli standard del settore in grado di autenticare gli utenti con account personali, aziendali e dell'istituto di istruzione.

##Passaggi successivi

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

È ora possibile passare ad argomenti più avanzati. È possibile consultare:

[Proteggere un'API Web con Modello app 2.0 in Node.js >>](active-directory-v2-devquickstarts-webapi-nodejs.md)

Per altre risorse consultare: - [l'anteprima di Modello app 2.0 >>](active-directory-appmodel-v2-overview.md) - [il tag "azure-active directory" StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO9-->