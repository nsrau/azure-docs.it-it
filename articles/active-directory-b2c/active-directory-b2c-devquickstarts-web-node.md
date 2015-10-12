<properties
	pageTitle="Aggiungere l’accesso a un’app Web nodeJS per l’anteprima di Azure B2C | Microsoft Azure"
	description="Come creare un'app Web Node. js che esegua l’accesso degli utenti con un tenant B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Anteprima di B2C: Aggiungere l'accesso a un'app Web NodeJS


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

**Passport** è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Impostare l'app per l'uso del plug-in azure-ad-passport di Passport.
3. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD.
4. Visualizzare dati relativi all'utente.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

The completed application is provided at the end of this tutorial as well.

> [AZURE.WARNING] 	For our B2C Preview you must use the same client ID/Application ID and policies for both the Web-API task server and the client that connects to it. This is true for our iOS and Android tutorials. If you have previously created an application in either of those quickstarts, please use those values instead of creating new ones below.

## 1. Get an Azure AD B2C directory

Before you can use Azure AD B2C, you must create a directory, or tenant.  A directory is a container for all your users, apps, groups, and so on.  If you don't have
one already, go [create a B2C directory](active-directory-b2c-get-started.md) before moving on.

## 2. Create an application

Now you need to create an app in your B2C directory, which gives Azure AD some information that it needs to securely communicate with your app.  Both the client app and web API will be represented by a single **Application ID** in this case, since they comprise one logical app.  To create an app,
follow [these instructions](active-directory-b2c-app-registration.md).  Be sure to

- Include a **web app/web api** in the application
- Enter `http://localhost/TodoListService` as a **Reply URL** - it is the default URL for this code sample.
- Create an **Application Secret** for your application and copy it down.  You will need it shortly.
- Copy down the **Application ID** that is assigned to your app.  You will also need it shortly.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3. Create your policies

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- Choose the **Display Name** and a few other sign-up attributes in your sign-up policy.
- Choose the **Display Name** and **Object ID** application claims in every policy.  You can choose other claims as well.
- Copy down the **Name** of each policy after you create it.  It should have the prefix `b2c_1_`.  You'll need those policy names shortly. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Once you have your three policies successfully created, you're ready to build your app.

Note that this article does not cover how to use the policies you just created.  If you want to learn about how policies work in Azure AD B2C,
you should start with the [.NET Web App getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

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

- In addition, we've use `passport-azure-ad` for our Preview in the skeleton of the quickstart. 

- `npm install passport-azure-ad`


This will install the libraries that passport-azure-ad depend on.

## 5. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `tenantName:` is the **tenant name** of your app, e.g. contoso.onmicrosoft.com

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDCStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Aggiungere alcuni log di registrazione var = bunyan.createLogger ({nome: 'Applicazione Web di Microsoft OIDC di esempio'}); ```

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

- Infine, aggiungere le route POST che trasferiranno le richieste di accesso correnti al motore `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
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
    
    res.redirect('/');
  });
```

## 4\. Usare Passport per inviare le richieste di accesso e disconnessione ad Azure AD

L'app è ora configurata correttamente per comunicare con l'endpoint v2.0 mediante il protocollo di autenticazione OpenID Connect. `passport-azure-ad` ha preso in considerazione tutti i dettagli relativi alla creazione dei messaggi di autenticazione, alla convalida dei token da Azure AD e alla gestione della sessione dell'utente. A questo punto, è sufficiente offrire agli utenti un modo per accedere, disconnettersi e raccogliere informazioni aggiuntive sull'utente connesso.

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


## 5\. Creare le viste e le route in express per richiamare i criteri

Il file `app.js` è stato completato. Ora è sufficiente aggiungere le route e le viste che consentono di richiamare i criteri di registrazione e accesso e gestire le route `/logout` e `/login` create.

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

- Creare la vista `/views/index.ejs` nella directory radice. Si tratta di una pagina semplice che richiama i criteri di registrazione e accesso e consente di ottenere informazioni sull'account. Si noti che è possibile usare il parametro condizionale `if (!user)` poiché l'utente passato nella richiesta dimostra che si dispone di un utente registrato.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
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

Infine compilare ed eseguire l'app.

Eseguire `node app.js` e passare a `http://localhost:3000`.


Iscrizione o accesso all’app tramite posta elettronica o Facebook. Disconnettersi e accedere nuovamente come un utente diverso.



##Passaggi successivi

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

È ora possibile passare ad argomenti più avanzati. È possibile consultare:

[Proteggere un'API Web con Modello B2C in Node.js >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO1-->