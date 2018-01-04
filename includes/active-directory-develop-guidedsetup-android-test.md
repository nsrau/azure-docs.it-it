## <a name="test-your-code"></a>Testare il codice

1. Distribuire il codice nel dispositivo/emulatore.
2. Quando si è pronti per testare l'applicazione, utilizzare un account di Microsoft Azure Active Directory (account aziendale o dell'istituto di istruzione) o un account Microsoft (live.com, outlook.com) per eseguire l'accesso. 

    ![Testare l'applicazione](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Immettere nome utente e password](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Fornire il consenso per l'accesso all'applicazione
La prima volta che si accede all'applicazione, viene chiesto di fornire il consenso dell'utente per consentire all'applicazione per accedere al profilo e per eseguire l'accesso: 

![Fornire il consenso dell'utente per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo che l'accesso, si otterranno i risultati restituiti dalla chiamata all'API Microsoft Graph. La chiamata all'API Microsoft Graph **me** endpoint restituisce il https://graph.microsoft.com/v1.0/me profilo utente. Per un elenco di endpoint di Microsoft Graph comuni, vedere [la documentazione per sviluppatori di Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede la **Read** ambito per la lettura di un profilo utente. Questo ambito viene automaticamente aggiunto per impostazione predefinita in ogni applicazione che viene registrato nel portale di registrazione. Altre API per Microsoft Graph, come le API personalizzate per il server back-end, potrebbe essere necessario gli altri ambiti. L'API di Microsoft Graph richiede la **Calendars.Read** ambito per visualizzare l'elenco di calendari dell'utente.

Per accedere a calendari dell'utente nel contesto di un'applicazione, aggiungere il **Calendars.Read** delega dell'autorizzazione per le informazioni di registrazione dell'applicazione. Aggiungere quindi il **Calendars.Read** ambito per il **acquireTokenSilent** chiamare. 

>[!NOTE]
>L'utente potrebbe essere richieste autorizzazioni aggiuntive quando si aumenta il numero di ambiti.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
