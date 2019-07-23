---
title: Introduzione a iOS - Microsoft Identity Platform | Azure
description: Informazioni su come le applicazioni iOS (Swift) possono chiamare un'API che richiede token di accesso tramite Microsoft Identity Platform
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872079"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Accesso utenti e chiamata di Microsoft Graph da un'app iOS

In questa esercitazione si apprenderà come integrare un'app iOS con Microsoft Identity Platform. L'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.  

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

## <a name="prerequisites"></a>Prerequisiti

- Per compilare l'app in questa guida, è necessario XCode versione 10.x. È possibile scaricare XCode dal [sito Web iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di download di XCode").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). È possibile usare un gestore delle dipendenze o aggiungere la libreria manualmente. Le istruzioni seguenti illustrano come eseguire queste operazioni.

Questa esercitazione creerà un nuovo progetto. Se si preferisce scaricare invece l'esercitazione completata [scaricare il codice](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Aprire Xcode e selezionare **Create a new Xcode project**.
2. Selezionare **iOS** > **App visualizzazione singola** e selezionare **Avanti**.
3. Specificare un nome di prodotto.
4. Impostare il **linguaggio** su **Swift** e selezionare **Avanti**.
5. Selezionare una cartella per creare l'app e fare clic su **Create** (Crea).

## <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://aka.ms/MobileAppReg)
2. Aprire il pannello [Registrazioni per l'app](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **+ Nuova registrazione**.
3. In **Nome** immettere un nome per l'app e quindi, senza impostare un URI di reindirizzamento, fare clic su **Registra**.
4. Nella sezione **Gestione** del riquadro visualizzato selezionare **Autenticazione**.
5. Fare clic su **Prova la nuova esperienza** nella parte superiore della schermata per aprire la nuova esperienza di registrazione dell'app e quindi fare clic su **+ Nuova registrazione** >  **+ Aggiungi una piattaforma** > **iOS**.
    - Immettere l'ID del bundle del progetto. Se è stato scaricato il codice, l'ID è `com.microsoft.identitysample.MSALiOS`. Se si sta creando un progetto personalizzato, selezionare il progetto in Xcode e aprire la scheda **Generale**. L'identificatore del bundle viene visualizzato nella sezione **Identità**.
6. Fare clic su `Configure` e salvare la **Configurazione MSAL** visualizzata nella pagina **Configurazione di iOS** in modo da poterla immettere durante la configurazione dell'app in seguito.  Fare clic su **Done**.

## <a name="add-msal"></a>Aggiungere MSAL

Scegliere uno dei modi seguenti per installare la libreria MSAL nell'app:

### <a name="cocoapods"></a>CocoaPods

1. Se si usa [CocoaPods](https://cocoapods.org/), installare `MSAL` creando innanzitutto un file vuoto denominato `podfile` nella stessa cartella del file `.xcodeproj` del progetto. Aggiungere quanto segue a `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
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
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Modificare il valore assegnato a `kClientID` in modo che corrisponda all'ID applicazione. Questo valore fa parte dei dati di configurazione di MSAL salvati durante il passaggio all'inizio di questa esercitazione per registrare l'applicazione nel portale di Azure.

## <a name="configure-url-schemes"></a>Configurare gli schemi URL

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Creare l'interfaccia utente dell'app

A questo punto creare un'interfaccia utente che includa un pulsante per chiamare l'API Microsoft Graph, un altro per disconnettersi e una visualizzazione di testo per visualizzare l'output aggiungendo il codice seguente alla classe `ViewController`:

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

A questo punto, sempre all'interno della classe `ViewController`, sostituire il metodo `viewDidLoad()` con:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Usare MSAL

### <a name="initialize-msal"></a>Inizializzare MSAL

Aggiungere il metodo `InitMSAL` seguente alla classe `ViewController`:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>Gestire il callback di accesso

Aprire il file `AppDelegate.swift` . Per gestire il callback dopo l'accesso, aggiungere `MSALPublicClientApplication.handleMSALResponse` alla classe `appDelegate` nel modo seguente:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Acquisire i token

A questo punto, è possibile implementare la logica di elaborazione dell'interfaccia utente dell'applicazione e ottenere i token in modo interattivo tramite MSAL.

MSAL espone due metodi principali per ottenere i token, ovvero `acquireTokenSilently()` e `acquireTokenInteractively()`: 

- `acquireTokenSilently()` prova a completare l'accesso per un utente e ottenere i token senza alcuna interazione dell'utente purché sia presente un account.

- `acquireTokenInteractively()` visualizza sempre l'interfaccia utente durante il tentativo di accesso dell'utente. Potrebbe usare i cookie di sessione nel browser o un account di Microsoft Authenticator per offrire un'esperienza di accesso SSO interattiva.

Aggiungere il codice seguente alla classe `ViewController`:

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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
3. Gestire gli errori. Per altri dettagli, vedere la [guida per la gestione degli errori di iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Gestire il caso di esito positivo.

Aggiungere il codice seguente alla classe `ViewController` .

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Abilitare la memorizzazione nella cache dei token

Per impostazione predefinita, MSAL memorizza nella cache i token dell'app nel keychain iOS. 

Per abilitare la memorizzazione nella cache dei token:
1. Passare a Xcode Project Settings (Impostazioni progetto Xcode) > **scheda Capabilities** (Funzionalità) > **Enable Keychain Sharing** (Abilita condivisione keychain).
2. Fare clic su **+** e immettere `com.microsoft.adalcache` come voce di **Keychain Groups** (Gruppi keychain).

### <a name="add-helper-methods"></a>Aggiungere metodi helper

Per completare l'esempio, aggiungere i metodi helper seguenti alla classe `ViewController`:

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

### <a name="multi-account-applications"></a>Applicazioni con più account

Questa app è stata sviluppata per uno scenario con un singolo account. MSAL supporta anche scenari con più account, ma richiede alcune operazioni aggiuntive per le app. È necessario creare un'interfaccia utente per consentire agli utenti di selezionare l'account da usare per ogni azione che richiede i token. In alternativa, l'app può implementare un'euristica per selezionare l'account da usare tramite il metodo `getAccounts()`.

## <a name="test-your-app"></a>Test dell'app

### <a name="run-locally"></a>Esecuzione locale

Compilare e distribuire l'app in un dispositivo di test o un emulatore. Dovrebbe essere possibile accedere e ottenere i token per account di Azure AD o gli account Microsoft personali.

Al primo accesso di un utente nell'app, verrà richiesto il consenso per le autorizzazioni richieste da Microsoft Identity Platform.  Anche se la maggior parte degli utenti è in grado di fornire il consenso, alcuni tenant di Azure AD hanno disabilitato il consenso dell'utente e sarà quindi necessario che gli amministratori forniscano il consenso per conto di tutti gli utenti. Per supportare questo scenario, registrare gli ambiti dell'app nel portale di Azure.

Dopo l'accesso, l'app visualizzerà i dati restituiti dall'endpoint `/me` di Microsoft Graph.

## <a name="get-help"></a>Ottenere aiuto

In caso di problemi con questa esercitazione o con Microsoft Identity Platform, vedere [Opzioni di supporto tecnico e assistenza per gli sviluppatori](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).
