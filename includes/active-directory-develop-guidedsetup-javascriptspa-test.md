## <a name="test-your-code"></a>Testare il codice

### <a name="test-with-visual-studio"></a>Eseguire test con Visual Studio
Se si usa Visual Studio, premere **F5** per eseguire il progetto. Il browser viene aperto sulla posizione http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Call Microsoft Graph API** (Chiama API Microsoft Graph).

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Eseguire test con Python o un altro server Web
Se non si usa Visual Studio, assicurarsi che il server Web sia stato avviato. Configurare il server per rimanere in ascolto di una porta TCP basata sulla posizione del file **index.html**. Per Python, avviare l'ascolto della porta eseguendo il terminale del prompt dei comandi dalla cartella dell'applicazione:
 
```bash
python -m http.server 8080
```
Aprire il browser e digitare http://<span></span>localhost:8080 o http://<span></span>localhost:{porta} dove **porta** è la porta che il server Web sta ascoltando. Dovrebbero essere visualizzati i contenuti del file index.html e il pulsante **Call Microsoft Graph API** (Chiama API Microsoft Graph).

## <a name="test-your-application"></a>Testare l'applicazione

Dopo che il browser ha caricato il file index.html, selezionare **Call Microsoft Graph API** (Chiama API Microsoft Graph). Alla prima esecuzione dell'applicazione il browser reindirizza l'utente all'endpoint di Microsoft Azure Active Directory (Azure AD) v2.0 e viene richiesto l'accesso:
 
![Accedere all'account JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Specificare il consenso per l'accesso all'applicazione

Al primo accesso all'applicazione viene richiesto di specificare il consenso per permettere all'applicazione di accedere al profilo e di completare l'accesso per l'utente:

![Specificare il consenso per l'accesso all'applicazione](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione
Dopo l'accesso, nella casella **Graph API Call Response** (Risposta alla chiamata API Graph) dovrebbero essere visualizzate le informazioni sul profilo utente.
 
![Risultati previsti dalla chiamata API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Dovrebbero essere anche visualizzate le informazioni di base sul token acquisite nelle caselle **Token di accesso** e **ID Token Claims** (Attestazione token ID).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito **user.read** per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito **Calendars.Read** per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata **Calendars.Read** alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito **Calendars.Read** alla chiamata **acquireTokenSilent**. 

>[!NOTE]
>Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se un'API back-end non richiede alcun ambito (opzione non consigliata), è possibile usare **clientId** come ambito nelle chiamate **acquireTokenSilent** e **acquireTokenRedirect**.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
