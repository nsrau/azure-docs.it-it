---
title: Replica del database globale di DocumentDB | Microsoft Docs
description: Informazioni su come gestire la replica globale dell'account DocumentDB con il portale di Azure.
services: documentdb
keywords: database globale, replica
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: mimig

---
# Come eseguire la replica del database globale di DocumentDB con il portale di Azure
Informazioni su come usare il portale di Azure per eseguire la replica in più aree per la disponibilità globale dei dati in Azure DocumentDB.

Per informazioni sul funzionamento della replica di database globale in DocumentDB, vedere [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md). Per informazioni su come eseguire la replica di database globale a livello di codice, vedere [Sviluppo con account DocumentDB in più aree](documentdb-developing-with-multiple-regions.md).

> [!NOTE]
> La distribuzione globale di database di DocumentDB è disponibile a livello generale ed è abilitata automaticamente per tutti gli account DocumentDB appena creati. La distribuzione globale in tutti gli account esistenti verrà presto abilitata. Nel frattempo, per abilitare la distribuzione globale per l'account è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a id="addregion"></a>Aggiungere aree di database globali
DocumentDB è disponibile nella maggior parte delle [aree di Azure][azureregions]. Dopo aver selezionato il livello di coerenza predefinito per l'account di database, è possibile associare una o più aree, a seconda del livello di coerenza predefinito e delle esigenze di distribuzione globale scelti.

1. Nel [portale di Azure](https://portal.azure.com/), nell’indice, fare clic su **Account DocumentDB**.
2. Nel pannello **Account DocumentDB** selezionare l'account di database da modificare.
3. Nel pannello dell'account fare clic su **Aggiungi/Rimuovi aree** dal menu.
4. Nel pannello **Aggiungi/Rimuovi aree** selezionare le aree da aggiungere o rimuovere e quindi fare clic su **OK**. L'aggiunta di aree ha un costo. Per altre informazioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/) o l'articolo [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md).
   
    ![Fare clic sulle aree nella mappa per aggiungerle o rimuoverle][1]

### Selezionare aree di database globali
Quando si configurano due o più aree, è consigliabile che le aree vengano selezionate in base alle coppie di aree descritte nell'articolo [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][bcdr].

In particolare, durante la configurazione di più aree, accertarsi di selezionare lo stesso numero di aree (+/-1 per pari/dispari) da ognuna delle colonne di aree abbinate. Ad esempio, per eseguire la distribuzione in quattro aree degli Stati Uniti, selezionare due aree degli Stati Uniti dalla colonna di sinistra e due dalla colonna di destra. Il set seguente sarebbe quindi corretto: Stati Uniti occidentali, Stati Uniti orientali, Stati Uniti centro-settentrionali e Stati Uniti centro-meridionali.

È importante seguire queste linee guida quando sono configurate solo due aree per gli scenari di ripristino di emergenza. Se le aree sono più di due, seguire queste linee guida può essere utile ma non è fondamentale, purché alcune delle aree selezionate seguano questo abbinamento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

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
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

<!---HONumber=AcomDC_0831_2016-->