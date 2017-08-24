
## <a name="test-your-code"></a>Testare il codice

Se si usa Visual Studio, premere `F5` per eseguire il progetto. Verrà aperto il browser e l'utente verrà indirizzato a *http://localhost:{porta}*, in cui verrà visualizzato il pulsante *Call Microsoft Graph API*.

Se non si usa Visual Studio, verificare che il server Web sia avviato e che la cartella contenente l'applicazione Web JavaScript sia configurata nel server Web. Aprire il browser e digitare *http://localhost:{porta}/percorso*, dove *porta* corrisponde alla porta su cui il server Web è in ascolto e *percorso* è il percorso di index.html.

Fare clic sul pulsante *Call Microsoft Graph API*. Se si tratta della prima volta, verrà visualizzata una finestra popup che chiede all'utente di eseguire l'accesso.
 
![Schermata di esempio](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla figura seguente, in cui è necessario accettare in modo esplicito:

 ![Schermata di consenso](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Poiché si sta eseguendo una query sull'API Microsoft Graph, potrebbe essere visualizzata un'altra pagina di consenso. Questa situazione si verifica a causa del *consenso dinamico*, in cui è necessario un consenso per ogni ambito richiesto dall'applicazione. Per l'applicazione di esempio generata da questa guida, viene fornito l'ambito *user.read* e quindi è necessario dare il consenso perché questa applicazione possa leggere il profilo dell'utente.

> [!IMPORTANT]
> Attualmente, a causa di un problema noto con il codice JavaScript *msal*, è necessario disabilitare il blocco popup nei browser come Chrome e Firefox in modo che la schermata del *consenso dinamico* possa funzionare correttamente. La disabilitazione del blocco popup è necessaria la prima volta che un utente chiama l'API Microsoft Graph usando `acquireTokenPopup`.

### <a name="expected-results"></a>Risultati previsti
Si dovrebbero visualizzare le informazioni del profilo utente restituite dalla risposta alla chiamata all'API Microsoft Graph.
 
 ![Risultati](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Verranno anche visualizzate le informazioni di base sul token acquisite nelle caselle *Access Token* e *ID Token Claims*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API di Microsoft Graph richiede l'ambito `user.read` per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione. Altre API per Microsoft Graph e alcune API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, per Microsoft Graph, l'ambito `Calendars.Read` è necessario per elencare i calendari dell'utente. Per poter accedere al calendario dell'utente nel contesto di un'applicazione, è necessario aggiungere l'autorizzazione delegata `Calendars.Read` alle informazioni di registrazione dell'applicazione e quindi aggiungere l'ambito `Calendars.Read` alla chiamata `acquireTokenSilent`. Con l'aumentare del numero di ambiti è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

Se per un'API back-end non è richiesto alcun ambito (non consigliabile), è possibile usare `clientId` come ambito nelle chiamate `acquireTokenSilent` e/o `acquireTokenPopup`.

<!--end-collapse-->
