---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013697"
---
## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

In questa sezione vengono descritte le funzionalità di Azure Time Series Insights che mantengono in esecuzione app e servizi, anche in caso di emergenza (noto come ripristino di *emergenza aziendale).*

### <a name="high-availability"></a>Disponibilità elevata

Come servizio di Azure, Time Series Insights offre alcune funzionalità di disponibilità elevata usando ridondanze a livello di area di Azure.As an Azure service, Time Series Insights provides certain *high availability* features by using rebedancies at the Azure region level. Ad esempio, Azure supporta le funzionalità di ripristino di emergenza tramite la funzionalità di disponibilità tra aree di Azure.For example, Azure supports disaster recovery capabilities through Azure's *cross-region availability* feature.

Altre funzionalità di disponibilità elevata fornite tramite Azure (e disponibili anche per qualsiasi istanza di Time Series Insights) includono:Additional high-availability features provided through Azure (and also available to any Time Series Insights instance) include:

- **Failover:** Azure fornisce [la replica geografica e il bilanciamento del carico.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
- **Ripristino dei dati** e **ripristino dell'archiviazione:** Azure offre [diverse opzioni per conservare e ripristinare i dati.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)
- **Azure Site Recovery:** Azure offre funzionalità di ripristino del sito tramite [Azure Site Recovery.](https://docs.microsoft.com/azure/site-recovery/)
- **Backup di Azure:** Backup di Azure supporta il backup locale e nel cloud delle macchine virtuali di Azure.Azure Backup : [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) supports both on-premises and in-cloud backup of Azure VMs.

Assicurarsi di abilitare le funzionalità di Azure pertinenti per fornire disponibilità elevata globale tra aree per i dispositivi e gli utenti.

> [!NOTE]
> Se Azure è configurato per abilitare la disponibilità tra aree, non è necessaria alcuna configurazione aggiuntiva per la disponibilità tra aree in Azure Time Series Insights.If Azure is configured to enable cross-region availability, no additional cross-region availability configuration is required in Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hub eventi

Alcuni servizi IoT di Azure includono anche funzionalità di ripristino di emergenza aziendali incorporate:Some Azure IoT services also include built-in business disaster recovery features:

- Ripristino di emergenza a disponibilità elevata dell'hub IoT di Azure, che include la ridondanza all'intra-areaAzure [IoT Hub high-availability disaster recovery](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), which includes intra-region redundancy
- [Criteri di Hub eventi di AzureAzure Event Hubs policies](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Ridondanza di Archiviazione di AzureAzure Storage redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

L'integrazione di Time Series Insights con gli altri servizi offre ulteriori opportunità di ripristino di emergenza. Ad esempio, i dati di telemetria inviati all'hub eventi potrebbero essere resi persistenti in un database di archiviazione BLOB di Azure di backup.

### <a name="time-series-insights"></a>Time Series Insights

Esistono diversi modi per mantenere in esecuzione i dati, le app e i servizi di Time Series Insights, anche in caso di interruzione. 

Tuttavia, è possibile determinare che è necessaria anche una copia di backup completa dell'ambiente Azure Time Series, per gli scopi seguenti:However, you might determine that a complete backup copy of your Azure Time Series environment is also required, for the following purposes:

- Come *istanza* di failover specifica per Time Series Insights per reindirizzare i dati e il traffico
- Per conservare i dati e le informazioni di controllo

In generale, il modo migliore per duplicare un ambiente Time Series Insights consiste nel creare un secondo ambiente Time Series Insights in un'area di Azure di backup. Gli eventi vengono inoltre inviati a questo ambiente secondario dall'origine eventi primaria. Assicurarsi di utilizzare un secondo gruppo di consumer dedicato. Seguire le linee guida per il ripristino di emergenza aziendale dell'origine, come descritto in precedenza.

Per creare un ambiente duplicato:

1. Creare un ambiente in una seconda area. Per altre informazioni, vedere Creare un nuovo ambiente Time Series Insights nel portale di Azure .For more [information,](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)read Create a new Time Series Insights environment in the Azure portal .
1. Creare un secondo gruppo di consumer dedicato per l'origine evento.
1. Connettere tale origine eventi al nuovo ambiente. Assicurarsi di designare il secondo gruppo di consumer dedicato.
1. Esaminare la documentazione relativa [all'hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e agli [hub eventi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) di Time Series Insights.

Se si verifica un evento:

1. Se l'area primaria viene compromessa durante un evento imprevisto di emergenza, reindirizzare le operazioni verso l'ambiente di backup di Time Series Insights.
1. Usare la seconda area per eseguire il backup e il ripristino di tutti i dati di telemetria e di query di Time Series Insights.Use your second region to back up and recover all Time Series Insights telemetry and query data.

> [!IMPORTANT]
> Se si verifica un failover:If a failover occurs:
> 
> * Potrebbe anche verificarsi un ritardo.
> * Potrebbe verificarsi un picco momentaneo nell'elaborazione dei messaggi, quando le operazioni vengono reindirizzate.
> 
> Per altre informazioni, vedere [Mitigate latency in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

