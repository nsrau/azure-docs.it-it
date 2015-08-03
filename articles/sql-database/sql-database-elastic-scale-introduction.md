<properties 
    pageTitle="Database SQL di Azure - strumenti di database elastici " 
    description="Ridimensionare facilmente le risorse del database nel cloud utilizzando gli strumenti dei database elastici." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/24/2015" 
    ms.author="sidneyh"/>

# Database SQL di Azure - strumenti di database elastici

## Promesse e sfide

Il database SQL di Azure offre le risorse di database virtualmente illimitate, nonché elasticità per carichi di lavoro transazionali. L’abilitazione di questa funzionalità è facilitata da funzionalità come la **libreria client di database elastici** e **lo strumento di suddivisione-unione**, noti insieme con il nome di **strumenti di database elastici**. Questi componenti sono progettati per semplificare lo sviluppo e la gestione di soluzioni per database partizionati, descritti in questo articolo.

Anche se il raggiungimento dell’elasticità e della scalabilità delle applicazioni cloud è stato semplice per il calcolo e l’archiviazione BLOB, questo rimane una sfida per l’elaborazione dei dati con stato in database relazionali. I problemi sono emersi principalmente nei due seguenti scenari:

* Aumento e riduzione della capacità per la parte del carico di lavoro relativa al database relazionale.
* Gestione degli hotspot che potrebbero verificarsi interessando un subset specifico di dati, ad esempio clienti finali (tenant) particolarmente impegnati.

In genere, questi scenari sono stati affrontati effettuando investimenti in server di database di scala maggiore per supportare l'applicazione. Questa opzione offre però possibilità limitate negli ambienti cloud, in cui l'intera elaborazione viene eseguita su appositi componenti hardware predefiniti. Invece, la distribuzione dei dati e dell’elaborazione su molti database strutturati in modo identico (un modello di scalabilità orizzontale noto come partizionamento orizzontale) fornisce un'interessante alternativa agli approcci tradizionali di scalabilità verticale sia in termini di costi che flessibilità.

Le funzionalità dei database elastici del database di SQL Azure rendono la scalabilità orizzontale più semplice e più flessibile. Se si sviluppano applicazioni di software come servizio, i **pool di database elastici** semplificano la creazione di singoli database per ogni cliente finale o tenant e consentono a ciascuno di aumentare o ridurre dinamicamente il footprint risorse automaticamente mantenendo un budget prevedibile. I **processi dei database elastici** consentono di eseguire in modo affidabile le operazioni di gestione su larga scala in un intero set di database tramite l'esecuzione di script T-SQL per eseguire le modifiche dello schema, la gestione delle credenziali o qualsiasi altra operazione di manutenzione del database desiderata. Per ulteriori informazioni sui processi dei database elastici, vedere [Panoramica dei processi dei database elastici](sql-database-elastic-jobs-overview.md).

Se si utilizzano database separati per ogni tenant o si comprimono più intervalli di dati in ogni raccolta di database, la libreria client dei database elastici e lo strumento di suddivisione/unione consentono di ridurre lo sforzo necessario per creare e gestire applicazioni che sfruttano i vantaggi del partizionamento orizzontale. Anziché scrivere codice per documentare il layout dei dati tra database e indirizzare le connessioni sul percorso corretto, la libreria client consente di concentrarsi sulla logica aziendale dell'applicazione.

## Scalabilità orizzontale e verticale
La figura seguente mostra le dimensioni orizzontali e verticali della scalabilità.
 
![Scalabilità orizzontale e verticale][4]

Per scalabilità orizzontale si intende l’aggiunta o la rimozione di database per regolare le prestazioni complessive o la capacità. Ciò viene spesso definito "scaling out". Il partizionamento orizzontale è un approccio comune alla scalabilità orizzontale in cui i dati vengono partizionati tra un insieme di database strutturati in modo identico.

Per scalabilità verticale si intende l’aumento o la riduzione del livello delle prestazioni di un singolo database, ed è nota anche come "scaling up".

La maggior parte delle applicazioni di database su scala cloud utilizzano una combinazione di questi due strategie. Un'applicazione di software come servizio può, ad esempio, utilizzare la scalabilità orizzontale per eseguire il provisioning di nuovi clienti finali e la scalabilità verticale per consentire l’espansione o la riduzione delle risorse del database di ogni cliente finale in base alle esigenze del carico di lavoro.

Gli strumenti dei database elastici semplificano la creazione di applicazioni che si basano sul partizionamento orizzontale, gestendo l'infrastruttura della scalabilità orizzontale. Utilizzando un pool di database elastici per la famiglia di database, la scalabilità verticale viene gestita automaticamente dal sistema. Con i pool, l’utente è responsabile dell’impostazione dei limiti generali del sistema e dell'intervallo di variazione che si desidera consentire a ogni database. È inoltre possibile modificare manualmente le edizioni dei database o i livelli delle risorse per i database che non fanno parte di pool di database elastici. Ad esempio, è possibile creare una nuova partizione per gestire un afflusso ingente di dati alla fine di un mese. Mentre arrivano i nuovi dati, il partizionamento viene aumentato e quindi viene ridotto con la diminuzione dell'afflusso di dati.

Per ulteriori informazioni sui pool di database elastici, vedere [Panoramica dei pool di database elastici](sql-database-elastic-pool.md).

## Funzionalità degli strumenti dei database elastici 

Le attività di sviluppo, ridimensionamento e gestione delle applicazioni con scalabilità orizzontale mediante il partizionamento orizzontale presentano difficoltà sia per gli sviluppatori che per gli amministratori. Gli strumenti dei database elastici semplificano le attività per entrambi i ruoli. I numeri nella figura seguente descrivono le funzionalità principali fornite dalla libreria client dei database elastici e lo strumento di suddivisione-unione. Nella figura viene illustrato un ambiente con molti database e ogni database corrisponde a una partizione. Gli strumenti semplificano lo sviluppo di applicazioni dei database SQL di Azure tramite le seguenti caratteristiche specifiche:

Per le definizioni dei termini usati in questo argomento, vedere il [Glossario degli strumenti dei database elastici](sql-database-elastic-scale-glossary.md).

![Funzionalità di scalabilità elastica][1]

1.  **Gestione mappe partizioni**: la gestione delle mappe partizioni è la possibilità per un'applicazione di gestire diversi metadati relativi alle partizioni. La gestione delle mappe partizioni è una funzionalità della libreria client dei database elastici. Gli sviluppatori possono usare questa funzionalità per registrare i database come partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi per i database, nonché gestire i metadati man mano che il numero e la composizione dei database si evolve, per rispecchiare le modifiche apportate alla capacità. La gestione delle mappe partizioni rappresenta gran parte del codice boilerplate che i clienti dovevano scrivere nelle proprie applicazioni quando implementavano autonomamente il partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md).
 
* **Routing dipendente dai dati**: immaginare una richiesta in arrivo nell'applicazione. In base al valore della chiave di partizionamento orizzontale della richiesta, l'applicazione deve stabilire il database corretto che contiene i dati per la chiave e quindi aprire una connessione ad esso per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura ora coperta dalle funzionalità della libreria client dei database elastici. Per informazioni dettagliate, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md).

* **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità come viene esposta tramite la libreria client gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md).


* **Lo strumento di suddivisione-unione**: se si sceglie di non utilizzare il modello semplice di allocazione di database separati a un pool di database elastici per ogni shardlet (tenant), l'applicazione potrebbe richiedere la distribuzione flessibile dei dati tra database quando le esigenze relative alla capacità variano in combinazione con l'espansione del business. Gli strumenti dei database elastici includono lo strumento di suddivisione-unione ospitato dal cliente per ribilanciare la distribuzione dei dati e la gestione di hotspot per le applicazioni partizionate in situazioni che comportano lo spostamento dei dati. Si basa su una funzionalità sottostante per lo spostamento su richiesta di shardlet tra database diversi e si integra con la gestione delle mappe partizioni per mantenere mapping coerenti e connessioni accurate di routing dipendente dai dati. Per informazioni dettagliate, vedere [Suddivisione e unione con gli strumenti di database elastico](sql-database-elastic-scale-overview-split-and-merge.md).

In generale, i clienti che utilizzano gli strumenti dei database elastici quando inviano operazioni locali della partizione anziché operazioni tra più partizioni con la propria semantica, possono aspettarsi di ottenere funzionalità T-SQL complete.

## Modelli comuni di partizionamento orizzontale

Il **partizionamento orizzontale** è una tecnica per distribuire grandi quantità di dati strutturati in modo identico tra più database indipendenti. È molto usato dagli sviluppatori cloud che creano offerte software come un servizio (SAAS) per clienti finali o aziende. Questi clienti finali vengono spesso definiti "tenant". Il partizionamento orizzontale può essere necessario per vari motivi:

* La quantità totale di dati è troppo elevata per un singolo database 
* La velocità effettiva delle transazioni del carico di lavoro complessivo supera le capacità di un singolo database 
* Può essere necessario isolare fisicamente i tenant, quindi predisporre database separati per ogni tenant 
* È possibile che sezioni diverse di un database risiedano in aree geografiche diverse per motivi di conformità, di prestazioni o geopolitici. 

In altri scenari, ad esempio l'inserimento di dati da dispositivi distribuiti, il partizionamento orizzontale può essere usato per riempire un set di database organizzati secondo una logica temporale. Ad esempio, è possibile destinare un database separato a ogni giorno o settimana. In tal caso, la chiave di partizionamento orizzontale può essere un integer che rappresenta la data (presente in tutte le righe delle tabelle partizionate) e l'applicazione deve instradare le query che recuperano informazioni per un intervallo di date al subset di database che coprono l'intervallo in questione.
 
Il partizionamento orizzontale rappresenta la scelta ottimale quando tutte le transazioni in un'applicazione possono essere limitate a un singolo valore di una chiave di partizionamento orizzontale. Questo garantisce che tutte le transazioni saranno locali rispetto a uno specifico database.

Alcune applicazioni usano l'approccio più semplice di creare un database separato per ogni tenant. Questo è il **modello di partizionamento orizzontale per singolo tenant**, che offre isolamento, funzionalità di backup e ripristino e scalabilità delle risorse in base alla granularità del tenant. Con il partizionamento orizzontale per singolo tenant, ogni database è associato a uno specifico valore di ID tenant (o valore di chiave del cliente), ma tale chiave non deve essere necessariamente presente nei dati. È responsabilità dell'applicazione indirizzare ogni richiesta al database appropriato e la libreria client dei database elastici può semplificare questa operazione.

![Single-tenant e multi-tenant][3]

Altri scenari prevedono di riunire più tenant all'interno dei database, anziché isolarli in database separati. Si tratta di un **modello di partizionamento orizzontale multi-tenant** tipico e la scelta di un modello di questo tipo può essere dovuta al fatto che un'applicazione gestisce un numero elevato di tenant di dimensioni molto limitate. Nel partizionamento orizzontale multi-tenant, tutte le righe delle tabelle di database sono progettate per contenere una chiave che identifica l'ID tenant o una chiave di partizionamento orizzontale. Anche in questo caso, il livello applicazione è responsabile dell'instradamento delle richieste del tenant al database appropriato e questo può essere supportato dalla libreria client dei database elastici. Inoltre, la sicurezza a livello di riga può essere utilizzata per filtrare le righe a cui ogni tenant può accedere: per informazioni dettagliate, vedere [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md). Con lo schema di partizionamento orizzontale multi-tenant potrebbe essere necessaria la ridistribuzione dei dati tra database e questa operazione è facilitata dallo strumento di suddivisione-unione dei database elastici.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/overview.png
[2]: ./media/sql-database-elastic-scale-intro/tenancy.png
[3]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png
[4]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
 

<!---HONumber=July15_HO4-->