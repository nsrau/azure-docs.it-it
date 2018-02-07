## <a name="test-your-code"></a>Testare il codice

Per eseguire test dell'applicazione in Visual Studio, premere **F5** per eseguire il progetto. Il browser viene aperto sulla posizione http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Accedi con Microsoft**. Selezionare il pulsante per avviare il processo di accesso.

Quando si è pronti per eseguire il test, usare un account Microsoft Azure Active Directory (Azure AD) (aziendale o dell'istituto di istruzione) o un account Microsoft personale (<span>live.</span>com oppure <span>outlook.</span>com) per accedere.

![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Accedere all'account Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web. La home page è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione delle applicazioni Microsoft. La home page include un messaggio di benvenuto di tipo "Hello \<Utente>", un collegamento per la disconnessione e un collegamento per la visualizzazione delle attestazioni dell'utente. Il collegamento per le attestazioni dell'utente porta al controller **Authorize** creato in precedenza.

### <a name="browse-to-see-the-users-claims"></a>Esplorare per visualizzare le attestazioni dell'utente
Per visualizzare le attestazioni dell'utente, selezionare il collegamento per passare alla visualizzazione dei controller disponibile solo per gli utenti autenticati.

#### <a name="view-the-claims-results"></a>Visualizzare i risultati delle attestazioni
Dopo il passaggio alla visualizzazione dei controller, dovrebbe essere visualizzata una tabella contenente le proprietà di base per l'utente:

|Proprietà |Valore |DESCRIZIONE |
|---|---|---|
|**Nome** |Nome e cognome dell'utente | Nome e cognome dell'utente.
|**Nome utente** |utente<span>@domain.com</span> | Nome utente usato per identificare l'utente.
|**Oggetto** |Oggetto |Stringa che identifica in modo univoco l'utente sul Web.|
|**ID tenant** |Guid | **GUID** che rappresenta in modo univoco l'organizzazione di Azure AD dell'utente.|

Dovrebbe essere visualizzata anche una tabella di tutte le attestazioni disponibili nella richiesta di autenticazione. Per altre informazioni, vedere l'[elenco di attestazioni disponibili in un token ID di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Eseguire test dell'accesso a un metodo con attributo Authorize (facoltativo)
Per eseguire test dell'accesso al controller **Authorize** per le attestazioni dell'utente come utente anonimo, seguire questa procedura:
1. Selezionare il collegamento per la disconnessione dell'utente e completare il processo di disconnessione.
2. Nel browser digitare http://<span></span>localhost:{porta}/authenticated per accedere al controller protetto con l'attributo **Authorize**.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Risultati previsti dopo l'accesso a un controller protetto
Viene richiesta l'autenticazione per usare la visualizzazione protetta dei controller.

## <a name="additional-information"></a>Informazioni aggiuntive

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, nel file **Global.asax** aggiungere l'attributo **AuthorizeAttribute** al filtro **GlobalFilters** nel metodo **Application_Start**:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Limitare l'accesso all'applicazione
Per impostazione predefinita, gli account personali come outlook.com, live.com e di altro tipo possono accedere all'applicazione. Anche gli account aziendali o dell'istituto di istruzione delle organizzazioni integrate con Azure AD possono accedere per impostazione predefinita.

Per limitare l'accesso degli utenti all'applicazione, sono disponibili alcune opzioni.

#### <a name="restrict-access-to-a-single-organization"></a>Limitare l'accesso a una singola organizzazione
È possibile limitare l'accesso all'applicazione ai soli account utente inclusi in una singola organizzazione di Azure AD:
1. Nel file **web.config** cambiare il valore per il parametro **Tenant**. Cambiare il valore da **Common** al nome del tenant dell'organizzazione, ad esempio **contoso.onmicrosoft.com**.
2. Nella classe **OWIN Startup** impostare l'argomento **ValidateIssuer** su **true**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Limitare l'accesso a un elenco di organizzazioni
È possibile limitare l'accesso ai soli account utente inclusi in un'organizzazione di Azure AD che fa parte dell'elenco di organizzazioni consentite:
1. Nel file **web.config** nella classe **OWIN Startup** impostare l'argomento **ValidateIssuer** su **true**.
2. Impostare il valore del parametro **ValidIssuers** sull'elenco di organizzazioni consentite.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Usare un metodo personalizzato per convalidare le autorità di certificazione
È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro **IssuerValidator**. Per altre informazioni su come usare questo parametro, vedere [TokenValidationParameters class](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) (Classe TokenValidationParameters) su MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
