---
title: Introduzione a iOS e macOS - Microsoft Identity Platform | Azure
description: Informazioni su come le applicazioni iOS e macOS (Swift) possono chiamare un'API che richiede token di accesso tramite Microsoft Identity Platform
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e3892a03ffe097a51f294e698168f00e1359f92
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960672"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Accesso utenti e chiamata di Microsoft Graph da un'app iOS o macOS

In questa esercitazione si apprenderà come integrare un'app iOS o macOS con Microsoft Identity Platform. L'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.  

Al termine della guida, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usa Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Contenuto dell'esercitazione

![Illustra come funziona l'app di esempio generata da questa esercitazione](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

L'app in questa esercitazione consentirà agli utenti di accedere e recuperare i dati per loro conto.  Questi dati saranno accessibili tramite un'API protetta (in questo caso l'API Microsoft Graph) che richiede l'autorizzazione ed è protetta da Microsoft Identity Platform.

Più in particolare:

* L'app consentirà l'accesso all'utente tramite un browser o Microsoft Authenticator.
* L'utente finale accetterà le autorizzazioni richieste dall'applicazione.
* All'app verrà fornito un token di accesso per l'API Microsoft Graph.
* Il token di accesso verrà incluso nella richiesta HTTP all'API Web.
* Elaborare la risposta di Microsoft Graph.

Questo esempio usa Microsoft Authentication Library (MSAL) per implementare l'autenticazione. MSAL rinnoverà automaticamente i token, distribuirà il Single Sign-On (SSO) tra le altre app nel dispositivo e gestirà gli account.

Questa esercitazione è applicabile alle app iOS e macOS. Alcuni passaggi sono però diversi tra le due piattaforme. 

## <a name="prerequisites"></a>Prerequisiti

- Per compilare l'app in questa guida, è necessario Xcode 10.x o versione successiva. È possibile scaricare Xcode dal [sito Web iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di download di Xcode").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). È possibile usare un gestore delle dipendenze o aggiungere la libreria manualmente. Le istruzioni seguenti illustrano come eseguire queste operazioni.

Questa esercitazione creerà un nuovo progetto. Se si preferisce scaricare invece l'esercitazione completata scaricare il codice:
- [Codice di esempio iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Codice di esempio macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Aprire Xcode e selezionare **Create a new Xcode project**.
2. Per le app iOS selezionare **iOS** > **App visualizzazione singola** e quindi **Avanti**.
3. Per le app macOS selezionare **macOS** > **App Cocoa** e scegliere **Avanti**.
4. Specificare un nome di prodotto.
5. Impostare il **linguaggio** su **Swift** e selezionare **Avanti**.
6. Selezionare una cartella per creare l'app e fare clic su **Create** (Crea).

## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://aka.ms/MobileAppReg)
2. Aprire il pannello [Registrazioni per l'app](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **+ Nuova registrazione**.
3. In **Nome** immettere un nome per l'app e quindi, senza impostare un URI di reindirizzamento, fare clic su **Registra**.
4. Nella sezione **Gestione** del riquadro visualizzato selezionare **Autenticazione**.

5. Fare clic su **Prova la nuova esperienza** nella parte superiore della schermata per aprire la nuova esperienza di registrazione dell'app e quindi fare clic su **+ Nuova registrazione** >  **+ Aggiungi una piattaforma** > **iOS**.
    - Immettere l'ID del bundle del progetto. Se è stato scaricato il codice, l'ID è `com.microsoft.identitysample.MSALiOS`. Se si sta creando un progetto personalizzato, selezionare il progetto in Xcode e aprire la scheda **Generale**. L'identificatore del bundle viene visualizzato nella sezione **Identità**.
    - Si noti che per macOS è necessario usare anche l'esperienza iOS. 
6. Fare clic su `Configure` e salvare la **Configurazione MSAL** visualizzata nella pagina **Configurazione di iOS** in modo da poterla immettere durante la configurazione dell'app in seguito.  Fare clic su **Done**.

## <a name="add-msal"></a>Aggiungere MSAL

Scegliere uno dei modi seguenti per installare la libreria MSAL nell'app:

### <a name="cocoapods"></a>CocoaPods

1. Se si usa [CocoaPods](https://cocoapods.org/), installare `MSAL` creando innanzitutto un file vuoto denominato `podfile` nella stessa cartella del file `.xcodeproj` del progetto. Aggiungere quanto segue a `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Sostituire `<your-target-here>` con il nome del progetto.
3. In una finestra del terminale passare alla cartella che contiene il file `podfile` creato ed eseguire `pod install` per installare la libreria MSAL.
4. Chiudere Xcode e aprire `<your project name>.xcworkspace` per ricaricare il progetto in Xcode.

### <a name="carthage"></a>Carthage

Se si usa [Carthage](https://github.com/Carthage/Carthage), installare `MSAL` aggiungendolo al file `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

In una finestra del terminale, nella stessa directory del file `Cartfile` aggiornato, eseguire il comando seguente per fare in modo che Carthage aggiorni le dipendenze nel progetto.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

È anche possibile usare Git Submodule o eseguire il checkout della versione più recente da usare come framework nell'applicazione.

## <a name="add-your-app-registration"></a>Aggiungere la registrazione per l'app

A questo punto, la registrazione dell'app verrà aggiunta al codice. 

Aggiungere innanzitutto l'istruzione import seguente all'inizio dei file `ViewController.swift` e `AppDelegate.swift`:

```swift
import MSAL
```

Aggiungere quindi il codice seguente a `ViewController.swift` prima di `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

L'unico valore da modificare in precedenza è quello assegnato a `kClientID` per fare in modo che corrisponda a quello di [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Questo valore fa parte dei dati di configurazione di MSAL salvati durante il passaggio all'inizio di questa esercitazione per registrare l'applicazione nel portale di Azure.

## <a name="for-ios-only-configure-url-schemes"></a>Configurare gli schemi URL (solo per iOS)

In questo passaggio verrà eseguita la registrazione di `CFBundleURLSchemes`, in modo che l'utente possa essere reindirizzato all'app dopo l'accesso. `LSApplicationQueriesSchemes` consente inoltre all'app di usare Microsoft Authenticator.

In Xcode aprire `Info.plist` come file del codice sorgente e aggiungere quanto seguente all'interno della sezione `<dict>`. Sostituire `[BUNDLE_ID]` con il valore usato nel portale di Azure che è `com.microsoft.identitysample.MSALiOS`, se il codice è stato scaricato. Se si sta creando un progetto personalizzato, selezionare il progetto in Xcode e aprire la scheda **Generale**. L'identificatore del bundle viene visualizzato nella sezione **Identità**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Configurare Sandbox app (solo per macOS)

1. Passare a Impostazioni progetto di Xcode > **scheda Funzionalità** > **Sandbox app**.
2. Selezionare la casella di controllo **Connessioni in uscita (client)** . 

## <a name="create-your-apps-ui"></a>Creare l'interfaccia utente dell'app

A questo punto creare un'interfaccia utente che includa un pulsante per chiamare l'API Microsoft Graph, un altro per disconnettersi e una visualizzazione di testo per visualizzare l'output aggiungendo il codice seguente alla classe `ViewController`:

### <a name="ios-ui"></a>Interfaccia utente iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>Interfaccia utente macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

A questo punto, sempre all'interno della classe `ViewController`, sostituire il metodo `viewDidLoad()` con:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Usare MSAL

### <a name="initialize-msal"></a>Inizializzare MSAL

Aggiungere il metodo `initMSAL` seguente alla classe `ViewController`:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Aggiungere quanto segue alla classe `ViewController` dopo il metodo `initMSAL`.

### <a name="ios-code"></a>Codice iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>Codice macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Gestire il callback di accesso (solo per iOS)

Aprire il file `AppDelegate.swift` . Per gestire il callback dopo l'accesso, aggiungere `MSALPublicClientApplication.handleMSALResponse` alla classe `appDelegate` nel modo seguente:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Se si usa Xcode 11**, è invece necessario inserire il callback MSAL in `SceneDelegate.swift`.
Se si supportano sia UISceneDelegate che UIApplicationDelegate per la compatibilità con le versioni precedenti di iOS, il callback MSAL deve essere inserito in entrambi i file.

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

#### <a name="acquire-tokens"></a>Acquisire i token

A questo punto, è possibile implementare la logica di elaborazione dell'interfaccia utente dell'applicazione e ottenere i token in modo interattivo tramite MSAL.

MSAL espone due metodi principali per ottenere i token, ovvero `acquireTokenSilently()` e `acquireTokenInteractively()`: 

- `acquireTokenSilently()` prova a completare l'accesso per un utente e ottenere i token senza alcuna interazione dell'utente purché sia presente un account.

- `acquireTokenInteractively()` visualizza sempre l'interfaccia utente durante il tentativo di accesso dell'utente. Potrebbe usare i cookie di sessione nel browser o un account di Microsoft Authenticator per offrire un'esperienza di accesso SSO interattiva.

Aggiungere il codice seguente alla classe `ViewController`:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Ottenere un token in modo interattivo

Il codice seguente consente di recuperare un token per la prima volta creando un oggetto `MSALInteractiveTokenParameters` e chiamando `acquireToken`. Successivamente si aggiungerà codice per:

1. Creare `MSALInteractiveTokenParameters` con ambiti.
2. Chiamare `acquireToken()` con i parametri creati.
3. Gestire gli errori. Per altri dettagli, vedere la [guida per la gestione degli errori MSAL per iOS e macOS](msal-handling-exceptions.md).
4. Gestire il caso di esito positivo.

Aggiungere il codice seguente alla classe `ViewController` .

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Ottenere un token in modo invisibile all'utente

Per acquisire un token aggiornato in modo invisibile all'utente, aggiungere il codice seguente alla classe `ViewController`. Crea un oggetto `MSALSilentTokenParameters` e chiama `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Chiamare l'API Microsoft Graph 

Dopo aver ottenuto un token, l'app può usarlo nell'intestazione HTTP per effettuare una richiesta autorizzata a Microsoft Graph:

| Chiave dell'intestazione    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

Aggiungere il codice seguente alla classe `ViewController`:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Per altre informazioni sull'API Microsoft Graph, vedere [API Microsoft Graph](https://graph.microsoft.com).

### <a name="use-msal-for-sign-out"></a>Usare MSAL per la disconnessione

A questo punto occorre aggiungere il supporto per la disconnessione.

> [!Important]
> La disconnessione con MSAL rimuove tutte le informazioni note su un utente da questa applicazione, ma l'utente avrà ancora una sessione attiva nel dispositivo. Se l'utente tenta di accedere nuovamente, è possibile che veda l'interfaccia utente per l'accesso, ma che non debba immettere nuovamente le credenziali dato che la sessione del dispositivo è ancora attiva.

Per aggiungere la funzionalità di disconnessione, aggiungere il codice seguente nella classe `ViewController`. Questo metodo scorre tutti gli account e li rimuove:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Abilitare la memorizzazione nella cache dei token

Per impostazione predefinita, MSAL memorizza nella cache i token dell'app nel keychain iOS o macOS. 

Per abilitare la memorizzazione nella cache dei token:
1. Assicurarsi che l'applicazione sia firmata correttamente
2. Passare a Xcode Project Settings (Impostazioni progetto Xcode) > **scheda Capabilities** (Funzionalità) > **Enable Keychain Sharing** (Abilita condivisione keychain).
3. Fare clic su **+** e immettere la voce seguente per **Gruppi di keychain**: 3. a Per iOS immettere `com.microsoft.adalcache` 3. b Per macOS immettere `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Aggiungere metodi helper
Per completare l'esempio, aggiungere i metodi helper seguenti alla classe `ViewController`.

### <a name="ios-ui"></a>Interfaccia utente iOS:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>Interfaccia utente macOS:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Applicazioni con più account

Questa app è stata sviluppata per uno scenario con un singolo account. MSAL supporta anche scenari con più account, ma richiede alcune operazioni aggiuntive per le app. È necessario creare un'interfaccia utente per consentire agli utenti di selezionare l'account da usare per ogni azione che richiede i token. In alternativa, l'app può implementare un'euristica per selezionare l'account da usare tramite il metodo `getAccounts()`.

## <a name="test-your-app"></a>Test dell'app

### <a name="run-locally"></a>Esecuzione locale

Compilare e distribuire l'app in un dispositivo di test o un simulatore. Dovrebbe essere possibile accedere e ottenere i token per account di Azure AD o gli account Microsoft personali.

Al primo accesso di un utente nell'app, verrà richiesto il consenso per le autorizzazioni richieste da Microsoft Identity Platform.  Anche se la maggior parte degli utenti è in grado di fornire il consenso, alcuni tenant di Azure AD hanno disabilitato il consenso dell'utente e sarà quindi necessario che gli amministratori forniscano il consenso per conto di tutti gli utenti. Per supportare questo scenario, registrare gli ambiti dell'app nel portale di Azure.

Dopo l'accesso, l'app visualizzerà i dati restituiti dall'endpoint `/me` di Microsoft Graph.

## <a name="get-help"></a>Ottenere aiuto

In caso di problemi con questa esercitazione o con Microsoft Identity Platform, vedere [Opzioni di supporto tecnico e assistenza per gli sviluppatori](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
