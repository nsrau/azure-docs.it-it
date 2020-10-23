---
title: Riferimento ai dati di monitoraggio dell'archiviazione code di Azure | Microsoft Docs
description: Informazioni di riferimento su log e metriche per il monitoraggio dei dati dall'archiviazione code di Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 396d2785d80ff29212dddcfb97adbff8ba9ce4c8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425165"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Riferimento ai dati di monitoraggio dell'archiviazione code di Azure

Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per Archiviazione di Azure, vedere [Monitoraggio di Archiviazione di Azure](monitor-queue-storage.md).

## <a name="metrics"></a>Metriche

Le tabelle seguenti elencano le metriche della piattaforma raccolte per Archiviazione di Azure. 

### <a name="capacity-metrics"></a>Metriche della capacità

I valori delle metriche relative alla capacità vengono inviati a Monitoraggio di Azure ogni ora. I valori vengono aggiornati ogni giorno. L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche relative alla capacità è un'ora (PT1H).

Archiviazione di Azure fornisce le metriche seguenti relative alla capacità in Monitoraggio di Azure.

#### <a name="account-level"></a>Livello di account

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Archiviazione code

Questa tabella mostra le [metriche di archiviazione delle code](/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices).

| Metrica | Descrizione |
| ------------------- | ----------------- |
| QueueCapacity | Quantità di archiviazione code usata dall'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| QueueCount   | Numero di code nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| QueueMessageCount | Numero approssimativo di messaggi della coda nel Servizio di accodamento dell'account di archiviazione. <br/><br/>Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

### <a name="transaction-metrics"></a>Metriche di transazione

Le metriche di transazione vengono generate su per tutte le richieste a un account di archiviazione da Archiviazione di Azure a Monitoraggio di Azure. In caso non siano presenti attività nell'account di archiviazione, non ci saranno dati per le metriche di transazione nel periodo. Tutte le metriche delle transazioni sono disponibili sia per l'account che per il livello di servizio di archiviazione di Accodamento. L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. Gli intervalli di tempo supportati per tutte le metriche di transazioni sono PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Archiviazione di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Log risorse (anteprima)

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Questa anteprima abilita i log per BLOB (compreso Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. gli account di archiviazione di tipo classico non sono supportati.

La tabella seguente elenca le proprietà dei log delle risorse di Archiviazione di Azure quando vengono raccolti nei log di Monitoraggio di Azure o in Archiviazione di Azure. Le proprietà descrivono l'operazione, il servizio e il tipo di autorizzazione usati per eseguire l'operazione.

### <a name="fields-that-describe-the-operation"></a>Campi che descrivono l'operazione

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campi che descrivono come è stata autenticata l'operazione

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campi che descrivono il servizio

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio di archiviazione di Azure, vedere [monitoraggio dell'archiviazione code di Azure](monitor-queue-storage.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md).