## <a name="test-your-code"></a>Testare il codice

Per eseguire il progetto, in Visual Studio, selezionare **F5**. L'applicazione **MainWindow** viene visualizzato, come illustrato di seguito:

![Testare l'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

La prima volta che si esegue l'applicazione e selezionare il **chiamare API di Microsoft Graph** pulsante, viene chiesto di accedere. Utilizzare un account di Azure Active Directory (account aziendale o dell'istituto di istruzione) o un account Microsoft (live.com, outlook.com) per eseguire il test.

![Accedere all'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Fornire il consenso per l'accesso all'applicazione
La prima volta che si accede all'applicazione, viene inoltre richiesto di fornire il consenso per consentire all'applicazione di accedere al profilo e di eseguire l'accesso in, come illustrato di seguito: 

![Fornire il consenso dell'utente per l'accesso all'applicazione](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, si dovrebbe vedere le informazioni sul profilo utente che viene restituiti dalla chiamata all'API Microsoft Graph. I risultati vengono visualizzati nel **risultati chiamare API** casella. Le informazioni di base relative al token che è stato acquisito tramite la chiamata a `AcquireTokenAsync` o `AcquireTokenSilentAsync` deve essere visibile nel **informazioni sul Token** casella. I risultati contengono le proprietà seguenti:

|Proprietà  |Format  |DESCRIZIONE |
|---------|---------|---------|
|**Nome** |Nome completo dell'utente |L'utente del nome e cognome.|
|**Nome utente** |<span>user@domain.com</span> |Il nome utente utilizzato per identificare l'utente.|
|**Scadenza del token** |Datetime |Ora in cui scade il token. MSAL estende la data di scadenza Rinnovando il token in base alle esigenze.|
|**Token di accesso** |string |La stringa di token che viene inviata su HTTP richiede che richiedono un *intestazione Authorization*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede la *Read* ambito per la lettura di un profilo utente. Questo ambito viene automaticamente aggiunto per impostazione predefinita in ogni applicazione che viene registrato nel portale di registrazione dell'applicazione. Altre API per Microsoft Graph, come le API personalizzate per il server back-end, potrebbe essere necessario gli altri ambiti. L'API di Microsoft Graph richiede la *Calendars.Read* ambito per visualizzare l'elenco di calendari dell'utente.

Per accedere a calendari dell'utente nel contesto di un'applicazione, aggiungere il *Calendars.Read* delega dell'autorizzazione per le informazioni di registrazione dell'applicazione. Aggiungere quindi il *Calendars.Read* ambito per il `acquireTokenSilent` chiamare. 

>[!NOTE]
>L'utente potrebbe essere richieste autorizzazioni aggiuntive quando si aumenta il numero di ambiti.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
