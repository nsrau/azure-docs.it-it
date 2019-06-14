---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431025"
---
## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

Questa sezione vengono descritte le funzionalità di Azure Time Series Insights che consentono di mantenere le App e servizi in esecuzione, anche se si verifica una situazione di emergenza (noto come *ripristino di emergenza aziendale*).

### <a name="high-availability"></a>Disponibilità elevata

Come un servizio di Azure Time Series Insights fornisce alcuni *disponibilità elevata* funzionalità con ridondanze a livello di area di Azure. Ad esempio, Azure supporta la funzionalità di ripristino di emergenza attraverso Azure *disponibilità tra aree* funzionalità.

Ulteriori funzionalità a disponibilità elevata fornite tramite Azure (e anche disponibile in qualsiasi istanza di Time Series Insights) includono:

- **Failover**: Azure offre [bilanciamento del carico e la replica geografica](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Il ripristino dei dati** e **ripristino archiviazione**: Azure offre [diverse opzioni per mantenere e ripristinare i dati](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Ripristino sito**: Azure offre funzionalità di ripristino sito attraverso [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Assicurarsi di che abilitare le funzionalità di Azure rilevanti fornire la disponibilità elevata globale e tra più aree per utenti e dispositivi.

> [!NOTE]
> Se Azure è configurata per abilitare la disponibilità tra più aree, è necessaria alcuna configurazione aggiuntiva disponibilità tra aree in Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hub eventi

Alcuni servizi IoT di Azure includono anche funzionalità di ripristino di emergenza aziendale integrate:

- [Ripristino di emergenza a disponibilità elevata dell'IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), tra cui la ridondanza tra aree
- [Criteri di hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy) (Ridondanza di Archiviazione di Azure)

L'integrazione di Time Series Insights con altri servizi fornisce le opportunità di ripristino di emergenza aggiuntiva. Ad esempio, i dati di telemetria inviati all'hub eventi può essere mantenuta in un backup del database di archiviazione Blob di Azure.

### <a name="time-series-insights"></a>Time Series Insights

Esistono diversi modi per mantenere i dati di Time Series Insights, App e servizi in esecuzione, anche se queste si compromesse. 

Tuttavia, si potrebbe determina che una copia di backup completa dell'ambiente Azure Time Series inoltre è necessaria, per gli scopi seguenti:

- Come un *istanza di failover* specificamente per Time Series Insights reindirizzare i dati e il traffico verso
- Per mantenere i dati e le informazioni di controllo

In generale, il modo migliore per duplicare un ambiente Time Series Insights consiste nel creare un secondo ambiente Time Series Insights in un'area di Azure backup. Gli eventi vengono inviati anche in questo ambiente secondario dall'origine evento primario. Assicurarsi di usare un gruppo di consumer in secondo luogo, dedicato. Attenersi alle istruzioni per il ripristino di emergenza aziendale che dell'origine, come descritto in precedenza.

Per creare un ambiente duplicato:

1. Creare un ambiente in una seconda area. Per altre informazioni, vedere [creare un nuovo ambiente Time Series Insights nel portale di Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Creare un secondo gruppo di consumer dedicato per l'origine evento.
1. Connettere tale origine eventi al nuovo ambiente. Assicurarsi di riservare il gruppo di consumer in secondo luogo, dedicato.
1. Time Series Insights esaminare [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) documentazione.

Se si verifica un evento:

1. Se l'area primaria viene compromessa durante un evento imprevisto di emergenza, reindirizzare le operazioni verso l'ambiente di backup di Time Series Insights.
1. Utilizzare la seconda area per eseguire il backup e ripristinare tutti i dati di telemetria e query Time Series Insights.

> [!IMPORTANT]
> Se si verifica un failover:
> 
> * Potrebbe anche verificarsi un ritardo.
> * Potrebbe verificarsi un picco temporaneo nell'elaborazione dei messaggi, come operazioni verranno reindirizzate.
> 
> Per altre informazioni, vedere [Mitigate latency in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency) (Mitigare la latenza in Time Series Insights).

