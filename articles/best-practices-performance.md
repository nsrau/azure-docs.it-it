<properties linkid="develop-dotnet-performance" urlDisplayName="Performance" pageTitle="Performance best practices - Azure" metaKeywords="Azure optimization, Azure best practice performance" description="Learn about best practices for performance in Azure." metaCanonical="" services="cloud-services,sql-database,storage,service-bus,virtual-network" documentationCenter=".NET" title="" authors="" solutions="" manager="" editor="" />

Procedure consigliate per le prestazioni nelle applicazioni Azure
=================================================================

In questa guida vengono fornite le linee guida sulle procedure e le tecniche consigliate da seguire per ottimizzare le prestazioni delle applicazioni Azure.

Si noti che l'utilizzo di Azure offre molti vantaggi e le prestazioni sono solo uno di questi. I consigli forniti in questo documento riguardano principalmente le prestazioni. In altri casi, le prestazioni potrebbero assumere un'importanza secondaria, ad esempio se si desidera delegare la gestione dell'hardware fisico ad Azure o usufruire dell'interessante opzione relativa al "pagamento in base al consumo". In questo documento non vengono valutati gli scenari in cui le prestazioni costituiscono una priorità secondaria.

Panoramica
----------

Le prestazioni possono essere definite come ["'la quantità di lavoro utile svolto' rispetto al tempo e alle risorse utilizzati".](http://go.microsoft.com/fwlink/?LinkId=252650)

Nella definizione sono inclusi due elementi: metriche e risorse. Le metriche delle prestazioni sono costituite da numeri che devono essere raggiunti per soddisfare i requisiti aziendali. Riguardano elementi quali tempo di risposta, velocità effettiva, disponibilità e così via. Le prestazioni includono inoltre il livello di utilizzo delle risorse necessario per raggiungere un determinato livello di metrica delle prestazioni. Poiché i costi rappresentano quasi sempre un requisito aziendale e le risorse hanno un costo in denaro, le prestazioni prevedono un utilizzo delle risorse più efficiente possibile.

### Ciclo di vita delle prestazioni

È possibile influire sulle prestazioni in due momenti diversi del ciclo di vita dell'applicazione:

-   nella fase di progettazione, quando si prendono decisioni architetturali fondamentali che possono interessare le prestazioni e
-   in fase di esecuzione, quando vengono identificati i colli di bottiglia o vengono effettuati il monitoraggio e la misurazione e così via.

Entrambe le attività sono necessarie. In questo white paper viene principalmente affrontata la fase di progettazione, in quanto gli errori architetturali sono più difficili da correggere in fase di esecuzione.

#### Modellazione dell'applicazione

È importante creare un modello dei più importanti scenari del cliente dell'applicazione. In questo caso, per "importante" si intende con il maggiore impatto sulle prestazioni. L'identificazione degli scenari e delle attività dell'applicazione necessarie consentono di eseguire il test del modello di prova

#### Test delle prestazioni del modello di prova

Il test delle prestazioni end-to-end completo è un passaggio critico nelle fasi di progettazione e distribuzione dell'applicazione. Le applicazioni Azure sono costituite da molte parti che possono includere componenti personalizzati, nonché quelli forniti da Microsoft. Microsoft non può eseguire il test delle prestazioni per ogni combinazione possibile di questi componenti. Di conseguenza, il test delle prestazioni dell'applicazione completo e corretto è un passaggio critico di qualsiasi distribuzione.

In base al modello di applicazione sviluppato, è necessario eseguire il test del modello di prova dell'applicazione non appena possibile ed effettuare il testi di carico per convalidare l'architettura dell'applicazione per verificare che questa soddisfi i requisiti di prestazioni in termini di scalabilità e latenza. È estremamente importante convalidare l'architettura e le ipotesi di progetto iniziali. Non è consigliabile scoprire che l'applicazione non è in grado di sostenere il carico previsto quando viene pubblicata. In Visual Studio sono disponibili gli strumenti per l'esecuzione del test di carico, come descritto nelle [informazioni generali sul test di carico di Visual Studio in Azure](http://www.visualstudio.com/get-started/load-test-your-app-vs).

### Differenze relative alle prestazioni in Azure

Nelle applicazioni Azure è possibile ottenere i miglioramenti delle prestazioni più significativi tramite la scalabilità orizzontale e il partizionamento delle risorse. La creazione di applicazioni scalabili in Azure richiede la scalabilità orizzontale delle risorse in base al relativo partizionamento fisico, ovvero database SQL, archiviazione, nodi di calcolo e così via. Questo partizionamento consente l'esecuzione parallela di attività dell'applicazione ponendo così le basi per prestazioni elevate, in quanto Azure dispone delle risorse di un intero data center e gestisce automaticamente il partizionamento fisico. Per ottenere questo livello di prestazioni complessive è necessario utilizzare modelli di progettazione con scalabilità orizzontale appropriati.

Paradossalmente, nonostante l'aumento delle prestazioni dell'applicazione complessiva, le prestazioni di ogni singola operazione risultano ridotte rispetto al relativo equivalente locale a causa dell'aumento della latenza di rete, della maggiore affidabilità derivante dalle operazioni di failover e così via. Ma il parallelismo consentito dall'utilizzo corretto delle risorse di partizionamento, compensa più che adeguatamente la riduzione delle singole prestazioni.

### Attività di progettazione necessarie

Alcuni fattori relativi alle prestazioni possono variare in base allo scenario dell'applicazione. Nel capitolo successivo verranno illustrati gli argomenti seguenti: scalabilità e partizionamento delle risorse, corretto posizionamento dei dati e ottimizzazione dell'utilizzo dei servizi di Azure.

Nel capitolo seguente verranno illustrati i fattori relativi alle prestazioni che riguardano qualsiasi applicazione Azure: latenza di rete, connessioni temporanee e così via.

Progettare per l'ottimizzazione delle prestazioni in un ambiente cloud
----------------------------------------------------------------------

Per la progettazione di un'applicazione Azure o la migrazione di un'applicazione locale ad Azure è necessario tenere conto degli elementi dipendenti dallo scenario seguenti:

-   Scalabilità orizzontale e partizionamento delle risorse: si tratta del meccanismo fondamentale per ottenere il parallelismo e di conseguenza prestazioni elevate.
-   Architettura dei dati: tipo di archiviazione dati da utilizzare per le diverse parti dei dati dell'applicazione.
-   Singole ottimizzazioni del servizio di Azure

Azure è in grado di raggiungere le prestazioni massime grazie alla scalabilità orizzontale e al partizionamento delle risorse che consentono la parallelizzazione di grandi quantità di attività. Questo risulta abbastanza ovvio se si pensa a un database SQL Azure di grandi dimensioni, ma ciò vale anche per qualsiasi altra risorsa che può diventare un collo di bottiglia.

In Azure sono disponibili le opzioni seguenti per l'archiviazione dei dati e la soluzione scelta può comportare un impatto significativo sulle prestazioni:

-   Database SQL di Azure
-   Cache di Azure
-   Archiviazione tabelle di Azure
-   Archiviazione BLOB di Azure
-   Unità di Azure
-   Code di Azure
-   Messaggistica negoziata del bus di servizio di Azure
-   Soluzioni di archiviazione per "Big Data" quali Hadoop

Poiché le specifiche variano, verranno illustrate le soluzioni in base agli scenari seguenti:

-   Servizio cloud di Azure con un database SQL
-   Servizio cloud di Azure che utilizza in modo intensivo le code di archiviazione
-   Sito Web di Azure che utilizza MySQL come database back-end
-   Applicazioni "Big Data"
-   Applicazioni che utilizzano un database back-end MySQL

### Scenario: Database SQL in un servizio cloud

La maggior parte dei principi di una progettazione di database ottimale si applica anche a un database SQL di Azure. È disponibile un'ampia raccolta di materiale in cui viene descritto come progettare schemi di SQL Server o del database SQL di Azure. Di seguito sono riportati alcuni riferimenti relativi alla progettazione dello schema di database SQL:

-   [Dati fondamentali della progettazione e modellazione di database](http://go.microsoft.com/fwlink/?LinkId=252675)
-   [Serie Stairway: Nozioni di base della progettazione di database](http://go.microsoft.com/fwlink/?LinkId=252676)
-   [Progettazione di database](http://go.microsoft.com/fwlink/?LinkId=252677)

Esistono due attività di progettazione principali fondamentalmente diverse in Azure:

-   Posizionare i dati in modo corretto: questa attività può prevedere lo spostamento di alcuni dati relazionali in BLOB o tabelle di Azure, se necessario.
-   Garantire la massima scalabilità: nel decidere se e come partizionare i dati.

#### Architettura dei dati: spostamento dei dati da un database SQL

Alcuni dati che spesso risiedono in SQL Server locale devono essere spostati altrove in Azure.

##### Spostamento dei dati in BLOB di Azure

I dati BLOB, ad esempio immagini o documenti, non devono essere archiviati in un database SQL ma nel servizio di archiviazione BLOB di Azure. Sebbene tali dati siano spesso ospitati in SQL Server locale, nel cloud l'utilizzo del servizio di archiviazione BLOB risulta molto più economico. Sarà necessario sostituire le chiavi esterne che fanno riferimento ai dati BLOB con identificatori di archiviazione BLOB per mantenere la possibilità di recuperare i dati BLOB e modificare le query che fanno riferimento ai dati.

##### Spostamento di tabelle SQL in Archiviazione tabelle di Azure

Per decidere se utilizzare o meno Archiviazione tabelle di Azure, è necessario esaminare i costi e le prestazioni. Archiviazione tabelle è una soluzione molto più economica rispetto all'archiviazione degli stessi dati in un database SQL. È tuttavia necessario valutare attentamente qual è il livello di utilizzo delle funzionalità relazionali di SQL quali join, filtro, query e così via da parte dei dati. Se l'utilizzo di queste funzionalità da parte dei dati è minimo, l'archiviazione in una tabella di Azure può rappresentare la soluzione ottimale.

Un modello di progettazione comune in cui è possibile valutare l'utilizzo di Archiviazione tabelle prevede una tabella con molte righe, ad esempio la tabella Clienti nel database di esempio AdventureWorks, in cui parecchie colonne non sono utilizzate dalla maggior parte dei clienti ma solo da un gruppo ristretto. Spesso, le colonne vengono suddivise in una seconda tabella (denominata ad esempio VarieClienti) con una relazione facoltativa 1 a 0 tra Clienti e la seconda tabella. In questo caso, sarebbe possibile spostare la seconda tabella in Archiviazione tabelle e valutare se le dimensioni della tabella e i modelli di accesso rendono questa soluzione più economica.

Per ulteriori informazione su Archiviazione tabelle, vedere:

-   [Archiviazione tabelle di Azure e database SQL di Azure: Confronto e contrapposizioni](http://msdn.microsoft.com/it-it/library/jj553018.aspx)
-   [Considerazioni relative alle prestazioni di Archiviazione tabelle di Azure](http://go.microsoft.com/fwlink/?LinkId=252663)
-   [Database SQL e Archiviazione tabelle di Azure](http://go.microsoft.com/fwlink/?LinkId=252664)
-   [Ottimizzazione delle prestazioni tramite l'invio in batch di inserimenti di Archiviazione tabelle di Azure](http://go.microsoft.com/fwlink/?LinkID=252665) in cui sono illustrati alcuni risultati relativi alle prestazioni.
-   [Guida alle prestazioni e all'elasticità di Database SQL](http://go.microsoft.com/fwlink/?LinkId=221876)

#### Partizionamento dei dati

Una delle risorse che vengono partizionate con maggiore frequenza sono i dati. Se si sta creando un Servizio cloud di Azure, è necessario valutare l'utilizzo della funzionalità di partizionamento orizzontale incorporata di Database SQL disponibile tramite federazioni.

Per informazioni generali sulle federazioni di Database SQL, vedere [Federazioni in database SQL di Azure](http://go.microsoft.com/fwlink/?LinkId=252668).

##### Attività di progettazione per le federazioni SQL

L'utilizzo delle federazioni di Database SQL in un servizio cloud di Azure richiede alcune modifiche dei principi di progettazione tradizionali. Tuttavia, la maggior parte delle procedure di progettazione ottimali per un database di SQL Server locale rappresenta un punto di partenza necessario per la progettazione di federazioni di Database SQL. Le due principali attività di progettazione riguardano le decisioni seguenti:

-   in base a cosa eseguire la federazione e

-   dove posizionare le tabelle non federate.

Per stabilire la modalità di federazione, è necessario esaminare lo schema del database e identificare le aggregazioni di tabelle correlate. Un aggregato è un set di tabelle correlate da relazioni uno-a-molti tramite le rispettive chiavi esterne, ad eccezione della radice dell'aggregato.

Ad esempio, nel noto database di esempio AdventureWorks, uno dei possibili aggregati è il set {Cliente, Ordine, RigaOrdine e alcune altre}. Un altro aggregato possibile è {Fornitore, Prodotto, RigaOrdine, Ordine}

Ogni aggregato è un candidato per la federazione. È necessario valutare l'area in cui si prevede un aumento delle dimensioni ed esaminare il carico di lavoro dell'applicazione: le query "ben allineate" con lo schema di federazione, ovvero che non richiedono dati da più di un membro della federazione, verranno eseguite correttamente. Per quelle non ben allineate è necessaria la logica nel livello dell'applicazione in quanto Database SQL attualmente non supporta i join tra database.

Per visualizzare un esempio di analisi di progettazione che esamina il database AdventureWorks per eseguirne la federazione e mostra le considerazioni in ogni fase della progettazione, vedere il post di blog relativo all'[approccio orientato alla scalabilità per la progettazione di database con le federazioni: Parte 1 - Selezione delle federazioni e della chiave della federazione](http://go.microsoft.com/fwlink/?LinkId=252671).

Dopo avere stabilito quali sono le tabelle per la federazione, è necessario aggiungere la chiave primaria della tabella principale aggregata come colonna a ognuna delle tabelle correlate.

Dopo avere stabilito la modalità di federazione delle tabelle, è necessario valutare la posizione delle tabelle di riferimento, nonché degli oggetti di database. Per ulteriori informazioni, vedere la discussione relativa a questo argomento nel post di blog relativo all'[approccio orientato alla scalabilità per la progettazione di database con le federazioni: Parte 2 - Aggiunta di annotazioni e distribuzione dello schema per le federazioni](http://go.microsoft.com/fwlink/?LinkId=252672). L'esecuzione di query più avanzate è descritta nella [Parte 2](http://go.microsoft.com/fwlink/?LinkId=252673).

##### Partizionamento gestito in proprio

Sono disponibili numerosi esempi che illustrano vari modi per eseguire il partizionamento dei dati. Se si sceglie di non utilizzare le federazioni per il partizionamento dell'istanza di Database SQL, è necessario scegliere un metodo di partizionamento appropriato per l'applicazione. Di seguito sono riportati alcuni esempi:

-   Un account completo scritto prima del rilascio della federazione è [Come eseguire il partizionamento orizzontale con Database SQL](http://go.microsoft.com/fwlink/?LinkId=252678).
-   [Libreria di partizionamento orizzontale di SQL Server e Database SQL](http://go.microsoft.com/fwlink/?LinkId=252679)

##### Partizionamento di altre risorse

Oltre a Database SQL è possibile eseguire il partizionamento di altre risorse. È ad esempio possibile eseguire il partizionamento di server applicazioni e dedicarli a database specifici. Si supponga che l'applicazione contenga N server applicazioni e N database. Se a ogni server applicazioni è consentito accedere a ogni database, verranno utilizzate N connessioni di database al quadrato e, in alcuni casi, raggiungere un limite superiore di Azure. Se tuttavia si limita l'accesso di ogni server applicazioni a un numero ristretto di database, è possibile ridurre in modo significativo il numero di connessioni utilizzato.

A seconda dell'applicazione è possibile applicare un simile ragionamento ad altre risorse.

#### Memorizzazione nella cache

Il servizio di caching di Azure offre memoria elastica distribuita per la memorizzazione nella cache di elementi quali stato della sessione ASP.net o valori a cui fanno normalmente riferimento le tabelle di riferimento di database SQL. Poiché gli oggetti sono distribuiti nella memoria, è possibile ottenere un miglioramento significativo delle prestazioni. E, dal momento che l'infrastruttura di memorizzazione nella cache è gestita da Azure, i costi di sviluppo per l'implementazione risultano minimi.

Prevedere una capacità di memorizzazione nella cache sufficiente in modo da memorizzare gli oggetti utilizzati con maggiore frequenza. In Database SQL sono presenti tabelle di riferimento che vengono utilizzate di frequente per la conversione di codici numerici in stringhe di caratteri descrittive più lunghe. In queste tabelle sono spesso inclusi dati quali nomi di paesi e di città, valori di codici postali validi, nomi dei reparti aziendali e così via. Per quanto riguarda le tabelle più piccole, potrebbe risultare utile archiviare l'intera tabella nella cache, mentre per quelle più grandi è consigliabile archiviare solo i valori utilizzati di frequente. L'aumento di prestazioni deriva dall'esecuzione di query multijoin che coinvolgono tali dati, in quanto per ogni valore trovato nella cache vengono risparmiati numerosi accessi al disco. Per informazioni introduttive e discussioni relative a prestazioni e servizio di caching in Azure, vedere l'articolo [Introduzione del servizio di memorizzazione nella cache di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=252680). Per un post di blog più recente, vedere l'argomento riguardante le [considerazioni relative alle prestazioni del servizio Cache di \#Azure](http://go.microsoft.com/fwlink/?LinkId=252681).

#### Scenario: Utilizzo dell'accodamento nelle applicazioni Azure

Un esempio di questo scenario prevede l'utilizzo di StreamInsight per popolare le code con messaggi che verranno elaborati in un momento successivo.

Le code di Azure vengono utilizzate per passare messaggi, disaccoppiare temporaneamente sottosistemi e per fornire bilanciamento e livellamento del carico.

In Azure sono disponibili due tecnologie di coda alternative: Code di archiviazione e bus di servizio di Azure.

Le code di archiviazione di Azure offrono funzionalità quali dimensione coda di grandi dimensioni, rilevamento stato e altro ancora. Il bus di servizio offre funzionalità quali pubblicazione/sottoscrizione, integrazione completa con Windows Communication Foundation (WCF), rilevamento automatico dei duplicati, recapito First In First Out (FIFO) garantito e altro ancora.

Per un confronto completo e dettagliato delle due tecnologie, vedere [Code di Azure e bus di servizio di Azure: confronto e contrapposizioni](http://go.microsoft.com/fwlink/?LinkId=252682).

Per ulteriori informazioni sulle prestazioni del bus di servizio, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata di Service Bus](http://go.microsoft.com/fwlink/?LinkID=252683).

#### Scenario: Applicazioni "Big Data"

I "Big Data" sono spesso un sottoprodotto di un altro sistema o applicazione. Tra gli esempi sono inclusi:

-   Log Web

-   Altri file di diagnostica, controllo e monitoraggio

-   Log sismici delle società petrolifere

-   Dati sui clic e altre informazioni lasciate dagli utenti che navigano in Internet

È possibile identificare i "Big Data" in base ai criteri seguenti:

-   Dimensioni (in genere, centinaia di terabyte o più)

-   Tipo: non relazionale, a schema variabile e file in un file system

I dati non sono in genere adatti per l'elaborazione in un database relazionale.

Esistono quattro tipi principali di archiviazione di dati non SQL:

-   Chiave-valore

-   Documento

-   Grafico

-   Colonna-famiglia

Azure offre il supporto diretto per Hadoop e consente inoltre l'utilizzo di altre tecnologie. Per informazioni sul servizio HDInsight di Azure, vedere:

-   [Big Data](/en-us/solutions/big-data/)
-   [Servizio HDInsight di Azure](/it-it/documentation/services/hdinsight/)
-   [Introduzione al servizio HDInsight di Azure](/it-it/documentation/articles/hdinsight-get-started/)

Per informazioni sulle discussioni relative ai problemi riscontrati con vari metodi di archiviazione NoSQL, vedere:

-   [Acquisire familiarità con NoSQL in Azure](http://go.microsoft.com/fwlink/?LinkId=252729)
-   [Database orientato alle aggregazioni](http://go.microsoft.com/fwlink/?LinkID=252731)
-   [Persistenza poliglotta](http://go.microsoft.com/fwlink/?LinkId=252732)

#### Altre ottimizzazioni delle prestazioni di singoli servizi di Azure

Nella maggior parte dei singoli servizi di Azure sono incluse funzionalità e impostazioni che possono determinare un impatto significativo sulle prestazioni e che pertanto devono essere esaminate.

##### Unità di Azure

È possibile simulare l'utilizzo del disco rigido di un'applicazione esistente tramite un'unità di Azure, il cui backup viene eseguito da un BLOB Windows e che quindi è persistente in caso di guasto del singolo computer.

##### Archiviazione locale

Sebbene non sia persistente in caso di guasto del computer, può essere utilizzata per archiviare le informazioni a cui si accede di frequente o risultati intermedi che verranno utilizzati altrove. Si tratta di una soluzione economica in quanto non è previsto alcun addebito per l'utilizzo.

##### Servizio di controllo di accesso (ACS) di Azure

I due fattori principali che influiscono sull'utilizzo delle risorse del Servizio di controllo di accesso, e quindi sulle prestazioni, sono le dimensioni del token e la crittografia. Per ulteriori informazioni, vedere le [Linee guida sulle prestazioni del Servizio di controllo di accesso (ACS)](http://go.microsoft.com/fwlink/?LinkId=252747).

##### Serializzazione

La serializzazione non costituisce una parte ovvia dell'ottimizzazione delle prestazioni, tuttavia, la riduzione del traffico di rete può risultare significativa in alcuni scenari. Per un esempio di come le dimensioni di serializzazione possono variare in base al protocollo, vedere le riduzioni mostrate in [Applicazioni Web di Azure e serializzazione](http://go.microsoft.com/fwlink/?LinkId=252749).

Se la quantità di dati spostati presenta un problema di prestazioni, utilizzare la più piccola tra le serializzazioni disponibili. Qualora le prestazioni di serializzazione non siano sufficienti, valutare l'utilizzo di formati di serializzazione personalizzati o di terze parti non Microsoft. Come sempre, è consigliabile eseguire il test del modello di prova.

### Siti Web di Azure con MySQL

Per consigli sulle prestazioni per MySQL, vedere i collegamenti riportati di seguito:

-   La ricerca del termine *performance* in [http://mysql.com](http://go.microsoft.com/fwlink/?LinkId=252775) restituisce numerosi risultati (informazioni in lingua inglese).
-   Per consultare altre risorse, visitare i forum all'indirizzo[http://forums.mysql.com/list.php?24](http://go.microsoft.com/fwlink/?LinkId=252776).

Progettazione per sistemi condivisi
-----------------------------------

Azure è progettato per l'esecuzione di più applicazioni simultanee replicate per il failover su più computer. Questo influisce sulle prestazioni dell'applicazione in vari modi:

-   Connessioni temporanee

-   Limitazioni delle risorse che limitano l'utilizzo massimo

-   Latenza di rete

-   Percorso fisico dei servizi

Queste considerazioni si applicano a tutte le architetture di applicazioni in quanto sono determinate dall'infrastruttura fisica dei data center di Azure. Per informazioni dettagliate, vedere [Guida alle prestazioni e all'elasticità di Database SQL](http://go.microsoft.com/fwlink/?LinkID=252666).

### Latenza di rete

Azure è una piattaforma basata su servizi di risorse condivise e questo significa che due tipo di latenze o interruzioni si verificano regolarmente. La prima riguarda il tempo impiegato per effettuare una richiesta e ricevere una risposta tramite Internet. Poiché tali richieste e risposte possono attraversare un qualsiasi numero di router prima di tornare al client, timeout e disconnessioni sono più frequenti rispetto alle reti locali fisse. La seconda riguarda il tempo impiegato da un sistema di risorse condivise come Azure per creare versioni di backup dei dati per la durabilità e per sostituire e reindirizzare le richieste a eventuali istanze rimosse. Queste latenze ed errori sono importanti per comprendere come compensare per raggiungere i requisiti di prestazioni nell'applicazione. Un test di carico di livello effettivo può fornire ulteriori informazioni sulle latenze riscontrate.

Tenere conto del fatto che le latenze potrebbero essere maggiori, in quanto è probabile che il data center si trovi fisicamente più lontano rispetto al server locale.

### Percorso fisico dei servizi

Se possibile, posizionare diversi nodi o livelli dell'applicazione all'interno dello stesso data center. In caso contrario, la latenza di rete e i costi risulteranno superiori.

Ad esempio, collocare l'applicazione Web nello stesso data center dell'istanza di database SQL a cui accede anziché in un altro data center o in locale.

### Connessioni temporanee

L'applicazione DEVE essere in grado di gestire le connessioni interrotte. Le connessioni interrotte sono inevitabili e intrinseche all'architettura del cloud, ad esempio per operazioni quali la sostituzione di un nodo non utilizzato, la suddivisione di un membro della federazione in Database SQL e così via. Per informazioni sul miglior framework disponibile per gestire queste situazioni, vedere l'argomento relativo al [blocco applicazione per la gestione degli errori temporanei](http://go.microsoft.com/fwlink/?LinkID=236901).

### Limitazione

Nel modo dei servizi, le risorse possono essere molto granulari e sono soggette a un modello di pagamento a consumo. Per tutte le risorse esiste tuttavia una garanzia minima in termini di dimensioni, velocità o velocità effettiva, fattore importante se ad esempio si ha l'esigenza di un database di determinate dimensioni, ma in altri casi è importante valutare i limiti esterni di un servizio. Poiché le applicazioni Azure vengono eseguite in un ambiente condiviso con altre applicazioni, Azure applica una serie di limitazioni delle risorse di cui è necessario tenere conto. Se si supera la limitazione per una risorsa, un'ulteriore richiesta per quella risorsa genererà un'eccezione.

### Capacità fisica

Nell'ambito della pianificazione delle prestazioni, la pianificazione della capacità riveste un'importanza essenziale: se non si è in grado di garantire uno spazio di archiviazione sufficiente di vari tipi, potrebbe risultare impossibile eseguire l'applicazione. Allo stesso modo, una capacità di memoria o del processore inadeguata potrebbe rallentare in modo significativo l'esecuzione dell'applicazione.

Azure riduce notevolmente il lavoro richiesto per la pianificazione della capacità in quanto molte attività svolte in precedenza, ad esempio il recupero e il provisioning di computer, si sono evolute drasticamente. In Azure, la pianificazione della capacità non si basa più sugli elementi fisici dell'elaborazione ma agisce a un livello di astrazione più elevato, richiedendo invece il numero necessario per gli elementi seguenti:

-   Nodi di elaborazione
-   Archiviazione BLOB
-   Archiviazione tabelle
-   Code e così via.

Grazie alla scalabilità di Azure, le decisioni iniziali relative alla capacità possono essere modificate: è relativamente facile aumentare o ridurre il numero di risorse Azure. Anche in questo caso, è importante che la pianificazione della capacità sia accurata, in quanto questo garantisce che dopo la pubblicazione dell'applicazione si verifichi un periodo di valutazione e correzione degli errori relativo alla capacità.

Per applicazioni con requisiti di risorse variabili nel tempo, valutare l'utilizzo del [blocco applicazione per la scalabilità automatica](http://go.microsoft.com/fwlink/?LinkId=252873). Questo blocco consente di impostare regole per aumentare e ridurre le istanze del ruolo. Sono definiti due tipi di regole:

-   Regole di vincolo che consentono di impostare un numero massimo/minimo di istanze in base all'ora del giorno

-   Regole reattive che hanno effetto al prodursi di determinate condizioni, ad esempio la percentuale di utilizzo della CPU

È inoltre possibile definire regole personalizzate. Per ulteriori informazioni, vedere l'argomento relativo al [blocco applicazione per la scalabilità automatica](http://go.microsoft.com/fwlink/?LinkId=252873).

La pianificazione della capacità è un'attività specifica a sé stante e in questo documento si presuppone che sia già stata effettuata. Per informazioni dettagliate sulla pianificazione della capacità in Azure, vedere [Pianificazione della capacità delle code e degli argomenti del bus di servizio](http://go.microsoft.com/fwlink/?LinkId=252875).

Monitoraggio e ottimizzazione delle prestazioni in fase di esecuzione
---------------------------------------------------------------------

Anche la progettazione più attenta non è in grado di garantire che non si verifichino problemi di prestazioni in fase di esecuzione, pertanto è necessario eseguire il monitoraggio delle prestazioni dell'applicazione su base continuativa per verificare che raggiunga le metriche delle prestazioni richieste e per correggere le situazioni in cui tali metriche non vengono raggiunte. Anche le applicazioni con una progettazione ottimale sono soggette a eventi imprevisti, ad esempio la crescita esponenziale dell'utilizzo o possibili modifiche all'ambiente di runtime, che possono determinare problemi di prestazioni che richiedono l'ottimizzazione. Spesso, l'identificazione e la risoluzione dei colli di bottiglia rappresenta una parte significativa del processo.

La risoluzione dei problemi relativi alle prestazioni in fase di esecuzione richiede un lavoro di preparazione per l'integrazione della registrazione e della gestione delle eccezioni per consentire la risoluzione dei problemi nel momento in cui insorgono. Per informazioni complete su questo argomento, vedere [Procedure consigliate per la risoluzione dei problemi per lo sviluppo di applicazioni Azure](http://go.microsoft.com/fwlink/?LinkID=252876).

Questi sono gli strumenti disponibili per il monitoraggio delle prestazioni di ogni servizio di Azure su base continuativa. È consigliabile integrare gli strumenti di registrazione nelle applicazioni affinché forniscano le informazioni dettagliate necessarie per la risoluzione dei problemi generali e relativi alle prestazioni.

### Database SQL

Si noti che SQL Profiler non è attualmente disponibile in Azure. Sono disponibili diverse soluzioni alternative per recuperare le informazioni relative alle prestazioni necessarie. Un'alternativa prevede che durante lo sviluppo il test iniziale venga effettuato in una versione locale del database in cui è disponibile SQL Profiler.

È inoltre possibile utilizzare il comando SET STATISTICS in Transact-SQL e utilizzare SQL Server Management Studio per visualizzare il piano di esecuzione generato da una query, poiché la codifica di query efficienti influisce in modo significativo sulle prestazioni. Per informazioni e spiegazioni dettagliate su come eseguire questa operazione, vedere l'articolo che spiega come [ottenere informazioni dettagliate sulle prestazioni in Database SQL](http://go.microsoft.com/fwlink/?LinkId=252877). Un altro approccio interessante è quello illustrato nel confronto dell'analisi delle prestazioni di [Database SQL ed SQL Server in locale](http://go.microsoft.com/fwlink/?LinkId=252878).

Di seguito sono riportati due argomenti relativi alle viste a gestione dinamica:

-   [Monitoraggio di database SQL mediante le viste a gestione dinamica](http://go.microsoft.com/fwlink/?LinkId=236195)
-   [Viste a gestione dinamica utili per l'analisi in Database SQL se non è disponibile SQL Profiler](http://go.microsoft.com/fwlink/?LinkId=252879)

### Risorse e strumenti di analisi

Sono disponibili numerosi strumenti non Microsoft di terze parti per l'analisi delle prestazioni di Azure:

-   [Cerebrata](http://go.microsoft.com/fwlink/?LinkId=252880)
-   [Test delle prestazioni di SQL Server e Database SQL: Enzo SQL Baseline](http://enzosqlbaseline.codeplex.com/)

Altre risorse

-   [Guida alle prestazioni e all'elasticità di Database SQL](http://go.microsoft.com/fwlink/?LinkID=252666)
-   [Database SQL](http://go.microsoft.com/fwlink/?LinkId=246930)
-   [Archiviazione](http://go.microsoft.com/fwlink/?LinkId=246933)
-   [Rete](http://go.microsoft.com/fwlink/?LinkId=252882)
-   [Bus di servizio](http://go.microsoft.com/fwlink/?LinkId=246934)
-   [Pianificazione di Azure - Guida all'integrazione di Azure nell'ambiente](http://go.microsoft.com/fwlink/?LinkId=252884)

