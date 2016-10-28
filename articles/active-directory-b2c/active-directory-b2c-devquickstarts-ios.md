<properties
	pageTitle="Azure Active Directory B2C: chiamare un'API Web da un'applicazione iOS con librerie di terze parti | Microsoft Azure"
	description="Questo articolo illustra come creare un'app iOS di elenco attività che chiama un'API Web Node.js usando token di connessione OAuth 2.0 con una libreria di terze parti"
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="07/26/2016"
	ms.author="brandwe"/>

# Azure AD B2C: chiamare un'API Web da un'applicazione iOS con una libreria di terze parti

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Questo consente agli sviluppatori di sfruttare le librerie che vogliono integrare con i servizi. Per aiutare gli sviluppatori a usare la piattaforma con altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano la [specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) potrà connettersi alla piattaforma delle identità Microsoft.


Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Per alcune funzionalità della piattaforma che trovano espressione in questi standard, ad esempio l'accesso condizionale e la gestione criteri di Intune, è necessario usare le librerie di identità di Microsoft Azure.
   
Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dalla piattaforma B2C. Per determinare se è consigliabile usare la piattaforma B2C, vedere l'articolo relativo alle [limitazioni di B2C](active-directory-b2c-limitations.md).


## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non ne è già disponibile una, prima di continuare [creare una directory B2C](active-directory-b2c-get-started.md).

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app che l'API Web sono rappresentate in questo caso da un singolo **ID applicazione**, perché includono un'app per la logica. Per creare un'app, seguire [questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di:

- Includere un **dispositivo mobile** nell'applicazione.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C, ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md). Questa app contiene un'esperienza di identità che combina accesso e iscrizione. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei criteri, assicurarsi di:

- Scegliere **Nome visualizzato** e gli attributi di iscrizione nei criteri.
- Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. Il nome dei criteri sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i criteri, è possibile passare alla creazione dell'app.


## Scaricare il codice

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c). Per seguire la procedura, è possibile [scaricare l'app come file ZIP](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c) (/archive/master.zip) o clonarla:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

In alternativa, scaricare il codice completo e iniziare subito:

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## Scaricare la libreria di terze parti nxoauth2 e avviare un'area di lavoro

Per questa procedura dettagliata verrà usato OAuth2Client di GitHub, una libreria OAuth2 per Mac OS X e iOS (Cocoa e Cocoa Touch). Questa libreria si basa sulla bozza 10 della specifica OAuth2. Implementa il profilo dell'applicazione nativa e supporta l'endpoint di autorizzazione dell'utente finale. Ecco tutto ciò che serve per l'integrazione con la piattaforma delle identità Microsoft.

### Aggiunta della libreria al progetto con CocoaPods

CocoaPods è un gestore delle dipendenze per i progetti Xcode. Gestisce automaticamente i passaggi di installazione precedenti.

```
$ vi Podfile
```
Aggiungere il codice seguente al podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Caricare ora il podfile usando cocoapods. Verrà creata una nuova area di lavoro XCode che verrà caricata.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## Struttura del progetto

Nello scheletro è configurata la struttura seguente per il progetto:

* Una **visualizzazione master** con un riquadro attività
* Una **visualizzazione per l'aggiunta di attività** per i dati relativi all'attività selezionata
* Una **visualizzazione di accesso** che consente a un utente di accedere all'app

Si passerà a vari file del progetto per aggiungere l'autenticazione. Altre parti del codice, ad esempio il codice visivo, non sono pertinenti all'identità e vengono fornite automaticamente.

## Creare il file `settings.plist` per l'applicazione

La presenza di una posizione centralizzata per l'inserimento dei valori di configurazione semplifica la configurazione dell'applicazione e consente anche di comprendere le finalità di ogni impostazione nell'applicazione. Per passare questi valori all'applicazione verrà sfruttato l'*elenco delle proprietà*.

* Creare o aprire il file `settings.plist` in `Supporting Files` nell'area di lavoro dell'applicazione

* Immettere i valori seguenti (che verranno presto esaminati in dettaglio)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>accountIdentifier</key>
	<string>B2C_Acccount</string>
	<key>clientID</key>
	<string><client ID></string>
	<key>clientSecret</key>
	<string></string>
	<key>authURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
	<key>loginURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/login</string>
	<key>bhh</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>tokenURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
	<key>keychain</key>
	<string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
	<key>contentType</key>
	<string>application/x-www-form-urlencoded</string>
	<key>taskAPI</key>
	<string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Ecco il dettaglio di questi valori.


Si noterà che in `authURL`, `loginURL`, `bhh` e `tokenURL` è necessario immettere il nome del tenant. Si tratta del nome del tenant B2C assegnato all'utente, ad esempio `kidventusb2c.onmicrosoft.com`. Se si usano le librerie di identità open source di Microsoft Azure, verrà effettuato il pull automatico di questi dati usando l'endpoint dei metadati. L'estrazione di questi valori è stata eseguita automaticamente.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Il valore `keychain` è il contenitore che verrà usato dalla libreria NXOAuth2Client per creare un portachiavi per l'archiviazione dei token. Per ottenere l'accesso SSO in più app, è possibile specificare lo stesso portachiavi in ogni applicazione, oltre a richiedere l'uso di tale portachiavi nei diritti XCode. Questa procedura è illustrata nella documentazione di Apple.

Gli elementi `<policy name>` alla fine di ogni URL sono le posizioni in cui verranno inseriti i criteri creati sopra. L'app chiamerà tali criteri a seconda del flusso.

`taskAPI` è l'endpoint REST che verrà chiamato con il token B2C per aggiungere attività o eseguire query sulle attività esistenti. È stato configurato specificamente per l'esempio. Non è necessario modificarlo perché l'esempio funzioni.

Gli altri valori sono necessari per usare la libreria e si limitano a creare automaticamente le posizioni per inserire i valori nel contesto.

Dopo aver creato il file `settings.plist`, è necessario il codice per leggere il file.

## Configurare una classe AppData per leggere le impostazioni

Creare un file semplice che analizzi il file `settngs.plist` creato sopra e renda le impostazioni disponibili in futuro per qualsiasi classe. Per evitare di creare una nuova copia dei dati ogni volta che vengono richiesti da una classe, verrà usato un modello singleton e ogni volta che vengono richieste le impostazioni verrà restituita la stessa istanza creata.

* Creare un file `AppData.h`:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Creare un file `AppData.m`:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Per accedere facilmente ai dati, è ora sufficiente chiamare `  AppData *data = [AppData getInstance];` in tutte le classi, come verrà illustrato di seguito.



## Configurare la libreria NXOAuth2Client in AppDelegate

Per configurare la libreria NXOAuthClient, sono necessari alcuni valori. Al termine, è possibile usare il token acquisito per chiamare l'API REST. Poiché ogni volta che viene caricata l'applicazione verrà chiamato `AppDelegate`, è opportuno inserire i valori di configurazione in tale file.
* Aprire il file `AppDelegate.m`.

* Importare alcuni file di intestazione che verranno usati più avanti.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Aggiungere il metodo `setupOAuth2AccountStore` in AppDelegate.

È necessario creare un AccountStore e quindi inserire i dati appena letti dal file `settings.plist`.

A questo punto è opportuno conoscere alcuni aspetti del servizio B2C che renderanno più comprensibile questo codice:


1. Azure AD B2C usa i *criteri* specificati nei parametri di query per gestire la richiesta. In questo modo, Azure Active Directory può fungere da servizio indipendente per l'applicazione. Per specificare questi parametri di query aggiuntivi, è necessario fornire i parametri dei criteri personalizzati al metodo `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:`.

2. Azure AD B2C usa gli ambiti così come gli altri server OAuth2. Poiché l'uso di B2C riguarda in uguale misura l'autenticazione di un utente e l'accesso alle risorse, tuttavia, per il funzionamento corretto del flusso sono assolutamente necessari alcuni ambiti, ovvero l'ambito `openid`. Gli SDK Microsoft Identity forniscono automaticamente l'ambito `openid`, che non è quindi visibile nella configurazione dell'SDK. Poiché viene usata una libreria di terze parti, tuttavia, è necessario specificare questo ambito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Successivamente, assicurarsi di includere la relativa chiamata in AppDelegate nel metodo `didFinishLaunchingWithOptions:`.

```
[self setupOAuth2AccountStore];
```


## Creare una classe `LoginViewController` per gestire le richieste di autenticazione

Viene usata una visualizzazione Web per l'accesso all'account. Ciò consente di chiedere all'utente altri fattori, ad esempio un SMS (se configurato), o di restituire messaggi di errore all'utente. Ora verrà configurata la visualizzazione Web e in seguito verrà scritto il codice per gestire i callback che si verificheranno in WebView dal servizio di gestione delle identità Microsoft.

* Creare una classe `LoginViewController.h`

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Ognuno di questi metodi verrà creato di seguito.

> [AZURE.NOTE] 
    Assicurarsi di associare `loginView` all'effettiva visualizzazione Web nello dello storyboard. In caso contrario, non sarà disponibile una visualizzazione Web al momento dell'autenticazione.

* Creare una classe `LoginViewController.m`

* Aggiungere alcune variabili per includere lo stato durante l'autenticazione

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Eseguire l'override dei metodi WebView per gestire l'autenticazione

È necessario indicare alla visualizzazione Web il comportamento da adottare quando un utente deve eseguire l'accesso come illustrato sopra. È semplicemente possibile tagliare e incollare il codice seguente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Scrivere il codice per gestire il risultato della richiesta OAuth2.

Sarà necessario il codice che gestirà l'URL di reindirizzamento restituito da WebView. Se non si riesce, si riproverà. Nel frattempo la libreria visualizzerà l'errore che è possibile visualizzare nella console o gestire in modo asincrono.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configurare le factory di notifica

Si crea lo stesso metodo usato sopra in `AppDelegate`, aggiungendo però in questo caso alcune istanze di `NSNotification` per ottenere informazioni su ciò che accade nel servizio. Verrà configurato un osservatore che segnalerà eventuali modifiche al token. Dopo aver ottenuto il token, l'utente torna a `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Aggiungere il codice con cui viene gestito l'utente ogni volta che viene avviata una richiesta di accesso nativo

Creare un metodo che verrà chiamato a ogni richiesta di autenticazione. Con questo metodo verrà effettivamente creata una visualizzazione Web.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Chiamare infine tutti i metodi scritti sopra ogni volta che viene caricata la classe `LoginViewController`. A tale scopo, aggiungere questi metodi al metodo `viewDidLoad` fornito da Apple

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

La creazione della principale modalità di interazione con l'applicazione ai fini dell'accesso è ora completata. Dopo l'accesso, sarà necessario usare i token ricevuti. A tale scopo verrà creato codice helper che chiamerà le API REST usando questa libreria.


## Creare una classe `GraphAPICaller` per gestire le richieste a un'API REST

Ogni volta che viene caricata l'app, viene caricata una configurazione. A questo punto è necessario eseguire alcune operazioni su di essa dopo avere ottenuto un token.

* Creare un file `GraphAPICaller.h`

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Come è possibile osservare da questo codice, verranno creati due metodi: uno per ottenere le attività da un'API e un altro per aggiungere attività all'API.

Dopo aver configurato l'interfaccia, aggiungere l'implementazione effettiva:

* Creare un `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## Eseguire l'app di esempio

Infine, compilare ed eseguire l'app in Xcode. Effettuare l'iscrizione o l'accesso all'app e creare le attività per l'utente connesso. Disconnettersi ed eseguire di nuovo l'accesso con un account utente diverso e creare le attività per tale utente.

Si noti che le attività sono archiviate per utente nell'API, perché l'API estrae l'identità dell'utente dal token di accesso che riceve.


## Passaggi successivi

È ora possibile passare ad argomenti più avanzati B2C. È possibile provare a:

[Chiamare un'API Web per Node.js da un'app Web Node.js]()

[Personalizzare l'esperienza utente per un'app B2C]()

<!---HONumber=AcomDC_1005_2016-->