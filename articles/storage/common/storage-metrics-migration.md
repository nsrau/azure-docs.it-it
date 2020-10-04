---
title: Passare dalla metrica Analisi archiviazione alle metriche di monitoraggio di Azure | Microsoft Docs
description: Informazioni su come eseguire la transizione da metriche di Analisi archiviazione (metriche classiche) a metriche in monitoraggio di Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 076f5573b599fbb83486087380174fc2da53986c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708578"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Transizione alle metriche in monitoraggio di Azure

Il **31 agosto 2023** analisi archiviazione metriche, dette anche *metriche classiche* , verranno ritirate. Per ulteriori informazioni, vedere l' [annuncio ufficiale](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se si usano metriche classiche, assicurarsi di passare alle metriche in monitoraggio di Azure prima di tale data. Questo articolo consente di eseguire la transizione. 

## <a name="steps-to-complete-the-transition"></a>Passaggi per completare la transizione

Per passare alle metriche in monitoraggio di Azure, è consigliabile usare l'approccio seguente.

1. Informazioni su alcune delle [principali differenze](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) tra le metriche classiche e le metriche in monitoraggio di Azure. 

2. Compilare un elenco di metriche classiche attualmente in uso.

3. Identificare le [metriche in monitoraggio di Azure che](#metrics-mapping-between-old-metrics-and-new-metrics) forniscono gli stessi dati delle metriche attualmente in uso. 
   
4. Creare [grafici](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) o [Dashboard](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) per visualizzare i dati delle metriche.

   > [!NOTE]
   > Le metriche in monitoraggio di Azure sono abilitate per impostazione predefinita, quindi non è necessario eseguire alcuna operazione per iniziare l'acquisizione delle metriche. È tuttavia necessario creare grafici o dashboard per visualizzare tali metriche. 
 
5. Se sono state create regole di avviso basate su metriche di archiviazione classiche, [creare regole di avviso](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) basate sulle metriche in monitoraggio di Azure. 

6. Dopo aver potuto visualizzare tutte le metriche in monitoraggio di Azure, è possibile disattivare la registrazione classica. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Metriche classiche rispetto a metriche in monitoraggio di Azure

Questa sezione descrive alcune delle principali differenze tra le due piattaforme di metrica.

La differenza principale riguarda il modo in cui vengono gestite le metriche. Le metriche classiche sono gestite da archiviazione di Azure, mentre le metriche in monitoraggio di Azure sono gestite da monitoraggio di Azure. Con le metriche classiche, archiviazione di Azure raccoglie i valori delle metriche, li aggrega e li archivia nelle tabelle che si trovano nell'account di archiviazione. Con le metriche in monitoraggio di Azure, archiviazione di Azure invia i dati delle metriche al back-end di monitoraggio di Azure. Monitoraggio di Azure offre un'esperienza di monitoraggio unificata che include i dati del portale di Azure e i dati inseriti. 

Per quanto riguarda il supporto delle metriche, le metriche classiche forniscono metriche di **capacità** solo per l'archiviazione BLOB di Azure. Le metriche in monitoraggio di Azure forniscono metriche di capacità per archiviazione BLOB, tabelle, file, code e Premium. Le metriche classiche forniscono la metrica **delle transazioni** su BLOB, tabelle, file di Azure e archiviazione code. Le metriche in monitoraggio di Azure aggiungono archiviazione Premium a tale elenco.

Se l'attività nell'account non attiva una metrica, le metriche classiche visualizzeranno un valore pari a zero (0) per la metrica. Le metriche in monitoraggio di Azure ometteranno interamente i dati, con conseguente ripulitura dei report. Con le metriche classiche, ad esempio, se non vengono segnalati errori di timeout del server, il `ServerTimeoutError` valore nella tabella Metrics viene impostato su 0. Il monitoraggio di Azure non restituisce dati quando si esegue una query sul valore della metrica `Transactions` con dimensione `ResponseType` uguale a `ServerTimeoutError` . 

Per altre informazioni sulle metriche in monitoraggio di Azure, vedere [metriche in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Eseguire il mapping delle metriche classiche alle metriche in monitoraggio di Azure

 Usare queste tabelle per identificare le metriche in monitoraggio di Azure che forniscono gli stessi dati delle metriche attualmente in uso. 

**Metriche della capacità**

| Metrica classica | Metrica in monitoraggio di Azure |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` con la dimensione `BlobType` uguale a `BlockBlob` o `PageBlob` |
| `ObjectCount`        | `BlobCount` con la dimensione `BlobType` uguale a `BlockBlob` o `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Sono inoltre disponibili diverse nuove metriche di capacità non disponibili come metriche classiche. Per visualizzare l'elenco completo, vedere [metriche](../common/monitor-storage-reference.md#metrics).

**Metriche delle transazioni**

| Metrica classica | Metrica in monitoraggio di Azure |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transazioni con dimensione `ResponseType` uguale a `AuthorizationError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousClientOtherError` | Transazioni con dimensione `ResponseType` uguale a `ClientOtherError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousClientTimeoutError` | Transazioni con dimensione `ResponseType` uguale a `ClientTimeoutError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousNetworkError` | Transazioni con dimensione `ResponseType` uguale a `NetworkError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousServerOtherError` | Transazioni con dimensione `ResponseType` uguale a `ServerOtherError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousServerTimeoutError` | Transazioni con dimensione `ResponseType` uguale a `ServerTimeoutError` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousSuccess` | Transazioni con dimensione `ResponseType` uguale a `Success` e dimensione `Authentication` uguale a `Anonymous` |
| `AnonymousThrottlingError` | Transazioni con dimensione `ResponseType` uguale a `ClientThrottlingError` o `ServerBusyError` e dimensione `Authentication` uguale a `Anonymous` |
| `AuthorizationError` | Transazioni con la dimensione `ResponseType` uguale a `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transazioni con la dimensione `ResponseType` uguale a `ClientOtherError` |
| `ClientTimeoutError` | Transazioni con la dimensione `ResponseType` uguale a `ClientTimeoutError` |
| `NetworkError` | Transazioni con la dimensione `ResponseType` uguale a `NetworkError` |
| `PercentAuthorizationError` | Transazioni con la dimensione `ResponseType` uguale a `AuthorizationError` |
| `PercentClientOtherError` | Transazioni con la dimensione `ResponseType` uguale a `ClientOtherError` |
| `PercentNetworkError` | Transazioni con la dimensione `ResponseType` uguale a `NetworkError` |
| `PercentServerOtherError` | Transazioni con la dimensione `ResponseType` uguale a `ServerOtherError` |
| `PercentSuccess` | Transazioni con la dimensione `ResponseType` uguale a `Success` |
| `PercentThrottlingError` | Transazioni con la dimensione `ResponseType` uguale a `ClientThrottlingError` o `ServerBusyError` |
| `PercentTimeoutError` | Transazioni con la dimensione `ResponseType` uguale `ServerTimeoutError` o `ResponseType` uguale a `ClientTimeoutError` |
| `SASAuthorizationError` | Transazioni con dimensione `ResponseType` uguale a `AuthorizationError` e dimensione `Authentication` uguale a `SAS` |
| `SASClientOtherError` | Transazioni con dimensione `ResponseType` uguale a `ClientOtherError` e dimensione `Authentication` uguale a `SAS` |
| `SASClientTimeoutError` | Transazioni con dimensione `ResponseType` uguale a `ClientTimeoutError` e dimensione `Authentication` uguale a `SAS` |
| `SASNetworkError` | Transazioni con dimensione `ResponseType` uguale a `NetworkError` e dimensione `Authentication` uguale a `SAS` |
| `SASServerOtherError` | Transazioni con dimensione `ResponseType` uguale a `ServerOtherError` e dimensione `Authentication` uguale a `SAS` |
| `SASServerTimeoutError` | Transazioni con dimensione `ResponseType` uguale a `ServerTimeoutError` e dimensione `Authentication` uguale a `SAS` |
| `SASSuccess` | Transazioni con dimensione `ResponseType` uguale a `Success` e dimensione `Authentication` uguale a `SAS` |
| `SASThrottlingError` | Transazioni con dimensione `ResponseType` uguale a `ClientThrottlingError` o `ServerBusyError` e dimensione `Authentication` uguale a `SAS` |
| `ServerOtherError` | Transazioni con la dimensione `ResponseType` uguale a `ServerOtherError` |
| `ServerTimeoutError` | Transazioni con la dimensione `ResponseType` uguale a `ServerTimeoutError` |
| `Success` | Transazioni con la dimensione `ResponseType` uguale a `Success` |
| `ThrottlingError` | `Transactions` con la dimensione `ResponseType` uguale a `ClientThrottlingError` o `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview.md)

