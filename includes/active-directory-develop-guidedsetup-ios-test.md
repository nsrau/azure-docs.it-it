## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Verificare l'esecuzione di query di API Graph di Microsoft dall'applicazione iOS

Per eseguire il codice nel simulatore, premere **comando** + **R**.

![Testare l'applicazione nel simulatore](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Quando si è pronti per eseguire il test, selezionare **chiamare API di Microsoft Graph**. Quando richiesto, immettere il nome utente e password.

### <a name="provide-consent-for-application-access"></a>Fornire il consenso per l'accesso all'applicazione
La prima volta che si accede all'applicazione, viene chiesto di fornire il consenso dell'utente per consentire all'applicazione per accedere al profilo e per eseguire l'accesso:

![Fornire il consenso dell'utente per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, si dovrebbero vedere le informazioni del profilo utente restituite dalla chiamata API di Microsoft Graph nel **registrazione** sezione. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede la **Read** ambito per la lettura di un profilo utente. Questo ambito viene automaticamente aggiunto per impostazione predefinita in ogni applicazione che viene registrato nel portale di registrazione. Altre API per Microsoft Graph, come le API personalizzate per il server back-end, potrebbe essere necessario gli altri ambiti. L'API di Microsoft Graph richiede la **Calendars.Read** ambito per visualizzare l'elenco di calendari dell'utente.

Per accedere a calendari dell'utente nel contesto di un'applicazione, aggiungere il **Calendars.Read** delega dell'autorizzazione per le informazioni di registrazione dell'applicazione. Aggiungere quindi il **Calendars.Read** ambito per il **acquireTokenSilent** chiamare. 

>[!NOTE]
>L'utente potrebbe essere richieste autorizzazioni aggiuntive quando si aumenta il numero di ambiti.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
