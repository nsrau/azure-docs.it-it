<properties title="Sharding Overview" pageTitle="Panoramica del partizionamento orizzontale" description="Reasons for sharding: scale database resources to increase availability or performance." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#Panoramica del partizionamento orizzontale 

##Principi relativi al partizionamento orizzontale 

Il **partizionamento orizzontale** è una tecnica per la distribuzione di grandi quantità di dati strutturati in modo identico tra più database indipendenti. È molto usato dagli sviluppatori cloud che creano offerte software come un servizio (SAAS) per clienti finali o aziende. Questi clienti finali vengono spesso definiti "tenant". Il partizionamento orizzontale può essere necessario per vari motivi: 

* La quantità totale di dati è troppo elevata per un singolo database 
* La velocità effettiva delle transazioni del carico di lavoro complessivo supera le capacità di un singolo database 
* Può essere necessario isolare fisicamente i tenant, quindi predisporre database separati per ogni tenant 
* È possibile che sezioni diverse di un database risiedano in aree geografiche diverse per motivi di conformità, di prestazioni o geopolitici. 
 
Il partizionamento orizzontale rappresenta la scelta ottimale quando tutte le transazioni in un'applicazione possono essere limitate a un singolo valore di una chiave di partizionamento orizzontale. Questo garantisce che tutte le transazioni saranno locali rispetto a uno specifico database. 

Alcune applicazioni usano l'approccio più semplice di creare un database separato per ogni tenant. Questo è il **modello di partizionamento orizzontale per singolo tenant**, che offre isolamento, funzionalità di backup e ripristino e scalabilità delle risorse in base alla granularità del tenant. Con il partizionamento orizzontale per singolo tenant, ogni database è associato a uno specifico valore di ID tenant (o valore di chiave del cliente), ma tale chiave non deve essere necessariamente presente nei dati. È responsabilità dell'applicazione instradare ogni richiesta al database appropriato. 

![][1]

Altri scenari prevedono di riunire più tenant all'interno dei database, anziché isolarli in database separati. Si tratta del **modello di partizionamento orizzontale multi-tenant** e la scelta di un modello di questo tipo può essere dovuta a considerazioni di costo, efficienza o al fatto che un'applicazione gestisce un numero elevato di tenant di dimensioni molto limitate. Nel partizionamento orizzontale multi-tenant, tutte le righe delle tabelle di database sono progettate per contenere una chiave che identifica l'ID tenant o una chiave di partizionamento orizzontale. Anche in questo caso, il livello applicazione è responsabile dell'instradamento delle richieste del tenant al database appropriato. 

In altri scenari, ad esempio l'inserimento di dati da dispositivi distribuiti, il partizionamento orizzontale può essere usato per compilare un set di database distribuiti in base a un periodo di tempo. Ad esempio, è possibile destinare un database separato a ogni giorno o settimana. In tal caso, la chiave di partizionamento orizzontale può essere un integer che rappresenta la data (presente in tutte le righe delle tabelle partizionate) e l'applicazione deve instradare le query che recuperano informazioni per un intervallo di date al subset di database che coprono l'intervallo in questione.

Indipendentemente dal modello di partizionamento orizzontale usato, una struttura dei dati speciale, definita **mappa partizioni**, viene usata come tabella di ricerca che associa i valori di chiave di partizionamento orizzontale ai database. Ciò permette all'applicazione di eseguire il routing per le richieste del database. Questa capacità è definita **routing dipendente dai dati** ed è una delle capacità principali necessarie per l'uso di un livello di dati partizionato da parte di un'applicazione. L'esempio [API del client di Scalabilità elastica](http://go.microsoft.com/?linkid=9862592) include un set completo di funzionalità necessarie per [gestire le mappe partizioni](http://go.microsoft.com/?linkid=9862595) e usarle in modo semplice ed efficace [Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596) in un'applicazione. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
