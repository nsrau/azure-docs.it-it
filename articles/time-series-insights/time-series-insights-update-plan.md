---
title: Pianificare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs
description: Pianificare un ambiente di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076997"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Pianificare un ambiente di anteprima di Azure Time Series Insights

Questo articolo descrive le procedure consigliate per pianificare e iniziare a usare rapidamente l'anteprima di Azure Time Series Insights.

## <a name="best-practices-for-planning-and-preparation"></a>Procedure consigliate per la pianificazione e preparazione

Per iniziare a usare Time Series Insights, è consigliabile conoscere:

* I vantaggi del provisioning di un ambiente di anteprima di Time Series Insights.
* Le proprietà di Timestamp e ID di Time Series.
* Che cos'è il nuovo modello Time Series e come crearne uno personalizzato.
* Come inviare in modo efficiente gli eventi in formato JSON. 
* Opzioni di ripristino di emergenza aziendale di Time Series Insights.

Time Series Insights usa un modello business con pagamento in base al consumo. Per altre informazioni sui costi e la capacità, vedere [Time Series Insights pricing](https://azure.microsoft.com/pricing/details/time-series-insights/) (Prezzi di Time Series Insights).

## <a name="the-time-series-insights-preview-environment"></a>Ambiente di anteprima di Time Series Insights

Quando si esegue il provisioning di un ambiente di anteprima di Time Series Insights, vengono create due risorse di Azure:

* Ambiente di anteprima di Time Series Insights
* Account di Archiviazione di Azure (uso generico V1)

Per iniziare, sono necessari altri tre elementi:
 
- Un [Modello Time Series](./time-series-insights-update-tsm.md) 
- Un [origine evento connessa a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Eventi che passino nell'origine evento](./time-series-insights-send-events.md) e che siano entrambi mappati al modello e abbiano un formato JSON valido 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Configurare le proprietà di Timestamp e ID di Time Series

Per creare un nuovo ambiente Time Series Insights, selezionare un ID Time Series. Tale operazione funge da partizione logica per i dati. Come accennato, assicurarsi di disporre dell'ID Time Series a portata di mano.

> [!IMPORTANT]
> Gli ID Time Series sono *immutabili* e *non possono essere modificati in un secondo momento*. Verificare ognuno di essi prima della selezione finale e del primo uso.

È possibile selezionare fino a tre (3) chiavi per distinguere le risorse in modo univoco. Per altre informazioni, leggere [Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md) (Procedure consigliate per la scelta di un ID Time Series) e [Storage and ingress](./time-series-insights-update-storage-ingress.md) (Archiviazione e ingresso).

Anche la proprietà Timestamp è importante. È possibile definire questa proprietà quando si aggiungono origini evento. Ogni origine evento include una proprietà Timestamp facoltativa, usata per tenere traccia delle origini evento nel tempo. I valori di Timestamp fanno distinzione tra maiuscole/minuscole e devono essere formattati in base alle singole specifiche di ogni origine evento.

> [!TIP]
> Verificare i requisiti di formattazione e analisi per le origini evento.

Se lasciato vuoto, come timestamp dell'evento viene usato il tempo di accodamento dell'evento di un origine evento. Se si inviano dati cronologici o eventi in batch, la personalizzazione della proprietà Timestamp è più utile rispetto al tempo di accodamento dell'evento. Per altre informazioni, leggere [How to add event sources in IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) (Procedura di aggiunta di origini evento nell'hub IoT). 

## <a name="understand-the-time-series-model"></a>Comprendere il modello Time Series

È ora possibile configurare il modello Time Series dell'ambiente Time Series Insights. Il nuovo modello semplifica l'individuazione e l'analisi dei dati IoT, consentendo la cura, la manutenzione e l'arricchimento dei dati Time Series e la preparazione di set di dati di livello consumer. Il modello usa **ID Time Series**, che esegue il mapping a un'istanza che associa la risorsa univoca a variabili, note come tipi, e gerarchie. Informazioni sul nuovo [modello Time Series](./time-series-insights-update-tsm.md).

Il modello è dinamico, in modo da poter essere compilato in qualsiasi momento. Per iniziare rapidamente, compilarlo e caricarlo prima di eseguire il push dei dati in Time Series Insights. Per compilare il modello, vedere [Use the Time Series Model](./time-series-insights-update-how-to-tsm.md) (Usare il modello Time Series).

Per molti clienti, il modello Time Series esegue il mapping a un modello di risorse esistente o al sistema ERP già in uso. Se non si dispone di un modello esistente, viene [offerta](https://github.com/Microsoft/tsiclient) un'esperienza utente predefinita per ottenerlo rapidamente. Per conoscere i vantaggi di un modello, visualizzare l'[ambiente demo di esempio](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Dare forma agli eventi

È possibile verificare il modo in cui si inviano eventi a Time Series Insights. In teoria, gli eventi sono denormalizzati in modo corretto ed efficiente.

Una buona norma:

* Archiviare i metadati del modello Time Series
* Modalità Time Series, campi di istanza ed eventi includono solo le informazioni necessarie, ad esempio:
  * ID Time Series
  * Timestamp

Per altre informazioni, vedere [Shape events](./time-series-insights-send-events.md#json) (Dare forma agli eventi).

## <a name="business-disaster-recovery"></a>BCDR (Business disaster recovery)

Time Series Insights è un servizio a disponibilità elevata che usa ridondanze a livello di area di Azure. Per usare queste funzionalità intrinseche non è necessaria alcuna configurazione. La piattaforma Microsoft Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree. Per offrire una disponibilità elevata globale tra le aree per dispositivi o utenti, sfruttare le funzionalità di ripristino di emergenza di Azure. 

Per informazioni sulle funzionalità integrate in Azure per la continuità aziendale e il ripristino di emergenza, vedere l'articolo [Azure business continuity technical guidance](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance) (Indicazioni tecniche sulla continuità aziendale di Azure). Per informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza, vedere il documento [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> 
>  Time Series Insights non dispone di continuità aziendale e ripristino di emergenza incorporati.
> Per impostazione predefinita, Archiviazione di Azure, l'hub IoT di Azure e gli hub eventi di Azure dispongono del ripristino integrato.

Per altre informazioni, leggere:

* [Azure Storage redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy) (Ridondanza di Archiviazione di Azure)
* [IoT Hub high-availability disaster recovery](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) (Disponibilità elevata e ripristino di emergenza dell'hub IoT)
* [Event Hub policies](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr) (Criteri dell'Hub eventi)

Gli utenti che richiedono continuità aziendale e ripristino di emergenza possono implementare una strategia di ripristino. Creare un secondo ambiente Time Series Insights in un'area di Azure di backup. Inviare eventi all'ambiente secondario dall'origine evento primaria. Usare un secondo gruppo di consumer dedicato e le linee guida BCDR dell'origine evento.

Seguire questi passaggi per creare e usare un ambiente Time Series Insights secondario.

1. Creare un ambiente in una seconda area. Per altre informazioni, vedere [Ambienti Time Series Insights](./time-series-insights-get-started.md).
1. Creare un secondo gruppo di consumer dedicato per l'origine evento. Connettere tale origine eventi al nuovo ambiente. Assicurarsi di designare il secondo gruppo di consumer dedicato. Per altre informazioni, vedere la [documentazione dell'hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) o la [documentazione dell'Hub eventi](./time-series-insights-data-access.md).
1. Se l'area primaria viene compromessa durante un evento imprevisto di emergenza, reindirizzare le operazioni verso l'ambiente di backup di Time Series Insights.

> [!IMPORTANT]
> * Si noti che, in caso di failover, è possibile che si verifichi un ritardo.
> * Il failover può causare anche un picco temporaneo nell'elaborazione dei messaggi quando le operazioni vengono reindirizzate.
> * Per altre informazioni, vedere [Mitigate latency in Time Series Insights](./time-series-insights-environment-mitigate-latency.md) (Mitigare la latenza in Time Series Insights).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, leggere:

- [Azure Time Series Insights Preview storage and ingress](./time-series-insights-update-storage-ingress.md) (Archiviazione e ingresso nell'anteprima di Azure Time Series Insights)
- [Data modeling](./time-series-insights-update-tsm.md) (Modellazione di dati)