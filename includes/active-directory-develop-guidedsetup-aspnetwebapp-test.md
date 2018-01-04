## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione in Visual Studio, premere **F5** per eseguire il progetto. Il browser apre l'http://<span></span>localhost: percorso {porta} e visualizzato il **accedere con Microsoft** pulsante. Selezionare il pulsante per avviare il processo di accesso.

Quando si è pronti per eseguire il test, utilizzare un account di Microsoft Azure Active Directory (Azure AD) (account aziendale o dell'istituto di istruzione) o un account Microsoft personale (<span>in tempo reale.</span> COM o <span>outlook.</span> COM) per accedere.

![Accedere con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Accedi al tuo account Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web. La pagina home è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione dell'applicazione di Microsoft. La home page include un messaggio di benvenuto "Hello \<utente >," un collegamento di disconnessione e un collegamento per visualizzare le attestazioni dell'utente. Il collegamento per le attestazioni dell'utente cerca di **Authorize** controller creato in precedenza.

### <a name="browse-to-see-the-users-claims"></a>Sfoglia per visualizzare le attestazioni dell'utente
Per visualizzare le attestazioni dell'utente, selezionare il collegamento per passare alla visualizzazione controller che è disponibile solo per gli utenti autenticati.

#### <a name="view-the-claims-results"></a>Visualizzare i risultati di attestazioni
Quando si Sfoglia per la visualizzazione del controller, dovrebbe essere una tabella che contiene le proprietà di base per l'utente:

|Proprietà |Valore |DESCRIZIONE |
|---|---|---|
|**Nome** |Nome completo dell'utente | L'utente del nome e cognome.
|**Nome utente** |utente<span>@domain.com</span> | Il nome utente utilizzato per identificare l'utente.
|**Oggetto** |Oggetto |Stringa che identifica in modo univoco l'utente attraverso il web.|
|**ID tenant** |Guid | Oggetto **guid** che rappresenta in modo univoco l'organizzazione dell'utente AD Azure.|

Inoltre, si dovrebbe essere una tabella di tutte le attestazioni presenti nella richiesta di autenticazione. Per ulteriori informazioni, vedere il [elenco delle attestazioni in un Token di Azure AD ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Verificare l'accesso a un metodo che presenta un attributo Authorize (facoltativo)
Per verificare l'accesso per il **Authorize** controller per le attestazioni dell'utente come utente anonimo, seguire questi passaggi:
1. Selezionare il collegamento per effettuare la disconnessione dell'utente e completare il processo di disconnessione.
2. Nel browser, digitare http://<span></span>localhost: {porta} / autenticati di accedere il controller che è protetto con il **Authorize** attributo.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Risultati previsti dopo l'accesso a un controller protetto
Viene chiesto di eseguire l'autenticazione per utilizzare la vista del controller protetto.

## <a name="additional-information"></a>Informazioni aggiuntive

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, il **Global. asax** file, aggiungere il **AuthorizeAttribute** attributo la **GlobalFilters** filtrare nel **applicazione Avvia** metodo:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Limitare l'accesso di accesso all'applicazione
Per impostazione predefinita, account personali come outlook.com, live.com e ad altri utenti possono accedere all'applicazione. Gli account aziendali o dell'istituto di istruzione nelle organizzazioni in cui sono integrate con Azure AD possono anche accedere per impostazione predefinita.

Per limitare l'accesso accesso utente per l'applicazione, sono disponibili diverse opzioni.

#### <a name="restrict-access-to-a-single-organization"></a>Limitare l'accesso a una singola organizzazione
È possibile limitare l'accesso di accesso per l'applicazione solo agli account utente in una singola organizzazione di Azure AD:
1. Nel **Web. config** file, modificare il valore per il **Tenant** parametro. Modificare il valore da **comuni** per il nome del tenant dell'organizzazione, ad esempio **contoso.onmicrosoft.com**.
2. Nel **di avvio di OWIN** , impostare il **ValidateIssuer** argomento **true**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Limitare l'accesso a un elenco di organizzazioni
È possibile limitare l'accesso di accesso per gli account utente solo in un'organizzazione di Azure AD che è presente nell'elenco di organizzazioni consentite:
1. Nel **Web. config** nel file il **di avvio di OWIN** , impostare il **ValidateIssuer** argomento **true**.
2. Impostare il valore della **ValidIssuers** parametro all'elenco di organizzazioni consentite.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Utilizzare un metodo personalizzato per convalidare l'autorità emittenti
È possibile implementare un metodo personalizzato per convalidare gli emittenti utilizzando il **IssuerValidator** parametro. Per ulteriori informazioni su come usare questo parametro, informazioni di [TokenValidationParameters classe](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) su MSDN.

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
