---
title: Replica del database globale di DocumentDB | Documentazione Microsoft
description: Informazioni su come gestire la replica globale dell&quot;account DocumentDB con il portale di Azure.
services: documentdb
keywords: database globale, replica
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cc393967fec1a98a4dd596a156c7a12e88959b04
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-perform-global-database-replication-using-the-azure-portal"></a>Come eseguire la replica del database globale usando il portale di Azure

Informazioni su come usare il portale di Azure per replicare i dati in più aree per la disponibilità globale dei dati in Azure DocumentDB e nell'API per MongoDB.

Per informazioni sul funzionamento della replica di database globale in DocumentDB, vedere [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md). Per informazioni su come eseguire la replica di database globale a livello di codice, vedere [Sviluppo con account DocumentDB in più aree](documentdb-developing-with-multiple-regions.md).

## <a id="addregion"></a>Aggiungere aree di database globali
DocumentDB è disponibile nella maggior parte delle [aree di Azure][azureregions]. Dopo aver selezionato il livello di coerenza predefinito per l'account di database, è possibile associare una o più aree, a seconda del livello di coerenza predefinito e delle esigenze di distribuzione globale scelti.

1. Nel [portale di Azure](https://portal.azure.com/), nella barra di sinistra, fare clic su **NoSQL (DocumentDB)**.
2. Nel pannello **DocumentDB (NoSQL)** selezionare l'account di database da modificare.
3. Nel pannello dell'account fare clic su **Replica i dati a livello globale** dal menu.
4. Nel pannello **Replica i dati a livello globale** selezionare le aree da aggiungere o rimuovere e quindi fare clic su **Salva**. L'aggiunta di aree ha un costo. Per altre informazioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/) o l'articolo [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md).
   
    ![Fare clic sulle aree nella mappa per aggiungerle o rimuoverle][1]
    
Dopo avere aggiunto una seconda area, l'opzione **Failover manuale** viene abilitata nel pannello **Replica i dati a livello globale** del portale. È possibile usare questa opzione per testare il processo di failover. Dopo avere aggiunto una terza area, l'opzione **Priorità di failover** viene abilitata nello stesso pannello per poter modificare l'ordine di failover per le operazioni di lettura.  

### <a name="selecting-global-database-regions"></a>Selezionare aree di database globali
Quando si configurano due o più aree, è consigliabile che le aree vengano selezionate in base alle coppie di aree descritte nell'articolo [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][bcdr].

In particolare, durante la configurazione di più aree, accertarsi di selezionare lo stesso numero di aree (+/-1 per pari/dispari) da ognuna delle colonne di aree abbinate. Ad esempio, per eseguire la distribuzione in quattro aree degli Stati Uniti, selezionare due aree degli Stati Uniti dalla colonna di sinistra e due dalla colonna di destra. Il set seguente sarebbe quindi corretto: Stati Uniti occidentali, Stati Uniti orientali, Stati Uniti centro-settentrionali e Stati Uniti centro-meridionali.

È importante seguire queste linee guida quando sono configurate solo due aree per gli scenari di ripristino di emergenza. Se le aree sono più di due, seguire queste linee guida può essere utile ma non è fondamentale, purché alcune delle aree selezionate seguano questo abbinamento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

### <a name="verifying-your-regional-setup-in-api-for-mongodb"></a>Verifica della configurazione a livello di area nell'API per MongoDB
Il modo più semplice per verificare in modo approfondito la configurazione globale nell'API per MongoDB consiste nell'eseguire il comando *isMaster()* da Mongo Shell.

Da Mongo Shell:

   ```
      db.isMaster()
   ```
   
Risultati dell'esempio:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10250",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10250",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10250",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
      }
   ```

## <a id="next"></a>Passaggi successivi
Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in DocumentDB](documentdb-consistency-levels.md).

Per informazioni sul funzionamento della replica di database globale in DocumentDB, vedere [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md). Per informazioni su come eseguire la replica di dati in più aree a livello di codice, vedere [Sviluppo con account DocumentDB in più aree](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

