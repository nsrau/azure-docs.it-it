<properties pageTitle="Anteprima di Azure Active Directory B2C: Chiamare un'API Web da un'app iOS | Microsoft Azure" description="Questo articolo illustra come creare un'app iOS "To-Do List" che chiama un'API Web Node.js web tramite token di connessione OAuth 2.0. Sia l'app iOS che l'API Web usano Azure Active Directory B2C per gestire le identità utente e autenticare gli utenti." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Anteprima di Azure AD B2C: Chiamare un'API Web da un'app iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di aggiungere funzionalità efficaci di gestione delle identità self-service alle app iOS e alle API Web con pochi brevi passaggi. Questo articolo illustra come creare un'app iOS "To-Do List" che chiama un'API Web Node.js tramite token di connessione OAuth 2.0. Sia l'app iOS che l'API Web usano Azure AD B2C per gestire le identità utente e autenticare gli utenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Per poter usare questa guida introduttiva è necessario avere già un'API Web protetta da Azure AD B2C. È stata compilata un'API Web che può essere usata sia per .NET che per Node.js. Questa procedura dettagliata presuppone che l'esempio di API Web per Node.js sia configurato. Per altre informazioni, vedere l'[esempio di API Web di Azure Active Directory per Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Questo articolo non descrive come implementare la gestione dell'iscrizione, dell'accesso e del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app che l'API Web sono rappresentate da un singolo **ID applicazione** in questo caso, perché includono un'app per la logica. Per creare un'app, seguire [questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di:

- Includere un'**app Web/API Web** nell'applicazione.
- Immettere `http://localhost:3000/auth/openid/return` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e prenderne nota. Sarà necessario più avanti.
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere il **Nome visualizzato** e gli attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Scaricare il codice

Il codice per questa esercitazione [è disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Per creare l'esempio passo dopo passo, è possibile [scaricare il progetto struttura come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **È necessario scaricare la struttura per completare questa esercitazione.** Data la complessità dell'implementazione di un'applicazione pienamente funzionante in iOS, la **struttura** contiene codice dell'esperienza utente che verrà eseguito al termine dell'esercitazione. Si tratta di una misura che permette agli sviluppatori di risparmiare tempo. Il codice dell'esperienza utente non è pertinente all'argomento relativo all'aggiunta di B2C a un'app iOS.

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) o nel ramo `complete` dello stesso repository.

A questo punto, caricare `podfile` tramite CocoaPods. Verrà creata una nuova area di lavoro Xcode da caricare. Se CocoaPods non è disponibile, [visitare il sito Web per configurarlo](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Configurare l'applicazione attività iOS

Perché l'app attività iOS possa comunicare con Azure AD B2C, è necessario fornire alcuni parametri comuni. Nella cartella `Microsoft Tasks` aprire il file `settings.plist` nella radice del progetto e sostituire i valori nella sezione `<dict>`. Questi valori verranno usati nell'app.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Ottenere i token di accesso e chiamare le API dell'attività

Questa sezione illustra come completare uno scambio di token OAuth 2.0 in un'app Web usando le librerie e i framework di Microsoft. Se non si ha familiarità con i codici di autorizzazione e i token di accesso, vedere il [riferimento al protocollo OAuth 2.0](active-directory-b2c-reference-protocols.md) per altre informazioni.

### Creare file di intestazione usando i metodi

Per ottenere un token con i criteri selezionati e quindi chiamare il server delle attività è necessario usare alcuni metodi. Configurare ora i metodi necessari.

Creare un file denominato `samplesWebAPIConnector.h` in `/Microsoft Tasks` nel progetto Xcode.

Aggiungere il codice seguente per definire la procedura necessaria:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Si tratta di semplici operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) eseguite nell'API e di un metodo `doPolicy`. Usando questo metodo è possibile ottenere un token con i criteri selezionati.

Si noti che è necessario definire altri due file di intestazione, che conterranno i dati relativi ai criteri e all'elemento attività. Creare i file di intestazione:

Creare il file `samplesTaskItem.h` con il codice seguente:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Creare anche il file `samplesPolicyData.h` per contenere i dati dei criteri:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Aggiungere un'implementazione per gli elementi attività e criteri

Ora che i file di intestazione sono disponibili, è possibile scrivere codice per archiviare i dati da usare per l'esempio.

Creare il file `samplesPolicyData.m` con il codice seguente:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Scrivere il codice di configurazione per la chiamata ad ADAL per iOS

Il codice rapido per archiviare gli oggetti per l'interfaccia utente ora è completo. A questo punto, è necessario scrivere codice per l'accesso ad Active Directory Authentication Library (ADAL) per iOS con i parametri inclusi in `settings.plist`. In questo modo è possibile ottenere un token di accesso da fornire al server delle attività.

Tutta la procedura verrà eseguita in `samplesWebAPIConnector.m`.

Creare prima di tutto l'implementazione di `doPolicy()` scritta nel file di intestazione `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

Il metodo è semplice. Accetta come input l'oggetto `samplesPolicyData` creato, l'elemento padre `ViewController` e un callback. Il callback verrà esaminato in modo più approfondito.

- Si noti che `completionBlock` prevede `ADProfileInfo` come un tipo restituito tramite un oggetto `userInfo`. `ADProfileInfo` è il tipo che contiene tutte le risposte dal server, incluse le attestazioni.
- Si noti anche che `readApplicationSettings` legge i dati forniti in `settings.plist`.
- Il risultato finale è un metodo `getClaimsWithPolicyClearingCache` di grandi dimensioni. Questa è la chiamata ad ADAL per iOS vera e propria che è necessario scrivere e verrà approfondita più avanti in questo articolo.

Scrivere il metodo di grandi dimensioni `getClaimsWithPolicyClearingCache`. Date le dimensioni, il metodo richiede una sezione distinta.

### Creare la chiamata ad ADAL per iOS

Dopo aver scaricato la struttura da GitHub, sono disponibili diverse chiamate per l'applicazione di esempio. I metodi seguono tutti il modello `get(Claims|Token)With<verb>ClearningCache`. L'uso delle convenzioni Objective C le rende molto simili alla lingua inglese. Ad esempio, "Ottieni un token con i parametri aggiuntivi da me forniti e cancella la cache" sarà `getTokenWithExtraParamsClearingCache()`.

Si scriverà "Ottieni attestazioni e un token con i criteri forniti e non cancellare la cache", ovvero `getClaimsWithPolicyClearingCache`. ADAL restituisce sempre un token, non è quindi necessario specificare "attestazioni e un token" nel metodo. Dal momento che a volte è necessario soltanto il token senza il sovraccarico dell'analisi delle attestazioni, nella struttura viene fornito un metodo senza attestazioni denominato `getTokenWithPolicyClearingCache`.

Scrivere il codice seguente:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

La prima parte del codice dovrebbe essere familiare.

- Caricare le impostazioni fornite in `settings.plist` e assegnarle a `data`.
- Impostare `ADAuthenticationError`, che accetta qualsiasi errore da ADAL per iOS.
- Creare `authContext`, che consente di impostare la chiamata ad ADAL, e passargli l'autorità necessaria per l'avvio del processo.
- Fornire a `authContext` un riferimento al controller padre in modo che sia possibile ripristinarlo.
- Convertire `redirectURI`, una stringa di `settings.plist`, nel tipo NSURL previsto da ADAL.
- Impostare `correlationId`. Si tratta di un UUID che può seguire la chiamata dal client al server e viceversa. Questo è utile per il debug.

Passare ora alla chiamata effettiva ad ADAL, che si differenzia rispetto agli usi precedenti di ADAL per iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

La chiamata è piuttosto semplice.

`scopes`: gli ambiti passati al server da richiedere al server per un utente che esegue l'accesso. Per l'anteprima di B2C si passa `client_id`, che in futuro verrà usato per la lettura degli ambiti. Dopo tale modifica questo documento verrà aggiornato. `additionalScopes`: gli ambiti aggiuntivi che è possibile usare per l'app. Se ne prevede l'uso in futuro. `clientId`: l'ID applicazione ottenuto dal portale. `redirectURI`: l'URI di reindirizzamento in cui è previsto l'invio del token. `identifier`: un modo per identificare un utente allo scopo di verificare se esiste un token utilizzabile nella cache. Questo permette di evitare la continua richiesta di nuovi token al server. È contenuto in un tipo denominato `ADUserIdentifier` ed è possibile specificare cosa usare come ID. Si consiglia di usare `username`. `promptBehavior`: un oggetto deprecato. Dovrebbe essere `AD_PROMPT_ALWAYS`. `extraQueryParameters`: qualsiasi elemento aggiuntivo che si vuole passare al server, in formato con codifica URL. `policy`: i criteri che vengono richiamati. Questa è la parte più importante della procedura dettagliata.

In `completionBlock` è possibile vedere che viene passato `ADAuthenticationResult`. Qui sono inclusi il token e le informazioni sul profilo, se la chiamata ha esito positivo.

Usando il codice appena illustrato è possibile ottenere un token per i criteri forniti. Il token può essere usato per chiamare l'API.

### Creare le chiamate di attività per il server

Il token ottenuto deve essere fornito all'API per l'autorizzazione.

È necessario implementare tre metodi:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` fornisce una matrice che rappresenta le attività nel server. `addTask` e `deleteTask` eseguono le azioni successive e restituiscono `true` o `false` in caso di esito positivo.

Scrivere prima `getTaskList`:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

La descrizione del codice dell'attività non rientra nell'ambito di questa procedura dettagliata, ma è interessante notare il metodo `craftRequest`, che accetta l'URL dell'attività. Questo metodo viene usato per creare la richiesta per il server, usando il token di accesso ricevuto. Scrivere il metodo.

Aggiungere il codice seguente al file `samplesWebAPIConnector.m`:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Il codice accetta un URI Web, aggiunge il token usando l'intestazione `Bearer` in HTTP e lo restituisce quindi all'utente. Chiamare l'API `getTokenClearingCache`. È sufficiente usare questa chiamata per ottenere un token dalla cache e per assicurarsi che sia ancora valido. Le chiamate `getToken` eseguono questa operazione richiedendo ADAL. Usare questo codice in ogni chiamata. A questo punto, è necessario creare i metodi delle attività aggiuntive.

Scrivere `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Questo metodo segue lo stesso modello, ma introduce anche l'ultimo metodo da implementare, `convertTaskToDictionary`. Questo metodo trasforma la matrice in un oggetto dizionario, che è più facile da modificare nei parametri di query da passare al server. Il codice è semplice:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

A questo punto, scrivere `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Aggiungere la disconnessione all'applicazione

L'ultima cosa da fare è implementare la disconnessione nell'applicazione. L'operazione è semplice. All'interno del file `sampleWebApiConnector.m`:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Eseguire l'app di esempio

Infine, compilare ed eseguire l'app in Xcode. Effettuare l'iscrizione o l'accesso all'app e creare le attività per l'utente connesso. Disconnettersi ed eseguire di nuovo l'accesso con un account utente diverso e creare le attività per tale utente.

Si noti che le attività sono archiviate per utente nell'API, perché l'API estrae l'identità dell'utente dal token di accesso che riceve.

Per riferimento, l'esempio completo [viene fornito come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Passaggi successivi

È ora possibile passare ad argomenti più avanzati B2C. È possibile provare a:

[Chiamare un'API Web per Node.js da un'app Web Node.js]()

[Personalizzare l'esperienza utente per un'app B2C]()

<!---HONumber=AcomDC_0302_2016-->