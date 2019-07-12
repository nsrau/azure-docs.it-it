---
title: Visualizzare le metriche con monitoraggio di Azure
description: Questo articolo illustra come monitorare le metriche con i grafici del portale Azure CLI di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795803"
---
# <a name="monitor-media-services-metrics"></a>Monitorare le metriche di servizi multimediali 

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e log di diagnostica che consentono di comprendere prestazioni delle applicazioni. Per una descrizione dettagliata di questa funzionalità e per il motivo per cui si desidera usare i log di diagnostica e metriche di servizi multimediali di Azure, vedere [log di diagnostica e metriche di servizi multimediali di monitoraggio](media-services-metrics-diagnostic-logs.md).

Monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query utilizzando CLI. Questo articolo illustra come monitorare le metriche con i grafici del portale Azure CLI di Azure.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure.](create-account-cli-how-to.md)
- Revisione [log di diagnostica e metriche di servizi multimediali di monitoraggio](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visualizzare le metriche nel portale di Azure

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare all'account di servizi multimediali di Azure e selezionare **metriche**.
1. Scegliere il **risorse** casella e selezionare la risorsa per cui si desidera monitorare le metriche. 

    Il **selezionare una risorsa** verrà visualizzata la finestra a destra di un elenco di risorse disponibili per l'utente. In questo caso viene visualizzata 

    * &lt;Nome account servizi multimediali&gt;
    * &lt;Nome account servizi multimediali&gt;/&lt;nome endpoint di streaming&gt;
    * &lt;nome account di archiviazione&gt;

    Selezionare la risorsa e premere **applica**. Per informazioni dettagliate sulle metriche e le risorse supportate, vedere [metriche di servizi multimediali di monitoraggio](media-services-metrics-diagnostic-logs.md).
 
    ![metrics](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Per passare tra le risorse per il quale si desidera monitorare le metriche, fare clic sui **risorsa** casella nuovamente e ripetere questo passaggio.
1. (Facoltativo) assegnare al grafico un nome (modificare il nome facendo clic sulla matita nella parte superiore).
1. Aggiungere metriche che si desidera visualizzare.

    ![metrics](media/media-services-metrics/metrics03.png)
1. È possibile aggiungere il grafico al dashboard.

## <a name="view-metrics-with-azure-cli"></a>Visualizzare le metriche con CLI di Azure

Per ottenere le metriche "dei servizi esterni" con CLI, eseguire quanto segue `az monitor metrics` riga di comando:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Per ottenere altre metriche, sostituire "Uscita" per il nome della metrica che si è interessati.

## <a name="see-also"></a>Vedere anche

* [Metriche di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Passaggi successivi

[Log di diagnostica](media-services-diagnostic-logs-howto.md)
