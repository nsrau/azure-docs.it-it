---
title: Guida alla migrazione di ADAL to MSAL (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra MSAL per iOS/macOS e azure AD Authentication Library per ObjectiveC (ADAL). ObjC) e come eseguire la migrazione a MSAL per iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085169"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Eseguire la migrazione delle applicazioni a MSAL per iOS e macOSMigrate applications to MSAL for iOS and macOS

La libreria di autenticazione di Azure Active Directory[(ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) è stata creata per lavorare con gli account di Azure Active Directory tramite l'endpoint v1.0.

La libreria di autenticazione Microsoft per iOS e macOS (MSAL) è progettata per funzionare con tutte le identità Microsoft, ad esempio gli account Azure Active Directory (Azure AD), gli account Microsoft personali e gli account Azure AD B2C tramite la piattaforma di identità Microsoft (formalmente l'endpoint di Azure AD v2.0).

La piattaforma di identità Microsoft presenta alcune differenze fondamentali con Azure Active Directory v1.0.The Microsoft identity platform has a few key differences with Azure Active Directory v1.0. Questo articolo evidenzia queste differenze e fornisce indicazioni per eseguire la migrazione di un'app da ADAL a MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Differenze di funzionalità delle app ADAL e MSAL

### <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

* ADAL supporta solo account aziendali e dell'istituto di istruzione, noti anche come account Azure AD.
* MSAL supporta account Microsoft personali (account MSA), ad esempio Hotmail.com, Outlook.com e Live.com.
* MSAL supporta gli account aziendali e dell'istituto di istruzione e gli account B2C di Azure AD.

### <a name="standards-compliance"></a>Conformità agli standard

* L'endpoint della piattaforma di identità Microsoft segue gli standard OAuth 2.0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

* L'endpoint di Azure Active Directory v1.0 richiede che tutte le autorizzazioni vengano dichiarate in anticipo durante la registrazione dell'applicazione. Ciò significa che tali autorizzazioni sono statiche.
* La piattaforma di identità Microsoft consente di richiedere le autorizzazioni in modo dinamico. Le app possono richiedere le autorizzazioni solo se necessario e richiederne altre in base alle esigenze dell'app.

Per altre informazioni sulle differenze tra Azure Active Directory v1.0 e la piattaforma di identità Microsoft, vedere [Perché eseguire l'aggiornamento alla piattaforma di identità Microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>Differenze tra librerie ADAL e MSAL

L'API pubblica MSAL riflette alcune differenze chiave tra Azure AD v1.0 e la piattaforma di identità Microsoft.The MSAL public API reflects a few key differences between Azure AD v1.0 and the Microsoft identity platform.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication anziché ADAuthenticationContext

`ADAuthenticationContext`è il primo oggetto creato da un'app ADAL. Rappresenta un'istanza di ADAL. Le app creano `ADAuthenticationContext` una nuova istanza di per ogni combinazione di cloud e tenant (autorità) di Azure Active Directory.Apps create a new instance of for each Azure Active Directory cloud and tenant (authority) combination. Lo `ADAuthenticationContext` stesso può essere usato per ottenere i token per più applicazioni client pubbliche.

In MSAL, l'interazione `MSALPublicClientApplication` principale è tramite un oggetto, che viene modellato in base a [OAuth 2.0 Public Client](https://tools.ietf.org/html/rfc6749#section-2.1). Un'istanza `MSALPublicClientApplication` di può essere usata per interagire con più cloud AAD e tenant, senza dover creare una nuova istanza per ogni autorità. Per la maggior `MSALPublicClientApplication` parte delle app, un'istanza è sufficiente.

### <a name="scopes-instead-of-resources"></a>Ambiti anziché risorse

In ADAL, un'app doveva fornire `https://graph.microsoft.com` un identificatore di risorsa come per acquisire token dall'endpoint di Azure Active Directory v1.0.In ADAL, an app had to provide a *resource* identifier like to acquire tokens from the Azure Active Directory v1.0 endpoint. Una risorsa può definire un numero di ambiti, o oAuth2Permissions nel manifesto dell'app, che riconosce. Ciò ha consentito alle app client di richiedere token da tale risorsa per un determinato set di ambiti predefiniti durante la registrazione dell'app.

In MSAL, invece di un singolo identificatore di risorsa, le app forniscono un set di ambiti per richiesta. Un ambito è un identificatore di risorsa seguito da un nome di autorizzazione nel modulo risorsa/autorizzazione. Ad esempio, usare `https://graph.microsoft.com/user.read`

Esistono due modi per fornire ambiti in MSAL:

* Fornisci un elenco di tutte le autorizzazioni necessarie alle tue app. Ad esempio: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    In questo caso, l'app richiede le `directory.read` autorizzazioni e `directory.write` . All'utente verrà chiesto di acconsentire a tali autorizzazioni se non hanno acconsentito a loro prima per questa applicazione. L'applicazione potrebbe anche ricevere autorizzazioni aggiuntive che l'utente ha già acconsentito per l'applicazione. All'utente verrà richiesto di concedere il consenso solo per le nuove autorizzazioni o per quelle che non sono state concesse.

* Ambito `/.default`.

Questo è l'ambito predefinito per ogni applicazione. Fa riferimento all'elenco statico di autorizzazioni configurate al momento della registrazione dell'applicazione. Il suo comportamento è `resource`simile a quello di . Ciò può essere utile durante la migrazione per garantire che venga mantenuto un set simile di ambiti ed esperienza utente.

Per utilizzare `/.default` l'ambito, aggiungere `/.default` all'identificatore di risorsa. Ad esempio `https://graph.microsoft.com/.default`. Se la risorsa termina`/`con una barra `/.default`( ), è comunque necessario aggiungere , inclusa`//`la barra iniziale, determinando un ambito che contiene una doppia barra ( ).

È possibile leggere ulteriori informazioni sull'utilizzo dell'ambito "/.default" [qui](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Supporto di diversi tipi di webView & browser

ADAL supporta solo UIWebView/WKWebView per iOS e WebView per macOS. MSAL per iOS supporta più opzioni per la visualizzazione di contenuto Web `UIWebView`quando si richiede un codice di autorizzazione e non supporta più ; che può migliorare l'esperienza utente e la sicurezza.

Per impostazione predefinita, MSAL in iOS utilizza [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), che è il componente Web consigliato da Apple per l'autenticazione su dispositivi iOS 12. Fornisce vantaggi Single Sign-On (SSO) attraverso la condivisione di cookie tra le app e il browser Safari.

Puoi scegliere di usare un componente Web diverso a seconda dei requisiti dell'app e dell'esperienza dell'utente finale desiderata. Per altre opzioni, vedere [Tipi di visualizzazione Web supportati.](customize-webviews.md)

Quando si esegue la migrazione `WKWebView` da ADAL a MSAL, fornisce l'esperienza utente più simile ad ADAL su iOS e macOS. Ti invitiamo `ASWebAuthenticationSession` a migrare su iOS, se possibile. Per macOS, ti invitiamo a utilizzare `WKWebView`.

### <a name="account-management-api-differences"></a>Differenze dell'API di gestione degli account

Quando si chiamano i `acquireToken()` `acquireTokenSilent()`metodi ADAL `ADUserInformation` o , si riceve `id_token` un oggetto contenente un elenco di attestazioni dall'oggetto che rappresenta l'account da autenticare. Inoltre, `ADUserInformation` restituisce `userId` un `upn` in base all'attestazione. Dopo l'acquisizione di token interattivi `userId` iniziale, ADAL si aspetta che lo sviluppatore fornisca tutte le chiamate silenziose.

ADAL non fornisce un'API per recuperare le identità utente note. Si basa sull'app per salvare e gestire tali account.

MSAL fornisce un set di API per elencare tutti gli account noti a MSAL senza dover acquisire un token.

Analogamente ad ADAL, MSAL restituisce le `id_token`informazioni sull'account che contiene un elenco di attestazioni dal file . Fa parte dell'oggetto `MSALAccount` all'interno dell'oggetto. `MSALResult`

MSAL fornisce un set di API per rimuovere gli account, rendendo gli account rimossi inaccessibili all'app. Dopo la rimozione dell'account, le chiamate successive all'acquisizione di token richiederanno all'utente di eseguire l'acquisizione di token interattiva. La rimozione dell'account si applica solo all'applicazione client che l'ha avviata e non rimuove l'account dalle altre app in esecuzione nel dispositivo o nel browser di sistema. Ciò garantisce che l'utente continui ad avere un'esperienza SSO sul dispositivo anche dopo la disconnessione da una singola app.

Inoltre, MSAL restituisce anche un identificatore di account che può essere utilizzato per richiedere un token in modo invisibile all'utente in un secondo momento. Tuttavia, l'identificatore `identifier` dell'account (accessibile tramite proprietà nell'oggetto) `MSALAccount` non è visualizzabile e non è possibile presupporre il formato in cui si trova né provare a interpretarlo o analizzarlo.

### <a name="migrating-the-account-cache"></a>Migrazione della cache degli account

Durante la migrazione da ADAL, le `userId`app in genere `identifier` memorizzano ADAL, che non dispone di MSAL. Come passaggio di migrazione una tantera, un'app può eseguire una query su un account MSAL usando userId di ADAL con l'API seguente:As a one-time migration step, an app can query an MSAL account using ADAL's userId with the following API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Questa API legge la cache di MSAL e ADAL per trovare l'account da ADAL userId (UPN).

Se l'account viene trovato, lo sviluppatore deve utilizzare l'account per eseguire l'acquisizione di token invisibile all'utente. La prima acquisizione di token invisibile all'utente aggiornerà effettivamente l'account e`identifier`lo sviluppatore otterrà un identificatore di account compatibile MSAL nel risultato MSAL ( ). Dopo di `identifier` che, solo deve essere utilizzato per le ricerche di account utilizzando la seguente API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Anche se è possibile continuare a `userId` utilizzare ADAL per tutte `userId` le operazioni in MSAL, poiché è basato su UPN, è soggetto a più limitazioni che causano un'esperienza utente non valida. Ad esempio, se l'UPN cambia, l'utente deve eseguire nuovamente l'accesso. Ti consigliamo che tutte le app `identifier` utilizzino l'account non visualizzabile per tutte le operazioni.

Ulteriori informazioni sulla [migrazione dello stato della cache](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Modifiche all'acquisizione di token

MSAL introduce alcune modifiche alle chiamate di acquisizione di token:

* Come ADAL, `acquireTokenSilent` si traduce sempre in una richiesta invisibile all'utente.
* A differenza `acquireToken` di ADAL, comporta sempre l'interfaccia utente utilizzabile dall'utente tramite la visualizzazione Web o l'app Microsoft Authenticator. A seconda dello stato SSO all'interno di webview/Microsoft Authenticator, all'utente potrebbe essere richiesto di immettere le proprie credenziali.
* In `acquireToken` ADAL, `AD_PROMPT_AUTO` con il primo tentativo di acquisizione di token invisibile all'utente e mostra l'interfaccia utente solo se la richiesta invisibile all'utente ha esito negativo. In MSAL, questa logica può `acquireTokenSilent` essere ottenuta chiamando prima e chiamando `acquireToken` solo se l'acquisizione invisibile all'utente ha esito negativo. Ciò consente agli sviluppatori di personalizzare l'esperienza utente prima di avviare l'acquisizione di token interattiva.

### <a name="error-handling-differences"></a>Differenze di gestione degli errori

MSAL offre maggiore chiarezza tra gli errori che possono essere gestiti dall'app e quelli che richiedono l'intervento dell'utente. Ci sono un numero limitato di errori che lo sviluppatore deve gestire:

* `MSALErrorInteractionRequired`: l'utente deve eseguire una richiesta interattiva. Ciò può essere causato da vari motivi, ad esempio una sessione di autenticazione scaduta, i criteri di accesso condizionale sono stati modificati, un token di aggiornamento scaduto o è stato revocato, non sono presenti token validi nella cache e così via.
* `MSALErrorServerDeclinedScopes`: la richiesta non è stata completata completamente e ad alcuni ambiti non è stato concesso l'accesso. Ciò può essere causato da un utente che rifiuta il consenso a uno o più ambiti.

La gestione di tutti gli altri errori [ `MSALError` nell'elenco](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) è facoltativa. È possibile utilizzare le informazioni contenute in tali errori per migliorare l'esperienza utente.

Per ulteriori informazioni sulla gestione degli errori MSAL, vedere [Gestione di eccezioni ed errori mediante MSAL.](msal-handling-exceptions.md)

### <a name="broker-support"></a>Supporto broker

MSAL, a partire dalla versione 0.3.0, fornisce il supporto per l'autenticazione mediata tramite l'app Microsoft Authenticator. Microsoft Authenticator consente inoltre il supporto per gli scenari di accesso condizionale. Esempi di scenari di accesso condizionale includono i criteri di conformità dei dispositivi che richiedono all'utente di registrare il dispositivo tramite Intune o registrarsi con AAD per ottenere un token. E i criteri di accesso condizionale di Mobile Application Management (MAM), che richiedono una prova di conformità prima che l'app possa ottenere un token.

Per abilitare il broker per l'applicazione:

1. Registrare un formato URI di reindirizzamento compatibile con il broker per l'applicazione. Il formato URI di `msauth.<app.bundle.id>://auth`reindirizzamento compatibile con broker è . Sostituire `<app.bundle.id>` con l'ID del bundle dell'applicazione. Se si sta eseguendo la migrazione da ADAL e l'applicazione era già in grado di broker, non c'è niente in più che devi fare. L'URI di reindirizzamento precedente è completamente compatibile con MSAL, pertanto è possibile andare al passaggio 3.

2. Aggiungere lo schema URI di reindirizzamento dell'applicazione al file info.plist. Per l'URI di reindirizzamento `msauth.<app.bundle.id>`MSAL predefinito, il formato è . Ad esempio:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Aggiungere schemi seguenti al file Info.plist dell'app in LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Aggiungere quanto segue al file AppDelegate.m per gestire i callback: Objective-C:
    
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

### <a name="business-to-business-b2b"></a>Business to Business (B2B)

In ADAL si creano `ADAuthenticationContext` istanze separate per ogni tenant per cui l'app richiede i token. Questo non è più un requisito in MSAL. In MSAL, è possibile creare `MSALPublicClientApplication` una singola istanza di e usarla per qualsiasi cloud e organizzazione AAD specificando un'autorità diversa per le chiamate acquireToken e acquireTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO in collaborazione con altri SDK

MSAL per iOS può ottenere SSO tramite una cache unificata con i seguenti SDK:

- Obiettivo ADAL 2.7.x
- MSAL.NET per Xamarin 2.4.x
- ADAL.NET per Xamarin 4.4.x

SSO si ottiene tramite la condivisione del portachiavi iOS ed è disponibile solo tra le app pubblicate dallo stesso account sviluppatore Apple.

SSO tramite la condivisione del portachiavi iOS è l'unico tipo di SSO silenzioso.

Su macOS, MSAL può ottenere SSO con altre applicazioni basate su MSAL per iOS e macOS e applicazioni basate su ADAL Objective-C.

MSAL su iOS supporta anche altri due tipi di SSO:

* SSO tramite il browser web. MSAL per iOS `ASWebAuthenticationSession`supporta , che fornisce SSO attraverso i cookie condivisi tra altre applicazioni sul dispositivo e in particolare il browser Safari.
* SSO tramite un broker di autenticazione. In un dispositivo iOS, Microsoft Authenticator funge da broker di autenticazione. Può seguire i criteri di accesso condizionale, ad esempio la richiesta di un dispositivo conforme, e fornisce SSO per i dispositivi registrati. Gli SDK MSAL che iniziano con la versione 0.3.0 supportano un broker per impostazione predefinita.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) supporta MSAL per iOS a partire dalla versione [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL nella stessa app

ADAL versione 2.7.0 e versioni successive, non può coesistere con MSAL nella stessa applicazione. Il motivo principale è a causa del codice comune del sottomodulo condiviso. Poiché Objective-C non supporta gli spazi dei nomi, se si aggiungono entrambi i framework ADAL e MSAL all'applicazione, saranno presenti due istanze della stessa classe. Non c'è garanzia per la quale uno viene scelto in fase di esecuzione. Se entrambi gli SDK usano la stessa versione della classe in conflitto, l'app potrebbe continuare a funzionare. Tuttavia, se si tratta di una versione diversa, l'app potrebbe riscontrare arresti anomali imprevisti difficili da diagnosticare.

L'esecuzione di ADAL e MSAL nella stessa applicazione di produzione non è supportata. Tuttavia, se stai solo testando e migrando i tuoi utenti da ADAL Objective-C a MSAL per iOS e macOS, puoi continuare a utilizzare [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). È l'unica versione che funziona con MSAL nella stessa applicazione. Non saranno disponibili nuovi aggiornamenti delle funzionalità per questa versione di ADAL, pertanto deve essere utilizzato solo a scopo di migrazione e test. L'app non deve basarsi su ADAL e la coesistenza MSAL a lungo termine.

La coesistenza ADAL e MSAL nella stessa applicazione non è supportata.
La coesistenza ADAL e MSAL tra più applicazioni è completamente supportata.

## <a name="practical-migration-steps"></a>Passaggi pratici per la migrazione

### <a name="app-registration-migration"></a>Migrazione della registrazione delle app

Non è necessario modificare l'applicazione AAD esistente per passare a MSAL e abilitare gli account AAD. Tuttavia, se l'applicazione basata su ADAL non supporta l'autenticazione negoziata, è necessario registrare un nuovo URI di reindirizzamento per l'applicazione prima di poter passare a MSAL.

L'URI di reindirizzamento `msauth.<app.bundle.id>://auth`deve essere nel seguente formato: . Sostituire `<app.bundle.id>` con l'ID del bundle dell'applicazione. Specificare l'URI di reindirizzamento nel portale di [Azure.](https://aka.ms/MobileAppReg)

Solo per iOS, per supportare l'autenticazione basata su certificati, è necessario registrare `msauth://code/<broker-redirect-uri-in-url-encoded-form>`un URI di reindirizzamento aggiuntivo nell'applicazione e nel portale di Azure nel formato seguente: Ad esempio, usare `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Ti consigliamo che tutte le app registrino entrambi gli URI di reindirizzamento.

Se desideri aggiungere il supporto per il consenso incrementale, seleziona le API e le autorizzazioni a cui l'app è configurata per richiedere l'accesso nella registrazione dell'app nella scheda **Autorizzazioni API.**

Se si esegue la migrazione da ADAL e si desidera supportare sia gli account AAD che MSA, la registrazione dell'applicazione esistente deve essere aggiornata per supportare entrambi. Non è consigliabile aggiornare immediatamente l'app di produzione esistente per supportare sia AAD che MSA. Creare invece un altro ID client che supporti sia AAD che MSA per il test e, dopo aver verificato il funzionamento di tutti gli scenari, aggiornare l'app esistente.

### <a name="add-msal-to-your-app"></a>Aggiungere MSAL all'app

È possibile aggiungere MSAL SDK all'app usando lo strumento di gestione dei pacchetti preferito. Vedere [le istruzioni dettagliate qui](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aggiornare il file Info.plist dell'app

Solo per iOS, aggiungi lo schema URI di reindirizzamento dell'applicazione al file info.plist. Per le applicazioni compatibili broker ADAL, dovrebbe essere già lì. Lo schema URI di reindirizzamento MSAL `msauth.<app.bundle.id>`predefinito sarà nel formato: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Aggiungere gli schemi seguenti al file Info.plist dell'app in `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aggiornare il codice AppDelegateUpdate your AppDelegate code

Solo per iOS, aggiungere quanto segue al file AppDelegate.m:

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

**Se si utilizza Xcode 11**, è necessario `SceneDelegate` inserire il callback MSAL nel file.
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

Ciò consente a MSAL di gestire le risposte dal broker e dal componente web.
Questo non era necessario in ADAL poiché "swizzled" metodi di delegato app automaticamente. L'aggiunta manuale è meno soggetta a errori e offre all'applicazione un maggiore controllo.

### <a name="enable-token-caching"></a>Abilitare la memorizzazione nella cache dei token

Per impostazione predefinita, MSAL memorizza nella cache i token dell'app nel keychain iOS o macOS. 

Per abilitare la memorizzazione nella cache dei token:
1. Assicurarsi che l'applicazione sia firmata correttamente
2. Vai alla **scheda** > Impostazioni progetto Xcode > Capacità**Abilita condivisione portachiavi**
3. Fare **+** clic e immettere una seguente voce **Gruppi Portachiavi:** 3.a Per iOS, immettere `com.microsoft.adalcache` 3.b Per macOS immettere`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Creare MSALPublicClientApplication e passare alle relative chiamate acquireToken e acquireTokeSilentCreate MSALPublicClientApplication and switch to its acquireToken and acquireTokeSilent calls

È possibile `MSALPublicClientApplication` creare usando il codice seguente:You can create using following code:

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

Chiamare quindi l'API di gestione degli account per verificare se sono presenti account nella cache:Then call the account management API to see if there are any accounts in the cache:

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



o leggere tutti i conti:

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



Se viene trovato un account, `acquireTokenSilent` chiamare l'API MSAL:

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
