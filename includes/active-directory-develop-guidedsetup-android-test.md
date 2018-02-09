## <a name="test-your-code"></a>Testare il codice

1. Distribuire il codice nel dispositivo/emulatore.

2. Quando si è pronti per testare l'applicazione, usare un account Azure Active Directory (aziendale o dell'istituto di istruzione) o un account Microsoft (ad esempio live.com o outlook.com) per eseguire l'accesso. 

    ![Testare l'applicazione](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Immettere nome utente e password](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione
Al primo accesso all'applicazione viene richiesto anche di specificare il consenso per permettere all'applicazione di accedere al profilo e di completare l'accesso per l'utente, come mostrato di seguito: 

![Specificare il consenso per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso dovrebbero essere visualizzati i risultati restituiti dalla chiamata all'API Microsoft Graph. La chiamata all'endpoint **me** dell'API Microsoft Graph restituisce il [profilo utente](https://graph.microsoft.com/v1.0/me). Per un elenco di endpoint di Microsoft Graph comuni, vedere la [documentazione per sviluppatori dell'API Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione dell'applicazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito *Calendars.Read* per elencare i calendari dell'utente. 

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`. 

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
