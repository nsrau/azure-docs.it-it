---
title: Riferimento ai dati di monitoraggio File di Azure | Microsoft Docs
description: Informazioni di riferimento su log e metriche per il monitoraggio dei dati da File di Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: f6dc523050fdcdbf54a6b9864f0a752698a85eed
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628935"
---
# <a name="azure-files-monitoring-data-reference"></a>Riferimento ai dati di monitoraggio File di Azure

Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per File di Azure, vedere [file di Azure di monitoraggio](storage-files-monitoring.md) .

## <a name="metrics"></a>Metriche

Le tabelle seguenti elencano le metriche della piattaforma raccolte per File di Azure. 

### <a name="capacity-metrics"></a>Metriche relative alla capacità

I valori delle metriche di capacità vengono aggiornati quotidianamente (fino a 24 ore). L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche relative alla capacità è un'ora (PT1H).

File di Azure fornisce le metriche di capacità seguenti in monitoraggio di Azure.

#### <a name="account-level"></a>Livello di account

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>File di Azure

Questa tabella mostra [file di Azure metrica](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Metrica | Descrizione |
| ------------------- | ----------------- |
| FileCapacity | Quantità di archiviazione file usata dall'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| FileCount   | Numero di file nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| FileShareCapacityQuota | Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024|
| FileShareCount | Numero di condivisioni file nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| FileShareProvisionedIOPS | Numero di IOPS sottoposti a provisioning in una condivisione file. Questa metrica è applicabile solo all'archiviazione file Premium. <br/><br/> Unità: byte <br/> Tipo di aggregazione: Media |
| FileShareSnapshotCount | Il numero di snapshot presenti nella condivisione nel servizio File di Azure dell'account di archiviazione. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: Media | 
|FileShareSnapshotSize|Quantità di spazio di archiviazione usata dagli snapshot nel servizio File di Azure dell'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media|

### <a name="transaction-metrics"></a>Metriche di transazione

Le metriche di transazione vengono generate su per tutte le richieste a un account di archiviazione da Archiviazione di Azure a Monitoraggio di Azure. In caso non siano presenti attività nell'account di archiviazione, non ci saranno dati per le metriche di transazione nel periodo. Tutte le metriche delle transazioni sono disponibili a livello di account e di servizio File di Azure. L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. Gli intervalli di tempo supportati per tutte le metriche di transazioni sono PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

File di Azure supporta le dimensioni seguenti per le metriche in monitoraggio di Azure.

> [!NOTE] 
> La dimensione della condivisione file non è disponibile per le condivisioni file standard (solo per le condivisioni file Premium). Quando si usano condivisioni file standard, le metriche fornite sono per tutte le condivisioni file nell'account di archiviazione. Per ottenere le metriche per condivisione per le condivisioni file standard, creare una condivisione file per ogni account di archiviazione.

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

- Per una descrizione del monitoraggio di archiviazione di Azure, vedere [file di Azure di monitoraggio](storage-files-monitoring-reference.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md).