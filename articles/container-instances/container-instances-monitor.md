---
title: Monitorare i contenitori in Istanze di Azure Container
description: Informazioni dettagliate su come monitorare il consumo delle risorse di calcolo, ad esempio CPU e memoria da parte dei contenitori in Istanze di Azure Container.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: 950d8b4b5ec1a55e2054039a01d6807915b5c714
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784074"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorare le risorse dei contenitori in Istanze di Azure Container

Monitoraggio di Azure offre informazioni approfondite sulle risorse di calcolo usate dalle istanze di contenitori. Usare Monitoraggio di Azure per tenere traccia dell'uso della CPU e della memoria dei gruppi di contenitori e dei relativi contenitori. I dati sull'uso delle risorse consentono di determinare le migliori impostazioni della CPU e della memoria per i gruppi di contenitori.

Questo documento illustra in dettaglio la raccolta dei dati sull'uso della CPU e della memoria per le istanze di contenitori tramite il portale di Azure e l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> In questo momento, le metriche di uso delle risorse sono disponibili solo per i contenitori di Linux.
>

## <a name="available-metrics"></a>Metriche disponibili

Monitoraggio di Azure offre le metriche sull'uso della **CPU** e della **memoria** per Istanze di Azure Container. Entrambe le metriche sono disponibili per un gruppo di contenitori e per i singoli contenitori.

Le metriche della CPU sono espresse in **millicore**. Un millicore equivale a 1/1.000 di un core della CPU, quindi 500 millicore, o 500 m, rappresentano il 50% dell'uso di un core della CPU.

Le metriche della memoria sono espresse in **byte**.

## <a name="get-metrics---azure-portal"></a>Ottenere le metriche: portale di Azure

Quando viene creato un gruppo di contenitori, i dati di Monitoraggio di Azure sono disponibili nel portale di Azure. Per visualizzare le metriche di un gruppo di contenitori, selezionare il gruppo di risorse e quindi il gruppo di contenitori. Qui è possibile vedere i grafici creati in precedenza per l'uso della memoria e della CPU.

![grafico doppio][dual-chart]

Se un gruppo di contenitori contiene più contenitori, usare una [dimensione][monitor-dimension] per presentare le metriche per ogni singolo contenitore. Per creare un grafico con le metriche di un singolo contenitore, eseguire la procedura seguente:

1. Nel menu di spostamento a sinistra selezionare **Monitoraggio**.
2. Selezionare un gruppo di contenitori e una metrica della CPU o della memoria.
3. Selezionare il pulsante delle dimensioni verde e quindi **Nome contenitore**.

![dimensioni][dimension]

## <a name="get-metrics---azure-cli"></a>Ottenere le metriche: interfaccia della riga di comando di Azure

I dati sull'uso della CPU e della memoria delle Istanze di Container possono essere raccolti usando anche l'interfaccia della riga di comando di Azure. Recuperare innanzitutto l'ID del gruppo di contenitori usando il comando seguente. Sostituire `<resource-group>` con il nome del gruppo di risorse e `<container-group>` con il nome del gruppo di contenitori.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Usare il comando seguente per ottenere le metriche sull'uso della **CPU**

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

e il comando seguente per ottenere le metriche sull'uso della **memoria**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

Per un gruppo di più contenitori, la dimensione `containerName` può essere aggiunta per restituire i dati per ogni contenitore.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Monitoraggio di Azure sono disponibili nella [panoramica di Monitoraggio di Azure][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
