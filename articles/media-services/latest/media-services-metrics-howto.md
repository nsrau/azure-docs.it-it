---
title: Visualizzare le metriche con Monitoraggio di AzureView metrics with Azure Monitor
description: Questo articolo illustra come monitorare le metriche con i grafici del portale di Azure e l'interfaccia della riga di comando di Azure.This article shows how to monitor metrics with the Azure portal charts and Azure CLI.
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382937"
---
# <a name="monitor-media-services-metrics"></a>Monitorare le metriche di Servizi multimediali

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni. Per una descrizione dettagliata di questa funzionalità e per informazioni sul motivo per cui si vuole usare le metriche e i log di diagnostica di Servizi multimediali di Azure, vedere [Monitorare le metriche e i log](media-services-metrics-diagnostic-logs.md)di diagnostica di Servizi multimediali.

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using Azure CLI. Questo articolo illustra come monitorare le metriche con i grafici del portale di Azure e l'interfaccia della riga di comando di Azure.This article shows how to monitor metrics with the Azure portal charts and Azure CLI.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimedialiCreate a Media Services account](create-account-cli-how-to.md)
- Esaminare [Monitorare le metriche di Servizi multimediali e i log di diagnosticaReview Monitor Media Services metrics and diagnostic logs](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visualizzare le metriche nel portale di AzureView metrics in Azure portal

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare all'account di Servizi multimediali di Azure e selezionare **Metriche**.
1. Fare clic sulla casella **RESOURCE** e selezionare la risorsa per la quale si desidera monitorare le metriche.

    La finestra **Seleziona una risorsa** viene visualizzata a destra con l'elenco delle risorse disponibili. In questo caso si vede:

    * &lt;Nome account di Servizi multimediali&gt;
    * &lt;&gt;/Nome&lt;dell'account di Servizi multimediali nome dell'endpoint di streaming&gt;
    * &lt;nome dell'account di archiviazione&gt;

    Selezionare la risorsa e premere **Applica**. Per informazioni dettagliate sulle risorse e le metriche supportate, vedere [Monitorare le metriche](media-services-metrics-diagnostic-logs.md)di Servizi multimediali.

    ![Metriche](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Per passare da una risorsa per la quale si desidera monitorare le metriche, fare di nuovo clic sulla casella **RESOURCE** e ripetere questo passaggio.
1. (Facoltativo) assegnare un nome al grafico (modificare il nome premendo la matita in alto).
1. Aggiungere le metriche che si desidera visualizzare.

    ![Metriche](media/media-services-metrics/metrics03.png)
1. È possibile aggiungere il grafico al dashboard.

## <a name="view-metrics-with-azure-cli"></a>Visualizzare le metriche con l'interfaccia della riga di comando di AzureView metrics with

Per ottenere le metriche "Egress" con `az monitor metrics` l'interfaccia della riga di comando di Azure, è necessario eseguire il comando seguente:To get "Egress" metrics with Azure CLI, you would run the following command:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Per ottenere altre metriche, sostituisci "Egress" con il nome della metrica a cui sei interessato.

## <a name="see-also"></a>Vedere anche

* [Metriche di Monitoraggio di AzureAzure Monitor Metrics](../../azure-monitor/platform/data-platform.md)
* [Creare, visualizzare e gestire avvisi di metrica tramite Monitoraggio di Azure.](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>Passaggi successivi

[Registri diagnostici](media-services-diagnostic-logs-howto.md)
