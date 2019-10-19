---
title: Pianificare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs
description: Pianificare un ambiente di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: b97db5fcebeea67cc593a4d2c1fd677a55ad8559
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550185"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Pianificare un ambiente di anteprima di Azure Time Series Insights

Questo articolo descrive le procedure consigliate per pianificare e iniziare rapidamente a usare l'anteprima di Azure Time Series Insights.

> [!NOTE]
> Per le procedure consigliate per pianificare un'istanza di Time Series Insights di disponibilità generale, vedere [pianificare l'Azure Time Series Insights ambiente di disponibilità generale](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Procedure consigliate per la pianificazione e preparazione

Per iniziare a usare Time Series Insights, è consigliabile conoscere:

* Cosa si ottiene quando si effettua [il provisioning di un ambiente Time Series Insights Preview](#the-preview-environment).
* Quali [sono gli ID e le proprietà timestamp delle serie temporali](#configure-time-series-ids-and-timestamp-properties).
* Qual è il nuovo [modello Time Series](#understand-the-time-series-model)e come crearne di personalizzati.
* Come [inviare eventi in modo efficiente in JSON](#shape-your-events).
* Time Series Insights [Opzioni di ripristino di emergenza aziendale](#business-disaster-recovery).

Azure Time Series Insights usa un modello di business con pagamento in base al consumo. Per altre informazioni sui costi e la capacità, vedere [Time Series Insights pricing](https://azure.microsoft.com/pricing/details/time-series-insights/) (Prezzi di Time Series Insights).

## <a name="the-preview-environment"></a>Ambiente di anteprima

Quando si esegue il provisioning di un ambiente di anteprima di Time Series Insights, vengono create due risorse di Azure:

* Un ambiente Anteprima di Azure Time Series Insights
* Un account di archiviazione di Azure per utilizzo generico V1

Per iniziare, sono necessari altri tre elementi:

* Un [Modello Time Series](./time-series-insights-update-tsm.md)
* Un [origine evento connessa a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventi che passino nell'origine evento](./time-series-insights-send-events.md) e che siano entrambi mappati al modello e abbiano un formato JSON valido

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurare gli ID della serie temporale e le proprietà timestamp

Per creare un nuovo ambiente Time Series Insights, selezionare un ID Time Series. Tale operazione funge da partizione logica per i dati. Come accennato, assicurarsi di disporre dell'ID Time Series a portata di mano.

> [!IMPORTANT]
> Gli ID delle serie temporali *non possono essere modificati in seguito*. Verificare ognuno di essi prima della selezione finale e del primo uso.

È possibile selezionare fino a tre chiavi per distinguere in modo univoco le risorse. Per altre informazioni, leggere [Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md) (Procedure consigliate per la scelta di un ID Time Series) e [Storage and ingress](./time-series-insights-update-storage-ingress.md) (Archiviazione e ingresso).

Anche la proprietà Timestamp è importante. È possibile definire questa proprietà quando si aggiungono origini evento. Ogni origine evento include una proprietà Timestamp facoltativa, usata per tenere traccia delle origini evento nel tempo. I valori di Timestamp fanno distinzione tra maiuscole/minuscole e devono essere formattati in base alle singole specifiche di ogni origine evento.

> [!TIP]
> Verificare i requisiti di formattazione e analisi per le origini evento.

Se lasciato vuoto, come timestamp dell'evento viene usato il tempo di accodamento dell'evento di un origine evento. Se si inviano dati cronologici o eventi in batch, la personalizzazione della proprietà Timestamp è più utile rispetto al tempo di accodamento dell'evento. Per altre informazioni, vedere come [aggiungere origini evento nell'hub Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Comprendere il modello Time Series

È ora possibile configurare il modello Time Series dell'ambiente Time Series Insights. Il nuovo modello semplifica l'individuazione e l'analisi dei dati IoT, consentendo la cura, la manutenzione e l'arricchimento dei dati Time Series e la preparazione di set di dati di livello consumer. Il modello utilizza gli ID della serie temporale, che vengono mappati a un'istanza di che associa la risorsa univoca a variabili, note come tipi e gerarchie. Informazioni sul nuovo [modello Time Series](./time-series-insights-update-tsm.md).

Il modello è dinamico, in modo da poter essere compilato in qualsiasi momento. Per iniziare rapidamente, compilarlo e caricarlo prima di eseguire il push dei dati in Time Series Insights. Per compilare il modello, vedere [Use the Time Series Model](./time-series-insights-update-how-to-tsm.md) (Usare il modello Time Series).

Per molti clienti, il modello Time Series esegue il mapping a un modello di risorse esistente o al sistema ERP già in uso. Se non si dispone di un modello esistente, viene [offerta](https://github.com/Microsoft/tsiclient) un'esperienza utente predefinita per ottenerlo rapidamente. Per conoscere i vantaggi di un modello, visualizzare l'[ambiente demo di esempio](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Dare forma agli eventi

È possibile verificare il modo in cui si inviano eventi a Time Series Insights. In teoria, gli eventi sono denormalizzati in modo corretto ed efficiente.

Una buona norma:

* Archiviare i metadati nel modello Time Series.
* La modalità della serie temporale, i campi dell'istanza e gli eventi includono solo le informazioni necessarie, ad esempio un ID o un timestamp della serie temporale.

Per altre informazioni, vedere [Shape events](./time-series-insights-send-events.md#supported-json-shapes) (Dare forma agli eventi).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [Azure Advisor](../advisor/advisor-overview.md) per pianificare le opzioni di configurazione per il ripristino di business.

- Per altre informazioni sull'archiviazione e sul traffico in [ingresso,](./time-series-insights-update-storage-ingress.md) vedere la Time Series Insights Preview.

- Informazioni sulla [modellazione dei dati](./time-series-insights-update-tsm.md) in Time Series Insights Preview.