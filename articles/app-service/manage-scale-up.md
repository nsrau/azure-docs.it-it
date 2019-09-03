---
title: Aumentare le funzionalità e la capacità - Servizio app di Azure | Microsoft Docs
description: Informazioni su come aumentare le prestazioni di un'app nel servizio app di Azure per aggiungere capacità e funzionalità.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d2c3b09277963781b90f65705e03f936f81b14ee
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232356"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Ridimensionare un'app nel servizio app Azure

Questo articolo illustra come passare a un piano superiore per un'app nel servizio app di Azure. Sono disponibili due flussi di lavoro per la scalabilità, l'aumento delle prestazioni e l'aumento del numero di istanze. Questo articolo illustra come aumentare le prestazioni.

* [Aumentare le prestazioni](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile ottenere più CPU, memoria, spazio su disco e altre funzionalità, ad esempio macchine virtuali dedicate, domini e certificati personalizzati, slot di staging, ridimensionamento automatico e altro ancora. È possibile aumentare le prestazioni cambiando il piano tariffario del piano di servizio app a cui appartiene l'app.
* [Aumentare il numero di istanze](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile incrementare il numero di istanze di macchine virtuali che eseguono l'app.
  È possibile specificare al massimo 20 istanze, in base al piano tariffario. Gli [ambienti del servizio app](environment/intro.md) nel piano **Isolato** aumentano ulteriormente il numero di istanze facendolo passare a 100. Per altre informazioni sull'aumento del numero di istanze, vedere [Ridimensionare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Questo articolo illustra come usare la scalabilità automatica, ovvero come ridimensionare automaticamente il numero di istanze sulla base di regole e pianificazioni predefinite.

Le impostazioni di scalabilità diventano effettive in pochi secondi e interessano tutte le app nel [piano di servizio app](../app-service/overview-hosting-plans.md).
Non richiedono di modificare il codice o ridistribuire l'applicazione.

Per informazioni sui prezzi e le funzionalità dei singoli piani di servizio app, vedere [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Prima di modificare il livello **Gratuito** di un piano di servizio app, è necessario innanzitutto rimuovere i [limiti di spesa](https://azure.microsoft.com/pricing/spending-limits/) applicati alla sottoscrizione di Azure. Per visualizzare o modificare le opzioni per la sottoscrizione del servizio app Microsoft Azure, vedere [Microsoft Azure sottoscrizioni][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Passare al piano tariffario superiore

> [!NOTE]
> Per passare al livello **PremiumV2**, vedere [Configure PremiumV2 tier for App Service](app-service-configure-premium-tier.md) (Configurare il livello PremiumV2 per il servizio app).
>

1. Accedere al [portale di Azure][portal] nel browser.

1. Nel menu a sinistra della pagina dell'app del servizio app selezionare **scalabilità verticale (piano di servizio app)** .
   
3. Scegliere il livello e quindi fare clic su **applica**. Selezionare le diverse categorie, ad esempio **produzione**, e vedere anche **Opzioni aggiuntive** per visualizzare più livelli.
   
    ![Passare al ridimensionamento di un'app di Azure.][ChooseWHP]

    Al termine dell'operazione, viene visualizzato un messaggio popup di notifica con un segno di spunta verde positivo.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Risorse correlate alla scalabilità
Se l'app dipende da altri servizi, ad esempio database SQL di Azure o Archiviazione di Azure, è possibile aumentare le prestazioni di tali risorse separatamente. Queste risorse non vengono gestite dal piano di servizio app.

1. Nella pagina **Panoramica** dell'app selezionare il collegamento gruppo di **risorse** .
   
    ![Aumentare le prestazioni delle risorse correlate all'app di Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Nella parte **Riepilogo** della pagina **gruppo di risorse** Selezionare una risorsa che si vuole ridimensionare. La schermata seguente mostra una risorsa del database SQL.
   
    ![Passare alla pagina del gruppo di risorse per aumentare le prestazioni dell'app di Azure](./media/web-sites-scale/ResourceGroup.png)

    Per applicare la scalabilità verticale alla risorsa correlata, vedere la documentazione relativa al tipo di risorsa specifico. Per scalare un singolo database SQL, ad esempio, vedere [ridimensionare le risorse di database singolo nel database SQL di Azure](../sql-database/sql-database-single-database-scale.md). Per applicare la scalabilità verticale a una risorsa di database di Azure per MySQL, vedere [ridimensionare le risorse MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Confrontare i piani tariffari

Per informazioni dettagliate, ad esempio sulle dimensioni delle macchine virtuali per ogni piano tariffario, vedere [Informazioni sui prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service).

Per una tabella dei limiti, delle quote e dei vincoli del servizio e per le funzionalità supportate di ogni piano, vedere [Limiti relativi a Servizio app](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Altre risorse

[Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Configurare il livello PremiumV2 per il servizio app](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
