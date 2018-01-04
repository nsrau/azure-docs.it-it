## <a name="test-your-code"></a>Testare il codice

### <a name="test-with-visual-studio"></a>Eseguire il test con Visual Studio
Se si utilizza Visual Studio, premere **F5** per eseguire il progetto. Il browser apre l'http://<span></span>localhost: percorso {porta} e visualizzato il **chiamare API di Microsoft Graph** pulsante.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Test con Python o un altro server web
Se non si utilizza Visual Studio, verificare che il server web sia avviato. Configurare il server per una porta TCP è in base alla posizione di ascolto del **index.html** file. Per Python, iniziare a in ascolto sulla porta eseguendo il prompt dei comandi terminal dalla cartella dell'applicazione.
 
```bash
python -m http.server 8080
```
Aprire il browser e digitare http://<span></span>localhost: 8080 o http://<span></span>localhost: {porta} dove **porta** è la porta è in ascolto il server web. Verrà visualizzato il contenuto del file index.html e **chiamare API di Microsoft Graph** pulsante.

## <a name="test-your-application"></a>Testare l'applicazione

Se il browser viene caricato il file index.html, fare clic su **chiamare API di Microsoft Graph**. La prima volta che si esegue l'applicazione, il browser si viene reindirizzati all'endpoint v 2.0 di Microsoft Azure Active Directory (Azure AD) e viene chiesto di accedere:
 
![Accedi al tuo account SPA JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Fornire il consenso per l'accesso all'applicazione

La prima volta che si accede all'applicazione, viene chiesto di fornire il consenso dell'utente per consentire all'applicazione per accedere al profilo e per eseguire l'accesso:

![Fornire il consenso dell'utente per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, si dovrebbero vedere le informazioni del profilo utente nel **risposta chiamata dell'API Graph** casella.
 
![Risultati previsti dalla chiamata all'API di Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Verrà visualizzato anche il token che è stato acquisito in informazioni di base di **Token di accesso** e **le attestazioni nei Token ID** caselle.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede la **Read** ambito per la lettura di un profilo utente. Questo ambito viene automaticamente aggiunto per impostazione predefinita in ogni applicazione che viene registrato nel portale di registrazione. Altre API per Microsoft Graph, come le API personalizzate per il server back-end, potrebbe essere necessario gli altri ambiti. L'API di Microsoft Graph richiede la **Calendars.Read** ambito per visualizzare l'elenco di calendari dell'utente.

Per accedere a calendari dell'utente nel contesto di un'applicazione, aggiungere il **Calendars.Read** delega dell'autorizzazione per le informazioni di registrazione dell'applicazione. Aggiungere quindi il **Calendars.Read** ambito per il **acquireTokenSilent** chiamare. 

>[!NOTE]
>L'utente potrebbe essere richieste autorizzazioni aggiuntive quando si aumenta il numero di ambiti.

Se un'API back-end non richieda un ambito (non consigliato), è possibile utilizzare il **clientId** dell'ambito nel **acquireTokenSilent** e **acquireTokenRedirect** chiamate.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
