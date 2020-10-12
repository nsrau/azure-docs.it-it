---
title: Visualizzare le metriche con monitoraggio di Azure
description: Questo articolo illustra come monitorare le metriche con i grafici portale di Azure e l'interfaccia della riga di comando di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 09c306b85c910e8ec2021abf15013935c44b0df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707711"
---
# <a name="monitor-media-services-metrics"></a>Monitorare le metriche di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni. Per una descrizione dettagliata di questa funzionalità e per sapere perché si vuole usare i log di diagnostica e le metriche di servizi multimediali di Azure, vedere [monitorare le metriche di servizi multimediali e i log di diagnostica](media-services-metrics-diagnostic-logs.md).

Monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query usando l'interfaccia della riga di comando di Azure. Questo articolo illustra come monitorare le metriche con i grafici portale di Azure e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure.](./create-account-howto.md)
- Esaminare  [le metriche dei servizi multimediali e i log di diagnostica](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visualizzare le metriche in portale di Azure

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare all'account di servizi multimediali di Azure e selezionare **metriche**.
1. Fare clic sulla casella **risorsa** e selezionare la risorsa per cui si desidera monitorare le metriche.

    Nella parte destra della finestra **selezionare una risorsa** verrà visualizzato l'elenco delle risorse disponibili. In questo caso, viene visualizzato quanto segue:

    * &lt;Nome dell'account di servizi multimediali&gt;
    * &lt;Nome dell'endpoint di &gt; / &lt; streaming del nome dell'account di servizi multimediali&gt;
    * &lt;nome dell'account di archiviazione&gt;

    Selezionare la risorsa e fare clic su **applica**. Per informazioni dettagliate sulle risorse e sulle metriche supportate, vedere [monitorare le metriche di servizi multimediali](media-services-metrics-diagnostic-logs.md).

    ![Screenshot che mostra la risorsa selezionata ed evidenzia il pulsante Applica.](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Per spostarsi tra le risorse per cui si desidera monitorare le metriche, fare di nuovo clic sulla casella **risorsa** e ripetere questo passaggio.
1. Facoltativamente, assegnare un nome al grafico (modificare il nome premendo la matita nella parte superiore).
1. Aggiungere le metriche che si desidera visualizzare.

    ![Metriche](media/media-services-metrics/metrics03.png)
1. È possibile aggiungere il grafico al dashboard.

## <a name="view-metrics-with-azure-cli"></a>Visualizzare le metriche con l'interfaccia della riga di comando di Azure

Per ottenere metriche "in uscita" con l'interfaccia della riga di comando di Azure, eseguire il `az monitor metrics` comando seguente:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Per ottenere altre metriche, sostituire "in uscita" con il nome della metrica a cui si è interessati.

## <a name="see-also"></a>Vedere anche

* [Metriche di Monitoraggio di Azure](../../azure-monitor/platform/data-platform.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passaggi successivi

[Log di diagnostica](media-services-diagnostic-logs-howto.md)
