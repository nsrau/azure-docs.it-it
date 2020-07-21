---
title: Pianificare l'ambiente Gen2-Azure Time Series Insights | Microsoft Docs
description: Procedure consigliate per configurare, gestire, pianificare e distribuire l'ambiente di Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: a0a17f28f2a87ae42b552bfa07732b8b8232ef5e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531439"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Pianificare l'ambiente Gen2 Azure Time Series Insights

Questo articolo descrive le procedure consigliate per pianificare e iniziare rapidamente a usare Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Procedure consigliate per la pianificazione e preparazione

Gli articoli seguenti illustrano le procedure consigliate per la pianificazione e la preparazione dell'ambiente.

* Cosa si ottiene quando si effettua [il provisioning di un ambiente di Azure Time Series Insights Gen2](#the-gen2-environment).
* Quali [sono gli ID e le proprietà timestamp delle serie temporali](#configure-time-series-ids-and-timestamp-properties).
* Qual è il nuovo [modello Time Series](#understand-the-time-series-model)e come crearne di personalizzati.
* Come [inviare eventi in modo efficiente in JSON](#shape-your-events).
* Azure Time Series Insights [Opzioni di ripristino di emergenza aziendale](#business-disaster-recovery).

Azure Time Series Insights usa un modello di business con pagamento in base al consumo. Per ulteriori informazioni sugli addebiti e sulla capacità, vedere [Azure Time Series Insights prezzi](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>Ambiente Gen2

Quando si esegue il provisioning di un ambiente Azure Time Series Insights Gen2, si creano due risorse di Azure:


* Ambiente Gen2 Azure Time Series Insights
* Un account di archiviazione Azure

Come parte del processo di provisioning, specificare se si vuole abilitare un archivio a caldo. Warm Store offre un'esperienza di query a più livelli. Se abilitata, è necessario specificare un periodo di conservazione compreso tra 7 e 30 giorni. Le query eseguite entro il periodo di conservazione dell'archivio caldo forniscono in genere tempi di risposta più rapidi. Quando una query si estende sul periodo di conservazione del negozio a caldo, viene servita dall'archivio a freddo.

Le query sull'archivio a caldo sono gratuite, mentre le query sull'archivio a freddo sono soggette a costi. È importante comprendere i modelli di query e pianificare la configurazione del negozio a caldo di conseguenza. È consigliabile che le analisi interattive sui dati più recenti risiedano nel negozio a caldo e l'analisi dei modelli e le tendenze a lungo termine risiedano a freddo.

> [!NOTE]
> Per altre informazioni su come eseguire query sui dati caldi, vedere le informazioni di [riferimento sulle API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Per iniziare, sono necessari altri tre elementi:

* Un [Modello Time Series](./concepts-model-overview.md)
* Un [origine evento connessa a Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Eventi che passino nell'origine evento](./time-series-insights-send-events.md) e che siano entrambi mappati al modello e abbiano un formato JSON valido

## <a name="review-azure-time-series-insights-gen2-limits"></a>Verifica Azure Time Series Insights limiti di Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurare gli ID della serie temporale e le proprietà timestamp

Per creare un nuovo ambiente di Azure Time Series Insights, selezionare un ID della serie temporale. Tale operazione funge da partizione logica per i dati. Come accennato, assicurarsi di disporre dell'ID Time Series a portata di mano.

> [!IMPORTANT]
> Gli ID delle serie temporali *non possono essere modificati in seguito*. Verificare ognuno di essi prima della selezione finale e del primo uso.

È possibile selezionare fino a tre chiavi per distinguere in modo univoco le risorse. Per ulteriori informazioni, leggere le [procedure consigliate per la scelta di un ID di serie temporali](./time-series-insights-update-how-to-id.md) e di [regole](concepts-json-flattening-escaping-rules.md)di inserimento.

Anche la proprietà **timestamp** è importante. È possibile definire questa proprietà quando si aggiungono origini evento. Ogni origine evento include una proprietà Timestamp facoltativa, usata per tenere traccia delle origini evento nel tempo. I valori di Timestamp fanno distinzione tra maiuscole/minuscole e devono essere formattati in base alle singole specifiche di ogni origine evento.

> [!TIP]
> Verificare i requisiti di formattazione e analisi per le origini evento.

Se lasciato vuoto, come timestamp dell'evento viene usato il tempo di accodamento dell'evento di un origine evento. Se si inviano dati cronologici o eventi in batch, la personalizzazione della proprietà Timestamp è più utile rispetto al tempo di accodamento dell'evento. Per altre informazioni, vedere come [aggiungere origini evento nell'hub Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendere il modello Time Series

È ora possibile configurare il modello Time Series dell'ambiente Azure Time Series Insights. Il nuovo modello semplifica l'individuazione e l'analisi dei dati IoT, consentendo la cura, la manutenzione e l'arricchimento dei dati Time Series e la preparazione di set di dati di livello consumer. Il modello usa ID Time Series, che esegue il mapping a un'istanza che associa la risorsa univoca a variabili, note come tipi, e gerarchie. Per altre informazioni, vedere Panoramica del [modello Time Series](./concepts-model-overview.md) .

Il modello è dinamico, in modo da poter essere compilato in qualsiasi momento. Per iniziare rapidamente, compilare e caricarlo prima di eseguire il push dei dati in Azure Time Series Insights. Per compilare il modello, vedere [usare il modello Time Series](./time-series-insights-update-how-to-tsm.md).

Per molti clienti, il modello Time Series esegue il mapping a un modello di risorse esistente o al sistema ERP già in uso. Se non si dispone di un modello esistente, viene [offerta](https://github.com/Microsoft/tsiclient) un'esperienza utente predefinita per ottenerlo rapidamente. Per conoscere i vantaggi di un modello, visualizzare l'[ambiente demo di esempio](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Dare forma agli eventi

È possibile verificare il modo in cui si inviano gli eventi a Azure Time Series Insights. In teoria, gli eventi sono denormalizzati in modo corretto ed efficiente.

Una buona norma:

* Archiviare i metadati nel modello Time Series.
* Verificare che la modalità della serie temporale, i campi dell'istanza e gli eventi includano solo le informazioni necessarie, ad esempio un ID della serie temporale o una proprietà timestamp.

Per ulteriori informazioni, leggere [eventi forma](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [Azure Advisor](../advisor/advisor-overview.md) per pianificare le opzioni di configurazione per il ripristino di business.
- Esaminare [Azure Advisor](../advisor/advisor-overview.md) per pianificare le opzioni di configurazione per il ripristino di business.
- Scopri di più sull'inserimento di [dati](./concepts-ingestion-overview.md) in Azure Time Series Insights Gen2.
- Vedere l'articolo sull' [archiviazione dei dati](./concepts-storage.md) in Azure Time Series Insights Gen2.
- Informazioni sulla [modellazione dei dati](./concepts-model-overview.md) in Azure Time Series Insights Gen2.
