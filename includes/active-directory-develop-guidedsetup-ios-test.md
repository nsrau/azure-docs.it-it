## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Verificare l'esecuzione di query di API Graph di Microsoft dall'applicazione iOS

Per eseguire il codice nel simulatore, premere **Comando** + **R**.

![Eseguire test dell'applicazione nel simulatore](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Quando si è pronti per eseguire test, selezionare **Call Microsoft Graph API** (Chiama API Microsoft Graph). Quando richiesto, immettere nome utente e password.

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione
Al primo accesso all'applicazione viene richiesto di specificare il consenso per permettere all'applicazione di accedere al profilo e di completare l'accesso per l'utente:

![Specificare il consenso per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, le informazioni del profilo utente restituite dalla chiamata all'API Microsoft Graph dovrebbero essere visualizzate nella sezione **Registrazione**. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito **user.read** per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito **Calendars.Read** per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata **Calendars.Read** alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito **Calendars.Read** alla chiamata **acquireTokenSilent**. 

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
