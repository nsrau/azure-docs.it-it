---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 07/09/2020
ms.openlocfilehash: f25c335c568c112c05f81df51d69e83aeff423e2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558327"
---
## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

Questa sezione descrive le funzionalità di Azure Time Series Insights per l'esecuzione di app e servizi, anche in caso di emergenza, nota come *ripristino di emergenza aziendale*.

### <a name="high-availability"></a>Disponibilità elevata

In qualità di servizio di Azure, Azure Time Series Insights fornisce alcune funzionalità di *disponibilità elevata* usando le ridondanze a livello di area di Azure. Ad esempio, Azure supporta le funzionalità di ripristino di emergenza tramite la funzionalità di *disponibilità tra aree* di Azure.

Le funzionalità aggiuntive di disponibilità elevata fornite tramite Azure (e disponibili anche per qualsiasi istanza di Azure Time Series Insights) includono:

- **Failover**: Azure fornisce la [replica geografica e il bilanciamento del carico](/azure/architecture/resiliency/recovery-loss-azure-region).
- Ripristino **dei dati** e **ripristino dell'archiviazione**: Azure offre [diverse opzioni per mantenere e ripristinare i dati](/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure fornisce le funzionalità di Site Recovery tramite [Azure Site Recovery](../articles/site-recovery/index.yml).
- **Backup** di Azure: [backup](../articles/backup/backup-architecture.md) di Azure supporta il backup sia in locale che nel cloud di macchine virtuali di Azure.

Assicurarsi di abilitare le funzionalità di Azure pertinenti per fornire la disponibilità elevata globale tra aree per i dispositivi e gli utenti.

> [!NOTE]
> Se Azure è configurato per abilitare la disponibilità tra aree, non è necessaria alcuna configurazione aggiuntiva di disponibilità tra aree in Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>Hub eventi e Internet

Alcuni servizi di Azure e includono anche funzionalità di ripristino di emergenza aziendale predefinite:

- [Ripristino di emergenza a disponibilità elevata dell'hub Azure](../articles/iot-hub/iot-hub-ha-dr.md), che include la ridondanza all'interno dell'area
- [Criteri di hub eventi di Azure](../articles/event-hubs/event-hubs-geo-dr.md)
- [Azure Storage redundancy](../articles/storage/common/storage-redundancy.md) (Ridondanza di Archiviazione di Azure)

L'integrazione di Azure Time Series Insights con gli altri servizi offre opportunità aggiuntive per il ripristino di emergenza. Ad esempio, la telemetria inviata all'hub eventi potrebbe essere salvata in modo permanente in un database di archiviazione BLOB di Azure di backup.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Sono disponibili diversi modi per proteggere i dati, le app e i servizi Azure Time Series Insights in esecuzione, anche se sono stati interrotti. 

Tuttavia, è possibile determinare che è necessaria anche una copia di backup completa dell'ambiente Azure Time Series, per gli scopi seguenti:

- Come *istanza di failover* in modo specifico per Azure Time Series Insights reindirizzare i dati e il traffico a
- Per mantenere i dati e le informazioni di controllo

In generale, il modo migliore per duplicare un ambiente di Azure Time Series Insights consiste nel creare un secondo ambiente Azure Time Series Insights in un'area di Azure di backup. Gli eventi vengono inviati anche a questo ambiente secondario dall'origine evento principale. Assicurarsi di usare un secondo gruppo di consumer dedicato. Seguire le linee guida per il ripristino di emergenza aziendale del codice sorgente, come descritto in precedenza.

Per creare un ambiente duplicato:

1. Creare un ambiente in una seconda area. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un nuovo ambiente Azure Time Series Insights nel portale di Azure](../articles/time-series-insights/time-series-insights-get-started.md).
1. Creare un secondo gruppo di consumer dedicato per l'origine evento.
1. Connettere tale origine eventi al nuovo ambiente. Assicurarsi di designare il secondo gruppo di consumer dedicato.
1. Esaminare la documentazione relativa all' [hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) Azure Time Series Insights e agli hub [eventi](../articles/time-series-insights/concepts-access-policies.md) .

Se si verifica un evento:

1. Se l'area primaria è interessata da un evento imprevisto di emergenza, reindirizzare le operazioni all'ambiente di Azure Time Series Insights di backup.
1. Usare la seconda area per eseguire il backup e il ripristino di tutti i dati di telemetria e di query Azure Time Series Insights.

> [!IMPORTANT]
> Se si verifica un failover:
> 
> * Potrebbe verificarsi anche un ritardo.
> * Potrebbe verificarsi un picco temporaneo nell'elaborazione del messaggio, perché le operazioni vengono reindirizzate.
> 
> Per altre informazioni, vedere [attenuare la latenza in Azure Time Series Insights](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).