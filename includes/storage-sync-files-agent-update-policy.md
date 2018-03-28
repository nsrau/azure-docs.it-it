L'agente Sincronizzazione file di Azure viene aggiornato a intervalli regolari per aggiungere nuove funzionalità e risolvere eventuali problemi. È consigliabile configurare Microsoft Update per ricevere gli aggiornamenti dell'agente Sincronizzazione file di Azure non appena vengono rilasciati.

#### <a name="major-vs-minor-agent-versions"></a>Confronto tra versioni principali e secondarie dell'agente
* Le versioni principali dell'agente contengono spesso nuove funzionalità e hanno un numero crescente come prima parte del numero di versione. Ad esempio: *2.\*.\**
* Le versioni secondarie dell'agente sono denominate anche "patch" e vengono rilasciate con maggiore frequenza rispetto alle versioni principali. Spesso contengono correzioni di bug e miglioramenti di entità minore, ma non nuove funzionalità. Ad esempio: *\*.3.\**

#### <a name="upgrade-paths"></a>Percorsi di aggiornamento
Esistono tre modi approvati e testati per installare gli aggiornamenti dell'agente Sincronizzazione file di Azure. Questi percorsi di aggiornamento funzionano sia per le versioni principali che per quelle secondarie.
1. **(Consigliato) Configurare Microsoft Update per scaricare e installare automaticamente gli aggiornamenti dell'agente.**  
    È consigliabile scaricare tutti gli aggiornamenti di Sincronizzazione file di Azure per avere accesso alle ultime correzioni per l'agente server. Microsoft Update semplifica questo processo, scaricando e installando gli aggiornamenti automaticamente.
2. **Applicare una patch a un agente Sincronizzazione file di Azure esistente usando un file di patch di Microsoft Update o un eseguibile con estensione msp. L'ultimo aggiornamento di Sincronizzazione file di Azure può essere scaricato da [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Eseguendo un eseguibile con estensione msp, l'installazione di Sincronizzazione file di Azure viene aggiornata con lo stesso metodo usato automaticamente da Microsoft Update nel percorso di aggiornamento precedente. Applicando una patch di Microsoft Update verrà eseguito un aggiornamento sul posto di un'installazione di Sincronizzazione file di Azure.
3. **Scaricare il programma di installazione più recente dell'agente Sincronizzazione file di Azure dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Il download del programma di installazione è un pacchetto di Microsoft Installer o un eseguibile con estensione msi.**  
    Per aggiornare un'installazione esistente dell'agente Sincronizzazione file di Azure, disinstallare la versione precedente e quindi installare l'ultima versione più recente dal programma di installazione scaricato. Il programma di installazione di Sincronizzazione file di Azure mantiene la registrazione del server, i gruppi di sincronizzazione e tutte le altre impostazioni.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanzie relative al ciclo di vita dell'agente e alla gestione del cambiamento
Sincronizzazione file di Azure è un servizio cloud e questo consente l'introduzione continua di nuove caratteristiche e funzionalità. Ciò significa che una specifica versione dell'agente Sincronizzazione file di Azure può essere supportata solo per un periodo di tempo limitato. Per semplificare la distribuzione si applicano le regole seguenti, per garantire che gli utenti abbiano a disposizione notifiche e tempo sufficienti per supportare gli aggiornamenti dell'agente nel processo di gestione delle modifiche:

- Le versioni principali dell'agente sono supportate per almeno sei mesi dalla data di rilascio iniziale.
- Microsoft garantisce che una sovrapposizione di almeno tre mesi tra il supporto delle versioni principali dell'agente. 
- Per i server registrati che usano un agente prossimo alla scadenza vengono generati avvisi almeno tre mesi prima della scadenza. È possibile verificare se un server registrato sta usando una versione precedente dell'agente nella sezione dei server registrati di un servizio di sincronizzazione archiviazione.
- La durata di una versione secondaria dell'agente è legata alla versione principale associata. Ad esempio, quando viene rilasciata la versione 3.0 dell'agente, le versioni 2.\* saranno tutte impostate in modo da scadere insieme.

> [!Note]
> L'installazione di una versione dell'agente con un avviso di scadenza comporterà la visualizzazione di un avviso ma avrà esito positivo. Il tentativo di installare o connettersi a una versione scaduta dell'agente non è supportato e verrà bloccato.