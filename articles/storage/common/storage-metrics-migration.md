---
title: Migrazione delle metriche di Archiviazione di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione dalle metriche legacy alle nuove metriche gestite da Monitoraggio di Azure.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: da8eb0b9e2e5aba60b61a36d83f525c7ce4a7958
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776262"
---
# <a name="azure-storage-metrics-migration"></a>Migrazione delle metriche di Archiviazione di Azure

La migrazione delle metriche di Archiviazione alla piattaforma di Monitoraggio di Azure è allineata alla strategia di unificazione dell'esperienza di Monitoraggio in Azure. In futuro, il servizio delle metriche legacy verrà terminato con un preavviso in base ai criteri di Azure. Se si usano metriche di archiviazione legacy, è necessario eseguire la migrazione prima della data stabilita per il termine del servizio per evitare di perdere le informazioni delle metriche.

Questo articolo illustra la migrazione dalle metriche legacy alle nuove metriche.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Nozioni di base sulle metriche legacy gestite da Archiviazione di Azure

Archiviazione di Azure raccoglie i valori delle metriche legacy, li aggrega e li archivia nelle tabelle $Metric nello stesso account di archiviazione. È possibile usare il portale di Azure per configurare il grafico di monitoraggio e gli SDK di Archiviazione di Azure per leggere i dati dalle tabelle $Metric in base allo schema. Per informazioni più dettagliate, è possibile vedere [Analisi archiviazione](./storage-analytics.md).

Le metriche legacy forniscono dati sulla capacità solo per BLOB e dati sulle transazioni per BLOB, tabelle, file e code.

Le metriche legacy sono state progettate in base a uno schema piatto. Viene pertanto restituito un valore zero anche quando non sono presenti modelli di traffico che attivano una metrica. Ad esempio, **ServerTimeoutError** viene impostato su 0 nelle tabelle $Metric anche quando non vengono restituiti errori di timeout del server dal traffico in tempo reale all'account di archiviazione.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Nozioni di base sulle nuove metriche gestite da Monitoraggio di Azure

Per le nuove metriche, Archiviazione di Azure invia i dati al back-end di Monitoraggio di Azure. Monitoraggio Azure offre un'esperienza di monitoraggio unificata, includendo i dati del portale e quelli dei processi di inserimento. Per informazioni più dettagliate, è possibile vedere questo [articolo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Le nuove metriche forniscono dati sulla capacità e sulle transazioni per BLOB, tabelle, file, code e risorse di archiviazione Premium.

Il supporto di più dimensioni è una delle caratteristiche di Monitoraggio di Azure. Il nuovo schema delle metriche in Archiviazione di Azure è definito in base a questa caratteristica. Per informazioni sulle dimensioni supportate per le metriche, è possibile vedere [Metriche di Archiviazione di Azure in Monitoraggio di Azure](./storage-metrics-in-azure-monitor.md). La progettazione basata su più dimensioni offre una soluzione più conveniente in termini sia di larghezza di banda per l'inserimento dati sia di capacità per le metriche di archiviazione. Di conseguenza, se il traffico non attiva determinate metriche, i dati delle metriche correlati non vengono generati. Se ad esempio il traffico non attiva errori di timeout del server, Monitoraggio di Azure non restituisce alcun dato quando si esegue una query sul valore della metrica **Transactions** con dimensione **ResponseType** uguale a **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Mapping tra le metriche legacy e le nuove metriche

Se si leggono i dati delle metriche a livello di codice, nei programmi è necessario adottare il nuovo schema delle metriche. Per comprendere meglio le modifiche, è possibile fare riferimento al mapping delle metriche illustrato nella tabella seguente:

**Metriche della capacità**

| Metrica legacy | Nuova metrica |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity con dimensione BlobType uguale a BlockBlob o PageBlob |
| ObjectCount         | BlobCount con dimensione BlobType uguale a BlockBlob o PageBlob |
| ContainerCount      | ContainerCount |

L'elenco seguente include le nuove metriche non supportate in precedenza:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Metriche delle transazioni**

| Metrica legacy | Nuova metrica |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transactions con dimensione ResponseType uguale a AuthorizationError |
| AnonymousClientOtherError | Transactions con dimensione ResponseType uguale a ClientOtherError |
| AnonymousClientTimeoutError | Transactions con dimensione ResponseType uguale a ClientTimeoutError |
| AnonymousNetworkError | Transactions con dimensione ResponseType uguale a NetworkError |
| AnonymousServerOtherError | Transactions con dimensione ResponseType uguale a ServerOtherError |
| AnonymousServerTimeoutError | Transactions con dimensione ResponseType uguale a ServerTimeoutError |
| AnonymousSuccess | Transactions con dimensione ResponseType uguale a Success |
| AnonymousThrottlingError | Transactions con dimensione ResponseType uguale a ClientThrottlingError o ServerBusyError |
| AuthorizationError | Transactions con dimensione ResponseType uguale a AuthorizationError |
| Disponibilità | Disponibilità |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transactions con dimensione ResponseType uguale a ClientOtherError |
| ClientTimeoutError | Transactions con dimensione ResponseType uguale a ClientTimeoutError |
| NetworkError | Transactions con dimensione ResponseType uguale a NetworkError |
| PercentAuthorizationError | Transactions con dimensione ResponseType uguale a AuthorizationError |
| PercentClientOtherError | Transactions con dimensione ResponseType uguale a ClientOtherError |
| PercentNetworkError | Transactions con dimensione ResponseType uguale a NetworkError |
| PercentServerOtherError | Transactions con dimensione ResponseType uguale a ServerOtherError |
| PercentSuccess | Transactions con dimensione ResponseType uguale a Success |
| PercentThrottlingError | Transactions con dimensione ResponseType uguale a ClientThrottlingError o ServerBusyError |
| PercentTimeoutError | Transactions con dimensione ResponseType uguale a ServerTimeoutError o ClientTimeoutError|
| SASAuthorizationError | Transactions con dimensione ResponseType uguale a AuthorizationError |
| SASClientOtherError | Transactions con dimensione ResponseType uguale a ClientOtherError |
| SASClientTimeoutError | Transactions con dimensione ResponseType uguale a ClientTimeoutError |
| SASNetworkError | Transactions con dimensione ResponseType uguale a NetworkError |
| SASServerOtherError | Transactions con dimensione ResponseType uguale a ServerOtherError |
| SASServerTimeoutError | Transactions con dimensione ResponseType uguale a ServerTimeoutError |
| SASSuccess | Transactions con dimensione ResponseType uguale a Success |
| SASThrottlingError | Transactions con dimensione ResponseType uguale a ClientThrottlingError o ServerBusyError |
| ServerOtherError | Transactions con dimensione ResponseType uguale a ServerOtherError |
| ServerTimeoutError | Transactions con dimensione ResponseType uguale a ServerTimeoutError |
| Success | Transactions con dimensione ResponseType uguale a Success |
| ThrottlingError | Transactions con dimensione ResponseType uguale a ClientThrottlingError o ServerBusyError |
| TotalBillableRequests | Transazioni |
| TotalEgress | Egress |
| TotalIngress | Dati in ingresso |
| TotalRequests | Transazioni |

## <a name="faq"></a>Domande frequenti

* Come eseguire la migrazione delle regole di avviso esistenti?

R: Se sono state create regole di avviso classiche in base a metriche di archiviazione legacy, è necessario creare nuove regole di avviso in base al nuovo schema delle metriche.

* I dati delle nuove metriche vengono archiviati nello stesso account di archiviazione per impostazione predefinita?

R: No. Se è necessario archiviare i dati delle metriche nell'account di archiviazione è possibile usare l'[API delle impostazioni di diagnostica in Monitoraggio di Azure](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate)

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriche di Archiviazione di Azure in Monitoraggio di Azure](./storage-metrics-in-azure-monitor.md)
