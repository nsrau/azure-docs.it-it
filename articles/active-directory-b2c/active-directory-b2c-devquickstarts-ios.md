<properties pageTitle="Anteprima Azure AD B2C: chiamata di un'API Web da un'applicazione iOS | Microsoft Azure" descrizione="Questo articolo illustra come creare un'app iOS "To-Do List" che chiama un'API Web node.js web tramite token di connessione OAuth 2.0. Sia l’app iOS che l’API Web utilizzano Azure AD B2C per gestire le identità degli utenti e autenticare gli utenti." servizi ="active-directory-b2c" documentationCenter=”ios” autori="brandwe" manager="msmbaldwin" editor =""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Anteprima di Azure AD B2C: Chiamata di un'API Web da un'applicazione iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C consente di aggiungere potenti funzionalità di gestione delle identità self-service alle app iOS e alle API Web con pochi brevi passaggi. Questo articolo descrive come creare un'app iOS "To-Do List" che chiama un’API Web node.js web tramite token di connessione OAuth 2.0. Sia l'app iOS che l'API Web usano Azure AD B2C per gestire le identità utente e autenticare gli utenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]Questa Guida rapida richiede come prerequisito che un'API Web sia protetta da Azure AD con B2C per poter funzionare completamente. È stata creata una da utilizzare sia per .Net che per node.js. Questa procedura dettagliata presuppone che l’API Web Node.js di esempio sia configurata. Consultare l’[esempio API Web Azure Active Directory per Node.js](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

## 1\. Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app che l'API Web saranno rappresentate da un singolo **ID applicazione** in questo caso, poiché includono un'app per la logica. Per creare un'app, seguire [questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di

- Includere un'**app Web/API Web** nell'applicazione
- Specificare `http://localhost:3000/auth/openid/return` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **Segreto applicazione** per l'applicazione e prenderne nota, perché verrà richiesto a breve.
- Copiare l'**ID applicazione** assegnato all'app, perché anche questo verrà richiesto a breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. Sarà necessario creare i criteri per ciascun tipo, come descritto nell'articolo [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere il **Nome visualizzato** e l'**ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ciascun criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. Sarà necessario usare i nomi dei criteri a breve. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per altre informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Scaricare il codice

Il codice per questa esercitazione è disponibile [in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Per creare l'esempio passo dopo passo, è possibile [scaricare un progetto scheletro come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) o clonare lo scheletro:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE]**Il download dello scheletro è necessario per completare questa esercitazione.** Data la complessità dell'implementazione di un'applicazione pienamente funzionante su iOS, lo **scheletro** prevede un codice UX che sarà eseguito dopo aver completato l'esercitazione seguente. Si tratta di una misura che consente di risparmiare tempo per gli sviluppatori. Il codice UX non è pertinente all'argomento di aggiunta B2C a un'applicazione iOS.

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) o nel ramo `complete` dello stesso repository.


Caricare ora il podfile usando cocoapods. Verrà creata una nuova area di lavoro XCode che verrà caricata. Se non si dispone di Cocoapods, visitare [il sito Web per installare cocoapods](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Configurare l'applicazione attività iOS

Affinché l'applicazione attività iOS comunichi con Azure AD B2C, è necessario fornire alcuni parametri comuni. Nella cartella `Microsoft Tasks` aprire il file `settings.plist` nella radice del progetto e sostituire i valori nella sezione `<dict>`. Questi valori verranno usati nell'app.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
		<string></string>
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

## 6\. Ottenere i token di accesso e chiamare le API dell'attività

Questa sezione mostra come completare uno scambio di token OAuth 2.0 in un'app Web usando le librerie e i framework di Microsoft. Se non si ha familiarità con i **codici di autorizzazione** e i **token di accesso**, provare a consultare le [informazioni di riferimento relative al protocollo OAuth 2.0](active-directory-b2c-reference-protocols.md).

#### Creare file di intestazione con i metodi da noi utilizzati.

Saranno necessari alcuni metodi per ottenere un token con il criterio selezionato e in seguito chiamare il server dell’Attività. Tali metodi verranno esaminati di seguito.

Creare un file denominato `samplesWebAPIConnector.h` in /Microsoft Tasks nel progetto XCode.

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

Si tratta di semplici operazioni CRUD sull'API e di un metodo `doPolicy` che consente di ottenere un token con i criteri desiderati.

Si noterà inoltre che esistono altri due file di intestazione ed è necessario definire quale conterrà l'Elemento attività e i Dati dei criteri. Per creare i file d’intestazione:

Creare un file denominato `samplesTaskItem.h` con il codice seguente:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Creare anche un file `samplesPolicyData.h` per contenere i dati dei criteri:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Aggiunta di un'implementazione per gli elementi Attività e Criterio

Ora che si hanno i file di intestazione, è necessario scrivere il codice per archiviare i dati che verranno utilizzati per l'esempio.

Creare un file denominato `samplesPolicyData.m` con il codice seguente:

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

#### Scrivere il codice di installazione per la chiamata in ADAL per iOS

Il codice rapido per archiviare gli oggetti per l'interfaccia utente è completo. È ora necessario scrivere il codice per accedere ad ADAL per iOS con i parametri introdotti nel file `settings.plist` per ottenere un token di accesso da fornire al server di attività. Il codice può essere complicato, pertanto prestare attenzione.

Tutto il lavoro verrà eseguito in `samplesWebAPIConnector.m`.

In primo luogo, creare l'implementazione `doPolicy()` scritta nel file di intestazione `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    NSDictionary* params = [self convertPolicyToDictionary:policy];
    
    [self getClaimsWithPolicyClearingCache:NO policy:policy params:params parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {
        
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

Si noterà che il metodo è piuttosto semplice. Esso accetta come input l'oggetto `samplesPolicyData` appena creato, l'elemento ViewController parent e quindi un callback. Il callback è interessante e sarà trattato in maniera dettagliata.

1. Si noterà che `completionBlock` ha ADProfileInfo come tipo che verrà restituito con un oggetto `userInfo`. ADProfileInfo è il tipo che contiene tutte le risposte dal server, in particolare le attestazioni. 

2. Si noterà `readApplicationSettings`, che legge i dati forniti in `settings.plist`.
3. Si noterà il metodo `convertPolicyToDictionary:policy`, che accetta il criterio e lo formatta come URL da inviare al server. Questo metodo di supporto verrà trattato successivamente.
4. È presente infine un metodo `getClaimsWithPolicyClearingCache` piuttosto esteso. Questo riguarda la chiamata effettiva a ADAL per iOS che è necessario scrivere, ma la si scriverà in un secondo momento.


Successivamente, verrà scritto il metodo `convertPolicyToDictionary` sottostante nel codice appena scritto:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

+(NSDictionary*) convertPolicyToDictionary:(samplesPolicyData*)policy
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    
    if (policy.policyID){
        [dictionary setValue:policy.policyID forKey:@"p"];
    }
    
    return dictionary;
}

```
Questo codice piuttosto semplice aggiunge semplicemente una p al criterio in modo che l'aspetto della query sia ?p=<policy>.

Di seguito verrà scritto il metodo `getClaimsWithPolicyClearingCache` esteso. Tale metodo è sufficientemente grande da meritare una sezione apposita.

#### Creare la chiamata in ADAL per iOS

Se è stato scaricato lo scheletro da GitHub, si noterà che ne esistono diversi in grado di agevolare l'applicazione di esempio. Gli scheletri seguono tutti il modello `get(Claims|Token)With<verb>ClearningCache`. Con l’utilizzo delle convenzioni Objetive C, esso appare molto simile all’inglese. Ad esempio "ottenere un Token con i parametri aggiuntivi forniti e cancellare la cache", Che è `getTokenWithExtraParamsClearingCache()`. Piuttosto semplice.

Si scriverà "ottenere Attestazioni e un token con i criteri forniti e non cancellare la cache" o `getClaimsWithPolicyClearingCache`. ADAL restituisce sempre un token, pertanto non è necessario specificare "Attestazioni e token" nel metodo. Poiché a volte è necessario soltanto il token senza il sovraccarico dell'analisi delle attestazioni, viene fornito un metodo senza attestazioni denominato `getTokenWithPolicyClearingCache` nello scheletro.

Tale codice è riportato di seguito:

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

La prima parte del codice dovrebbe essere familiare. Caricare le impostazioni fornite in `Settings.plist` e assegnare il tutto a `data`. Viene quindi impostato una classe `ADAuthenticationError` che accetti qualsiasi errore proveniente da ADAL per iOS. È anche possibile creare un oggetto `authContext` che è l'impostazione della chiamata ad ADAL, che viene passata ad *authority* per avviare i processi. Viene inoltre fornito ad `authContext` un riferimento al controller padre in modo che sia possibile ritornare a esso. È anche possibile convertire `redirectURI`, una stringa di `settings.plist`, nel tipo NSURL previsto da ADAL. È necessario infine impostare un oggetto `correlationId`, che è semplicemente un UUID in grado di seguire la chiamata dal client al server e viceversa. Questo è utile per il debug.

Ora, passare alla chiamata effettiva a ADAL, dove la chiamata è diversa rispetto agli utilizzi precedenti di ADAL per iOS:

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

Si noterà che la chiamata è piuttosto semplice.

**scopes**: ambiti che vengono passati al server e che si desidera richiedere al server per l'accesso dell'utente. Per l'anteprima B2C viene passato il parametro client\_id. Verrà tuttavia modificato per leggere gli ambiti in futuro. Questo documento verrà quindi aggiornato. **addtionalScopes**: ambiti aggiuntivi che è possibile usare per l'applicazione. Verranno usati in futuro. **clientId**: ID dell'applicazione ottenuto dal portale **redirectURI**: reindirizzamento in cui si prevede che il token venga rispedito. **identifier**: modalità di identificazione dell'utente, in modo da controllare se è disponibile un token utilizzabile nella cache invece di dover sempre richiedere al server un altro token. Si noterà che questa operazione viene eseguita in un tipo denominato `ADUserIdentifier` e che è possibile specificare l'ID che si desidera usare. È consigliabile usare il nome utente. **promptBehavior**: deprecato, deve essere AD\_PROMPT\_ALWAYS. **extraQueryParameters**: qualsiasi parametro aggiuntivo che si desidera passare al server nel formato codificato dell'URL. **policy**: criterio che viene richiamato. La parte più importante di questa procedura dettagliata.

Si noti che in completionBlock viene passato l'elemento `ADAuthenticationResult`, che dispone del token e delle informazioni sul profilo (se la chiamata ha avuto esito positivo)

Utilizzando il codice sopra, è possibile acquisire un token per il criterio fornito. Utilizzare questo token per chiamare l'API.

#### Creare Chiamate Attività al server

Ora che si dispone di un token, è necessario fornirlo all'API per l’autorizzazione.

Come detto in precedenza, è necessario implementare tre metodi:

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

`getTasksList` fornisce una matrice che rappresenta le attività nel server. `addTask` e `deleteTask` eseguono l'azione successiva e restituiscono TRUE o FALSE in caso di esito positivo.

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

Non rientra nell'ambito di questa procedura dettagliata illustrare il codice attività, ma è interessante notare un metodo `craftRequest` accetta l'URL dell'attività. Questo metodo viene utilizzato per creare la richiesta, con il token di accesso pervenuto, per il server. Il metodo viene riportato di seguito:

Aggiungere il codice seguente al file 'samplesWebAPIConnector.m':

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

Come è possibile osservare, esso accetta un URI Web, vi aggiunge il token con l'intestazione `Bearer` in HTTP e lo restituisce quindi all'utente. Chiamare l'API `getTokenClearingCache`. Questa operazione può sembrare strana inizialmente, ma la chiamata viene usata semplicemente per ottenere un token dalla cache e assicurarsi che sia ancora valido (le chiamate getToken * eseguono questa operazione interrogando ADAL). Utilizzare il codice in ogni chiamata. Si torni ora alla creazione dei metodi Attività aggiuntivi.

Verrà scritto `addTask`:

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

Questo codice segue lo stesso modello, ma introduce un ultimo metodo da implementare, ovvero `convertTaskToDictionary`. Questo metodo accetta la matrice e la rende un oggetto dizionario che viene modificato più facilmente nei parametri di query che devono essere passati al server. Questo codice è molto semplice:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

```

Verrà scritto infine `deleteTask`:

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

### Aggiungere la disconnessione all'applicazione.

L'ultima cosa da fare è implementare la Disconnessione per l’applicazione. Questa operazione è piuttosto semplice. Di nuovo nel file `sampleWebApiConnector.m`:

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

## 9\. Eseguire l'app di esempio

Infine, compilare ed eseguire l’app in xCode. Effettuare l'iscrizione o l'accesso all'app e creare le attività per l'utente connesso. Disconnettersi ed eseguire di nuovo l'accesso con un account utente diverso e creare le attività per l’utente in questione.

Osservare che le attività sono archiviate per utente nell'API, perché l'API estrae l'identità dell'utente dai token di accesso che riceve.

Come riferimento, l'esempio completato [è disponibile qui in un file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Passaggi successivi

È ora possibile passare ad argomenti più avanzati B2C. È possibile consultare:

[Chiamata di un'API Web Node.js da un'applicazione Web Node.js >>]()

[Personalizzazione dell’UX del’App B2C>>]()

<!---HONumber=Oct15_HO1-->