<properties 
	pageTitle="Scalabilità elastica del database SQL di Azure" 
	description="Possibilità di applicare facilmente la scalabilità delle risorse di database nel cloud usando la funzionalità Elastic Scale del database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Informazioni generali sulla scalabilità elastica del database SQL di Azure 
Introduzione all'anteprima pubblica di Scalabilità elastica del database SQL di Azure 

## Promesse e sfide
La funzionalità Scalabilità elastica del database SQL di Azure mantiene le promesse del cloud computing, offrendo elasticità e capacità virtualmente illimitate per la piattaforma di database SQL di Azure. Ad oggi, i provider di servizi cloud sono stati in grado di offrire gran parte dei benefici promessi in termini di capacità illimitata di calcolo e archiviazione BLOB. L'elasticità, tuttavia, resta ancora un problema nell'ambito dell'elaborazione di dati con stato nel cloud e, in particolare, della gestione di database relazionali. I problemi sono emersi principalmente nei due seguenti scenari: 

* Aumento e riduzione della capacità per la parte del carico di lavoro relativa al database relazionale.
* Gestione delle aree di utilizzo critiche per i carichi di lavoro di database con stato e i relativi dati.

In genere questi scenari vengono affrontati acquistando ulteriori componenti hardware destinati a ospitare il livello dati dell'applicazione. Questa opzione offre però possibilità limitate negli ambienti cloud, in cui l'intera elaborazione viene eseguita su appositi componenti hardware predefiniti. Il partizionamento orizzontale, ovvero la distribuzione ed elaborazione dei dati tra più unità di scala per motivi di capacità, fornisce una valida alternativa agli approcci tradizionali con scalabilità orizzontale, in termini sia di costi che di elasticità. Nel corso degli anni è emerso che, per il successo delle iniziative di partizionamento orizzontale, i clienti dovevano implementare approcci personalizzati a livello del database SQL di Azure. Per alcuni, questo si traduceva nella gestione di centinaia o migliaia di database SQL di Azure e nella presenza nel livello di dati di moltissimo codice associato alle complessità del partizionamento orizzontale anziché alla logica di business dell'applicazione. 

Lavorando direttamente con i clienti nel corso degli anni, abbiamo visto emergere da questi progetti diversi modelli di partizionamento orizzontale. Scalabilità elastica del database SQL di Azure fornisce librerie client e offerte di servizio basate su tali modelli, consentendo di sviluppare, ridimensionare e gestire più facilmente i livelli di dati con stato delle applicazioni Azure

e dunque di concentrarsi sulla logica di business dell'applicazione anziché sulla creazione di un'infrastruttura per il partizionamento orizzontale.
## Scalabilità orizzontale e verticale
La seguente figura mostra la differenza tra scalabilità orizzontale e verticale. Questi sono i due modi in cui è possibile implementare la scalabilità in una soluzione condivisa. 
![Horizontal versus Vertical Scaleout][4]

Usare la scalabilità orizzontale per aumentare la capacità. Ad esempio, quando si avvicina una scadenza fiscale, aumentare lo spazio per archiviare i documenti in arrivo.

Usare la scalabilità verticale per aumentare le prestazioni di una partizione. Questa esigenza può verificarsi quando vengono elaborati molti dati e la partizione diventa conseguentemente un'area sensibile nel sistema. Ad esempio, è possibile creare una nuova partizione per gestire un afflusso ingente di dati alla fine di un mese. Mentre arrivano i nuovi dati, il partizionamento viene aumentato e quindi viene ridotto con la diminuzione dell'afflusso di dati.

Per altre informazioni sugli scenari di scalabilità, vedere la pagina relativa alla [suddivisione e all'unione con la scalabilità elastica](sql-database-elastic-scale-overview-split-and-merge.md).


## Funzionalità 

Le attività di sviluppo, ridimensionamento e gestione delle applicazioni con scalabilità orizzontale mediante il partizionamento orizzontale presentano difficoltà sia per gli sviluppatori che per gli amministratori. Scalabilità elastica del database SQL di Azure semplifica il lavoro di entrambi i ruoli. I dati nel grafico illustrano le funzionalità principali fornite con questa versione di anteprima pubblica. 
La parte inferiore mostra il livello dati dell'applicazione e la distribuzione dei dati tra diversi database, denominati partizioni. Si suppone che più database archivino i dati di più partizioni. 

Per le definizioni dei termini usati in questo argomento, vedere il [glossario della scalabilità elastica](sql-database-elastic-scale-glossary.md).

## Scalabilità elastica con partizionamento orizzontale 
L'**elasticità di partizionamento** consente agli amministratori di automatizzare la scalabilità verticale (aumento o riduzione del livello di prestazioni cambiando l'edizione di una singola partizione) e orizzontale (aggiunta o rimozione di partizioni da una mappa partizioni) dell'ambiente partizionato mediante script di PowerShell e il servizio Automazione di Azure. Per informazioni dettagliate, vedere la pagina relativa all'[elasticità di partizionamento](sql-database-elastic-scale-elasticity.md).

La seguente figura mostra lo sviluppatore e l'amministratore rispettivamente a sinistra e a destra. I clienti che inviano operazioni locali della partizione anziché operazioni tra più partizioni con la propria semantica, possono aspettarsi di ottenere funzionalità T-SQL complete. 
La versione di anteprima pubblica di Scalabilità elastica del database SQL di Azure semplifica lo sviluppo di applicazioni di database SQL di Azure partizionate grazie alle funzionalità specifiche illustrate di seguito: 

![Elastic Scale Capabilities][1]

1.  **Gestione mappe partizioni**: la gestione delle mappe partizioni è la possibilità per un'applicazione di gestire diversi metadati relativi alle partizioni. La gestione delle mappe partizioni è una funzionalità della libreria client di Scalabilità elastica. Gli sviluppatori possono usare questa funzionalità per registrare partizioni, descrivere i mapping di singole chiavi di partizionamento orizzontale o di intervalli di chiavi alle partizioni, nonché gestire i metadati man mano che il layout delle partizioni nel livello dati si evolve, per rispecchiare le modifiche apportate alla capacità. La gestione delle mappe partizioni rappresenta gran parte del codice boilerplate che i clienti dovevano scrivere nelle proprie applicazioni quando implementavano autonomamente il partizionamento orizzontale. Per informazioni dettagliate, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md)
 
* **Routing dipendente dai dati**: si immagini una richiesta in arrivo nell'applicazione. In base al valore della chiave di partizionamento orizzontale della richiesta, l'applicazione deve stabilire quale sia la partizione che contiene i dati e quindi aprire una connessione alla partizione per elaborare la richiesta. Il routing dipendente dai dati consente di aprire connessioni con una singola e semplice chiamata alla mappa partizioni dell'applicazione. Il routing dipendente dai dati è un'altra area del codice dell'infrastruttura ora coperta dalle funzionalità della libreria client di Scalabilità elastica. Per informazioni dettagliate, vedere [Routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md)

* **Query su più partizioni**: l'esecuzione di query su più partizioni opera quando una richiesta include più partizioni o tutte le partizioni. Una query su più partizioni esegue lo stesso codice T-SQL in tutte le partizioni o in un set di partizioni. I risultati restituiti dalle partizioni coinvolte vengono uniti in un set di risultati complessivi mediante la semantica di UNION ALL. La funzionalità viene esposta tramite la libreria client e gestisce numerose attività, tra cui gestione delle connessioni, gestione dei thread, gestione degli errori ed elaborazione dei risultati intermedi e consente di eseguire query su centinaia di partizioni. Per informazioni dettagliate, vedere [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md).


* **Servizio di suddivisione e unione**: quando la capacità deve variare in base alle esigenze aziendali, le applicazioni devono ridistribuire in modo flessibile i dati tra più database. Scalabilità elastica offre un'esperienza di servizio ospitato presso il cliente per l'aumento e la riduzione della capacità del livello dati e la gestione delle aree critiche per le applicazioni partizionate in situazioni che prevedono anche lo spostamento di dati. Si basa su una funzionalità sottostante per lo spostamento su richiesta di shardlet tra partizioni diverse e si integra con la gestione delle mappe partizioni per mantenere mapping coerenti e connessioni accurate di routing dipendente dai dati. Per informazioni dettagliate, vedere la pagina relativa alla [suddivisione e all'unione con la scalabilità elastica](sql-database-elastic-scale-overview-split-and-merge.md)

## Modelli comuni di partizionamento orizzontale

Il **partizionamento orizzontale** è una tecnica per distribuire grandi quantità di dati strutturati in modo identico tra più database indipendenti. È molto usato dagli sviluppatori cloud che creano offerte software come un servizio (SAAS) per clienti finali o aziende. Questi clienti finali vengono spesso definiti "tenant". Il partizionamento orizzontale può essere necessario per vari motivi: 

* La quantità totale di dati è troppo elevata per un singolo database 
* La velocità effettiva delle transazioni del carico di lavoro complessivo supera le capacità di un singolo database 
* Può essere necessario isolare fisicamente i tenant, quindi predisporre database separati per ogni tenant 
* È possibile che sezioni diverse di un database risiedano in aree geografiche diverse per motivi di conformità, di prestazioni o geopolitici. 

In altri scenari, ad esempio l'inserimento di dati da dispositivi distribuiti, il partizionamento orizzontale può essere usato per riempire un set di database organizzati secondo una logica temporale. Ad esempio, è possibile destinare un database separato a ogni giorno o settimana. In tal caso, la chiave di partizionamento orizzontale può essere un integer che rappresenta la data (presente in tutte le righe delle tabelle partizionate) e l'applicazione deve instradare le query che recuperano informazioni per un intervallo di date al subset di database che coprono l'intervallo in questione.
 
Il partizionamento orizzontale rappresenta la scelta ottimale quando tutte le transazioni in un'applicazione possono essere limitate a un singolo valore di una chiave di partizionamento orizzontale. Questo garantisce che tutte le transazioni saranno locali rispetto a uno specifico database. 

Alcune applicazioni usano l'approccio più semplice di creare un database separato per ogni tenant. Questo è il **modello di partizionamento orizzontale per singolo tenant**, che offre isolamento, funzionalità di backup e ripristino e scalabilità delle risorse in base alla granularità del tenant. Con il partizionamento orizzontale per singolo tenant, ogni database è associato a uno specifico valore di ID tenant (o valore di chiave del cliente), ma tale chiave non deve essere necessariamente presente nei dati. È responsabilità dell'applicazione instradare ogni richiesta al database appropriato. 

![Single tenant versus multi-tenant][3]

Altri scenari prevedono di riunire più tenant all'interno dei database, anziché isolarli in database separati. Si tratta di un **modello di partizionamento orizzontale multi-tenant** tipico e la scelta di un modello di questo tipo può essere dovuta a considerazioni di costo, efficienza o al fatto che un'applicazione gestisce un numero elevato di tenant di dimensioni molto limitate. Nel partizionamento orizzontale multi-tenant, tutte le righe delle tabelle di database sono progettate per contenere una chiave che identifica l'ID tenant o una chiave di partizionamento orizzontale. Anche in questo caso, il livello applicazione è responsabile dell'instradamento delle richieste del tenant al database appropriato. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
[3]:./media/sql-database-elastic-scale-intro/single_v_multi_tenant.png
[4]:./media/sql-database-elastic-scale-intro/h_versus_vert.png

<!--HONumber=47-->
