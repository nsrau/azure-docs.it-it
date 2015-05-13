#Gestione dati e analisi business

La gestione e l'analisi dei dati nel cloud è importante quanto in altri ambiti. In Azure sono pertanto disponibili svariate tecnologie per l'utilizzo di dati relazionali e non relazionali. In questo articolo viene presentata ogni opzione disponibile.

##Sommario      

- [Archiviazione BLOB](#blob)
- [Esecuzione di DBMS in una macchina virtuale](#dbinvm)
- [Database SQL](#sqldb)
	- [Sincronizzazione dati SQL](#datasync)
	- [Creazione di report di dati SQL tramite macchine virtuali](#datarpt)
- [Archiviazione tabelle](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Archiviazione BLOB

Il termine "BLOB" è l'acronimo di "Binary Large OBject" \(oggetto binario di grandi dimensioni\) e descrive esattamente la natura di questo oggetto, ovvero una raccolta di informazioni binarie. Benché siano semplici, gli oggetti BLOB sono molto utili. Nella [figura 1](#Fig1) sono illustrate le nozioni di base dell'archiviazione BLOB di Azure.

<a name="Fig1"></a>![Diagramma di oggetti BLOB][blobs]
 
**Figura 1: Archiviazione BLOB di Azure consente di memorizzare i dati binari \(BLOB\) in contenitori.**

Per usare i BLOB, è necessario creare innanzitutto un *account di archiviazione di Azure*. Nel corso di questa procedura si specifica il data center in cui verranno archiviati gli oggetti creati usando questo account. Dovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore nell'account di archiviazione. Per accedere a un oggetto BLOB, un'applicazione specifica un URL con il formato seguente:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt;

&lt;\*StorageAccount\*&gt; è un identificatore univoco assegnato quando viene creato un nuovo account di archiviazione, mentre &lt;\*Container\*&gt; e &lt;\*BlobName\*&gt; sono i nomi di un contenitore specifico e di un oggetto BLOB in tale contenitore.

In Azure sono disponibili due tipi diversi di oggetti BLOB. Le opzioni disponibili sono:

- BLOB *in blocchi*, ognuno dei quali può contenere fino a 200 gigabyte di dati. Come indicato dal nome, un oggetto BLOB in blocchi è suddiviso in alcuni blocchi. Se si verifica un errore durante il trasferimento di un BLOB in blocchi, sarà possibile riprendere la trasmissione dal blocco più recente, invece di inviare di nuovo l'intero oggetto BLOB. Gli oggetti BLOB in blocchi costituiscono un approccio abbastanza generico all'archiviazione e sono i tipi di oggetti BLOB attualmente più usati.

- BLOB *di pagine*, ognuno dei quali può raggiungere dimensioni pari a un terabyte. Gli oggetti BLOB di pagine sono stati progettati per l'accesso casuale, quindi ogni oggetto è suddiviso in alcune pagine. Un'applicazione potrà leggere e scrivere in singole pagine casuali nell'oggetto BLOB. In Macchine virtuali di Azure, ad esempio, le macchine virtuali create usano gli oggetti BLOB di pagine come archivio permanente per i dischi del sistema operativo e per i dischi di dati.

Indipendentemente dalla scelta di oggetti BLOB in blocchi o di pagine, le applicazioni possono accedere ai dati in molti modi diversi. Di seguito sono indicate alcune opzioni disponibili:

- In modo diretto tramite un protocollo di accesso RESTful \(ad esempio, basato su HTTP\). Questa opzione può essere usata sia dalle applicazioni Azure che dalle applicazioni esterne, incluse le applicazioni in esecuzione locale.
- Tramite la libreria del client di archiviazione di Azure, che offre un'interfaccia più semplice per gli sviluppatori, oltre al protocollo di accesso a oggetti BLOB RESTful non elaborati. Anche questa opzione può essere usata sia dalle applicazioni Azure che dalle applicazioni esterne per l'accesso agli oggetti BLOB tramite questa libreria.
- Tramite le unità Azure, un'opzione che consente a un'applicazione Azure di gestire un oggetto BLOB di pagine come unità locale con file system NTFS. Per l'applicazione, l'oggetto BLOB di pagine appare come un normale file system Windows, a cui si accede tramite I/O file standard. Le operazioni di lettura e scrittura vengono in effetti inviate all'oggetto BLOB di pagine sottostante, che implementa l'unità Azure. 

Per evitare eventuali errori hardware e ottimizzare la disponibilità, ogni oggetto BLOB viene replicato in tre computer in un data center di Azure. La scrittura in un oggetto BLOB comporta l'aggiornamento di tutte e tre le copie, in modo che le letture successive non rilevino risultati incoerenti. È inoltre possibile specificare che i dati di un oggetto BLOB devono essere copiati in un altro data center di Azure situato nella stessa area geografica ma ad almeno 800 km di distanza. Questo tipo di copia, definito *replica geografica*, viene eseguito entro pochi minuti dall'aggiornamento di un oggetto BLOB e risulta molto utile in caso di ripristino di emergenza.

È inoltre possibile rendere disponibili i dati degli oggetti BLOB tramite la *rete per la distribuzione di contenuti \(CDN\) di Azure*. Grazie alla memorizzazione nella cache dei dati degli oggetti BLOB in dozzine di server in tutto il mondo, la rete CDN consente di velocizzare l'accesso alle informazioni a cui si accede ripetutamente.

Pur essendo estremamente semplici, gli oggetti BLOB rappresentano la scelta ideale per molte situazioni, ad esempio per l'archiviazione e lo streaming di video e audio, oltre che per i backup e altri tipi di archiviazione di dati. Gli sviluppatori possono inoltre usare gli oggetti BLOB per l'archiviazione di qualsiasi tipo di dato non strutturato. La possibilità di usare un sistema semplice per l'archiviazione e l'accesso di dati binari può risultare estremamente utile.


## <a name="dbinvm"></a>Esecuzione di DBMS in una macchina virtuale

Molte applicazioni si avvalgono attualmente di un sistema di gestione di database \(DBMS, Database Management System\). I sistemi relazionali quali SQL Server sono i più usati, ma gli approcci non relazionali, comunemente noti come *NoSQL*, sono sempre più diffusi. Per consentire alle applicazioni cloud di usare queste opzioni di gestione dati, in Macchine virtuali di Azure è possibile eseguire un DBMS \(relazionale o NoSQL\) in una macchina virtuale. Nella [figura 2](#Fig2) viene mostrato l'utilizzo di SQL Server.

<a name="Fig2"></a>![Diagramma di SQL Server in una macchina virtuale][SQLSvr-vm]
 
**Figura 2: Macchine virtuali di Azure consente l'esecuzione di un DBMS in una macchina virtuale. Il salvataggio permanente viene offerto dagli oggetti BLOB.**

Per gli sviluppatori e gli amministratori di database questo scenario risulta molto simile all'esecuzione dello stesso software nel proprio data center. In questo esempio è possibile usare quasi tutte le funzionalità di SQL Server e si dispone di accesso amministrativo completo al sistema. L'utente sarà inoltre responsabile della gestione del server database, esattamente come per l'esecuzione in locale.

Come illustrato nella [figura 2](#Fig2), i database sembrano essere archiviati sul disco locale della macchina virtuale su cui viene eseguito il server. Dietro le quinte, tuttavia, ognuno di questi dischi viene scritto in un BLOB di Azure. Questa procedura è analoga all'utilizzo di un SAN nel proprio data center, con un oggetto BLOB che ha una funzione analoga a LUN. Esattamente come nel caso di qualsiasi oggetto BLOB di Azure, i dati in esso contenuti vengono replicati tre volte in un data center e, se richiesto dall'utente, in un altro data center nella stessa area geografica. È inoltre possibile usare opzioni quali il mirroring di database di SQL Server per garantire una maggiore affidabilità.

Un'altra soluzione per l'utilizzo di SQL Server in una macchina virtuale consiste nella creazione di un'applicazione ibrida, in cui i dati si trovano su Azure mentre la logica dell'applicazione viene eseguita in locale. Ad esempio, ciò potrebbe risultare efficace nel caso in cui applicazioni in esecuzione in diverse posizioni o in diversi dispositivi mobili debbano condividere gli stessi dati. Per semplificare le comunicazioni tra il database del cloud e la logica locale, un'organizzazione può usare Rete virtuale di Azure per creare una connessione VPN \(Virtual Private Network, rete privata virtuale\) tra un data center di Azure e il proprio data center locale.


## <a name="sqldb"></a>Database SQL

In genere, l'esecuzione di un DBMS in una macchina virtuale è la prima opzione a cui si pensa per la gestione di dati strutturati nel cloud. Non si tratta tuttavia dell'unica soluzione disponibile e non è sempre la soluzione migliore. In alcuni casi la gestione dei dati tramite un approccio di tipo PaaS \(Platform as a Service\) è più appropriato. In Azure è disponibile una tecnologia PaaS definita database SQL, che consente la gestione dei dati relazionali, come illustrato nella [figura 3](#Fig3).

<a name="Fig3"></a>![Diagramma di database SQL][SQL-db]
 
**Figura 3: Database SQL offre un servizio di archiviazione relazionale PaaS condiviso.**

Database SQL non fornisce a ogni cliente un'istanza fisica di SQL Server, offre invece un servizio multi-tenant, con un database SQL logico per ogni cliente. Tutti i clienti condividono le capacità di calcolo e di archiviazione offerte dal servizio. Analogamente all'Archiviazione BLOB, tutti i dati in database SQL vengono archiviati in tre computer distinti in un data center di Azure, assicurando ai database una disponibilità elevata predefinita.

Per un'applicazione, database SQL risulta molto simile a SQL Server. Le applicazioni possono eseguire query SQL sulle tabelle relazionali, possono usare le stored procedure T-SQL e infine eseguire transazioni in più tabelle. Poiché le applicazioni accedono a database SQL tramite il protocollo TDS \(Tabular Data Stream\), lo stesso protocollo usato per accedere a SQL Server, potranno usare i dati mediante Entity Framework, ADO.NET, JDBC e altre interfacce note per l'accesso ai dati.

Database SQL è un servizio cloud in esecuzione nei data center di Azure. Non sarà pertanto necessario gestire alcun aspetto fisico del sistema, ad esempio l'utilizzo dei dischi. Non sarà inoltre necessario occuparsi dell'aggiornamento del software o della gestione di altre attività amministrative di base. Ogni organizzazione dei clienti mantiene ovviamente il controllo sui propri database, inclusi gli schemi e gli accessi utente, ma molte attività amministrative di base vengono eseguite automaticamente.

Anche se database SQL risulta simile a SQL Server per le applicazioni, il suo comportamento non è esattamente uguale a quello di un DBMS in esecuzione in una macchina fisica o virtuale. Essendo in esecuzione su un hardware condiviso, le prestazioni risultano variabili in base al carico applicato a tale hardware da tutti i clienti. Ciò significa che le prestazioni, ad esempio, di una stored procedure in database SQL possono presentare differenze in giorni diversi.

Database SQL consente attualmente di creare un database in grado di contenere fino a 150 gigabyte. Se è necessario usare database di dimensioni superiori, il servizio offre un'opzione denominata *federazione*. A tale scopo, è necessario che un amministratore di database crei due o più *membri di federazione*, ognuno dei quali è costituito da un database distinto con il proprio schema. I dati vengono distribuiti tra tali membri, una procedura a volte definita *partizionamento orizzontale*, e a ogni membro viene assegnata una *chiave federativa* univoca. Un'applicazione esegue query SQL rispetto ai dati, specificando la chiave federativa che identifica il membro di federazione a cui deve fare riferimento la query. Ciò consente di usare un approccio relazionale tradizionale con quantità elevate di dati. Sono necessari, come sempre, alcuni compromessi. Ad esempio, le query e le transazioni non sono in grado di estendersi ai membri di federazione. Nel caso in cui un servizio PaaS relazionale rappresenti la soluzione migliore e questi compromessi siano accettabili, l'utilizzo della federazione SQL può costituire l'opzione ottimale.

Database SQL può essere usato da applicazioni in esecuzione su Azure o altrove, ad esempio nel proprio data center locale. Risulta pertanto molto utile per applicazioni cloud che necessitano di dati relazionali, oltre che per applicazioni locali che possono trarre vantaggio dall'archiviazione dei dati nel cloud. Un'applicazione mobile può avvalersi di database SQL per la gestione di dati relazionali condivisi, ad esempio, come nel caso di un'applicazione per l'inventario in esecuzione presso diversi rivenditori in tutto il mondo.

Durante la valutazione di database SQL, è necessario esaminare un aspetto ovvio e importante, ovvero quando è consigliabile eseguire SQL Server in una macchina virtuale e quando invece database SQL risulta l'opzione migliore. Anche in questo caso sono necessari alcuni compromessi e l'approccio migliore dipende dai requisiti specifici.

Un modo semplice per valutare questo aspetto consiste nel considerare database SQL come ideale per le nuove applicazioni e SQL Server in una macchina virtuale come ottimale in caso di spostamento nel cloud di un'applicazione locale esistente. Potrebbe tuttavia risultare utile esaminare questa decisione in modo più dettagliato. Ad esempio, database SQL è più semplice da usare, poiché richiede livelli minimi di configurazione e amministrazione. L'esecuzione di SQL Server in una macchina virtuale, tuttavia, potrebbe garantire prestazioni più prevedibili, in quanto non si tratta di un servizio condiviso, e consente di supportare database non federati di dimensioni superiori rispetto a database SQL. Database SQL offre tuttavia la replica predefinita di dati ed elaborazione, consentendo in pratica di ottenere un DBMS a disponibilità elevata con uno sforzo minimo. Anche se SQL Server offre maggiore controllo e un insieme di opzioni leggermente più ampio, database SQL risulta più semplice da configurare e significativamente meno impegnativo da gestire.

È infine importante notare che database SQL non è l'unico servizio dati PaaS disponibile in Azure. Altre opzioni sono rese disponibili dai partner Microsoft. Ad esempio, ClearDB offre una soluzione PaaS MySQL, mentre Cloudant vende una soluzione NoSQL. I servizi dati PaaS rappresentano la soluzione ideale in molte situazioni, quindi questo approccio alla gestione dati è un aspetto importante di Azure.


### <a name="datasync"></a>Sincronizzazione dati SQL

Benché database SQL mantenga tre copie di ogni database in un singolo data center di Azure, non esegue automaticamente la replica dei dati tra i data center di Azure. Per eseguire tale operazione è disponibile Sincronizzazione dati SQL, come illustrato nella [figura 4](#Fig4).

<a name="Fig4"></a>![Diagramma di Sincronizzazione dati SQL][SQL-datasync]
 
**Figura 4: Sincronizzazione dati SQL consente di sincronizzare i dati disponibili in database SQL con i dati presenti in Azure e nei data center locali.**

Come mostrato nel diagramma, Sincronizzazione dati SQL consente di sincronizzare i dati in diverse posizioni. Si supponga di eseguire un'applicazione in più data center di Azure, ad esempio, con dati archiviati in database SQL. È possibile usare Sincronizzazione dati SQL per mantenere la sincronizzazione di tali dati. Sincronizzazione dati SQL consente inoltre di sincronizzare dati tra un data center di Azure e un'istanza di SQL Server in esecuzione in un data center locale. Ciò può risultare utile per la gestione di una copia locale dei dati usata dalle applicazioni locali e una copia sul cloud usata dalle applicazioni in esecuzione su Azure. Benché non sia mostrato nella figura, è possibile usare Sincronizzazione dati SQL anche per sincronizzare i dati tra database SQL e SQL Server in esecuzione in una macchina virtuale in Azure o altrove.

La sincronizzazione può essere bidirezionale ed è possibile determinare esattamente i dati da sincronizzare e la frequenza della sincronizzazione. La sincronizzazione tra database non è tuttavia atomica, presenta sempre un minimo ritardo. Comunque lo si utilizzi, l'impostazione di una sincronizzazione con Sincronizzazione dati SQL viene gestita completamente dalla configurazione, senza che sia necessario scrivere codice.


### <a name="datarpt"></a>Creazione di report di dati SQL tramite macchine virtuali

Quando un database include dati, è probabile che si desideri creare report usando tali dati. Azure consente di eseguire SQL Server Reporting Services \(SSRS\) in Macchine virtuali di Azure. Questa procedura è funzionalmente equivalente all'esecuzione di SQL Server Reporting Services in locale. È quindi possibile usare SSRS per eseguire report relativi ai dati archiviati in un database SQL di Azure. Nella [figura 5](#Fig5) viene mostrato il funzionamento di questo processo.

<a name="Fig5"></a>![Diagramma della creazione di report SQL][SQL-report]
 
**Figura 5: SQL Server Reporting Services in esecuzione in Macchine virtuali di Azure offre servizi di creazione di report per i dati disponibili in database SQL.**

Prima che un utente possa visualizzare un report, è necessario che l'aspetto di tale report venga definito \(passaggio 1\). Grazie a SSRS su una macchina virtuale, tale operazione può essere eseguita usando uno dei due strumenti seguenti: Server Data Tools, parte di SQL Server 2012, o il suo predecessore, Business Intelligence \(BI\) Development Studio. Analogamente a SSRS, queste definizioni di report vengono espresse nel linguaggio RDL \(Report Definition Language\). Dopo la creazione dei file RDL per un report, tali file vengono caricati in una macchina virtuale nel cloud \(passaggio 2\). La definizione del report è ora pronta per l'utilizzo.

Un utente dell'applicazione effettua quindi l'accesso al report \(passaggio 3\). L'applicazione passa la richiesta alla macchina virtuale con SSRS \(passaggio 4\), che contatta database SQL o altre origini dati per ottenere i dati necessari \(passaggio 5\). SSRS usa tali dati e i file RDL rilevanti per eseguire il rendering del report \(passaggio 6\), quindi restituisce il report all'applicazione \(passaggio 7\), che lo visualizza all'utente \(passaggio 8\).

Incorporare un report in un'applicazione, ovvero lo scenario appena illustrato, non costituisce l'unica opzione disponibile. È possibile visualizzare i report anche in Gestione report nella macchina virtuale, in SharePoint nella macchina virtuale o in altri modi. È inoltre possibile combinare i report, inserendo in un report un collegamento a un altro report.

SSRS in una macchina virtuale di Azure offre tutte le funzionalità disponibili in una soluzione di creazione di report nel cloud. Nei report è possibile usare qualsiasi origine dati supportata da SSRS. Le applicazioni e i report possono includere codice incorporato o assembly per il supporto dei comportamenti personalizzati. L'esecuzione e il rendering dei report risultano molto veloci, poiché il contenuto e il motore del server di report sono in esecuzione nello stesso server virtuale.



## <a name="tblstor"></a>Archiviazione tabelle

I dati relazionali risultano utili in molte situazioni, ma non costituiscono sempre la soluzione ottimale. Se l'applicazione necessita di accesso semplice e rapido a quantità molto elevate di dati poco strutturati, ad esempio, è possibile che un database relazionale non sia appropriato. Una tecnologia NoSQL rappresenta probabilmente un'opzione migliore in questo caso.

Archiviazione tabelle di Azure è un esempio di questo tipo di approccio NoSQL. Nonostante il nome, Archiviazione tabelle non supporta le tabelle relazionali standard. Offre invece un *archivio chiave/valore*, associando un set di dati a una chiave specifica, quindi consentendo a un'applicazione di accedere a tali dati fornendo la chiave. Nella [figura 6](#Fig6) sono illustrate le nozioni di base.

<a name="Fig6"></a>![Diagramma di Archiviazione tabelle][SQL-tblstor]
 
**Figura 6: Archiviazione tabelle di Azure è un archivio chiave/valore che offre un accesso semplice e rapido a quantità elevate di dati.**

Analogamente agli oggetti BLOB, ogni tabella è associata a un account di archiviazione di Azure. I nomi delle tabelle sono simili a quelli degli oggetti BLOB, con un URL nel formato

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Come mostrato nella figura, ogni tabella è suddivisa in alcune partizioni, ognuna delle quali può essere archiviata in una macchina separata. Questa è una forma di partizionamento orizzontale, analoga alla federazione SQL. Le applicazioni Azure e le applicazioni in esecuzione altrove possono accedere a una tabella usando il protocollo OData RESTful oppure la libreria del client di archiviazione di Azure.

Ogni partizione di una tabella include alcune *entità*, ognuna delle quali può contenere fino a 255 *proprietà*. Ogni proprietà dispone di un nome, un tipo \(ad esempio Binary, Bool, DateTime, Int o String\) e un valore. A differenza dell'archiviazione relazionale, queste tabelle non hanno schema fisso, quindi diverse entità nella stessa tabella possono includere proprietà di tipo diverso. È ad esempio possibile che un'entità sia associata a una proprietà String contenente un nome, mentre un'altra entità nella stessa tabella contenga due proprietà Int che includono un numero di ID cliente e una classificazione creditizia.

Per identificare un'entità specifica in una tabella, è necessario che un'applicazione specifichi la chiave dell'entità. La chiave è costituita da due parti, ovvero una *chiave di partizione* che identifica una partizione specifica e una *chiave di riga* che identifica un'entità all'interno di tale partizione. Nella [figura 6](#Fig6), ad esempio, il client richiede l'entità con chiave di partizione A e chiave di riga 3, e Archiviazione tabelle restituisce tale entità, incluse tutte le proprietà in essa contenute.

Questa struttura consente di usare tabelle di grandi dimensioni, poiché una singola tabella può contenere fino a 100 terabyte di dati, e offre un accesso rapido ai dati contenuti nelle tabelle. Comporta tuttavia alcune limitazioni. Ad esempio, non è previsto alcun supporto per aggiornamenti transazionali in più tabelle o in più partizioni di una singola tabella. È possibile raggruppare un set di aggiornamenti a una tabella in una transazione atomica solo se tutte le entità interessate si trovano nella stessa partizione. Non è inoltre possibile eseguire una query in una tabella in base ai valori delle rispettive proprietà e non sono consentiti i join tra più tabelle. A differenza dei database relazionali, inoltre, le tabelle non includono supporto per le stored procedure.

Archiviazione tabelle di Azure è una soluzione ideale per applicazioni che necessitano di accesso semplice e rapido a quantità elevate di dati poco strutturati. Ad esempio, un'applicazione Internet che archivia informazioni sui profili per molti utenti potrebbe usare le tabelle. La rapidità di accesso è essenziale in questa situazione e l'applicazione non necessita probabilmente di tutte le funzionalità di SQL. La rinuncia a tali funzionalità a favore di velocità e dimensioni elevate può risultare a volte consigliabile, quindi Archiviazione tabelle rappresenta la soluzione ideale per alcune situazioni.


## <a name="hadoop"></a>Hadoop

Le organizzazioni hanno creato data warehouse per decenni. Queste raccolte di informazioni, molto spesso archiviate in database relazionali, consentono agli utenti di usare i dati e apprendere dai dati in molti modi diversi. A tale scopo, in SQL Server, ad esempio, vengono spesso usati strumenti quali SQL Server Analysis Services.

Si supponga tuttavia che si desideri eseguire analisi su dati non relazionali. I dati possono essere disponibili in molte forme diverse, ovvero informazioni da sensori o tag RFID, file di log in server farm, dati clickstream prodotti da applicazioni Web, immagini da dispositivi diagnostici medici e altro ancora. Tali dati potrebbero essere inoltre di dimensioni elevate, troppo elevate per un utilizzo efficace con un data warehouse tradizionale. Problemi come questo, relativi ai Big Data, rari fino a qualche anno fa, sono ora diventati piuttosto comuni.

Per analizzare questo tipo di Big Data, il settore IT si è orientato in gran parte verso un'unica soluzione, ovvero la tecnologia open source Hadoop. Hadoop viene eseguito su un cluster di macchine fisiche o virtuali, distribuendo i dati usati in tali macchine ed elaborandoli in parallelo. Maggiore il numero di macchine disponibili per Hadoop, più veloce sarà il completamento dell'operazione da eseguire.

Questo tipo di situazione è ideale per il cloud pubblico. Invece di mantenere un numero elevato di server locali, che potrebbero rimanere inattivi per la maggior parte del tempo, l'esecuzione di Hadoop nel cloud consente di creare e pagare macchine virtuali solo quando sono necessarie. Una quantità sempre maggiore di Big Data da analizzare con Hadoop viene inoltre creata direttamente nel cloud, riducendo quindi la necessità di spostare i dati. Per semplificare l'utilizzo di queste sinergie, Microsoft offre un servizio Hadoop su Azure. Nella [figura 7](#Fig7) vengono illustrati i componenti principali di questo servizio.

<a name="Fig7"></a>![Diagramma di Hadoop][hadoop]

**Figura 7: Hadoop su Azure esegue processi MapReduce per l'elaborazione in parallelo dei dati usando più macchine virtuali.**

Per usare Hadoop su Azure, è innanzitutto necessario richiedere alla piattaforma cloud di creare un cluster Hadoop, specificando il numero di macchine virtuali necessarie. La configurazione di un cluster Hadoop non è un'operazione semplice, quindi è consigliabile che questa attività venga eseguita automaticamente da Azure. Al termine delle operazioni, si chiude il cluster. Non è necessario pagare risorse di calcolo non usate.

Un'applicazione Hadoop, definita in genere un *processo*, usa un modello di programmazione noto come *MapReduce*. Come illustrato nella figura, la logica per un processo MapReduce viene eseguita simultaneamente in più macchine virtuali. Grazie all'elaborazione in parallelo dei dati, Hadoop è in grado di analizzare i dati in modo molto più veloce rispetto alle soluzioni basate su una singola macchina.

In Azure i dati usati da un processo MapReduce vengono in genere inseriti in Archiviazione BLOB. In Hadoop, tuttavia, i processi MapReduce richiedono l'archiviazione dei dati nel file system *Hadoop Distributed File System \(HDFS\)*. HDFS è analogo ad Archiviazione BLOB, per quanto riguarda alcuni aspetti, ad esempio la replica di dati tra più server fisici. Invece di duplicare questa funzionalità, Hadoop su Azure espone l'Archiviazione BLOB tramite l'API HDFSl, come mostrato nella figura. Anche se la logica del processo MapReduce ritiene di accedere file HDFS normali, il processo sta in realtà usando dati in streaming dagli oggetti BLOB. Per supportare l'esecuzione di più processi sugli stessi dati, Hadoop su Azure consente inoltre la copia di dati dagli oggetti BLOB in HDFS completi in esecuzione nelle macchine virtuali.

Attualmente i processi MapReduce vengono in genere scritti in Java, un approccio supportato da Hadoop su Azure. Microsoft ha inoltre aggiunto supporto per la creazione di processi MapReduce in altri linguaggi, inclusi C\#, F\# e JavaScript. L'obiettivo consiste nel semplificare l'accesso a questa tecnologia per Big Data da parte di un gruppo sempre più ampio di sviluppatori.

Oltre a HDFS e MapReduce, Hadoop include altre tecnologie che consentono di analizzare i dati senza dovere scrivere processi MapReduce. Ad esempio, Pig è un linguaggio di alto livello progettato per l'analisi di Big Data, mentre Hive offre un linguaggio analogo a SQL, denominato HiveQL. Sia Pig che Hive generano in effetti processi MapReduce che elaborano dati HDFS, ma tale complessità viene nascosta agli utenti. Entrambi sono disponibili con Hadoop su Azure.

Microsoft offre inoltre un driver HiveQL per Excel. Grazie a un componente aggiuntivo di Excel, i business analyst possono creare query HiveQL e pertanto processi MapReduce direttamente da Excel, quindi elaborare e visualizzare i risultati usando PowerPivot e altri strumenti di Excel. Hadoop su Windows include anche altre tecnologie, ad esempio le librerie di apprendimento di Mahout, il sistema di graph mining Pegasus e altro ancora.

L'analisi dei Big Data è importante, quindi Hadoop svolge un ruolo essenziale. Grazie all'offerta di Hadoop come servizio gestito su Azure, oltre a collegamenti a strumenti noti quali Excel, Microsoft desidera rendere accessibile tale tecnologia a un gruppo più ampio di utenti.

In genere, i dati di qualsiasi tipo sono molto importanti. Azure include quindi una gamma di opzioni per la gestione dati e le analisi business. Indipendentemente dall'applicazione che si desidera creare, è probabile che in questa piattaforma cloud si sarà in grado di trovare lo strumento ideale per le proprie necessità.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

<!--HONumber=52-->
