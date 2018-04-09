---
title: Distribuzione globale di Azure Cosmos DB
description: Informazioni su come replicare i dati a livello globale con Azure Cosmos DB nel portale di Azure
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/26/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: b62d1cc3b7ea79adbf24f214ba3bb9e92c3a1f0c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
È possibile ottenere maggiori informazioni sulla distribuzione globale di Azure Cosmos DB nel video seguente in cui Andrew Liu, Program Manager di Azure Cosmos DB, illustra in dettaglio la funzionalità di distribuzione globale.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]

Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Aggiungere aree di database globali tramite il portale di Azure
Azure Cosmos DB è disponibile in tutte le [aree di Azure][azureregions] del mondo. Dopo aver selezionato il livello di coerenza predefinito per l'account di database, è possibile associare una o più aree, a seconda del livello di coerenza predefinito e delle esigenze di distribuzione globale scelti.

1. Nella barra a sinistra del [portale di Azure](https://portal.azure.com/) fare clic su **Azure Cosmos DB**.
2. Nella pagina **Azure Cosmos DB** selezionare l'account di database da modificare.
3. Nella pagina dell'account fare clic su **Replica i dati a livello globale** dal menu.
4. Nella pagina **Replica i dati a livello globale** selezionare le aree da aggiungere o rimuovere facendo clic su di esse nella mappa e quindi scegliere **Salva**. L'aggiunta di aree ha un costo. Per altre informazioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) o l'articolo [Distribuire i dati a livello globale con Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).
   
    ![Fare clic sulle aree nella mappa per aggiungerle o rimuoverle][1]
    
Dopo l'aggiunta di una seconda area, viene abilitata l'opzione **Failover manuale** nella pagina **Replica i dati a livello globale** del portale. È possibile usare questa opzione per testare il processo di failover o per modificare l'area di scrittura primaria. Dopo avere aggiunto una terza area, l'opzione **Priorità di failover** viene abilitata nella stessa pagina per poter modificare l'ordine di failover per le operazioni di lettura.  

### <a name="selecting-global-database-regions"></a>Selezionare aree di database globali
Esistono due scenari comuni per la configurazione di due o più aree:

1. Offerta di accesso con bassa latenza ai dati indipendentemente dalla posizione del mondo in cui si trovano gli utenti finali
2. Aggiunta di resilienza a livello di area per garantire continuità aziendale e ripristino di emergenza (BCDR)

Per offrire l'accesso con bassa latenza agli utenti finali, è consigliabile distribuire l'applicazione e Azure Cosmos DB nelle aree corrispondenti alla località in cui si trovano gli utenti dell'applicazione.

Per finalità BCDR, è consigliabile aggiungere le aree in base alle coppie di aree descritte nell'articolo [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
