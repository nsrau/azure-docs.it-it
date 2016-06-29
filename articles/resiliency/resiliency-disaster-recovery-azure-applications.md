<properties
   pageTitle="Ripristino di emergenza per le applicazioni basate su Azure | Microsoft Azure"
   description="Panoramiche tecniche e informazioni approfondite sulla progettazione e la creazione di applicazioni per il ripristino di emergenza in Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="hanuk;jroth;aglick"/>

#Ripristino di emergenza per le applicazioni basate su Microsoft Azure

Mentre la disponibilità elevata riguarda la gestione degli errori temporanei, il ripristino di emergenza riguarda la perdita irreversibile di funzionalità dell'applicazione. Si consideri ad esempio uno scenario in cui un'area diventa inaspettatamente inattiva. In questo caso, è necessario avere un piano per eseguire l'applicazione o accedere ai dati all'esterno dell'area di Azure. L'esecuzione di questo piano prevede l'impiego di persone, processi e applicazioni di supporto che consentono il funzionamento del sistema. Il livello di funzionalità del servizio durante un'emergenza è determinato dai proprietari dei processi aziendali e delle tecnologie, che definiscono le modalità operative del sistema in caso di emergenza. Questo livello di funzionalità può assumere forme diverse e prevedere la completa indisponibilità, una disponibilità parziale, con funzionalità ridotte o ritardi nell'elaborazione, o la disponibilità completa.

##Funzionalità del ripristino di emergenza in Azure

Come per la disponibilità, Azure offre [indicazioni tecniche sulla resilienza](./resiliency-technical-guidance.md) progettate per supportare il ripristino di emergenza. Esiste anche un rapporto tra alcune delle funzionalità di disponibilità di Azure e il ripristino di emergenza. Ad esempio, la gestione dei ruoli tra domini di errore aumenta la disponibilità di un'applicazione. Senza questo tipo di gestione, un errore hardware non gestito diventerebbe uno scenario di "emergenza". Di conseguenza, l'applicazione corretta delle funzionalità e delle strategie di disponibilità deve essere considerata una parte importante delle iniziative per rendere le applicazioni a prova di emergenza. Questo articolo, tuttavia, va oltre i problemi generali relativi alla disponibilità per illustrare eventi di emergenza più gravi e più rari.

##Più aree di data center

I data center di Azure sono dislocati in diverse aree del mondo. Questa infrastruttura supporta diversi scenari di ripristino di emergenza, ad esempio la replica geografica fornita dal sistema di Archiviazione di Azure nelle aree secondarie. Significa anche che è possibile distribuire in modo semplice ed economico un servizio cloud in diverse località del mondo. È utile confrontare questo con i costi e le difficoltà correlate alla gestione di data center propri in più aree. La distribuzione di dati e servizi in più aree consente di proteggere l'applicazione dal rischio di gravi interruzioni in una singola area.

##Gestione traffico di Azure

Quando si verifica un problema specifico di un'area, è necessario reindirizzare il traffico a servizi o distribuzioni in un'altra area. Il routing può essere eseguito manualmente, ma l'uso di un processo automatizzato risulta più efficiente. Gestione traffico di Azure è progettato per questa attività. Consente di gestire automaticamente il failover del traffico utente a un'altra area in caso di errore dell'area primaria. Poiché la gestione del traffico rappresenta un aspetto importante della strategia complessiva, è fondamentale comprendere le nozioni di base di Gestione traffico di Azure.

Nella figura seguente, gli utenti si connettono a un URL specificato per Gestione traffico (__http://myATMURL.trafficmanager.net__) e che astrae l'URL del sito effettivo (__http://app1URL.cloudapp.net__ e \___http://app2URL.cloudapp.net__). In base alla modalità di applicazione dei criteri di instradamento configurati per gli utenti, questi verranno indirizzati al sito corretto effettivo, quando necessario. Le opzioni dei criteri sono round robin, prestazioni o failover. Ai fini di questo articolo si esaminerà solo l'opzione di failover.

![Routing tramite Gestione traffico di Microsoft Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Quando si configura Gestione traffico, si fornisce un nuovo prefisso DNS di Gestione traffico. Si tratta del prefisso URL da fornire agli utenti per accedere al servizio. Gestione traffico astrae ora il bilanciamento del carico a un livello superiore e non a livello di area. Viene eseguito il mapping del DNS di Gestione traffico a un CNAME per tutte le distribuzioni gestite.

In Gestione traffico si specifica la priorità delle distribuzioni a cui gli utenti saranno instradati in caso di errori. Gestione traffico monitora gli endpoint delle distribuzioni e rileva se quella primaria non riesce. In caso di errore, analizza l'elenco delle distribuzioni con priorità e instrada gli utenti a quella successiva.

Nonostante spetti a Gestione traffico scegliere la destinazione in caso di failover, è possibile decidere se il dominio di failover è inattivo o attivo quando non è in modalità di failover. Questa funzionalità non è correlata a Gestione traffico di Azure. Gestione traffico rileva un errore nel sito primario ed esegue il rollover al sito di failover. Il rollover avviene indipendentemente dal fatto che il sito venga attualmente usato o meno dagli utenti.

Per altre informazioni sul funzionamento di Gestione traffico, vedere i collegamenti seguenti:

 * [Panoramica di Gestione traffico](../traffic-manager/traffic-manager-overview.md)
 * [Configurare metodo di routing failover](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##Scenari di emergenza in Azure

Le sezioni seguenti illustrano diversi tipi di scenari di emergenza. Le interruzioni dei servizi a livello di area non sono l'unica causa di errori a livello di applicazione. Errori di progettazione o di amministrazione possono determinare interruzioni dei servizi. È importante prendere in considerazione le possibili cause di un errore durante le fasi di progettazione e di test del piano di ripristino. Un buon piano sfrutta le funzionalità di Azure e le integra con strategie specifiche dell'applicazione. La risposta scelta è dettata dall'importanza dell'applicazione nonché dagli obiettivi punto di ripristino (RPO) e tempo di ripristino (RTO).

###Errore dell'applicazione

Gestione traffico di Microsoft Azure gestisce automaticamente gli errori derivanti dal software del sistema operativo o dall'hardware sottostante della macchina virtuale host. Azure crea una nuova istanza del ruolo in un server funzionante e la aggiunge alla rotazione del servizio di bilanciamento del carico. Se il numero di istanze del ruolo è maggiore di uno, Azure passa l'elaborazione alle altre istanze del ruolo in esecuzione, sostituendo il nodo che non funziona.

Esistono errori gravi delle applicazioni che si verificano indipendentemente dagli errori hardware o del sistema operativo. L'applicazione può smettere di funzionare a causa di eccezioni irreversibili dovute a logica non corretta o a problemi di integrità dei dati. È necessario integrare dati di telemetria sufficienti nel codice in modo che un sistema di monitoraggio possa rilevare le condizioni di errore e inviare notifiche a un amministratore dell'applicazione. Grazie alla piena conoscenza dei processi di ripristino di emergenza, l'amministratore può decidere di richiamare un processo di failover o di accettare semplicemente un'interruzione della disponibilità per risolvere gli errori critici.

###Danneggiamento dei dati

Azure archivia automaticamente i dati del database SQL di Azure e di Archiviazione di Azure tre volte, in modo ridondante, in diversi domini di errore nella stessa area. Se si usa la replica geografica, i dati vengono archiviati altre tre volte in un'area diversa. Se tuttavia gli utenti o l'applicazione danneggiano i dati nella copia primaria, questi vengono replicati rapidamente in altre copie. Ciò comporta purtroppo la creazione di tre copie di dati danneggiati.

Per gestire il possibile danneggiamento dei dati, sono disponibili due opzioni. Prima di tutto, è possibile gestire una strategia di backup personalizzata. È possibile archiviare i backup in Azure oppure in locale in base ai requisiti aziendali o alle normative di governance. Un'altra possibilità consiste nell'usare la nuova opzione Ripristino temporizzato per il database SQL. Per altre informazioni, vedere la sezione sulle [strategie dei dati per il ripristino di emergenza](#data-strategies-for-disaster-recovery).

###Interruzione della rete

Se parti della rete di Azure non sono accessibili, potrebbe non essere possibile accedere all'applicazione o ai dati. Se una o più istanze del ruolo non risultano disponibili a causa di problemi di rete, Azure sfrutta le istanze rimanenti dell'applicazione ancora disponibili. Se l'applicazione non può accedere ai dati a causa di un'interruzione della rete di Azure, è possibile ricorrere all'esecuzione locale in modalità con funzionalità ridotte usando i dati memorizzati nella cache. È necessario progettare la strategia di ripristino di emergenza per l'esecuzione in modalità con funzionalità ridotte dell'applicazione. Per alcune applicazioni questa opzione potrebbe non essere praticabile.

Un'altra opzione consiste nell'archiviare i dati in una posizione alternativa finché non viene ripristinata la connettività. Se la modalità con funzionalità ridotte non è praticabile, le opzioni rimanenti sono l'interruzione dell'applicazione o il failover a un'area alternativa. La progettazione di un'applicazione per l'esecuzione con funzionalità ridotte dipende da considerazioni sia di natura aziendale che tecnica. Questi aspetti sono illustrati dettagliatamente nella sezione [Esecuzione delle applicazioni con funzionalità ridotte](#degraded-application-functionality).

###Errore di un servizio dipendente

Azure fornisce molti servizi in cui si possono verificare periodicamente tempi di inattività. Si consideri ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache/). Questo servizio multi-tenant offre funzionalità di memorizzazione nella cache per le applicazioni. È importante valutare l'impatto sull'applicazione qualora un servizio dipendente non fosse disponibile. Per molti aspetti questo scenario è analogo a quello di un'interruzione della rete, tuttavia se si considera ogni servizio singolarmente, è possibile apportare miglioramenti al piano generale.

Cache Redis di Azure conferisce all'applicazione funzionalità di memorizzazione nella cache all'interno della distribuzione del servizio cloud, con conseguenti vantaggi per il ripristino di emergenza. Prima di tutto, il servizio ora viene eseguito su ruoli locali rispetto alla distribuzione. È quindi possibile monitorare e gestire in modo più efficace lo stato della cache come parte dei processi di gestione complessivi del servizio cloud. Questo tipo di memorizzazione nella cache espone anche nuove funzionalità. Una di queste è la disponibilità elevata per i dati memorizzati nella cache, che consente di proteggerli in caso di errore di un singolo nodo, mantenendo copie duplicate dei dati in altri nodi.

Si noti che la disponibilità elevata riduce la velocità effettiva e aumenta la latenza a causa dell'aggiornamento della copia secondaria durante le operazioni di scrittura. Raddoppia inoltre la quantità di memoria usata per ogni elemento, quindi è bene tenerne conto durante la pianificazione. Questo esempio specifico dimostra che ogni servizio dipendente può avere funzionalità che migliorano in generale la disponibilità e la resistenza agli errori irreversibili.

È consigliabile comprendere le implicazioni di un'interruzione per ogni servizio dipendente Nell'esempio di memorizzazione nella cache, potrebbe risultare possibile accedere ai dati direttamente da un database finché non si ripristina la cache. Si tratterebbe di una condizione con funzionalità ridotte in termini di prestazioni, ma sarebbero disponibili tutte le funzionalità correlate ai dati.

###Interruzione del servizio a livello di area

Gli errori indicati in precedenza sono principalmente errori gestibili nella stessa area di Azure. È tuttavia necessario prepararsi anche all'eventualità che si verifichi un'interruzione del servizio nell'intera area. Se si verifica un'interruzione del servizio a livello di area, le copie ridondanti locali dei dati non sono disponibili. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e delle tabelle in un'area diversa. Se Microsoft dichiara l'area perdita di un'area, Azure esegue di nuovo il mapping di tutte le voci DNS all'area con replica geografica.

>[AZURE.NOTE] Tenere presente che non è possibile controllare questo processo e che verrà eseguito solo in caso di interruzione del servizio a livello di area. Per questo motivo, è necessario affidarsi ad altre strategie di backup specifiche dell'applicazione per ottenere il massimo livello di disponibilità. Per altre informazioni, vedere la sezione sulle [strategie dei dati per il ripristino di emergenza](#data-strategies-for-disaster-recovery).

###Interruzione del servizio a livello di Azure

Nella pianificazione delle emergenze, è necessario considerare l'intera gamma delle possibili condizioni. Una delle interruzioni del servizio più gravi riguarda l'eventuale coinvolgimento contemporaneo di tutte le aree di Azure. Come per altre interruzioni del servizio, in questo caso è possibile scegliere di accettare il rischio di un tempo di inattività temporaneo. Interruzioni del servizio diffuse che si estendono a più aree dovrebbero essere più rare rispetto a interruzioni del servizio isolate che interessano servizi dipendenti o singole aree.

Per alcune applicazioni cruciali si può tuttavia decidere che è necessario avere un piano di backup anche per questo tipo di scenario. Il piano per questo evento può includere il failover in servizi in [cloud alternativi](#alternative-cloud) o [soluzioni ibride locali e cloud](#hybrid-on-premises-and-cloud-solution).

###Esecuzione delle applicazioni con funzionalità ridotte

Un'applicazione ben progettata usa in genere una raccolta di moduli che comunicano tra loro tramite l'implementazione di modelli di scambio di informazioni a regime di controllo libero. Un'applicazione adatta a scenari di ripristino di emergenza richiede la separazione delle attività a livello di modulo, in modo che l'interruzione di un servizio dipendente non causi l'interruzione dell'intera applicazione. Si consideri, ad esempio, un'applicazione Web di e-commerce di una società Y, che potrebbe essere costituita dai moduli seguenti:

 * __Catalogo prodotti__: consente agli utenti di esplorare i prodotti.
 * __Carrello acquisti__: consente agli utenti di aggiungere o rimuovere prodotti nel carrello.
 * __Stato dell'ordine__: mostra lo stato di spedizione degli ordini dell'utente.
 * __Invio dell'ordine__: finalizza la sessione di acquisto tramite l'invio dell'ordine con il pagamento.
 * __Elaborazione dell'ordine__: convalida l'integrità dei dati dell'ordine e verifica la disponibilità della quantità richiesta.

Quando una parte dipendente di un modulo dell'applicazione non è disponibile, come funzionerà il modulo finché non si provvede al ripristino? Un sistema ben strutturato implementa limiti di isolamento con la separazione delle attività sia in fase di progettazione che di esecuzione. È possibile classificare ogni errore come reversibile o irreversibile. Gli errori irreversibili interrompono il funzionamento del modulo, mentre quelli reversibili possono essere attenuati con alternative. Come illustrato nella sezione sulla disponibilità elevata, alcuni problemi possono essere nascosti agli utenti mediante la gestione degli errori e l'adozione di azioni alternative. In caso di un'interruzione del servizio più grave, l'applicazione potrebbe risultare completamente non disponibile. Una terza opzione consiste tuttavia nel continuare a rispondere alle richieste degli utenti in modalità con funzionalità ridotte.

Se ad esempio il database che ospita gli ordini è inattivo, il modulo di elaborazione degli ordini non riesce più a elaborare le transazioni di vendita. A seconda dell'architettura, potrebbe essere difficile, se non impossibile, continuare a usare le parti dell'applicazione per l'invio e l'elaborazione degli ordini. Se l'applicazione non è progettata per gestire questo scenario, potrebbe passare completamente in modalità offline.

In questo stesso scenario è tuttavia possibile che i dati dei prodotti siano archiviati in una posizione diversa. In questo caso, si può comunque continuare a usare il modulo del catalogo per visualizzare i prodotti. Nella modalità con funzionalità ridotte l'applicazione continua a presentare agli utenti le funzionalità disponibili, come la visualizzazione del catalogo prodotti. Altri componenti dell'applicazione, come le query sugli ordini o sull'inventario, non sono invece disponibili.

Un'altra variante della modalità con funzionalità ridotte è incentrata sulle prestazioni invece che sulle funzionalità. Si consideri ad esempio uno scenario in cui il catalogo prodotti viene memorizzato nella cache tramite il servizio Cache Redis di Azure. Se il servizio di caching non è più disponibile, l'applicazione può accedere direttamente alla risorsa di archiviazione server per recuperare i dati del catalogo. Questo tipo di accesso può tuttavia risultare più lento rispetto alla versione memorizzata nella cache. Per questo motivo, le prestazioni dell'applicazione risultano ridotte finché il servizio di caching non viene completamente ripristinato.

La decisione sulla misura in cui un'applicazione continuerà a funzionare in modalità con funzionalità ridotte implica considerazioni di natura sia aziendale che tecnica. L'applicazione deve anche stabilire come informare gli utenti dei problemi temporanei. In questo esempio l'applicazione può consentire la visualizzazione dei prodotti e la relativa aggiunta al carrello acquisti. Quando però l'utente prova a effettuare un acquisto, l'applicazione informa l'utente che il modulo di vendita è temporaneamente inaccessibile. Non è una situazione ideale per il cliente, ma impedisce un'interruzione del servizio a livello di applicazione.

##Strategie dei dati per il ripristino di emergenza

La gestione corretta dei dati rappresenta l'area più complessa di qualsiasi piano di ripristino di emergenza. Il ripristino dei dati è anche la parte del processo di ripristino che richiede in genere più tempo. Le diverse opzioni relative alle modalità con funzionalità ridotte implicano sfide complesse per il ripristino e la coerenza dei dati in seguito a un errore.

Uno dei fattori riguarda la necessità di ripristinare o mantenere una copia dei dati dell'applicazione. Questi dati verranno usati per finalità transazionali o di riferimento in un sito secondario. Una configurazione locale richiede un processo di pianificazione lungo e costoso per implementare una strategia di ripristino di emergenza in più aree. Per praticità, la maggior parte dei provider di servizi cloud, incluso Azure, consente l'immediata distribuzione di applicazioni in più aree. Queste aree sono distribuite geograficamente in modo che l'eventualità che si verifichi un'interruzione del servizio in più aree sia estremamente rara. La strategia di gestione dei dati tra più aree è uno dei fattori determinanti per il successo di qualsiasi piano di ripristino di emergenza.

Le sezioni seguenti illustrano le tecniche di ripristino di emergenza correlate ai backup dei dati, ai dati di riferimento e ai dati transazionali.

###Backup e ripristino

I backup regolari dei dati delle applicazioni possono supportare alcuni scenari di ripristino di emergenza. Le tecniche variano a seconda delle diverse risorse di archiviazione.

Per i livelli Basic, Standard e Premium del database SQL è possibile sfruttare la funzionalità di ripristino temporizzato del database. Per altre informazioni, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md). Un'altra possibilità consiste nell'usare la replica geografica attiva per il database SQL. Questa opzione replica automaticamente le modifiche al database nei database secondari nella stessa area di Azure o in un'area di Azure diversa e offre una potenziale alternativa per alcune delle tecniche di sincronizzazione dei dati più manuali illustrate in questo documento. Per altre informazioni, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](../sql-database/sql-database-geo-replication-overview.md).

È anche possibile usare un approccio più manuale per backup e ripristino. Usare il comando DATABASE COPY per creare una copia del database. È necessario usare questo comando per ottenere un backup con coerenza delle transazioni. È anche possibile usare il servizio di importazione/esportazione del database SQL di Azure. Questo servizio supporta l'esportazione dei database in file BACPAC archiviati nell'archivio BLOB di Azure.

La ridondanza predefinita del servizio di archiviazione di Azure crea due repliche del file di backup nella stessa area. La frequenza di esecuzione del processo di backup determina tuttavia l'RPO, ovvero la quantità di dati che potrebbero andare persi negli scenari di emergenza. Se ad esempio si esegue un backup ogni ora esatta e l'emergenza si verifica due minuti prima dello scadere dell'ora, si perderanno 58 minuti di dati creati dopo l'ultimo backup. Inoltre, per tutelarsi da un'eventuale interruzione a livello di un'area, è consigliabile copiare i file BACPAC in un'area alternativa. Sarà quindi possibile ripristinare questi backup nell'area alternativa. Per altre informazioni, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md).

Per Archiviazione di Azure è possibile sviluppare un processo di backup personalizzato o usare strumenti di backup di terze parti. Si noti che nella maggior parte delle progettazioni di applicazioni in cui le risorse di archiviazione fanno riferimento le une alle altre esistono complessità aggiuntive. Si consideri ad esempio un database SQL con una colonna di collegamento a un BLOB in Archiviazione di Azure. Se i backup non vengono eseguiti contemporaneamente, è possibile che il database contenga il puntatore a un BLOB di cui non è stato eseguito il backup prima dell'errore. L'applicazione o il piano di ripristino di emergenza devono implementare i processi per la gestione di questa incoerenza dopo un ripristino.

###Modelli di dati di riferimento per il ripristino di emergenza

I dati di riferimento sono dati di sola lettura che supportano le funzionalità di un'applicazione. In genere non sono soggetti a modifiche frequenti. Anche se il backup e il ripristino rappresentano un metodo per gestire le interruzioni a livello di area, l'RTO è relativamente lungo. Quando si distribuisce l'applicazione in un'area secondaria, è possibile adottare alcune strategie che migliorano l'RTO per i dati di riferimento.

Poiché i dati di riferimento vengono raramente modificati, è possibile migliorare l'RTO mantenendo una copia permanente di tali dati nell'area secondaria. Ciò consente di eliminare i tempi necessari per ripristinare i backup in caso di emergenza. Per soddisfare i requisiti di ripristino di emergenza in più aree, è necessario distribuire insieme l'applicazione e i dati di riferimento in più aree. Come indicato in [Modello dei dati di riferimento per la disponibilità elevata](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), i dati di riferimento possono essere distribuiti al ruolo stesso, a una risorsa di archiviazione esterna o a una combinazione di entrambi.

Il modello di distribuzione dei dati di riferimento tra nodi di calcolo soddisfa i requisiti di ripristino di emergenza in modo implicito. La distribuzione dei dati di riferimento al database SQL richiede la distribuzione di una copia dei dati in ogni area. La stessa strategia è applicabile ad Archiviazione di Azure. È necessario distribuire una copia di tutti i dati di riferimento archiviati nel servizio Archiviazione di Azure all'area primaria e a quella secondaria.

![Pubblicazione dei dati di riferimento nelle aree primaria e secondaria](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

È necessario implementare routine di backup personalizzate specifiche dell'applicazione per tutti i dati, inclusi quelli di riferimento. Le copie con replica geografica tra aree vengono usate solo in caso di interruzione del servizio a livello di un'intera area. Per evitare tempi di inattività prolungati, distribuire le parti cruciali dei dati dell'applicazione nell'area secondaria. Per un esempio di questa topologia, vedere il [modello attivo-passivo](#active-passive).

###Modelli di dati transazionali per il ripristino di emergenza

L'implementazione di una strategia che prevede la disponibilità di funzionalità complete in caso di emergenza richiede una replica asincrona dei dati transazionali nell'area secondaria. Le finestre temporali possibili in cui può essere eseguita la replica determinano le caratteristiche dell'RPO dell'applicazione. I dati persi durante la finestra di replica saranno comunque ripristinabili dall'area primaria e potranno anche essere uniti successivamente a quelli nell'area secondaria.

Le architetture di esempio seguenti offrono alcune idee sui diversi modi possibili di gestire i dati transazionali in uno scenario di failover. È importante notare che questi esempi non sono esaustivi. Ad esempio, le posizioni di archiviazione intermedie, come le code, possono essere sostituite dal database SQL di Azure. Le code stesse possono essere del servizio Archiviazione o del bus di servizio di Azure. Vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md). Anche le destinazioni di archiviazione server possono variare, ad esempio tabelle di Azure invece del database SQL. Potrebbero inoltre essere presenti ruoli di lavoro inseriti come intermediari in vari passaggi. L'importante è non emulare queste architetture in modo preciso, ma valutare varie alternative per il ripristino di dati transazionali e dei moduli correlati.

####Replica di dati transazionali in preparazione del ripristino di emergenza

Si consideri un'applicazione che usa le code di Archiviazione di Azure per i dati transazionali In questo modo i ruoli di lavoro possono elaborare i dati transazionali nel database server in un'architettura separata. Ciò richiede l'uso di una qualche forma di memorizzazione temporanea nella cache per le transazioni, se i ruoli front-end richiedono l'esecuzione immediata di query sui dati. A seconda del livello di tolleranza alla perdita dei dati, si può scegliere di replicare le code, il database o tutte le risorse di archiviazione. Limitandosi alla replica del database, in caso di interruzione dell'area primaria, i dati nelle code possono essere comunque ripristinati quando l'area primaria torna disponibile.

Il diagramma seguente illustra un'architettura in cui il database server è sincronizzato tra più in aree.

![Replica di dati transazionali in preparazione del ripristino di emergenza](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

Il problema maggiore nell'implementazione di questa architettura consiste nella strategia di replica tra aree. Azure offre un servizio di sincronizzazione dati SQL per questo tipo di replica. Questo servizio è tuttavia ancora in anteprima e non è consigliabile usarlo in ambienti di produzione. Per altre informazioni, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md). Per le applicazioni di produzione, è necessario investire in una soluzione di terze parti o creare una logica di replica personalizzata nel codice. In base all'architettura, la replica può essere bidirezionale, che è anche più complessa.

In una potenziale implementazione si potrebbe usare la coda intermedia illustrata nell'esempio precedente. Il ruolo di lavoro che elabora i dati nella destinazione di archiviazione finale può apportare la modifica sia nell'area primaria che in quella secondaria. Non si tratta di attività semplici, ma le istruzioni complete relative al codice di replica non rientrano nell'ambito di questo articolo. L'aspetto importante è che occorre dedicare molto tempo e attività di test alla modalità di replica dei dati nell'area secondaria. Si consiglia di eseguire anche attività di elaborazione e test aggiuntive per accertarsi che i processi di failover e ripristino gestiscano correttamente eventuali incoerenze dei dati o transazioni duplicate.

>[AZURE.NOTE] Questo articolo si incentra soprattutto sulla piattaforma distribuita come servizio (PaaS). Esistono comunque altre opzioni di replica e disponibilità per applicazioni ibride che usano macchine virtuali di Azure. Queste applicazioni ibride usano l'infrastruttura come servizio (IaaS) per ospitare SQL Server in macchine virtuali in Azure. Ciò consente di adottare gli approcci tradizionali di SQL Server alla disponibilità, ad esempio Gruppi di disponibilità AlwaysOn o il log shipping. Alcune tecniche, come AlwaysOn, funzionano solo tra istanze di SQL Server locali e macchine virtuali di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####Applicazione in modalità con funzionalità ridotte per l'acquisizione di transazioni

Si consideri una seconda architettura funzionante in modalità con funzionalità ridotte. L'applicazione nell'area secondaria disattiva tutte le funzionalità, ad esempio creazione di report, business intelligence o svuotamento delle code. Accetta solo i tipi di flussi di lavoro transazionali più importanti, secondo quanto definito dai requisiti aziendali. Il sistema acquisisce le transazioni e le scrive nelle code. Può differire l'elaborazione dei dati durante la fase iniziale dell'interruzione del servizio. Se il sistema nell'area primaria si riattiva entro l'intervallo di tempo previsto, le code possono essere svuotate dai ruoli di lavoro dell'area primaria, eliminando così la necessità di unire i database. Se l'interruzione del servizio nell'area primaria si prolunga oltre l'intervallo accettabile, può iniziare a elaborare le code.

In questo scenario il database nell'area secondaria contiene dati transazionali incrementali che devono essere uniti dopo la riattivazione dell'area primaria. Il diagramma seguente illustra questa strategia per l'archiviazione temporanea di dati transazionali fino al ripristino dell'area primaria.

![Applicazione in modalità con funzionalità ridotte per l'acquisizione di transazioni](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Per altre informazioni sulle tecniche di gestione dati per applicazioni Azure resilienti, vedere l'articolo su [FailSafe: Informazioni aggiuntive per architetture cloud resilienti](https://channel9.msdn.com/Series/FailSafe).

##Topologie di distribuzione per il ripristino di emergenza

È necessario preparare le applicazioni cruciali nell'eventualità di un'interruzione del servizio a livello di area. A questo scopo, integrare una strategia di distribuzione in più aree nella pianificazione operativa.

Le distribuzioni in più aree possono includere processi IT avanzati per pubblicare l'applicazione e i dati di riferimento nell'area secondaria in seguito a un'emergenza. Se l'applicazione richiede il failover immediato, il processo di distribuzione può comportare una configurazione di tipo attivo/passivo o attivo/attivo. Questo tipo di distribuzione include istanze esistenti dell'applicazione in esecuzione nell'area alternativa. Uno strumento di routing come Gestione traffico di Azure offre servizi di bilanciamento del carico a livello di DNS. Può rilevare le interruzioni del servizio e indirizzare gli utenti in aree diverse, quando necessario.

Per un ripristino di emergenza di Azure corretto, è importante definirne l'architettura all'interno della soluzione fin dall'inizio. Il cloud offre opzioni aggiuntive per il ripristino da condizioni di errore durante un'emergenza che non sono disponibili in un provider di hosting tradizionale. In particolare, è possibile allocare in modo rapido e dinamico le risorse a un'altra area. Si evitano così costi consistenti per risorse inattive in attesa che si verifichi un errore.

Le sezioni seguenti illustrano diverse topologie di distribuzioni per il ripristino di emergenza. Una maggiore disponibilità comporta generalmente un compromesso in termini di aumento dei costi o della complessità.

###Distribuzione in una singola area

Le distribuzioni in una singola area non sono in effetti topologie di ripristino di emergenza, ma sono illustrate come termine di confronto rispetto ad altre architetture. Le distribuzioni di questo tipo sono comuni per le applicazioni in Azure. Non si tratta comunque di una valida alternativa a un piano di ripristino di emergenza.

Il diagramma seguente illustra un'applicazione in esecuzione in una singola area di Azure. Gestione traffico di Azure e l'uso di domini di errore e di aggiornamento aumentano la disponibilità dell'applicazione nell'area.

![Distribuzione in una singola area](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

In questo caso è evidente che il database rappresenta un singolo punto di errore. Anche se Azure replica i dati tra diversi domini di errore in repliche interne, il tutto avviene nella stessa area. L'applicazione non può quindi supportare un errore irreversibile. Se l'area diventa inattiva, tutti i domini di errore diventano inattivi, incluse tutte le istanze dei servizi e le risorse di archiviazione.

Per tutte le applicazioni tranne le meno importanti è necessario definire un piano per la distribuzione tra più aree. È inoltre consigliabile tenere conto dell'RTO e dei vincoli di costi quando si valuta la topologia di distribuzione da usare.

Di seguito vengono esaminati modelli specifici che supportano il failover tra aree diverse. In entrambi questi esempi si usano due aree per descrivere il processo.

###Ridistribuzione in un'area di Azure secondaria

Nel modello di ridistribuzione a un'area secondaria, solo quella primaria dispone di applicazioni e database in esecuzione. L'area secondaria non è configurata per un failover automatico. Quando si verifica un'emergenza, è quindi necessario attivare tutti i componenti del servizio nella nuova area. Sono inclusi il caricamento di un servizio cloud in Azure, la distribuzione del servizio cloud, il ripristino dei dati e la modifica del DNS per il reindirizzamento del traffico.

Anche se si tratta della più economica tra le opzioni per più aree, è la soluzione con le funzionalità peggiori in termini di RTO. In questo modello i backup del database e del pacchetto del servizio vengono archiviati in locale o nell'archiviazione BLOB di Azure dell'area secondaria. È tuttavia necessario distribuire un nuovo servizio e ripristinare i dati prima di tornare operativi. Anche se si automatizza completamente il trasferimento dati dall'archivio di backup, l'attivazione del nuovo ambiente di database richiedere comunque molto tempo. Lo spostamento di dati dall'archiviazione del disco di backup al database vuoto nell'area secondaria costituisce la parte più onerosa del processo di ripristino. Si tratta tuttavia di un'operazione necessaria per portare il nuovo database a uno stato operativo dal momento che non viene replicato.

L'approccio migliore consiste nell'archiviare i pacchetti del servizio nell'archiviazione BLOB nell'area secondaria. Questo elimina la necessità di caricare il pacchetto in Azure, come invece avviene quando si esegue la distribuzione da un computer di sviluppo locale. I pacchetti del servizio possono essere distribuiti rapidamente in un nuovo servizio cloud dall'archiviazione BLOB tramite script di PowerShell.

Questa opzione è utile solo per applicazioni non cruciali che possono tollerare un RTO elevato. Ad esempio, questa soluzione può funzionare per un'applicazione che può rimanere non disponibile per diverse ore, ma che deve tornare operativa entro 24 ore.

![Ridistribuzione in un'area di Azure secondaria](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###Modello attivo/passivo

Il modello attivo/passivo rappresenta la scelta preferita da molte società. Offre miglioramenti in termini di RTO con un aumento dei costi relativamente contenuto rispetto al modello di ridistribuzione. Anche questo scenario prevede un'area di Azure primaria e una secondaria. Tutti il traffico è indirizzato alla distribuzione attiva nell'area primaria. L'area secondaria è preparata in modo ottimale per il ripristino di emergenza, perché il database è in esecuzione in entrambe le aree e tra di esse è inoltre presente un meccanismo di sincronizzazione. Questo approccio in stand-by può comportare due varianti: un approccio con il solo database o una distribuzione completa nell'area secondaria.

####Solo database

Nella prima variante del modello attivo/passivo, l'applicazione del servizio cloud è distribuita solo nell'area primaria. Tuttavia, a differenza del modello di ridistribuzione, il contenuto del database è sincronizzato con entrambe le aree. Per altre informazioni, vedere la sezione [Modelli di dati transazionali per il ripristino di emergenza](#transactional-data-pattern-for-disaster-recovery). In caso di emergenza, i requisiti di attivazione sono minori. Si avvia l'applicazione nell'area secondaria, si modificano le stringhe di connessione al nuovo database e si modificano le voci DNS per reindirizzare il traffico.

Come nel modello di ridistribuzione, i pacchetti del servizio devono essere già archiviati nell'archiviazione BLOB di Azure nell'area secondaria per una distribuzione più rapida. A differenza del modello di ridistribuzione, non si incorre nella maggior parte degli oneri aggiuntivi necessari per l'operazione di ripristino del database. Il database è pronto e in esecuzione. Ciò consente di risparmiare una notevole quantità di tempo, rendendo questo un modello conveniente per il ripristino di emergenza. È anche il modello di ripristino di emergenza più diffuso.

![Modello attivo/passivo, solo database](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####Replica completa

Nella seconda variante del modello attivo/passivo è presente una distribuzione completa sia nell'area primaria che in quella secondaria. Questa distribuzione include i servizi cloud e un database sincronizzato. Tuttavia, solo l'area primaria gestisce attivamente le richieste di rete degli utenti. Quella secondaria si attiva solo quando nell'area primaria si verifica un'interruzione del servizio. In questo caso tutte le richieste di rete vengono reindirizzate all'area secondaria. Gestione traffico di Azure può gestire automaticamente questo tipo di failover.

Il failover avviene più rapidamente rispetto alla variante con il solo database, perché i servizi sono già distribuiti. Questo modello assicura un RTO molto basso: l'area di failover secondaria deve essere immediatamente pronta in caso di problemi con quella primaria.

Oltre a un tempo di risposta più rapido, questo modello implica il vantaggio della pre-allocazione e distribuzione dei servizi di backup. Non occorre preoccuparsi del fatto che un'area non abbia spazio sufficiente per allocare nuove istanze in caso di emergenza. Si tratta di un aspetto importante se la capacità dell'area di Azure secondaria è prossima all'esaurimento. Non esiste la garanzia (contratto di servizio) che sia immediatamente possibile distribuire numerosi nuovi servizi cloud in qualsiasi area.

Per ottenere il tempo di risposta più rapido con questo modello, è necessario avere un numero di istanze del ruolo simile nell'area primaria e in quella secondaria. Nonostante i vantaggi, i costi associati alle istanze di calcolo inutilizzate sono elevati e questa potrebbe non essere quindi la scelta finanziaria più prudente. Per questo motivo, nell'area secondaria spesso viene usata una versione leggermente ridotta dei servizi cloud. È quindi possibile eseguire rapidamente il failover e aumentare il numero di istanze della distribuzione secondaria, se necessario. È consigliabile automatizzare il processo di failover in modo che, se l'area primaria non è accessibile, vengano attivate istanze aggiuntive a seconda del carico. Ciò può implicare un meccanismo di ridimensionamento automatico come i [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Il diagramma seguente illustra un modello in cui l'area primaria e quella secondaria contengono una distribuzione completa del servizio cloud secondo il modello attivo/passivo.

![Modello attivo/passivo, replica completa](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###Modello attivo/attivo

A questo punto, è probabilmente possibile dedurre l'evoluzione di questi modelli, ovvero che alla riduzione dell'RTO corrisponde un aumento di costi e complessità. La soluzione di tipo attivo/attivo contraddice questa tendenza per quanto riguarda i costi.

In un modello attivo/attivo i servizi cloud e il database sono distribuiti completamente in entrambe le aree. A differenza del modello attivo/passivo, entrambe le aree ricevono il traffico degli utenti. Questa opzione implica il tempo di ripristino più rapido. I servizi sono già ridimensionati per gestire una parte del carico in ognuna delle aree e il DNS è già abilitato all'uso dell'area secondaria. La complessità aggiuntiva consiste nel determinare la modalità di indirizzamento degli utenti all'area appropriata. È possibile adottare una pianificazione di tipo round-robin, ma è più probabile che determinati utenti preferiscano usare un'area specifica in cui risiede la copia primaria dei propri dati.

In caso di failover, è sufficiente disabilitare il DNS per l'area primaria, in modo da indirizzare tutto il traffico a quella secondaria.

Anche in questo modello esistono alcune varianti. Ad esempio, il diagramma seguente illustra un modello in cui la copia master del database è di proprietà dell'area primaria. I servizi cloud in entrambe le aree scrivono sul database primario. La distribuzione secondaria può leggere il database primario o quello replicato. La replica in questo esempio è unidirezionale.

![Modello attivo/attivo](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Il diagramma precedente presenta un aspetto negativo dell'architettura di tipo attivo/attivo. La seconda area deve accedere al database nella prima, perché la copia master risiede in quest'ultima. Le prestazioni si riducono sensibilmente quando si accede a dati dall'esterno di un'area. Nelle chiamate di database tra aree è necessario prendere in considerazione un tipo di strategia di invio in batch per migliorare le prestazioni. Per altre informazioni, vedere [Come usare l'invio in batch per migliorare le prestazioni delle applicazioni di database SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Un'architettura alternativa può prevedere che ogni area acceda direttamente il proprio database. In questo modello è necessario un tipo di replica bidirezionale per sincronizzare i database in ogni area.

Nel modello attivo/attivo può non essere necessario avere nell'area primaria lo stesso numero di istanze del modello attivo/passivo. Se in un'architettura di tipo attivo/passivo sono disponibili dieci istanze nell'area primaria, in un'architettura di tipo attivo/attivo potrebbero esserne sufficienti solo cinque in ogni area. Entrambe le aree ora condividono il carico. Ciò può determinare una riduzione dei costi rispetto al modello attivo/passivo se si tiene in warm standby l'area passiva con dieci istanze in attesa di failover.

Tenere presente che fino a quando non si ripristina l'area primaria, quella secondaria potrebbe ricevere un aumento improvviso di nuovi utenti. Se sono presenti 10.000 utenti in ogni server quando si verifica un'interruzione del servizio nell'area primaria, quella secondaria potrebbe trovarsi improvvisamente a doverne gestire 20.000. Le regole di monitoraggio nell'area secondaria devono rilevare questo aumento e raddoppiare le istanze in questa area. Per altre informazioni su questo aspetto, vedere la sezione sul [rilevamento degli errori](#failure-detection).

##Soluzioni ibride locali e cloud

Un'altra strategia per il ripristino di emergenza consiste nel progettare un'applicazione ibrida eseguita sia in locale che nel cloud. A seconda dell'applicazione, l'area primaria può trovarsi in una posizione o nell'altra. Si considerino le architetture precedenti e si immagini l'area primaria o secondaria come una posizione locale.

Le architetture ibride presentano alcune sfide. Per prima cosa, la maggior parte di questo articolo è incentrata su modelli di architettura PaaS. Le applicazioni PaaS in Azure si basano in genere su costrutti specifici di Azure, ad esempio ruoli, servizi cloud e Gestione traffico. Per creare una soluzione locale per questo tipo di applicazione PaaS, è necessaria un'architettura sostanzialmente diversa. Ciò potrebbe non essere fattibile dal punto di vista della gestione o dei costi.

Una soluzione ibrida per il ripristino di emergenza presenta tuttavia meno ostacoli per le architetture tradizionali che sono state semplicemente trasferite nel cloud. Questo accade per le architetture che utilizzano la tecnologia IaaS. Le applicazioni IaaS usano macchine virtuali nel cloud per le quali possono esistere equivalenti diretti in locale. L'uso di reti virtuali consente anche di connettere macchine nel cloud a risorse di rete locali. Ciò offre diverse opportunità che non sono possibili con le applicazioni solo PaaS. Ad esempio, SQL Server può trarre vantaggio dalle soluzioni di ripristino di emergenza come Gruppi di disponibilità AlwaysOn e il mirroring del database. Per informazioni dettagliate, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Le soluzioni di tipo IaaS forniscono inoltre alle applicazioni locali una modalità più semplice per usare Azure come opzione di failover. È possibile avere un'applicazione completamente funzionante in un'area locale esistente. Se tuttavia non sono disponibili risorse sufficienti per gestire un'area geograficamente separata per il failover, è possibile scegliere di usare macchine e reti virtuali per eseguire l'applicazione in Azure. In questo caso, è necessario definire i processi per la sincronizzazione dei dati nel cloud. La distribuzione di Azure diventa quindi l'area secondaria da usare per il failover. L'area primaria rimane l'applicazione locale. Per altre informazioni sull'architettura IaaS e le relative funzionalità, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

##Cloud alternativi

In alcune situazioni nemmeno l'affidabilità di Microsoft Cloud può soddisfare le regole di conformità interne o i criteri richiesti dall'organizzazione. Neanche i sistemi di backup meglio progettati e la migliore preparazione possono mettere al riparo da un'interruzione globale del servizio di un provider di servizi cloud.

È consigliabile confrontare i requisiti di disponibilità con i costi e la complessità di una disponibilità maggiore. Eseguire un'analisi dei rischi e definire gli obiettivi per RTO e RPO della soluzione. Se l'applicazione non può tollerare alcun tipo di interruzione, può essere opportuno valutare l'uso di un'altra soluzione cloud. A meno che l'intera rete Internet non diventi inaccessibile, un'altra soluzione cloud può essere ancora disponibile se Azure dovesse diventare inaccessibile a livello globale.

Come nello scenario ibrido, le distribuzioni di failover nelle architetture di ripristino di emergenza precedenti possono essere presenti anche in un'altra soluzione cloud. I siti cloud di ripristino di emergenza alternativi devono essere usati solo per soluzioni con un RTO che non ammette periodi di inattività, se non brevissimi. Si noti che una soluzione che usa un sito di ripristino di emergenza esterno ad Azure richiede maggiore impegno per le operazioni di configurazione, sviluppo, distribuzione e gestione. Risulta inoltre più difficile implementare le procedure consigliate nelle architetture tra cloud. Nonostante le piattaforme cloud si basino su concetti generali simili, le API e le architetture sono diverse.

Se si decide di suddividere il ripristino di emergenza tra piattaforme diverse, è consigliabile progettare livelli di astrazione all'interno della soluzione. In questo modo non sarà necessario sviluppare e gestire due versioni diverse della stessa applicazione per diverse piattaforme cloud in caso di emergenza. Come nello scenario ibrido, l'uso di macchine virtuali di Azure o del servizio contenitore di Azure potrebbe essere più facile in questi casi rispetto alle progettazioni PaaS specifiche del cloud.

##Automazione

Alcuni modelli fin qui illustrati richiedono l'attivazione rapida delle distribuzioni offline, nonché il ripristino di parti specifiche di un sistema. L'automazione, ovvero lo scripting, supporta la possibilità di attivare le risorse su richiesta e di distribuire rapidamente le soluzioni. In questo articolo l'automazione correlata al ripristino di emergenza corrisponde a [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), ma l'[API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) resta sempre un'opzione.

Lo sviluppo di script facilita la gestione delle fasi del ripristino di emergenza che Azure non gestisce in modo trasparente. Questo implica il vantaggio di produrre risultati coerenti in ogni occasione, riducendo quindi al minimo la possibilità di errore umano. La disponibilità di script di ripristino di emergenza predefiniti consente inoltre di ridurre i tempi di ricompilazione di un sistema e dei relativi componenti durante una condizione di emergenza. Quando il sito è inattivo e si continua a perdere denaro, non è certo il momento adatto per provare a definire manualmente una procedura di ripristino.

Dopo aver creato gli script, è consigliabile testarli più volte dall'inizio alla fine. Dopo avere verificato le funzionalità di base, testarli nella [simulazione di un'emergenza](#disaster-simulation). Ciò consente di rilevare difetti negli script o nei processi.

Una procedura consigliata per l'automazione consiste nel creare un repository di script di PowerShell, o script dell'interfaccia della riga di comando, per il ripristino di emergenza di Azure. Contrassegnarli e classificarli chiaramente per agevolare la ricerca. Designare una persona che gestisca il repository e il controllo delle versioni degli script. Documentarli accuratamente con spiegazioni dei parametri ed esempi di utilizzo. Accertarsi anche che la documentazione sia sincronizzata con le distribuzioni di Azure. Ciò evidenzia lo scopo di avere una persona responsabile di tutte le parti del repository.

##Rilevamento degli errori

Per poter gestire correttamente i problemi relativi a disponibilità e ripristino di emergenza, è necessario poter rilevare e diagnosticare gli errori. È consigliabile eseguire attività avanzate di monitoraggio di server e distribuzioni, per sapere rapidamente quando un sistema o alcuni suoi componenti smettono di funzionare. Parte di questa attività può essere eseguita dagli strumenti di monitoraggio dello stato complessivo del servizio cloud e delle relative dipendenze. Uno strumento di Microsoft è [System Center 2016](https://www.microsoft.com/it-IT/server-cloud/products/system-center-2016/). Anche strumenti di terze parti possono offrire funzionalità di monitoraggio. La maggior parte delle soluzioni di monitoraggio controlla i contatori delle prestazioni chiave e la disponibilità dei servizi.

Anche se questi strumenti sono essenziali, non sostituiscono la necessità di pianificare il rilevamento e la segnalazione degli errori in un servizio cloud. È necessaria una pianificazione per un uso corretto della Diagnostica di Microsoft Azure. Anche contatori delle prestazioni personalizzati o voci del log eventi possono far parte della strategia complessiva. In questo modo è possibile avere più dati in caso di errore per diagnosticare rapidamente il problema e ripristinare tutte le funzionalità. Sono inoltre disponibili altre metriche che gli strumenti di monitoraggio possono usare per determinare l'integrità di un'applicazione. Per altre informazioni, vedere [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Per informazioni su come pianificare un "modello di integrità" generale, vedere l'articolo su [FailSafe: Informazioni aggiuntive per architetture cloud resilienti](https://channel9.msdn.com/Series/FailSafe).

##Simulazione di un'emergenza

I test di simulazione prevedono la creazione di situazioni reali nell'ambiente operativo effettivo, per osservare in che modo reagiscono i membri del team. Consentono inoltre di valutare l'efficienza delle soluzioni descritte nel piano di ripristino. Le simulazioni devono essere eseguite in modo che gli scenari creati non interrompano le attività aziendali effettive, senza tuttavia risultare meno "reali".

Provare a progettare un tipo di "centralino" nell'applicazione per simulare manualmente problemi di disponibilità. Ad esempio, tramite un commutatore software, attivare eccezioni di accesso al database per il modulo di elaborazione degli ordini, causandone il malfunzionamento. Approcci semplici di questo tipo possono essere adottati per altri moduli a livello di interfaccia di rete.

Durante la simulazione vengono evidenziati eventuali problemi non gestiti in modo adeguato. Gli scenari simulati devono essere completamente controllabili. In questo modo, anche se il piano di ripristino ha esito negativo, è possibile ripristinare la situazione senza causare danni significativi. È inoltre importante che i responsabili ad alto livello siano informati sui tempi e sulle modalità di esecuzione delle simulazioni. Questo piano deve includere informazioni sul tempo o sulle risorse la cui produttività potrebbe essere compromessa durante i test di simulazione. Quando si sottopone a test il proprio piano di ripristino di emergenza, è anche importante definire le modalità di valutazione dei risultati.

Esistono diverse altre tecniche utilizzabili per testare i piani di ripristino di emergenza. La maggior parte è tuttavia costituita da varianti delle tecniche di base. L'obiettivo principale dei test consiste nel valutare la fattibilità e l'efficacia del piano di ripristino. I test del piano di ripristino di emergenza sono incentrati sui dettagli per individuare falle nel piano di base

##Passaggi successivi

Questo articolo fa parte di una serie dedicata al [ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L'articolo precedente della serie è [Disponibilità elevata per le applicazioni basate su Microsoft Azure](./resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0615_2016-->