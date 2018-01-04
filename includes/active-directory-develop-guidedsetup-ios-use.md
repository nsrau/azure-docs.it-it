## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare Microsoft Authentication Library (MSAL) per ottenere un token per l'API Microsoft Graph

Aprire `ViewController.swift` e sostituire il codice con:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni
#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo
Se si chiama il metodo `acquireToken`, viene visualizzata una finestra del browser in cui si chiede all'utente di eseguire l'accesso. In genere, le applicazioni chiedono agli utenti di accedere in modo interattivo la prima volta che devono accedere a una risorsa protetta o quando un'operazione invisibile di acquisizione di un token ha esito negativo (ad esempio, perché la password dell'utente è scaduta).

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile
Il metodo `acquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito `acquireToken` la prima volta, per le chiamate successive il metodo comunemente usato per ottenere i token usati per accedere a risorse protette è `acquireTokenSilent`: le chiamate per richiedere o rinnovare token vengono eseguite in modo invisibile per l'utente.

Alla fine, tuttavia, `acquireTokenSilent` avrà esito negativo perché, ad esempio, l'utente si sarà disconnesso o avrà modificato la password in un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MSALErrorCode.interactionRequired`. L'applicazione può gestire questa eccezione in due modi:

1.  Eseguire subito una chiamata a `acquireToken`, in modo da chiedere all'utente di eseguire l'accesso. Questo criterio viene usato in genere nelle applicazioni online in cui non sono disponibili contenuti offline per l'utente. L'applicazione di esempio generata tramite questa installazione guidata usa questo criterio: è possibile vederlo in azione la prima volta che viene eseguita l'applicazione. Poiché nessun utente ha mai usato l'applicazione, `applicationContext.users().first` conterrà un valore null e verrà generata un'eccezione ` MSALErrorCode.interactionRequired `. Il codice dell'esempio gestirà quindi l'eccezione chiamando `acquireToken`, ovvero chiedendo all'utente di eseguire l'eccesso.

2.  Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `acquireTokenSilent` in un secondo momento. Questo metodo viene usato in genere quando l'utente può accedere ad altre funzionalità dell'applicazione senza essere interrotto, ad esempio quando nell'applicazione sono disponibili contenuti offline. In questo caso, l'utente può decidere quando eseguire l'accesso per accedere alla risorsa protetta o per aggiornare informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `acquireTokenSilent` se la rete viene ripristinata dopo essere stata temporaneamente non disponibile.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il nuovo metodo seguente a `ViewController.swift`. Questo metodo consente di eseguire una richiesta `GET` all'API Graph di Microsoft tramite un'*intestazione dell'autorizzazione HTTP*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
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

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
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

1.  Aprire `AppDelegate.swift` e importare MSAL:

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

