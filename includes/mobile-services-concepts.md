## <a name="what-is"></a>Informazioni su Servizi mobili

Servizi Mobili di Azure è un'offerta studiata per semplificare la
creazione di app per dispositivi mobili complete di tutte le
funzionalità utilizzando Azure. Si tratta di una combinazione di servizi
di Azure che rendono disponibili funzionalità back-end per supportare le
app, elencate di seguito:

* Procedure semplificate di provisioning e gestione delle tabelle per
  l'archiviazione dei dati dell'app.
* Integrazione con servizi di notifica per recapitare notifiche push
  all'app.
* Integrazione con provider di identità riconosciuti per
  l'autenticazione.
* Controllo granulare per le autorizzazioni di accesso alle tabelle.
* Logica di business personalizzata nel server.
* Integrazione con altri servizi cloud.
* Supporto della scalabilità di un'istanza di servizio mobile.
* Monitoraggio e registrazione dei servizi.

Servizi mobili fornisce una libreria client per ogni piattaforma
supportata, semplificando ulteriormente lo sviluppo di app nelle
piattaforme in cui viene utilizzato.
## <a name="concepts"> </a>Concetti

Di seguito sono riportati i concetti e le funzionalità importanti di
Servizi mobili:

<!--![1][1]-->

* **Chiave applicazione:** valore univoco generato da Servizi mobili,
  distribuito con l'app e presentato nelle richieste generate dai
  client. Anche se risulta utile per limitare l'accesso al servizio
  mobile da parte di client casuali, la chiave non è sicura e non deve
  essere utilizzata per l'autenticazione degli utenti dell'app.

* **Token di autenticazione:** token di accesso generato da Servizi
  mobili dopo l'autenticazione di un utente.

* **Schema dinamico:** consente di aggiungere automaticamente una
  colonna a una tabella in base ai campi dell'oggetto JSON inviato in
  una richiesta di inserimento o aggiornamento. Lo schema dinamico deve
  essere disabilitato quando l'app entra in produzione.

* **Provider di identità:** servizio esterno, come Facebook, Twitter,
  Google o account Microsoft, utilizzato per l'autenticazione degli
  utenti del servizio mobile.

* **Autorizzazione:** livello di autenticazione minimo necessario per
  richiamare un'operazione su tabella.

* **Notifica push:** messaggio avviato dal servizio e inviato a un
  dispositivo o un utente registrato.

* **Scalabilità:** possibilità di aggiungere, a un costo aggiuntivo,
  potenza di elaborazione, prestazioni e spazio di archiviazione quando
  l'app acquisisce popolarità.

* **Processo pianificato:** codice di script del server eseguito in base
  a una pianificazione predeterminata o su richiesta.

* **Script del server:** codice JavaScript per la logica di business
  personalizzata archiviata nel server. Il codice è registrato in
  un'operazione su tabella (lettura, inserimento, aggiornamento,
  eliminazione) o in un processo pianificato.

* **Tabella:** i dati utente vengono archiviati in tabelle nei database
  SQL. È possibile creare queste tabelle nel portale di gestione.

* **Operazione su tabella:** richiesta HTTP ricevuta dal client per la
  lettura, l'inserimento, l'aggiornamento o l'eliminazione di dati di
  tabella.

<!-- Images. -->

