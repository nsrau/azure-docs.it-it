---
title: Monitorare i contenitori in Istanze di Azure Container
description: Come monitorare il consumo delle risorse di calcolo, ad esempio CPU e memoria, da parte dei contenitori in Istanze di Azure Container.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 4c1208d8cbc795e53128df0ccf93e79dc427abad
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325845"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorare le risorse dei contenitori in Istanze di Azure Container

[Monitoraggio di Azure][azure-monitoring] offre informazioni dettagliate sulle risorse di calcolo usate dalle istanze di contenitore. I dati sull'utilizzo delle risorse consentono di determinare le impostazioni ottimali di CPU e memoria per i gruppi di contenitori. Monitoraggio di Azure fornisce inoltre le metriche per il monitoraggio dell'attività di rete nelle istanze di contenitore.

Questo documento illustra in dettaglio la raccolta delle metriche di Monitoraggio di Azure per le istanze di contenitore tramite il portale di Azure e l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Le metriche di Monitoraggio di Azure in Istanze di Azure Container sono attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

In questo momento, le metriche di Monitoraggio di Azure sono disponibili solo per i contenitori Linux.

## <a name="available-metrics"></a>Metriche disponibili

Monitoraggio di Azure fornisce le [metriche per Istanze di Azure Container][supported-metrics] seguenti. Queste metriche sono disponibili per un gruppo di contenitori e per singoli contenitori.

* **Utilizzo CPU**: misurato in **millicore**. Un millicore equivale a 1/1.000° di core della CPU, quindi 500 millicore, o 500 m, rappresentano il 50% di utilizzo di un core della CPU. Aggregato come **utilizzo medio** tra tutti i core.

* **Utilizzo memoria**: valore aggregato come **media byte**.

* **Byte di rete ricevuti al secondo** e **Byte di rete trasmessi al secondo**: valore aggregato come **media byte al secondo**. 

## <a name="get-metrics---azure-portal"></a>Ottenere le metriche: portale di Azure

Quando viene creato un gruppo di contenitori, i dati di Monitoraggio di Azure sono disponibili nel portale di Azure. Per visualizzare le metriche per un gruppo di contenitori, passare alla pagina **Panoramica** per il gruppo di contenitori. Qui è possibile visualizzare i grafici già creati per ogni metrica disponibile.

![grafico doppio][dual-chart]

Se un gruppo di contenitori contiene più contenitori, usare una [dimensione][monitor-dimension] per presentare le metriche per contenitore. Per creare un grafico con le metriche di un singolo contenitore, eseguire la procedura seguente:

1. Nella pagina **Panoramica** selezionare uno dei grafici delle metriche, ad esempio **CPU**. 
1. Selezionare il pulsante **Applicare separazione** e quindi **Nome contenitore**.

![dimensioni][dimension]

## <a name="get-metrics---azure-cli"></a>Ottenere le metriche: interfaccia della riga di comando di Azure

È anche possibile raccogliere le metriche per le istanze di contenitore tramite l'interfaccia della riga di comando di Azure. Recuperare innanzitutto l'ID del gruppo di contenitori usando il comando seguente. Sostituire `<resource-group>` con il nome del gruppo di risorse e `<container-group>` con il nome del gruppo di contenitori.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Usare il comando seguente per ottenere le metriche sull'uso della **CPU**

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Modificare il valore del parametro `--metric` nel comando per ottenere altre [metriche supportate][supported-metrics]. Ad esempio, usare il comando seguente per ottenere le metriche di utilizzo della **memoria**. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Per un gruppo multi-contenitore, è possibile aggiungere la dimensione `containerName` per restituire i dati per ogni contenitore.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Monitoraggio di Azure, vedere la [panoramica di Monitoraggio di Azure][azure-monitoring].

Informazioni su come creare gli [avvisi delle metriche][metric-alert] per ricevere le notifiche quando le metriche per Istanze di Azure Container superano le soglie.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
