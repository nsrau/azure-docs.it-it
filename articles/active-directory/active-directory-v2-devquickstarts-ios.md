<properties
	pageTitle="App iOS v2.0 di Azure AD | Microsoft Azure"
	description="Come compilare un'app per iOS che consente agli utenti di accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione usando librerie di terze parti."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="brandwe"/>

# Aggiungere informazioni di accesso a un'app iOS usando una libreria di terze parti con l'API Graph mediante l'endpoint v2.0

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Gli sviluppatori possono usare qualsiasi libreria che desiderano integrare ai servizi. Per aiutare gli sviluppatori a usare la piattaforma con altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano [la specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) possono connettersi alla piattaforma delle identità Microsoft.

Con l'applicazione creata in questa procedura dettagliata, gli utenti possono accedere alla propria organizzazione e cercare altro all'interno dell'organizzazione tramite l'API Graph.

Se non si ha familiarità con OAuth2 o OpenID Connect, gran parte di questo esempio risulterà poco chiara. Per approfondimenti, è consigliabile leggere [Protocolli della versione 2.0: flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
    Per alcune funzionalità della piattaforma che trovano espressione negli standard OAuth2 o OpenID Connect, ad esempio l'accesso condizionale e la gestione criteri di Intune, è necessario usare le librerie di identità open source di Microsoft Azure.

Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0.

> [AZURE.NOTE]
    Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).

## Scaricare il codice da GitHub
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonare la struttura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

È anche possibile scaricare l'esempio e iniziare subito:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## Registrare un'app
Creare una nuova app nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com) o seguire la procedura riportata in [Come registrare un'app con l'endpoint v2.0](active-directory-v2-app-registration.md). Verificare di:

- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.
- Aggiungere la piattaforma **Mobile** per l'app.
- Copiare l'**URI di reindirizzamento** dal portale. È necessario usare il valore predefinito `urn:ietf:wg:oauth:2.0:oob`.


## Scaricare la libreria di terze parti NXOAuth2 e creare un'area di lavoro

Per questa procedura dettagliata verrà usato OAuth2Client di GitHub, una libreria OAuth2 per Mac OS X e iOS (Cocoa e Cocoa Touch). Questa libreria si basa sulla bozza 10 della specifica OAuth2. Questa libreria implementa il profilo dell'applicazione nativa e supporta l'endpoint di autorizzazione dell'utente. Ecco tutto ciò che serve per l'integrazione con la piattaforma delle identità di Microsoft.

### Aggiungere la libreria al progetto con CocoaPods

CocoaPods è un gestore delle dipendenze per i progetti Xcode. Gestisce automaticamente i passaggi di installazione precedenti.

```
$ vi Podfile
```
1. Aggiungere il codice seguente al podfile:

	```
	 platform :ios, '8.0'

	 target 'QuickStart' do

	 pod 'NXOAuth2Client'

	 end
	```

2. Caricare il podfile usando CocoaPods. Verrà creata una nuova area di lavoro Xcode da caricare.

	```
	$ pod install
	...
	$ open QuickStart.xcworkspace
	```

## Esplorare la struttura del progetto

Nello scheletro è configurata la struttura seguente per il progetto:

- Una visualizzazione master con una ricerca UPN
- Una visualizzazione dettagli per i dati sull'utente selezionato
- Una visualizzazione di accesso che consente a un utente di accedere all'app per eseguire query al grafico

Si passerà a diversi file nella struttura per aggiungere l'autenticazione. Altre parti del codice, ad esempio il codice visivo, non sono pertinenti all'identità e vengono fornite automaticamente.

## Configurare il file settings.plst nella libreria

-	Nel progetto QuickStart, aprire il file `settings.plist`. Sostituire i valori degli elementi nella sezione in modo che corrispondano ai valori usati nel portale di Azure. Il codice farà riferimento a questi valori ogni volta che viene usata la libreria di autenticazione di Active Directory.
    -	`clientId` è l'ID client dell'applicazione copiato dal portale.
    -	`redirectUri` è l'URL di reindirizzamento fornito dal portale.

## Configurare la libreria NXOAuth2Client in LoginViewController

Per configurare la libreria NXOAuth2Client, sono necessari alcuni valori. Dopo avere completato questa attività, è possibile usare il token acquisito per le chiamate all'API Graph. Poiché `LoginView` verrà chiamato ogni volta che è necessario eseguire l'autenticazione, è opportuno inserire i valori di configurazione nel file.

- Aggiungere alcuni valori al file `LoginViewController.m` per impostare il contesto per l'autenticazione e l'autorizzazione. I dettagli sui valori seguono il codice.

	```objc
	NSString *scopes = @"openid offline_access User.Read";
	NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
	NSString *loginURL = @"https://login.microsoftonline.com/common/login";
	NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
	NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
	NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
	static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
	NSURL *myRequestedUrl;
	NSURL *myLoadedUrl;
	bool loginFlow = FALSE;
	bool isRequestBusy;
	NSURL *authcode;
	```

Esaminiamo i dettagli del codice.

La prima stringa è per `scopes`. Il valore `User.Read` consente di leggere il profilo di base dell'utente connesso.

Per altre informazioni su tutti gli ambiti disponibili, vedere [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes) (Ambiti di autorizzazione di Microsoft Graph).

Per `authURL`, `loginURL`, `bhh`, `tokenURL` è consigliabile usare i valori forniti in precedenza. Se si usano le librerie di identità di Microsoft Azure open source, verrà eseguito il pull automatico di questi dati usando gli endpoint dei metadati. L'estrazione di questi valori è stata eseguita automaticamente.

Il valore `keychain` è il contenitore che la libreria NXOAuth2Client userà per creare un portachiavi in cui archiviare i token. Per ottenere l'accesso Single Sign-On SSO in più app, è possibile specificare lo stesso portachiavi in ogni applicazione, oltre a richiedere l'uso di tale portachiavi nei diritti XCode. Questa procedura è illustrata nella documentazione di Apple.

Gli altri valori sono necessari per usare la libreria e creare automaticamente le posizioni per inserire i valori nel contesto.

### Creare una cache di URL

All'interno di `(void)viewDidLoad()`, che viene sempre chiamato dopo il caricamento della visualizzazione, il codice seguente prepara una cache per l'uso.

Aggiungere il codice seguente:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### Creare una visualizzazione Web per l'accesso

Una visualizzazione Web consente di chiedere all'utente altri fattori, ad esempio un SMS (se configurato), o di restituire messaggi di errore all'utente. Ora verrà configurata la visualizzazione Web e in seguito verrà scritto il codice per gestire i callback che si verificheranno nella visualizzazione Web dal servizio di gestione delle identità.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### Eseguire l'override dei metodi WebView per gestire l'autenticazione

Per indicare alla visualizzazione Web cosa accade quando un utente deve eseguire l'accesso come illustrato in precedenza, è possibile incollare il codice seguente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### Scrivere il codice per gestire il risultato della richiesta OAuth2.

Il codice seguente gestirà l'URL di reindirizzamento restituito dalla visualizzazione Web. Se l'autenticazione ha esito negativo, il codice ripete l'operazione. Nel frattempo la libreria visualizzerà l'errore che è possibile visualizzare nella console o gestire in modo asincrono.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### Configurare il contesto OAuth (archivio di account chiamato)

Qui è possibile chiamare `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` nell'archivio account condiviso per ogni servizio a cui si desidera che l'applicazione abbia accesso. Il tipo di account è una stringa che viene usata come identificatore per un determinato servizio. Poiché si accede all'API Graph, il codice fa riferimento a esso come `"myGraphService"`. Verrà quindi configurato un osservatore che segnalerà eventuali modifiche al token. Dopo aver ottenuto il token, l'utente torna a `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## Configurare la visualizzazione master per cercare e visualizzare gli utenti dall'API Graph

Un'app Master-View-Controller (MVC) che visualizza i dati restituiti nella griglia non rientra nell'ambito di questa procedura dettagliata, ma online sono disponibili diverse esercitazioni che illustrano come compilarne una. Tutto il codice è contenuto nel file dello scheletro. È tuttavia necessario gestire alcuni elementi dell'applicazione MVC:

* Rilevare quando un utente digita qualcosa nel campo di ricerca
* Fornire un oggetto di dati a MasterView in modo che possa visualizzare i risultati nella griglia

Queste operazioni verranno eseguite sotto.

### Aggiungere un controllo per verificare se si è connessi

Le funzionalità dell'applicazione sono limitate se l'utente non è connesso, quindi è opportuno controllare se è già presente un token nella cache. Se il token non è presente, l'utente viene reindirizzato a LoginView per l'accesso. Se si richiama, il modo migliore per eseguire azioni quando viene caricata una visualizzazione consiste nell'usare il metodo `viewDidLoad()` fornito da Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### Aggiornare la visualizzazione tabella quando vengono ricevuti i dati

Quando l'API Graph restituisce i dati, è necessario visualizzarli. Per semplicità viene fornito tutto il codice necessario per aggiornare la tabella. È sufficiente incollare i valori corretti nel codice boilerplate MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### Consente di chiamare l'API Graph quando qualcuno digita nel campo di ricerca

Quando un utente digita un testo di ricerca nella casella, è necessario inserirlo nell'API Graph. La classe `GraphAPICaller`, che verrà compilata nel codice seguente, separa le funzionalità di ricerca dalla presentazione. Per ora è opportuno scrivere il codice che alimenta i caratteri di ricerca dell'API Graph. A questo scopo, viene fornito un metodo denominato `lookupInGraph` che considera la stringa che si vuole cercare.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## Scrivere una classe helper per accedere all'API Graph

Questo è l'elemento principale dell'applicazione. Mentre prima è stato inserito il codice nel modello MVC predefinito da Apple, qui viene scritto il codice per eseguire una query del grafico mentre l'utente digita e per restituire tali dati. Di seguito viene riportato il codice con una spiegazione dettagliata.

### Creare un nuovo file di intestazione Objective C

Assegnare al file il nome `GraphAPICaller.h` e aggiungere il codice seguente.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Qui è possibile osservare che si sta specificando un metodo che accetta una stringa e restituisce completionBlock. Questo completionBlock, come è intuibile, aggiornerà la tabella fornendo un oggetto con dati popolati in tempo reale mentre l'utente esegue la ricerca.


### Creare un nuovo file Objective C

Assegnare al file il nome `GraphAPICaller.m` e aggiungere il metodo seguente.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Ora questo metodo verrà analizzato in dettaglio.

L'elemento principale di questo codice è il metodo `NXOAuth2Request` che accetta i parametri definiti prima nel file settings.plist.

Il primo passaggio consiste nel creare la chiamata API Graph corretta. Poiché si sta chiamando `/users`, lo si specifica aggiungendolo alla risorsa API Graph insieme alla versione. Questi elementi vengono inseriti in un file delle impostazioni esterno perché possono essere modificati man mano che l'API evolve.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

È quindi necessario specificare altri parametri che verranno forniti alla chiamata API Graph. È *molto importante* non inserire i parametri nell'endpoint della risorsa perché i caratteri non conformi all'URI vengono eliminati in fase di esecuzione. Tutto il codice della query deve essere fornito nei parametri.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Come si può notare, viene chiamato un metodo `convertParamsToDictionary` che non è ancora stato scritto. Verrà fatto ora alla fine del file:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Ora il metodo `NXOAuth2Request` verrà usato per ottenere di nuovo i dati dall'API in formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Infine si esaminerà come vengono restituiti i dati a MasterViewController. I dati vengono restituiti come serializzati e devono essere deserializzati e caricati in un oggetto che MainViewController possa usare. A questo scopo, la struttura include un file `User.m/h` che crea un oggetto User. L'oggetto User viene popolato con le informazioni del grafico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## Eseguire l'esempio

Se è stata usata la struttura o si è seguita la procedura dettagliata, l'applicazione verrà ora eseguita. Avviare il simulatore e fare clic su **Accedi** per usare l'applicazione.

## Ottenere aggiornamenti della sicurezza per il prodotto

È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [Security TechCenter](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.

<!---HONumber=AcomDC_0720_2016-->