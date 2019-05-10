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
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236524"
---
## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

Questa sezione vengono descritte le funzionalità di Azure Time Series Insights che consentono di mantenere le App e servizi in esecuzione anche se si verifica una situazione di emergenza (**ripristino di emergenza aziendale**).

### <a name="high-availability"></a>Disponibilità elevata

Come un servizio di Azure Time Series Insights fornisce alcuni **disponibilità elevata** features usando ridondanze a livello di area di Azure. Ad esempio, Microsoft Azure supporta la funzionalità di ripristino di emergenza attraverso Azure **disponibilità tra aree** funzionalità.

Le funzionalità aggiuntive di disponibilità elevata fornite tramite Azure (e anche disponibile in qualsiasi istanza di Time Series Insights) includono:

1. **Failover**: Azure offre [replica geografica e il caricamento di bilanciamento del carico](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Il ripristino dei dati** e **ripristino archiviazione**: Azure offre [diverse opzioni per mantenere e ripristinare i dati](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** features attraverso [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Assicurarsi di abilitare queste funzionalità di Azure fornire globale, tra aree e disponibilità elevata per i dispositivi e utenti.

> [!NOTE]
> Se Azure è configurata per abilitare **disponibilità tra aree**, alcuna configurazione aggiuntiva disponibilità tra aree non è necessario all'interno di Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hub eventi

Alcuni servizi IoT di Azure includono anche funzionalità di ripristino di emergenza aziendale integrate:

1. [Ripristino di emergenza a disponibilità elevata dell'IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) tra cui la ridondanza tra aree.
1. [Criteri dell'Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Ridondanza di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

L'integrazione di Time Series Insights con questi altri servizi fornisce le opportunità di ripristino di emergenza aggiuntiva. Ad esempio, i dati di telemetria inviati all'hub eventi può essere mantenuta in un backup del database di archiviazione Blob di Azure.

### <a name="time-series-insights"></a>Time Series Insights

Esistono diversi modi per mantenere i dati di Time Series Insights, App e servizi in esecuzione anche se queste si compromesse. Si potrebbe anche determinare che una copia completa, duplicata e backup dell'ambiente Azure Time Series è necessaria:

1. Come un oggetto specifico di Time Series Insights **istanza di failover** per reindirizzare i dati e il traffico verso.
1. Per scopi di mantenimento dati e il controllo.

In generale, il modo migliore per duplicare un ambiente Time Series Insights consiste nel creare un secondo ambiente Time Series Insights in un'area di Azure backup. Gli eventi vengono inviati anche in questo ambiente secondario dall'origine evento primario. Assicurarsi di utilizzare un gruppo di consumer dedicato, secondo e di seguire istruzioni per il ripristino di emergenza business dell'origine (fornite in precedenza).

In particolare, per creare un ambiente duplicato:

1. Creare un ambiente in una seconda area ([creare un nuovo ambiente Time Series Insights nel portale di Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Creare un secondo gruppo di consumer dedicato per l'origine evento.
1. Connettersi a tale origine eventi nel nuovo ambiente assicurandosi di definire il gruppo di consumer in secondo luogo, dedicato.
1. Time Series Insights esaminare [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [dell'hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentazione.

Infine:

* Se l'area primaria viene compromessa durante un evento imprevisto di emergenza, reindirizzare le operazioni verso l'ambiente di backup di Time Series Insights.
* Eseguire il backup e ripristinare tutti i dati di telemetria e query Time Series Insights, usare la seconda area.

> [!IMPORTANT]
> * Si noti che, in caso di failover, è possibile che si verifichi un ritardo.
> * Il failover potrebbe causare anche un picco temporaneo nell'elaborazione dei messaggi come operazioni verranno reindirizzate.
> * Per altre informazioni, vedere [Mitigate latency in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency) (Mitigare la latenza in Time Series Insights).