# Procedure consigliate per la sicurezza di Internet of Things

Proteggere un'infrastruttura Internet of Things (IoT) richiede una strategia di sicurezza rigorosa e approfondita. Dalla protezione dei dati nel cloud, alla messa in sicurezza dell'integrità dei dati in transito sulla rete Internet pubblica e alla capacità di eseguire in modo sicuro il provisioning dei dispositivi, ogni strato aumenta il livello di sicurezza dell'intera infrastruttura.

## Protezione di un'infrastruttura IoT
 
Questa strategia di sicurezza può essere sviluppata e implementata con la partecipazione attiva dei vari attori coinvolti nella produzione, nello sviluppo e nella distribuzione di dispositivi e infrastrutture IoT. Di seguito è riportata una descrizione dettagliata degli attori.

- **Produttore/integratore di hardware IoT**: in genere si tratta dei produttori dell'hardware IoT da distribuire, integratori di hardware che si occupano dell'assemblaggio di hardware da produttori diversi, oppure di fornitori hardware per la distribuzione IoT prodotta o integrata da altri fornitori.
- **Sviluppatore di soluzioni IoT**: lo sviluppo di una soluzione IoT viene in genere effettuato da uno sviluppatore di soluzioni che potrebbe far parte di un team interno oppure da un integratore di sistema (SI) specializzato in questa attività. Lo sviluppatore di soluzioni IoT può sviluppare vari componenti della soluzione IoT partendo da zero, integrare vari componenti predefiniti o open source, oppure adottare soluzioni preconfigurate che necessitano di un adattamento minore.
- **Distributore di soluzioni IoT**: una volta sviluppata, la soluzione IoT deve essere distribuita nell'ambiente. Ciò implica la distribuzione dell'hardware, l'interconnessione dei dispositivi e la distribuzione di soluzioni su dispositivi hardware o nel cloud.
- **Operatore di soluzioni IoT**: una volta distribuita, la soluzione IoT richiede operazioni, monitoraggio, aggiornamenti e manutenzione a lungo termine. Questa operazione può essere eseguita da un team interno che comprende esperti di tecnologie informatiche, team per le operazioni hardware e team di manutenzione, nonché specialisti di dominio che monitorano il corretto funzionamento dell'intera infrastruttura IoT. 

Le sezioni che seguono descrivono le procedure consigliate per ognuno di questi attori al fine di sviluppare, distribuire e gestire un'infrastruttura IoT protetta.

## Produttore/integratore di hardware IoT

Si invitano i produttori e gli integratori di hardware IoT a seguire le procedure consigliate riportate più avanti:

- **Impostare l'hardware sui requisiti minimi**: la progettazione dell'hardware deve includere esclusivamente le funzionalità minime necessarie per il suo funzionamento. Ad esempio, è possibile includere porte USB solo se necessario al funzionamento del dispositivo. Queste funzionalità aggiuntive espongono il dispositivo a vettori di attacchi indesiderati, che sarebbe opportuno evitare. 
- **Realizzare hardware a prova di manomissione**: un meccanismo integrato per il rilevamento di manomissioni fisiche dell'hardware, come ad esempio l'apertura del coperchio del dispositivo, la rimozione di una parte del dispositivo e così via. Questi segnali di manomissione possono essere inseriti nel flusso dei dati caricati nel cloud, consentendo in tal modo la segnalazione di questi eventi agli operatori. 
- **Creare un hardware sicuro**: se il costo del venduto lo consente, creare funzionalità di sicurezza, come ad esempio l'archiviazione protetta e crittografata e la funzionalità di avvio basata su Trusted Platform Module (TPM). Queste funzionalità rendono i dispositivi più sicuri, proteggendo l'intera infrastruttura IoT.
- **Implementare aggiornamenti sicuri**: l'aggiornamento del firmware durante il ciclo di vita del dispositivo è inevitabile. La creazione di dispositivi con percorsi protetti per gli aggiornamenti e crittografia protetta della versione del firmware consentirà al dispositivo di rimanere protetto durante e dopo gli aggiornamenti.

## Sviluppatore di soluzioni IoT

Si invitano gli sviluppatori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

- **Applicare una metodologia di sviluppo software protetta**: lo sviluppo di software protetti richiede una riflessione totale riguardo alla sicurezza dall'inizio del progetto fino all'implementazione, al testing e alla distribuzione. La scelta di piattaforme, linguaggi e strumenti è influenzata dalla metodologia. Microsoft Security Development Lifecycle fornisce un approccio dettagliato per la creazione di software protetti.
- **Scegliere software open source con attenzione**: il software open source consente di sviluppare soluzioni in modo rapido. Quando si sceglie il software open source, valutare il livello di attività della community per ogni componente open source. Una community attiva garantisce supporto per il software: sarà possibile individuare e risolvere i problemi. Al contrario, un software open source incomprensibile e inattivo non sarà supportato e i problemi molto probabilmente non verranno individuati.
- **Eseguire l'integrazione con attenzione**: molti dei problemi di sicurezza del software intervengono al confine tra librerie e API. Le funzionalità non necessarie per la distribuzione in corso potrebbero essere ancora disponibili tramite un livello di API. Assicurarsi che tutte le interfacce dei componenti integrati siano protette garantisce la sicurezza dell'intero sistema.      

## Distributore di soluzioni IoT

Si invitano i distributori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

- **Distribuire l'hardware in modo sicuro**: le distribuzioni IoT potrebbero determinare il posizionamento non protetto dell'hardware, ad esempio in spazi pubblici o in posizioni non controllate. In questi casi, assicurarsi che la distribuzione dell'hardware garantisca la massima protezione dalle manomissioni. Se l'hardware dispone di porte USB o di altro tipo, assicurarsi che questi elementi siano protetti. Molti vettori di attacco possono usarle come punto di ingresso.
- **Proteggere le chiavi di autenticazione**: durante la distribuzione, ogni dispositivo richiede gli ID dei dispositivi e le chiavi di autenticazione associate generate dal servizio cloud. Conservare fisicamente queste chiavi al sicuro anche dopo la distribuzione. Qualsiasi chiave compromessa può essere usata da un dispositivo non autorizzato per passare come dispositivo esistente.

## Operatore di soluzioni IoT

Si invitano gli operatori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

- **Aggiornare il sistema regolarmente**: assicurarsi che tutti i sistemi operativi e i driver dei dispositivi vengano aggiornati alle versioni più recenti. Attivando gli aggiornamenti automatici su Windows 10 (IoT o altri SKU), il sistema viene aggiornato regolarmente da Microsoft, garantendo un sistema operativo protetto per i dispositivi IoT. Su altri sistemi operativi, ad esempio Linux, l'aggiornamento regolare garantisce protezione anche contro gli attacchi dannosi. 
- **Proteggere i sistemi da attività dannose**: se il sistema operativo lo consente, aggiungere funzionalità antivirus e anti-malware recenti su ogni sistema operativo del dispositivo. In questo modo è possibile mitigare le minacce più esterne. La maggior parte dei sistemi operativi moderni, come Windows 10 IoT e Linux, possono essere protetti da questa minaccia tramite procedure appropriate. 
- **Effettuare controlli regolari**: controllare la presenza di problemi di sicurezza all'infrastruttura IoT è un fattore chiave durante la risposta agli incidenti di sicurezza. La maggior parte dei sistemi operativi, come ad esempio Windows 10 (IoT e altri SKU), offre la registrazione integrata degli eventi che è opportuno esaminare frequentemente per assicurarsi che non si verifichino violazioni della protezione. Le informazioni di controllo possono essere inviate e analizzate come flusso di dati di telemetria separati al servizio cloud.
- **Proteggere fisicamente l'infrastruttura IoT**: gli attacchi più dannosi per la sicurezza dell'infrastruttura IoT vengono lanciati tramite l'accesso fisico ai dispositivi. La protezione contro l'uso non autorizzato di porte USB e altri accessi fisici è un fattore molto importante per la sicurezza e la protezione. La registrazione degli accessi fisici, come l'uso delle porte USB, è fondamentale per rilevare eventuali violazioni. Anche in questo caso, Windows 10 (IoT e altri SKU) offre la registrazione dettagliata di questi eventi.
- **Proteggere le credenziali del cloud**: le credenziali per l'autenticazione nel cloud usate per la configurazione e il funzionamento di una distribuzione IoT costituiscono probabilmente il modo più semplice per accedere e compromettere un sistema IoT. Proteggere le credenziali modificando frequentemente la password e non usarle per i computer pubblici. 

Le capacità dei vari dispositivi IoT variano. Da un lato, alcuni dispositivi potrebbero essere computer dotati di sistemi operativi desktop tradizionali, mentre in altri, i dispositivi potrebbero disporre di sistemi operativi molto leggeri. Le procedure consigliate per la protezione descritte in precedenza possono essere applicate a questi dispositivi in modo diverso. Se specificato, è necessario attenersi alle procedure aggiuntive per la sicurezza e la distribuzione fornite dal produttore dei dispositivi.

Alcuni dispositivi legacy e limitati potrebbero non essere stati progettati in modo specifico per la distribuzione IoT. Questi dispositivi potrebbero non essere in grado di crittografare i dati, stabilire connessioni a Internet, fornire controllo avanzato e così via. In questi casi, è opportuno usare un gateway moderno e sicuro sul campo per aggregare i dati dai dispositivi legacy al fine di garantire la protezione necessaria per la connessione di questi dispositivi a Internet. I gateway sul campo offrono in questo caso autenticazione protetta, negoziazione per le sessioni crittografate, ricezione di comandi dal cloud e molte altre funzionalità di sicurezza.

<!---HONumber=AcomDC_0622_2016-->