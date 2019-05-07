---
title: Migrazione delle metriche di Archiviazione di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione dalle metriche precedenti alle nuove metriche gestite da Monitoraggio di Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 58ac15c1aba715c9a5b67e723401b531e76608b2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153605"
---
# <a name="azure-storage-metrics-migration"></a>Migrazione delle metriche di Archiviazione di Azure

Per allinearsi alla strategia di unificazione dell'esperienza di monitoraggio in Azure, Archiviazione di Azure integra le metriche nella piattaforma di Monitoraggio di Azure. In futuro, l'uso delle metriche precedenti terminerà con una notifica tempestiva basata sui criteri di Azure. Se si usano le metriche di archiviazione precedenti, è necessario eseguire la migrazione prima della data stabilita per il termine del servizio, per evitare di perdere le informazioni delle metriche.

Questo articolo illustra come eseguire la migrazione dalle metriche precedenti alle nuove metriche.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Informazioni sulle metriche precedenti gestite da Archiviazione di Azure

Archiviazione di Azure raccoglie i valori delle metriche precedenti, li aggrega e li archivia nelle tabelle $Metric nello stesso account di archiviazione. È possibile usare il portale di Azure per configurare un grafico di monitoraggio. È anche possibile usare Azure Storage SDK per leggere i dati dalle tabelle $Metric in base allo schema. Per altre informazioni, vedere [Analisi archiviazione](./storage-analytics.md).

Le metriche precedenti forniscono metriche relative alla capacità solo per Archiviazione BLOB di Azure. Le metriche precedenti forniscono metriche relative alle transazioni per l'archiviazione BLOB, l'archiviazione tabelle, File di Azure e l'archiviazione code.

Le metriche precedenti sono progettate in base a uno schema piatto. Viene pertanto restituito un valore zero anche quando non sono presenti modelli di traffico che attivano una metrica. Ad esempio, il valore **ServerTimeoutError** viene impostato su 0 nelle tabelle $Metric anche quando non si ricevono errori di timeout del server dal traffico in tempo reale in un account di archiviazione.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Nozioni di base sulle nuove metriche gestite da Monitoraggio di Azure

Per le nuove metriche relative all'archiviazione, Archiviazione di Azure invia i dati al back-end di Monitoraggio di Azure. Monitoraggio di Azure offre un'esperienza di monitoraggio unificata, che include i dati del portale e quelli dei processi di inserimento dati. Per informazioni più dettagliate, è possibile vedere questo [articolo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Le nuove metriche forniscono dati sulla capacità e sulle transazioni per BLOB, tabelle, file, code e risorse di archiviazione Premium.

La multidimensionalità è una delle funzionalità offerte da Monitoraggio di Azure. Il nuovo schema delle metriche in Archiviazione di Azure è definito in base a questa caratteristica. Per informazioni sulle dimensioni supportate per le metriche, vedere [Metriche di Archiviazione di Azure in Monitoraggio di Azure](./storage-metrics-in-azure-monitor.md). La progettazione basata su più dimensioni offre una soluzione più conveniente in termini sia di larghezza di banda per l'inserimento dati sia di capacità per le metriche di archiviazione. Di conseguenza, se il traffico non attiva determinate metriche, i dati delle metriche correlati non vengono generati. Se, ad esempio, il traffico non ha attivato errori di timeout del server, Monitoraggio di Azure non restituisce alcun dato quando si esegue una query sul valore della metrica **Transactions** con dimensione **ResponseType** uguale a **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapping tra le metriche precedenti e le nuove metriche

Se si leggono i dati delle metriche a livello di codice, nei programmi è necessario adottare il nuovo schema delle metriche. Per comprendere meglio le modifiche, è possibile fare riferimento al mapping illustrato nella tabella seguente:

**Metriche della capacità**

| Metrica precedente | Nuova metrica |
| ------------------- | ----------------- |
| **Capacity**            | **BlobCapacity** con dimensione **BlobType** equivalente a **BlockBlob** o **PageBlob** |
| **ObjectCount**        | **BlobCount** con dimensione **BlobType** equivalente a **BlockBlob** o **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

L'elenco seguente include le nuove metriche non supportate in precedenza:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metriche delle transazioni**

| Metrica precedente | Nuova metrica |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transactions con dimensione **ResponseType** uguale a **AuthorizationError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousClientOtherError** | Transactions con dimensione **ResponseType** uguale a **ClientOtherError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousClientTimeoutError** | Transactions con dimensione **ResponseType** uguale a **ClientTimeoutError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousNetworkError** | Transactions con dimensione **ResponseType** uguale a **NetworkError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousServerOtherError** | Transactions con dimensione **ResponseType** uguale a **ServerOtherError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousServerTimeoutError** | Transactions con dimensione **ResponseType** uguale a **ServerTimeoutError** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousSuccess** | Transactions con dimensione **ResponseType** uguale a **Success** e dimensione **Authentication** uguale a **Anonymous** |
| **AnonymousThrottlingError** | Transactions con dimensione **ResponseType** uguale a **ClientThrottlingError** or **ServerBusyError** e dimensione **Authentication** uguale a **Anonymous** |
| **AuthorizationError** | Transactions con dimensione **ResponseType** equivalente a **AuthorizationError** |
| **Disponibilità** | **Disponibilità** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transactions con dimensione **ResponseType** equivalente a **ClientOtherError** |
| **ClientTimeoutError** | Transactions con dimensione **ResponseType** equivalente a **ClientTimeoutError** |
| **NetworkError** | Transactions con dimensione **ResponseType** equivalente a **NetworkError** |
| **PercentAuthorizationError** | Transactions con dimensione **ResponseType** equivalente a **AuthorizationError** |
| **PercentClientOtherError** | Transactions con dimensione **ResponseType** equivalente a **ClientOtherError** |
| **PercentNetworkError** | Transactions con dimensione **ResponseType** equivalente a **NetworkError** |
| **PercentServerOtherError** | Transactions con dimensione **ResponseType** equivalente a **ServerOtherError** |
| **PercentSuccess** | Transactions con dimensione **ResponseType** equivalente a **Success** |
| **PercentThrottlingError** | Transactions con dimensione **ResponseType** equivalente a **ClientThrottlingError** o **ServerBusyError** |
| **PercentTimeoutError** | Transactions con dimensione **ResponseType** equivalente a **ServerTimeoutError** o **ResponseType** equivalente a **ClientTimeoutError** |
| **SASAuthorizationError** | Transactions con dimensione **ResponseType** uguale a **AuthorizationError** e dimensione **Authentication** uguale a **SAS** |
| **SASClientOtherError** | Transactions con dimensione **ResponseType** uguale a **ClientOtherError** e dimensione **Authentication** uguale a **SAS** |
| **SASClientTimeoutError** | Transactions con dimensione **ResponseType** uguale a **ClientTimeoutError** e dimensione **Authentication** uguale a **SAS** |
| **SASNetworkError** | Transactions con dimensione **ResponseType** uguale a **NetworkError** e dimensione **Authentication** uguale a **SAS** |
| **SASServerOtherError** | Transactions con dimensione **ResponseType** uguale a **ServerOtherError** e dimensione **Authentication** uguale a **SAS** |
| **SASServerTimeoutError** | Transactions con dimensione **ResponseType** uguale a **ServerTimeoutError** e dimensione **Authentication** uguale a **SAS** |
| **SASSuccess** | Transactions con dimensione **ResponseType** uguale a **Success** e dimensione **Authentication** uguale a **SAS** |
| **SASThrottlingError** | Transactions con dimensione **ResponseType** uguale a **ClientThrottlingError** or **ServerBusyError** e dimensione **Authentication** uguale a **SAS** |
| **ServerOtherError** | Transactions con dimensione **ResponseType** equivalente a **ServerOtherError** |
| **ServerTimeoutError** | Transactions con dimensione **ResponseType** equivalente a **ServerTimeoutError** |
| **Success** | Transactions con dimensione **ResponseType** equivalente a **Success** |
| **ThrottlingError** | **Transactions** con dimensione **ResponseType** equivalente a **ClientThrottlingError** o **ServerBusyError**|
| **TotalBillableRequests** | **Transazioni** |
| **TotalEgress** | **Dati in uscita** |
| **TotalIngress** | **Dati in ingresso** |
| **TotalRequests** | **Transazioni** |

## <a name="faq"></a>Domande frequenti

### <a name="how-should-i-migrate-existing-alert-rules"></a>Come eseguire la migrazione delle regole di avviso esistenti?

Se sono state create regole di avviso classiche in base a metriche di archiviazione precedenti, è necessario creare nuove regole di avviso in base al nuovo schema delle metriche.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>I dati delle nuove metriche vengono archiviati nello stesso account di archiviazione per impostazione predefinita?

 No. Per archiviare i dati delle metriche in un account di archiviazione, usare l'[API delle impostazioni di diagnostica di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriche di Archiviazione di Azure in Monitoraggio di Azure](./storage-metrics-in-azure-monitor.md)
