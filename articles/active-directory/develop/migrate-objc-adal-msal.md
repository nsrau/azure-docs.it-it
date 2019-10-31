---
let application: MSALPublicClientApplication!
title: Eseguire la migrazione di app a MSAL. ObjectiveC
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra Microsoft Authentication Library per ObjectiveC (MSAL per iOS e macOS) e la libreria Autenticazione di Azure AD per ObjectiveC (ADAL. ObjC) e come eseguire la migrazione a MSAL per iOS e macOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ccf88bcffdf484772f5f3ad35316d2c74fb104e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175634"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Eseguire la migrazione di applicazioni a MSAL per iOS e macOS

La libreria di autenticazione Azure Active Directory ([adal Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) è stata creata per lavorare con gli account di Azure Active Directory tramite l'endpoint v 1.0.

Microsoft Authentication Library per iOS e macOS (MSAL) è progettato per funzionare con tutte le identità Microsoft, ad esempio account Azure Active Directory (Azure AD), account Microsoft personali e account Azure AD B2C tramite la piattaforma di identità Microsoft (formalmente endpoint Azure AD v 2.0).

La piattaforma di identità Microsoft presenta alcune differenze principali rispetto a Azure Active Directory versione 1.0. Questo articolo evidenzia queste differenze e fornisce indicazioni per la migrazione di un'app da ADAL a MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Differenze tra le funzionalità delle app ADAL e MSAL

### <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

* ADAL supporta solo account aziendali e dell'Istituto di istruzione, noti anche come account Azure AD.
* MSAL supporta gli account Microsoft personali (account MSA), ad esempio Hotmail.com, Outlook.com e Live.com.
* MSAL supporta gli account aziendali e dell'Istituto di istruzione e gli account Azure AD B2C.

### <a name="standards-compliance"></a>Conformità agli standard

* L'endpoint della piattaforma Microsoft Identity segue gli standard OAuth 2,0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

* Per l'endpoint Azure Active Directory v 1.0 è necessario dichiarare in anticipo tutte le autorizzazioni durante la registrazione dell'applicazione. Ciò significa che le autorizzazioni sono statiche.
* La piattaforma Microsoft Identity consente di richiedere le autorizzazioni in modo dinamico. Le app possono richiedere le autorizzazioni solo in base alle esigenze e richiedere un maggior numero di richieste.

Per ulteriori informazioni sulle differenze tra Azure Active Directory v 1.0 e la piattaforma Microsoft Identity, vedere la pagina relativa [all'aggiornamento alla piattaforma di identità Microsoft (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>Differenze tra la libreria ADAL e MSAL

L'API pubblica MSAL riflette alcune differenze principali tra Azure AD v 1.0 e la piattaforma di identità Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication anziché ADAuthenticationContext

`ADAuthenticationContext` è il primo oggetto creato da un'app ADAL. Rappresenta la creazione di un'istanza di ADAL. Le app creano una nuova istanza di `ADAuthenticationContext` per ogni combinazione di Azure Active Directory cloud e tenant (Authority). È possibile usare lo stesso `ADAuthenticationContext` per ottenere i token per più applicazioni client pubbliche.

In MSAL, l'interazione principale avviene tramite un oggetto `MSALPublicClientApplication`, che viene modellato dopo il [client pubblico OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-2.1). È possibile usare un'istanza di `MSALPublicClientApplication` per interagire con più cloud AAD e tenant, senza dover creare una nuova istanza per ogni autorità. Per la maggior parte delle app, è sufficiente una `MSALPublicClientApplication` istanza.

### <a name="scopes-instead-of-resources"></a>Ambiti anziché risorse

In ADAL un'app doveva fornire un identificatore di *risorsa* come `https://graph.microsoft.com` per acquisire i token dall'endpoint Azure Active Directory v 1.0. Una risorsa può definire un certo numero di ambiti, o oAuth2Permissions nel manifesto dell'applicazione, che riconosce. In questo modo le app client possono richiedere token da tale risorsa per un determinato set di ambiti predefiniti durante la registrazione dell'app.

In MSAL, invece di un singolo identificatore di risorsa, le app forniscono un set di ambiti per ogni richiesta. Un ambito è un identificatore di risorsa seguito da un nome di autorizzazione nel formato risorsa/autorizzazione. Ad esempio: `https://graph.microsoft.com/user.read`

Esistono due modi per specificare gli ambiti in MSAL:

* Fornire un elenco di tutte le autorizzazioni necessarie per le app. ad esempio: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    In questo caso, l'app richiede le autorizzazioni `directory.read` e `directory.write`. All'utente verrà richiesto di fornire il consenso per le autorizzazioni se tali autorizzazioni non sono state consentite prima di questa app. L'applicazione potrebbe inoltre ricevere autorizzazioni aggiuntive che l'utente ha già acconsentito per l'applicazione. All'utente verrà richiesto di fornire il consenso solo per le nuove autorizzazioni o per le autorizzazioni che non sono state concesse.

* Ambito `/.default`.

Si tratta dell'ambito predefinito per ogni applicazione. Si riferisce all'elenco statico di autorizzazioni configurate quando l'applicazione è stata registrata. Il comportamento è simile a quello di `resource`. Questa operazione può essere utile quando si esegue la migrazione per assicurarsi che venga mantenuto un set di ambiti e un'esperienza utente analoghi.

Per usare l'ambito `/.default`, aggiungere `/.default` all'identificatore di risorsa. Ad esempio: `https://graph.microsoft.com/.default`. Se la risorsa termina con una barra (`/`), è comunque necessario accodare `/.default`, inclusa la barra di avanzamento, ottenendo un ambito con una barra doppia (`//`).

Per altre informazioni sull'uso dell'ambito "/.default", vedere [qui](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Supporto di diversi tipi di visualizzazione WebView & browser

ADAL supporta solo UIWebView/WKWebView per iOS e WebView per macOS. MSAL per iOS supporta più opzioni per la visualizzazione del contenuto Web quando viene richiesto un codice di autorizzazione e non supporta più `UIWebView`; in questo modo è possibile migliorare l'esperienza utente e la sicurezza.

Per impostazione predefinita, MSAL in iOS USA [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), ovvero il componente Web Apple consiglia di eseguire l'autenticazione nei dispositivi iOS 12 +. Fornisce vantaggi di Single Sign-on (SSO) tramite la condivisione di cookie tra le app e il browser Safari.

È possibile scegliere di usare un componente Web diverso a seconda dei requisiti dell'app e dell'esperienza dell'utente finale desiderata. Per altre opzioni, vedere [tipi di visualizzazione Web supportati](customize-webviews.md) .

Quando si esegue la migrazione da ADAL a MSAL, `WKWebView` offre l'esperienza utente più simile a ADAL in iOS e macOS. Se possibile, si consiglia di eseguire la migrazione a `ASWebAuthenticationSession` in iOS. Per macOS, si consiglia di usare `WKWebView`.

### <a name="account-management-api-differences"></a>Differenze tra API di gestione degli account

Quando si chiamano i metodi ADAL `acquireToken()` o `acquireTokenSilent()`, si riceve un oggetto `ADUserInformation` contenente un elenco di attestazioni dal `id_token` che rappresenta l'account in fase di autenticazione. Inoltre, `ADUserInformation` restituisce una `userId` basata sull'attestazione `upn`. Dopo l'acquisizione iniziale del token interattivo, ADAL prevede che lo sviluppatore fornisca `userId` in tutte le chiamate invisibili.

ADAL non fornisce un'API per recuperare le identità utente note. Si basa sull'app per il salvataggio e la gestione di tali account.

MSAL fornisce un set di API per elencare tutti gli account noti a MSAL senza dover acquisire un token.

Analogamente a ADAL, MSAL restituisce le informazioni sull'account che contengono un elenco di attestazioni dal `id_token`. Fa parte dell'oggetto `MSALAccount` all'interno dell'oggetto `MSALResult`.

MSAL fornisce un set di API per rimuovere gli account, rendendo gli account rimossi inaccessibili per l'app. Dopo la rimozione dell'account, le chiamate di acquisizione dei token successive richiederanno all'utente di eseguire l'acquisizione di token interattivi. La rimozione dell'account si applica solo all'applicazione client che lo ha avviata e non rimuove l'account dalle altre app in esecuzione sul dispositivo o dal browser del sistema. Ciò garantisce che l'utente continui a avere un'esperienza SSO sul dispositivo anche dopo la disconnessione da una singola app.

Inoltre, MSAL restituisce anche un identificatore di account che può essere usato per richiedere un token in modo invisibile all'utente in un secondo momento. Tuttavia, l'identificatore di account (accessibile tramite `identifier` proprietà nell'oggetto `MSALAccount`) non è visualizzabile e non è possibile presupporre il formato in cui si trova né provare a interpretarlo o analizzarlo.

### <a name="migrating-the-account-cache"></a>Migrazione della cache degli account

Quando si esegue la migrazione da ADAL, le app in genere archiviano la `userId`di ADAL, che non ha i `identifier` richiesti da MSAL. Come passaggio di migrazione monouso, un'app può eseguire una query su un account MSAL usando l'ID utente di ADAL con l'API seguente:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Questa API legge sia la cache di MSAL sia quella di ADAL per trovare l'account in base all'ID utente (UPN) di ADAL.

Se l'account viene trovato, lo sviluppatore deve usare l'account per eseguire l'acquisizione di token invisibile all'utente. La prima acquisizione di token invisibile all'utente aggiornerà efficacemente l'account e lo Sviluppatore otterrà un identificatore di account compatibile con MSAL nel risultato di MSAL (`identifier`). In seguito, è consigliabile usare solo `identifier` per le ricerche di account usando l'API seguente:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Sebbene sia possibile continuare a usare la `userId` di ADAL per tutte le operazioni in MSAL, poiché `userId` si basa su UPN, è soggetto a più limitazioni che comportano un'esperienza utente non valida. Se, ad esempio, l'UPN viene modificato, l'utente deve eseguire di nuovo l'accesso. È consigliabile che tutte le app usino l'account non visualizzabile `identifier` per tutte le operazioni.

Scopri di più sulla [migrazione dello stato della cache](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Modifiche all'acquisizione di token

MSAL introduce alcune modifiche alle chiamate di acquisizione dei token:

* Analogamente a ADAL, `acquireTokenSilent` restituisce sempre una richiesta invisibile all'utente.
* A differenza di ADAL, `acquireToken` restituisce sempre un'interfaccia utente utilizzabile dall'utente tramite la visualizzazione Web o l'app Microsoft Authenticator. A seconda dello stato SSO all'interno di WebView/Microsoft Authenticator, all'utente potrebbe essere richiesto di immettere le proprie credenziali.
* In ADAL, `acquireToken` con `AD_PROMPT_AUTO` tenta prima l'acquisizione di token invisibile all'utente e Mostra solo l'interfaccia utente se la richiesta invisibile non riesce. In MSAL è possibile ottenere questa logica chiamando prima `acquireTokenSilent` e chiamando `acquireToken` solo se l'acquisizione invisibile all'utente non riesce. Ciò consente agli sviluppatori di personalizzare l'esperienza utente prima di avviare l'acquisizione di token interattivo.

### <a name="error-handling-differences"></a>Differenze di gestione degli errori

MSAL offre maggiore chiarezza tra gli errori che possono essere gestiti dall'app e quelli che richiedono l'intervento dell'utente. Il numero di errori che lo sviluppatore deve gestire è limitato:

* `MSALErrorInteractionRequired`: l'utente deve eseguire una richiesta interattiva. Questo problema può essere causato da diversi motivi, ad esempio una sessione di autenticazione scaduta, che i criteri di accesso condizionale sono stati modificati, che un token di aggiornamento è scaduto o è stato revocato, non sono presenti token validi nella cache e così via.
* `MSALErrorServerDeclinedScopes`: la richiesta non è stata completata completamente e non è stato concesso l'accesso ad alcuni ambiti. Questo problema può essere causato da un utente che declina il consenso per uno o più ambiti.

La gestione di tutti gli altri errori nell' [elenco`MSALError`](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) è facoltativa. Per migliorare l'esperienza utente, è possibile utilizzare le informazioni contenute in tali errori.

Per ulteriori informazioni sulla gestione degli errori MSAL, vedere [gestione di eccezioni ed errori mediante MSAL](msal-handling-exceptions.md) .

### <a name="broker-support"></a>Supporto broker

MSAL, a partire dalla versione 0.3.0, fornisce il supporto per l'autenticazione negoziata usando l'app Microsoft Authenticator. Microsoft Authenticator Abilita anche il supporto per gli scenari di accesso condizionale. Esempi di scenari di accesso condizionale includono i criteri di conformità dei dispositivi che richiedono all'utente di registrare il dispositivo tramite Intune o di registrarsi con AAD per ottenere un token. E i criteri di accesso condizionale per la gestione di applicazioni mobili (MAM), che richiedono la verifica della conformità prima che l'app possa ottenere un token.

Per abilitare Service Broker per l'applicazione:

1. Registrare un formato URI di reindirizzamento compatibile con Service Broker per l'applicazione. Il formato dell'URI di reindirizzamento compatibile con Service Broker è `msauth.<app.bundle.id>://auth`. Sostituire `<app.bundle.id>` con l'ID bundle dell'applicazione. Se si esegue la migrazione da ADAL e l'applicazione è già in grado di supportare il broker, non è necessario eseguire alcuna operazione aggiuntiva. L'URI di reindirizzamento precedente è completamente compatibile con MSAL, pertanto è possibile procedere al passaggio 3.

2. Aggiungere lo schema URI di reindirizzamento dell'applicazione al file INFO. plist. Per l'URI di reindirizzamento MSAL predefinito, il formato è `msauth.<app.bundle.id>`. ad esempio:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Aggiungere gli schemi seguenti al file INFO. plist dell'app in LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Aggiungere il codice seguente al file AppDelegate. m per gestire i callback: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Business to business (B2B)

In ADAL è possibile creare istanze separate di `ADAuthenticationContext` per ogni tenant per cui l'app richiede i token. Questo non è più un requisito in MSAL. In MSAL è possibile creare una singola istanza di `MSALPublicClientApplication` e usarla per qualsiasi cloud e organizzazione di AAD specificando un'autorità diversa per le chiamate acquireToken e acquireTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO in partnership con altri SDK

MSAL per iOS può ottenere l'accesso SSO tramite una cache unificata con gli SDK seguenti:

- ADAL Objective-C 2.7. x +
- MSAL.NET per Novell 2.4. x +
- ADAL.NET per Novell 4.4. x +

L'accesso SSO viene effettuato tramite la condivisione del keychain iOS ed è disponibile solo tra le app pubblicate dallo stesso account per sviluppatori Apple.

L'accesso SSO tramite la condivisione del keychain iOS è l'unico tipo di accesso SSO invisibile all'utente.

In macOS, MSAL può ottenere l'accesso SSO con altre MSAL per le applicazioni basate su iOS e macOS e per le applicazioni ADAL basate su Objective-C.

MSAL in iOS supporta anche altri due tipi di SSO:

* SSO tramite il Web browser. MSAL per iOS supporta `ASWebAuthenticationSession`, che fornisce l'accesso SSO tramite cookie condivisi tra altre app nel dispositivo e in particolare il browser Safari.
* SSO tramite un broker di autenticazione. In un dispositivo iOS Microsoft Authenticator funge da broker di autenticazione. Può seguire i criteri di accesso condizionale, ad esempio richiedere un dispositivo conforme, e fornisce l'accesso SSO per i dispositivi registrati. Gli SDK di MSAL a partire dalla versione 0.3.0 supportano Service Broker per impostazione predefinita.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[INTUNE Mam SDK](https://docs.microsoft.com/intune/app-sdk-get-started) supporta MSAL per iOS a partire dalla versione [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL nella stessa app

ADAL versione 2.7.0 e successive non possono coesistere con MSAL nella stessa applicazione. Il motivo principale è dovuto al codice comune del modulo secondario condiviso. Poiché Objective-C non supporta gli spazi dei nomi, se si aggiungono i Framework ADAL e MSAL all'applicazione, saranno presenti due istanze della stessa classe. Non esiste alcuna garanzia per chi viene selezionato in fase di esecuzione. Se entrambi gli SDK usano la stessa versione della classe in conflitto, è possibile che l'app continui a funzionare. Tuttavia, se si tratta di una versione diversa, è possibile che l'app riscontri arresti anomali imprevisti difficili da diagnosticare.

L'esecuzione di ADAL e MSAL nella stessa applicazione di produzione non è supportata. Tuttavia, se si sta eseguendo solo il test e la migrazione degli utenti da ADAL Objective-C a MSAL per iOS e macOS, è possibile continuare a usare [adal Objective-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Si tratta dell'unica versione che funziona con MSAL nella stessa applicazione. Non saranno presenti nuovi aggiornamenti delle funzionalità per questa versione di ADAL, pertanto devono essere usati solo a scopo di migrazione e test. L'app non deve basarsi su ADAL e MSAL coesistenza a lungo termine.

La coesistenza di ADAL e MSAL nella stessa applicazione non è supportata.
La coesistenza di ADAL e MSAL tra più applicazioni è completamente supportata.

## <a name="practical-migration-steps"></a>Passaggi pratici per la migrazione

### <a name="app-registration-migration"></a>Migrazione della registrazione dell'app

Non è necessario modificare l'applicazione AAD esistente per passare a MSAL e abilitare gli account AAD. Tuttavia, se l'applicazione basata su ADAL non supporta l'autenticazione negoziata, sarà necessario registrare un nuovo URI di reindirizzamento per l'applicazione prima di poter passare a MSAL.

Il formato dell'URI di reindirizzamento deve essere il seguente: `msauth.<app.bundle.id>://auth`. Sostituire `<app.bundle.id>` con l'ID bundle dell'applicazione. Specificare l'URI di reindirizzamento nel [portale di Azure](https://aka.ms/MobileAppReg).

Solo per iOS, per supportare l'autenticazione basata su certificati, è necessario registrare un URI di reindirizzamento aggiuntivo nell'applicazione e il portale di Azure nel formato seguente: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Ad esempio: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

È consigliabile che tutte le app registrino entrambi gli URI di reindirizzamento.

Se si vuole aggiungere il supporto per il consenso incrementale, selezionare le API e le autorizzazioni a cui è configurata l'app per richiedere l'accesso nella registrazione dell'app nella scheda **autorizzazioni API** .

Se si sta eseguendo la migrazione da ADAL e si vogliono supportare sia gli account AAD che MSA, è necessario aggiornare la registrazione dell'applicazione esistente per supportare entrambi. Non è consigliabile aggiornare l'app di produzione esistente per supportare immediatamente AAD e MSA. In alternativa, creare un altro ID client che supporti sia AAD che MSA per il test e dopo aver verificato che tutti gli scenari funzionino, aggiornare l'app esistente.

### <a name="add-msal-to-your-app"></a>Aggiungere MSAL all'app

È possibile aggiungere MSAL SDK all'app usando lo strumento di gestione dei pacchetti preferito. Vedere [le istruzioni dettagliate qui](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aggiornare il file INFO. plist dell'app

Solo per iOS aggiungere lo schema URI di reindirizzamento dell'applicazione al file INFO. plist. Per le app compatibili con ADAL broker, dovrebbe essere già disponibile. Il formato dell'URI di reindirizzamento MSAL predefinito sarà: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Aggiungere gli schemi seguenti al file INFO. plist dell'app in `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aggiornare il codice AppDelegate

Solo per iOS, aggiungere il codice seguente al file AppDelegate. m:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Se si usa Xcode 11**, è necessario inserire il callback di MSAL nel file di `SceneDelegate`.
Se si supportano sia UISceneDelegate che UIApplicationDelegate per la compatibilità con le versioni precedenti di iOS, il callback MSAL deve essere inserito in entrambi i file.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Ciò consente a MSAL di gestire le risposte provenienti dal broker e dal componente Web.
Questa operazione non è necessaria in ADAL perché i metodi delegati di app "swizzled" vengono automaticamente. L'aggiunta manuale è meno soggetta a errori e fornisce all'applicazione un maggiore controllo.

### <a name="enable-token-caching"></a>Abilitare la memorizzazione nella cache dei token

Per impostazione predefinita, MSAL memorizza nella cache i token dell'app nel keychain iOS o macOS. 

Per abilitare la memorizzazione nella cache dei token:
1. Assicurarsi che l'applicazione sia firmata correttamente
2. Passare a Xcode Project Settings (Impostazioni progetto Xcode) > **scheda Capabilities** (Funzionalità) > **Enable Keychain Sharing** (Abilita condivisione keychain).
3. Fare clic su **+** e immettere una voce dei **gruppi di Keychain** seguente: 3. a per iOS, immettere `com.microsoft.adalcache` 3. b per MacOS immettere `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Crea MSALPublicClientApplication e passa alle chiamate acquireToken e acquireTokeSilent

È possibile creare `MSALPublicClientApplication` usando il codice seguente:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Chiamare quindi l'API di gestione account per verificare se sono presenti account nella cache:

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



oppure leggere tutti gli account:

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Se viene trovato un account, chiamare l'API `acquireTokenSilent` MSAL:

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
