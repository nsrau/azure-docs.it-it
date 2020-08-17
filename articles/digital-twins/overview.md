---
title: Cos'è il servizio Gemelli digitali di Azure?
titleSuffix: Azure Digital Twins
description: Panoramica delle operazioni che è possibile eseguire con Gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 0e17ecea6c85329b22310ebe1d06e2fe63cdd773
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042820"
---
# <a name="what-is-azure-digital-twins"></a>Cos'è il servizio Gemelli digitali di Azure?

**Gemelli digitali di Azure** è un'offerta PaaS che consente la creazione di grafici di conoscenze basati su modelli digitali di interi ambienti. Questi ambienti possono essere edifici, fabbriche, fattorie, reti energetiche, ferrovie, stadi e altro ancora, persino intere città. Questi modelli digitali possono essere usati per ottenere informazioni che consentono di migliorare i prodotti, ottimizzare le operazioni, ridurre i costi e rivoluzionare l'esperienza dei clienti.

È possibile sfruttare la propria esperienza del settore oltre a quella di Gemelli digitali di Azure per creare soluzioni personalizzate e connesse che consentono di:
* Modellare qualsiasi ambiente e implementare i gemelli digitali in modo scalabile e sicuro
* Connettere risorse come i dispositivi IoT e i sistemi aziendali esistenti
* Usare un sistema di eventi affidabile per creare una logica di business dinamica e l'elaborazione dati
* Eseguire l'integrazione con i dati di Azure, le analisi e i servizi di intelligenza artificiale per tenere traccia del passato e quindi prevedere il futuro

## <a name="azure-digital-twins-capabilities"></a>Funzionalità di Gemelli digitali di Azure

Di seguito è riportato un riepilogo delle funzionalità fornite da Gemelli digitali di Azure.

### <a name="open-modeling-language"></a>Linguaggio di modellazione aperto

In Gemelli digitali di Azure è possibile definire le entità digitali che rappresentano le persone, i luoghi e gli elementi dell'ambiente fisico usando tipi di gemelli personalizzati denominati [**modelli**](concepts-models.md). 

Queste definizioni dei modelli possono essere pensate come un vocabolario specializzato per descrivere la propria attività. Per una soluzione di gestione degli edifici, ad esempio, è possibile definire modelli come "edificio", "piano" e "ascensore". È quindi possibile creare **gemelli digitali** basati su questi modelli per rappresentare l'ambiente specifico.

I modelli sono definiti in un linguaggio simile a JSON denominato [DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) e descrivono i gemelli in termini di proprietà dello stato, eventi di telemetria, comandi, componenti e relazioni.
* I modelli definiscono le **relazioni** semantiche tra le entità in modo da potere connettere i gemelli in un grafo della conoscenza che riflette le loro interazioni. È possibile pensare ai modelli come a dei sostantivi in una descrizione del mondo e alle relazioni come a dei verbi.
* È anche possibile specializzare i gemelli usando l'ereditarietà del modello. Un modello può ereditare da un altro.

DTDL viene usato per i modelli di dati in tutti gli altri servizi IoT di Azure, tra cui [Plug and Play (PnP) IoT](../iot-pnp/overview-iot-plug-and-play.md) e [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md). In questo modo è possibile garantire la connessione e la compatibilità della soluzione Gemelli digitali di Azure con altre parti dell'ecosistema di Azure.

### <a name="live-execution-environment"></a>Ambiente di esecuzione live

I modelli digitali in Gemelli digitali di Azure sono rappresentazioni live aggiornate del mondo reale. Usando le relazioni nei modelli DTDL personalizzati, è possibile connettere i gemelli in un **grafo live** che rappresenta l'ambiente.

È possibile aprire una visualizzazione del grafo di Gemelli digitali di Azure tramite un'applicazione di esempio, [**Azure Digital Twins explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Ecco un esempio di questa visualizzazione:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Screenshot dell'applicazione di esempio Azure Digital Twins explorer, che mostra un grafo dei nodi che rappresentano i gemelli digitali" lightbox="media/includes/azure-digital-twins-explorer.png":::

Gemelli digitali di Azure fornisce un **sistema di eventi** completo per mantenere aggiornato il grafo con l'elaborazione dati e la logica di business. È possibile connettere risorse di calcolo esterne, ad esempio [Funzioni di Azure](../azure-functions/functions-overview.md), per gestire l'elaborazione dati in modo flessibile e personalizzato.

È anche possibile estrarre informazioni dettagliate dall'ambiente di esecuzione live usando l'**API di query** avanzata di Gemelli digitali di Azure. L'API consente di eseguire query con condizioni di ricerca avanzate, inclusi i valori delle proprietà, le relazioni, le proprietà delle relazioni, le informazioni sul modello e altro ancora. È anche possibile combinare le query, raccogliendo un'ampia gamma di informazioni dettagliate sull'ambiente e rispondendo a domande personalizzate che sono importanti per l'utente.

### <a name="input-from-iot-and-business-systems"></a>Input da sistemi IoT e aziendali

Per mantenere aggiornato l'ambiente di esecuzione live di Gemelli digitali di Azure con il mondo reale, è possibile usare l'[hub IoT](../iot-hub/about-iot-hub.md) per connettere la soluzione ai dispositivi IoT e IoT Edge. Questi dispositivi gestiti dall'hub sono rappresentati come parte del grafo dei gemelli e forniscono i dati che controllano il modello.

A questo scopo è possibile creare un nuovo hub IoT con Gemelli digitali di Azure oppure connettere un hub IoT esistente insieme ai dispositivi già gestiti.

È anche possibile gestire Gemelli digitali di Azure da altre origini dati, usando le API REST o i connettori ad altri servizi come [App per la logica](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Output in TSI, archiviazione e analisi

I dati nel modello Gemelli digitali di Azure possono essere instradati ai servizi di Azure downstream per ulteriore analisi o archiviazione. Questa operazione viene fornita tramite **route di eventi**, che usano l'[hub eventi](../event-hubs/event-hubs-about.md), la [griglia di eventi](../event-grid/overview.md) o il [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md) per gestire i flussi di dati desiderati.

Alcune operazioni che è possibile eseguire con le route di eventi includono:
* Archiviazione dei dati di Gemelli digitali di Azure in [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analisi dei dati di Gemelli digitali di Azure con [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) o altri strumenti di analisi dei dati Microsoft
* Integrazione di flussi di lavoro di dimensioni maggiori con App per la logica
* Connessione di Gemelli digitali di Azure a Time Series Insights per tenere traccia della cronologia delle serie temporali di ogni gemello
* Allineamento di un modello serie temporale in Time Series Insights con un'origine in Gemelli digitali di Azure

Si tratta di un altro modo in cui Gemelli digitali di Azure può connettersi a una soluzione più ampia e supportare le esigenze personalizzate per continuare a lavorare con queste informazioni.

## <a name="azure-digital-twins-in-a-solution-context"></a>Gemelli digitali di Azure in un contesto della soluzione

Gemelli digitali di Azure viene comunemente usato in combinazione con altri servizi di Azure come parte di una soluzione IoT più ampia. 

Una soluzione completa che usa Gemelli digitali di Azure può contenere le parti seguenti:
* Istanza del servizio Gemelli digitali di Azure che consente di archiviare i modelli di gemelli e il grafo dei gemelli con il relativo stato e orchestrare l'elaborazione degli eventi.
* Una o più app client che gestiscono l'istanza di Gemelli digitali di Azure configurando modelli, creando topologie ed estraendo informazioni approfondite dal grafico dei gemelli.
* Una o più risorse di calcolo esterne per elaborare gli eventi generati da Gemelli digitali di Azure o da origini dati connesse, ad esempio i dispositivi. Un modo comune per fornire le risorse di calcolo è tramite [Funzioni di Azure](../azure-functions/functions-overview.md).
* Un hub IoT per fornire funzionalità di gestione dei dispositivi e flusso dei dati IoT.
* Servizi downstream per gestire attività quali l'integrazione del flusso di lavoro, ad esempio [App per la logica](../logic-apps/logic-apps-overview.md), archiviazione offline sicura, integrazione di serie temporali o analisi. 

Il diagramma seguente illustra il punto in cui si trova Gemelli digitali di Azure nel contesto di una soluzione Azure IoT più grande.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagramma che illustra le origini di input, i servizi di output e la comunicazione bidirezionale con le app client e le risorse di calcolo esterne." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits-in-public-preview"></a>Limiti del servizio durante l'anteprima pubblica

> [!IMPORTANT]
> Gemelli digitali di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere le [*Condizioni supplementari per l'uso delle anteprime di Microsoft Azure*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per un elenco dei limiti di Gemelli digitali di Azure durante l'anteprima pubblica, vedere [*Riferimento: Limiti del servizio durante l'anteprima pubblica*](reference-service-limits.md).

## <a name="next-steps"></a>Passaggi successivi

Se si è già lavorato con la versione di anteprima precedente di Gemelli digitali di Azure, scoprire cosa è cambiato:
* [*Panoramica: Differenze rispetto alla versione precedente*](overview-differences.md)

In alternativa, continuare ad approfondire l'uso di Gemelli digitali di Azure con la prima esercitazione:

> [!div class="nextstepaction"]
> [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
