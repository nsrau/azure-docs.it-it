---
title: Aumentare funzionalità e capacità
description: Informazioni su come aumentare le prestazioni di un'app nel servizio app di Azure. È possibile ottenere più CPU, memoria, spazio su disco e funzionalità aggiuntive.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 8085cbd3d19bd60c50de38950625de2fcd1e5c27
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962486"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Aumentare le prestazioni di un'app nel servizio app di Azure

Questo articolo illustra come passare a un piano superiore per un'app nel servizio app di Azure. Sono disponibili due flussi di lavoro per la scalabilità, l'aumento delle prestazioni e l'aumento del numero di istanze. Questo articolo illustra come aumentare le prestazioni.

* [Aumentare le prestazioni](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile ottenere più CPU, memoria, spazio su disco e altre funzionalità, ad esempio macchine virtuali dedicate, domini e certificati personalizzati, slot di staging, ridimensionamento automatico e altro ancora. È possibile aumentare le prestazioni cambiando il piano tariffario del piano di servizio app a cui appartiene l'app.
* [Aumentare il numero di istanze](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile incrementare il numero di istanze di macchine virtuali che eseguono l'app.
  A seconda del piano tariffario, è possibile aumentare il numero di istanze fino a un massimo di 30. Gli [ambienti del servizio app](environment/intro.md) nel piano **Isolato** aumentano ulteriormente il numero di istanze facendolo passare a 100. Per altre informazioni sull'aumento del numero di istanze, vedere [Ridimensionare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md). Questo articolo illustra come usare la scalabilità automatica, ovvero come ridimensionare automaticamente il numero di istanze sulla base di regole e pianificazioni predefinite.

Le impostazioni di scalabilità diventano effettive in pochi secondi e interessano tutte le app nel [piano di servizio app](../app-service/overview-hosting-plans.md).
Non richiedono di modificare il codice o ridistribuire l'applicazione.

Per informazioni sui prezzi e le funzionalità dei singoli piani di servizio app, vedere [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Prima di modificare il livello **Gratuito** di un piano di servizio app, è necessario innanzitutto rimuovere i [limiti di spesa](https://azure.microsoft.com/pricing/spending-limits/) applicati alla sottoscrizione di Azure. Per visualizzare o modificare le opzioni per la sottoscrizione del servizio app di Microsoft Azure, vedere [Sottoscrizioni di Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Passare al piano tariffario superiore

> [!NOTE]
> Per passare al livello **PremiumV2**, vedere [Configure PremiumV2 tier for App Service](app-service-configure-premium-tier.md) (Configurare il livello PremiumV2 per il servizio app).
>

1. Accedere al [portale di Azure][portal] nel browser.

1. Nella pagina dell'app Servizio app selezionare **Aumenta prestazioni (piano di servizio app)** dal menu a sinistra.
   
3. Scegliere il livello e quindi selezionare **Applica**. Selezionare le diverse categorie, ad esempio **Produzione**, e **Visualizza opzioni aggiuntive** per visualizzare più livelli.
   
    ![Passare al ridimensionamento di un'app di Azure.][ChooseWHP]

    Al termine dell'operazione, viene visualizzato un popup di notifica con un segno di spunta verde che indica l'esito positivo.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Risorse correlate alla scalabilità
Se l'app dipende da altri servizi, ad esempio database SQL di Azure o Archiviazione di Azure, è possibile aumentare le prestazioni di tali risorse separatamente. Queste risorse non vengono gestite dal piano di servizio app.

1. Nella pagina **Panoramica** per l'app selezionare il collegamento **Gruppo di risorse**.
   
    ![Aumentare le prestazioni delle risorse correlate all'app di Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Nella parte **Riepilogo** della pagina **Gruppo di risorse** selezionare la risorsa da ridimensionare. La schermata seguente illustra una risorsa del database SQL.
   
    ![Passare alla pagina del gruppo di risorse per aumentare le prestazioni dell'app di Azure](./media/web-sites-scale/ResourceGroup.png)

    Per aumentare le prestazioni della risorsa correlata, vedere la documentazione relativa al tipo specifico di risorsa. Ad esempio, per aumentare le prestazioni di un singolo database SQL, vedere [Ridimensionare le risorse di database singoli nel database SQL di Azure](../azure-sql/database/single-database-scale.md). Per aumentare le prestazioni di una risorsa del Database di Azure per MySQL, vedere l'articolo sulla [scalabilità delle risorse MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Confrontare i piani tariffari

Per informazioni dettagliate, ad esempio sulle dimensioni delle macchine virtuali per ogni piano tariffario, vedere [Informazioni sui prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service).

Per una tabella dei limiti, delle quote e dei vincoli del servizio e per le funzionalità supportate di ogni piano, vedere [Limiti relativi a Servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Altre risorse

[Scalare il conteggio delle istanze manualmente o automaticamente](../azure-monitor/platform/autoscale-get-started.md)  
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