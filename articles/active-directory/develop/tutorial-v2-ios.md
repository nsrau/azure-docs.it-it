---
title: Introduzione a iOS - Microsoft Identity Platform | Azure
description: Informazioni su come le applicazioni iOS (Swift) possono chiamare un'API che richiede token di accesso tramite Microsoft Identity Platform
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550566"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Accesso utenti e chiamata di Microsoft Graph da un'app iOS

In questa esercitazione si apprenderà come creare un'applicazione iOS e integrarla in Microsoft Identity Platform. In particolare, questa app consentirà a un utente di accedere, otterrà un token di accesso per chiamare l'API Microsoft Graph ed effettuerà una richiesta di base all'API Microsoft Graph.  

Al termine della guida, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usa Azure Active Directory.

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

L'app in questo esempio consentirà agli utenti di accedere e otterrà i dati per loro conto.  Questi dati saranno accessibili tramite un'API protetta (in questo caso l'API Microsoft Graph) che richiede l'autorizzazione ed è anche protetta da Microsoft Identity Platform.

Più in particolare:

* L'app consentirà l'accesso all'utente tramite un browser o Microsoft Authenticator.
* L'utente finale accetterà le autorizzazioni richieste dall'applicazione. 
* All'app verrà fornito un token di accesso per l'API Microsoft Graph.
* Il token di accesso verrà incluso nella richiesta HTTP all'API Web.
* Elaborare la risposta di Microsoft Graph.

Questo esempio usa Microsoft Authentication Library (MSAL) per implementare l'autenticazione. MSAL rinnoverà automaticamente i token, distribuirà SSO tra le altre app nel dispositivo e gestirà gli account.

## <a name="prerequisites"></a>Prerequisiti

- Per l'esempio creato in questa guida, è necessario XCode versione 10.x. È possibile scaricare XCode dal [sito Web iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di download di XCode").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). È possibile usare il gestore delle dipendenze o aggiungerle manualmente. È disponibile una sezione di seguito con [ulteriori informazioni](#add-msal). 

## <a name="set-up-your-project"></a>Configurare il progetto

Questa esercitazione creerà un nuovo progetto. Se si preferisce scaricare invece l'esercitazione completata [scaricare il codice](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Aprire Xcode e selezionare **Create a new Xcode project**.
2. Selezionare **iOS > Single view Application** e quindi **Next**.
3. Assegnare un nome prodotto e fare clic su **Next**.
4. Selezionare una cartella per creare l'app e fare clic su *Create* (Crea).

## <a name="register-your-application"></a>Registrare l'applicazione 

È possibile registrare l'applicazione in uno dei due modi descritti nelle due sezioni successive.

### <a name="register-your-app"></a>Registrare l'app

1. Passare al [portale di Azure](https://aka.ms/MobileAppReg) e selezionare `New registration`. 
2. Immettere un nome per l'app in **Nome** e selezionare `Register`. **Non impostare un URI di reindirizzamento in questa fase**. 
3. Nella sezione `Manage` passare a `Authentication` > `Add a platform` > `iOS`
    - Immettere l'ID del bundle del progetto. Se è stato scaricato il codice, l'ID è `com.microsoft.identitysample.MSALiOS`.
4. Selezionare `Configure` e archiviare `MSAL Configuration` per un uso successivo. 

## <a name="add-msal"></a>Aggiungere MSAL

### <a name="get-msal"></a>Ottenere MSAL

#### <a name="cocoapods"></a>CocoaPods

È possibile usare [CocoaPods](https://cocoapods.org/) per installare `MSAL` aggiungendolo al `Podfile` nella destinazione:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

È possibile usare [Carthage](https://github.com/Carthage/Carthage) per installare `MSAL` aggiungendolo al `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manualmente

È anche possibile usare Git Submodule o eseguire il checkout della versione più recente e usarla come framework nell'applicazione.

### <a name="add-your-app-registration"></a>Aggiungere la registrazione per l'app

Aggiungere poi la registrazione dell'app al codice. Aggiungere ***ID client/applicazione*** a `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Configurare gli schemi URL

Registrare `CFBundleURLSchemes` per consentire un callback, in modo che l'utente possa essere reindirizzato all'app dopo l'accesso.

`LSApplicationQueriesSchemes` consente di usare l'app per usare Microsoft Authenticator, se disponibile. 

A tale scopo, aprire `Info.plist` come codice sorgente e aggiungere quanto segue, sostituendo ***BUNDLE_ID*** con l'ID configurato nel portale di Azure.

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

### <a name="import-msal"></a>Importare MSAL

Importare il framework MSAL nei file `ViewController.swift` e `AppDelegate.swift`:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Creare l'interfaccia utente dell'app

Per eseguire questa esercitazione, è necessario creare:

- Un pulsante per chiamare l'API Graph
- Un pulsante per la disconnessione
- Un controllo TextView per la registrazione

In `ViewController.swift` definire le proprietà e `initUI()` come indicato di seguito:

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

Aggiungere quindi il codice seguente in `viewDidLoad()` di ViewController.swift:

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

È prima di tutto necessario creare una `MSALPublicClientApplication` con un'istanza di `MSALPublicClientConfiguration` per l'applicazione:

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

### <a name="handle-the-callback"></a>Gestire il callback

Per gestire il callback dopo l'accesso, aggiungere `MSALPublicClientApplication.handleMSALResponse` in `appDelegate`:

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

MSAL espone due metodi principali per ottenere i token: `acquireTokenSilently` e `acquireTokenInteractively`.  

`acquireTokenSilently()` tenta di completare l'accesso per un utente e di ottenere i token senza alcuna interazione dell'utente se è presente un account.

`acquireTokenInteractively` mostrerà sempre l'interfaccia utente durante il tentativo di accesso dell'utente e di ottenere i token. Tuttavia, potrebbe usare i cookie di sessione nel browser o un account di Microsoft Authenticator per offrire un'esperienza SSO interattiva. 

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

Per acquisire un token per la prima volta, sarà necessario creare un `MSALInteractiveTokenParameters` e chiamare `acquireToken`.

1. Creare `MSALInteractiveTokenParameters` con ambiti.
2. Chiamare `acquireToken` con i parametri creati.
3. Gestire l'errore in modo appropriato. Per altri dettagli, vedere la [guida per la gestione degli errori di iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Gestire il caso di esito positivo. 

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

Per acquisire un token aggiornato in modo invisibile all'utente, sarà necessario creare un `MSALSilentTokenParameters` e chiamare `acquireTokenSilent`:

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

Dopo aver ottenuto un token tramite `self.accessToken`, l'app può usare questo valore nell'intestazione HTTP per effettuare una richiesta autorizzata a Microsoft Graph:

| chiave dell'intestazione    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

Aggiungere quanto segue a `ViewController.swift`:

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

Altre informazioni sull'[API Microsoft Graph](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Usare MSAL per la disconnessione

Verrà ora aggiunto il supporto per la disconnessione dell'app. 

È importante sottolineare che la disconnessione con MSAL rimuove tutte le informazioni note disponibili su un utente da questa applicazione, ma l'utente avrà ancora una sessione attiva nel dispositivo. Se l'utente tenta di accedere nuovamente potrebbero essere richieste interazioni, ma è possibile che non debba immettere nuovamente le credenziali dato che la sessione del dispositivo è attiva. 

Per aggiungere la disconnessione, copiare il metodo seguente in `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
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

Per abilitare la memorizzazione nella cache dei token, passare alle impostazioni del progetto Xcode > `Capabilities tab` > `Enable Keychain Sharing` > fare clic su `Plus` > immettere **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Aggiungere metodi helper

Aggiungere questi metodi helper per completare l'esempio:

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

Questa app è stata sviluppata per uno scenario con un singolo account. MSAL supporta anche scenari con più account, ma richiede alcune operazioni aggiuntive per le app. È necessario creare un'interfaccia utente per consentire agli utenti di selezionare l'account da usare per ogni azione che richiede i token. In alternativa, l'app può implementare un'euristica per selezionare l'account da usare tramite il metodo `getAllAccounts(...)`.

## <a name="test-your-app"></a>Test dell'app

### <a name="run-locally"></a>Esecuzione locale

Se il codice è stato costruito seguendo le indicazioni precedenti, provare a compilare e distribuire l'app in un dispositivo di test o un emulatore. Dovrebbe essere possibile accedere e ottenere i token per account di Azure AD o account Microsoft personali. Dopo l'accesso dell'utente, questa app visualizzerà i dati restituiti dall'endpoint `/me` di Microsoft Graph. 

Se si verificano problemi, è possibile segnalarli a Microsoft aprendo un problema in questo documento o nella libreria MSAL. 

### <a name="consent-to-your-app"></a>Fornire il consenso all'app

Al primo accesso di qualsiasi utente nell'app, verrà richiesto il consenso per le autorizzazioni richieste da Microsoft Identity Platform.  Anche se la maggior parte degli utenti sarà in grado di fornire il consenso, alcuni tenant di Azure AD hanno disabilitato il consenso dell'utente e sarà quindi necessario che gli amministratori forniscano il consenso per conto di tutti gli utenti.  Per supportare questo scenario, assicurarsi di registrare gli ambiti dell'app nel portale di Azure.

## <a name="help-and-support"></a>Guida e supporto

In caso di problemi con questa esercitazione o con Microsoft Identity Platform, vedere le [informazioni di Guida e supporto tecnico](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

