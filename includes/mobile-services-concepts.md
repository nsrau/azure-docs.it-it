## <a name="what-is"></a>Informazioni su Servizi mobili

Servizi mobili di Azure è una piattaforma di sviluppo per applicazioni mobili altamente scalabile che permette di aggiungere funzionalità avanzate alle app per dispositivi mobili mediante Azure.

Con Servizi mobili è possibile:

+ **Compilare app native e multipiattaforma**: connettere le app per iOS, Android, Windows o app multipiattaforma Xamarin o Cordova (Phonegap) al servizio mobile back-end usando SDK nativi.  
+ **Inviare notifiche push agli utenti**: inviare notifiche push agli utenti dell'app.
+ **Autenticare gli utenti**: sfruttare provider di identità noti come Facebook e Twitter per autenticare gli utenti dell'app.
+ **Archiviare dati nel cloud**: archiviare i dati degli utenti in un database SQL (per impostazione predefinita) o in Mongo DB, DocumentDB, tabelle di Azure o BLOB di Azure. 
+ **Creare app offline con sincronizzazione**: rendere le app disponibili all'uso offline e usare Servizi mobili per sincronizzare i dati in background.
+ **Monitorare e ridimensionare le app**: monitorare l'utilizzo delle app e ridimensionare il back-end secondo l'aumento della domanda. 

## <a name="concepts"> </a>Concetti relativi a Servizi mobili

Di seguito sono illustrati i concetti e le funzionalità più importanti di Servizi mobili:

+ **Chiave applicazione:** valore univoco usato per limitare l'accesso al servizio mobile da parte di client casuali. Questa "chiave" non è un token di sicurezza e non viene usata per l'autenticazione degli utenti dell'app.    
+ **Back-end:** istanza del servizio mobile che supporta l'app. Un servizio mobile viene implementato come un progetto (*back-end .NET* ) o come progetto Node.js (*back-end JavaScript*).
+ **Provider di identità:** servizio esterno, considerato attendibile da Servizi mobili, che autentica gli utenti dell'app. I provider supportati includono: Facebook, Twitter, Google, account Microsoft e Azure Active Directory. 
+ **Notifica push:** messaggio avviato dal servizio e inviato a un dispositivo o un utente registrato tramite Hub di notifica di Azure.
+ **Scala:** possibilità di aggiungere, a un costo aggiuntivo, potenza di elaborazione, prestazioni e spazio di archiviazione quando l'app acquisisce popolarità.
+ **Processo pianificato:** codice personalizzato eseguito in base a una pianificazione predeterminata o su richiesta.

<!---HONumber=July15_HO2-->