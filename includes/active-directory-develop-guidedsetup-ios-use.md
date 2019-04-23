---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: b7883de410a1fd281a154a792dd45132c08f0c03
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804312"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare Microsoft Authentication Library (MSAL) per ottenere un token per l'API Microsoft Graph

Aprire `ViewController.swift` e sostituire il codice con:

```swift
import UIKit
import MSAL

// A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {

    // Replace Your_Application_Id_Here with the client ID you received in the portal. The below is for running the demo only.
    let kClientID = "Your_Application_Id_Here"

    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"

    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }

    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
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

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo

Se si chiama il metodo `acquireToken`, viene visualizzata una finestra del browser in cui si chiede all'utente di eseguire l'accesso. In genere, le applicazioni chiedono agli utenti di accedere in modo interattivo la prima volta che devono accedere a una risorsa protetta o quando un'operazione invisibile di acquisizione di un token ha esito negativo (ad esempio, perché la password dell'utente è scaduta).

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile

Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito `acquireToken` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`: le chiamate per richiedere o rinnovare token vengono eseguite in modo invisibile per l'utente.

Alla fine, tuttavia, `acquireTokenSilent` avrà esito negativo perché, ad esempio, l'utente si sarà disconnesso o avrà modificato la password in un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MSALErrorCode.interactionRequired`. L'applicazione può gestire questa eccezione in due modi:

1. Eseguire subito una chiamata a `acquireToken`, in modo da chiedere all'utente di eseguire l'accesso. Questo criterio viene usato in genere nelle applicazioni online in cui non sono disponibili contenuti offline per l'utente. L'applicazione di esempio generata tramite questa installazione guidata usa questo criterio: è possibile vederlo in azione la prima volta che viene eseguita l'applicazione. Poiché nessun utente ha mai usato l'applicazione, `applicationContext.allAccounts().first` conterrà un valore null e verrà generata un'eccezione `MSALErrorCode.interactionRequired`. Il codice dell'esempio gestirà quindi l'eccezione chiamando `acquireToken`, ovvero chiedendo all'utente di eseguire l'eccesso.

2. Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questo metodo viene usato in genere quando l'utente può accedere ad altre funzionalità dell'applicazione senza essere interrotto, ad esempio quando nell'applicazione sono disponibili contenuti offline. In questo caso, l'utente può decidere quando eseguire l'accesso per accedere alla risorsa protetta o per aggiornare informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `acquireTokenSilent` se la rete viene ripristinata dopo essere stata temporaneamente non disponibile.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il nuovo metodo seguente a `ViewController.swift`. Questo metodo consente di eseguire una richiesta `GET` all'API Graph di Microsoft tramite un'*intestazione dell'autorizzazione HTTP*:

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

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Esecuzione di una chiamata REST a un'API protetta

In questa applicazione di esempio, viene usato il metodo `getContentWithToken()` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per questo esempio, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Configurare la disconnessione

Aggiungere il metodo seguente al file `ViewController.swift` per disconnettere l'utente:

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```

<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Altre informazioni sulla disconnessione

Il metodo `signoutButton` sopra riportato rimuove l'utente dalla cache utente di MSAL: in questo modo, MSAL dimenticherà l'utente corrente e un'eventuale richiesta futura di acquisizione di un token riuscirà solo se eseguita in modo interattivo.

Anche se l'applicazione in questo esempio supporta un unico utente, MSAL supporta anche scenari in cui è possibile eseguire contemporaneamente l'accesso di più account, come nel caso di un'applicazione di posta elettronica in cui un utente dispone di più account.
<!--end-collapse-->

## <a name="register-the-callback"></a>Registrare il callback

Dopo l'autenticazione dell'utente, il browser reindirizza di nuovo l'utente all'applicazione. Eseguire la procedura descritta di seguito per registrare questo callback:

1. Aprire `AppDelegate.swift` e importare MSAL:

```swift
import MSAL
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Aggiungere il metodo seguente alla classe <code>AppDelegate</code> per gestire i callback:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
