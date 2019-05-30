---
title: File di inclusione
description: File di inclusione
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388761"
---
## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

Questa sezione descrive le funzionalità di Azure Time Series Insights che consentono di mantenere le App e servizi in esecuzione anche se si verifica un'emergenza. Questo concetto è noto come ripristino di emergenza aziendale.

### <a name="high-availability"></a>Disponibilità elevata

Come un servizio di Azure Time Series Insights offre alcune funzionalità a disponibilità elevata usando ridondanze a livello di area di Azure. Ad esempio, Microsoft Azure supporta la funzionalità di ripristino di emergenza tramite funzionalità di disponibilità tra aree di Azure.

Ulteriori funzionalità a disponibilità elevata fornite tramite Azure e che sono anche disponibili in qualsiasi istanza di Time Series Insights, includere:

* **Failover**: Azure offre [bilanciamento del carico e la replica geografica](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Il ripristino dei dati** e **ripristino archiviazione**: Azure offre [diverse opzioni per mantenere e ripristinare i dati](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Ripristino sito**: Le funzionalità disponibili attraverso [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Assicurarsi di abilitare queste funzionalità di Azure fornire la disponibilità elevata globale e tra più aree per utenti e dispositivi.

> [!NOTE]
> Se Azure è configurata per abilitare la disponibilità tra più aree, non è necessario all'interno di Azure Time Series Insights alcuna configurazione aggiuntiva disponibilità tra aree.

### <a name="iot-and-event-hubs"></a>IoT e hub eventi

Alcuni servizi IoT di Azure includono anche funzionalità di ripristino di emergenza aziendale integrate:

* [Ripristino di emergenza a disponibilità elevata dell'IoT Hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), che include la ridondanza tra aree.
* [Criteri di hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Ridondanza di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

L'integrazione di Time Series Insights con questi altri servizi fornisce le opportunità di ripristino di emergenza aggiuntiva. Ad esempio, i dati di telemetria inviati all'hub eventi può essere mantenuta in un backup del database di archiviazione Blob di Azure.

### <a name="time-series-insights"></a>Time Series Insights

Esistono diversi modi per mantenere i dati di Time Series Insights, App e servizi in esecuzione anche se queste si compromesse. Si potrebbe anche determinare che una copia di backup completa dell'ambiente Azure Time Series è necessaria:

* Come istanza di failover di Time Series Insights specifici per reindirizzare i dati e il traffico verso.
* Per scopi di mantenimento dati e il controllo.

In generale, il modo migliore per duplicare un ambiente Time Series Insights consiste nel creare un secondo ambiente Time Series Insights in un'area di Azure backup. Gli eventi vengono inviati anche in questo ambiente secondario dall'origine evento primario. Assicurarsi di utilizzare un gruppo di consumer dedicato in secondo luogo e seguire business disaster recovery linee guida tale dell'origine, fornite in precedenza.

In particolare, per creare un ambiente duplicato:

1. Creare un ambiente in una seconda area. Per istruzioni, vedere [creare un nuovo ambiente Time Series Insights nel portale di Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Creare un secondo gruppo di consumer dedicato per l'origine evento.
1. Connettere tale origine eventi al nuovo ambiente. Assicurarsi di definire il secondo gruppo di consumer dedicato.
1. Time Series Insights esaminare [hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [dell'hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentazione.

Infine:

1. Se l'area primaria viene compromessa durante un evento imprevisto di emergenza, reindirizzare le operazioni verso l'ambiente di backup di Time Series Insights.
1. Utilizzare la seconda area per eseguire il backup e ripristinare tutti i dati di telemetria e query Time Series Insights.

> [!IMPORTANT]
> * Si noti che, in caso di failover, è possibile che si verifichi un ritardo.
> * Il failover potrebbe causare anche un picco temporaneo nell'elaborazione dei messaggi come operazioni verranno reindirizzate.
> * Per altre informazioni, vedere [Mitigate latency in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency) (Mitigare la latenza in Time Series Insights).
