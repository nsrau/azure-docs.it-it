---
title: Esempi Node.js NoSQL per DocumentDB | Documentazione Microsoft
description: "Trovare esempi Node.js NoSQL su GitHub per attività comuni in DocumentDB, incluse operazioni CRUD per documenti JSON in database NoSQL."
keywords: Esempi di Node.js
services: documentdb
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: nodejs
ms.assetid: d87d97be-47a5-4928-8d46-a541fbb33213
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: moderakh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9a74b023c12f77fccdd989fa8f7ffa7b2ac83db0


---
# <a name="documentdb-nodejs-examples"></a>Esempi di Node.js in DocumentDB
> [!div class="op_single_selector"]
> * [Esempi di .NET](documentdb-dotnet-samples.md)
> * [Esempi di Node.js](documentdb-nodejs-samples.md)
> * [Esempi di Python](documentdb-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)
> 
> 

Il repository [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) di GitHub include soluzioni di esempio che eseguono operazioni CRUD e altre operazioni comuni su risorse di Azure DocumentDB. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Node.js di esempio.
* Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

1. Per usare questi esempi Node.js, è necessario un account Azure:
   * È possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.
     * È possibile [attivare i benefici della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): con la sottoscrizione Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
2. È anche necessario [Node.js SDK](documentdb-sdk-node.md).
   
   > [!NOTE]
   > Ogni esempio è indipendente e le operazioni di installazione e pulizia sono eseguite automaticamente. Di conseguenza, gli esempi eseguono più chiamate a [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). A ogni chiamata, viene addebitata alla sottoscrizione 1 ora di utilizzo per ogni livello di prestazioni della raccolta in fase di creazione.
   > 
   > 

## <a name="database-examples"></a>Esempi di database
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) del progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase) |
| [Eseguire query in un account per un database](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases) |
| [Leggere un database in base all'ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Elencare database per un account](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Eliminare un database](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase) |

## <a name="collection-examples"></a>Esempi di raccolta
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) del progetto [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una raccolta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Leggere un elenco di raccolte in un database](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections) |
| [Ottenere una raccolta in base a _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Ottenere una raccolta in base all'ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Ottenere il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) |
| [Cambiare il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) |
| [Eliminare una raccolta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection) |

## <a name="document-examples"></a>Esempi di documento
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) del progetto [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare i documenti](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument) |
| [Leggere un feed di documenti per una raccolta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Leggere un documento in base all'ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Leggere il documento solo se è stato modificato](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Eseguire query per documenti](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) |
| [Sostituire un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument) |
| [Sostituire documenti con verifica degli ETag condizionale](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Eliminare un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument) |

## <a name="indexing-examples"></a>Esempi di indicizzazione
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) del progetto [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| Creare una raccolta con indicizzazione predefinita |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |
| [Indicizzare manualmente un documento specifico](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective) |
| [Escludere manualmente un documento specifico dall'indice](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Usare l'indicizzazione differita per l'importazione in massa o raccolte con operazioni di lettura intense](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode) |
| [Includere percorsi specifici di un documento nell'indicizzazione](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Escludere determinati percorsi dall'indicizzazione](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Consentire un'analisi su un percorso di stringa durante un'operazione di intervallo](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions) |
| [Creare un indice di intervallo su un percorso di stringa](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument) |
| [Creare una raccolta con indexPolicy predefinito e quindi aggiornarla online](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |

Per altre informazioni sull'indicizzazione, vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

## <a name="server-side-programming-examples"></a>Esempi di programmazione lato server
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) del progetto [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una stored procedure](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure) |
| [Eseguire una stored procedure](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure) |

Per altre informazioni sulla programmazione lato server, vedere [Programmazione sul lato server DocumentDB: stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

## <a name="partitioning-examples"></a>Esempi di partizionamento
Il file [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) del progetto [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Usare un'attività HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html) |

Per altre informazioni sul partizionamento dei dati in DocumentDB, vedere [Partizionamento e scalabilità in Azure DocumentDB](documentdb-partition-data.md).




<!--HONumber=Nov16_HO3-->


