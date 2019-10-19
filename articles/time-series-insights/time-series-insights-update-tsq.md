---
title: Esecuzione di query sui dati nell'anteprima di Azure Time Series Insights | Microsoft Docs
description: Esecuzione di query sui dati nell'anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0b2f89a846747eaf78c60077372b48802506731e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553374"
---
# <a name="data-querying"></a>Esecuzione di query sui dati

La versione di anteprima di Azure Time Series Insights consente di eseguire query sui dati per eventi e metadati archiviati nell'ambiente tramite API di area pubblica. Tali API vengono usate anche nell'[anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).

In Time Series Insights sono disponibili tre categorie di API principali.

* **API ambiente**: Abilita le query dell'ambiente di Time Series Insights stesso. Esempi di query sono l'elenco di ambienti a cui il chiamante può accedere e i metadati di ambiente.

* **Modello Time Series-API query (TSM-Q)** : Abilita le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sui metadati archiviati nella parte relativa all'ambiente del modello Time Series. Esempi sono costituiti da istanze, tipi e gerarchie.

* **API TSQ (Time Series query)** : Abilita il recupero dei dati degli eventi durante la registrazione dal provider di origine. Tali API possono eseguire operazioni per trasformare, combinare ed eseguire calcoli sui dati delle serie temporali.

Il [linguaggio TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) è una quarta categoria potente. Tale linguaggio usa i modelli di serie temporali per abilitare la composizione di calcoli avanzati.

## <a name="azure-time-series-insights-preview-core-apis"></a>API principali dell'anteprima di Azure Time Series Insights

Sono supportate le API principali seguenti.

[Panoramica delle query sulle serie di ![Time](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

Sono disponibili le API di ambiente seguenti.

* [Get Environment API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): restituisce l'elenco di ambienti a cui il chiamante è autorizzato ad accedere.
* [Get Environment Availability API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): restituisce la distribuzione del numero di eventi sul timestamp dell'evento `$ts`. Tale API consente di stabilire se sono presenti eventi nel timestamp restituendo il numero di eventi, se presenti.
* [Get Event schema API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): restituisce i metadati dello schema dell'evento per un intervallo di ricerca specificato. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

Sono disponibili le API TSM-Q (Time Series Model-Query) seguenti.

* [API delle impostazioni del modello](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Abilita Get e patch per il tipo predefinito e il nome del modello dell'ambiente.
* [API dei tipi](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Abilita CRUD sui tipi Time Series e sulle variabili associate.
* [API gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Abilita CRUD nelle gerarchie della serie temporale e nei percorsi dei campi associati.
* [API istanze](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Abilita CRUD nelle istanze della serie temporale e nei campi di istanza associati.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Sono disponibili le API TSQ (Time Series Query) seguenti.

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): consente la query e il recupero dei dati di Time Series Insights dagli eventi quando vengono registrati nel Time Series Insights dal provider di origine.

* [Ottenere l'API della serie](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): consente la query e il recupero dei dati di Time Series Insights dagli eventi acquisiti usando i dati registrati in transito. I valori restituiti si basano sulle variabili definite nel modello o nella funzione inline specificata.

    >[!NOTE]
    > La clausola di aggregazione viene ignorata anche se è specificata in un modello oppure in una funzione inline specificata.

  L'API Get Series restituisce un modello di serie temporale per ogni variabile e per ogni intervallo. Un valore di serie temporale è un formato usato da Time Series Insights per restituire l'output JSON da una query. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati.

* [API della serie aggregata](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): consente la query e il recupero dei dati di Time Series Insights dagli eventi acquisiti tramite il campionamento e l'aggregazione dei dati registrati.

  L'API Aggregate Series restituisce un valore di serie temporale per ogni variabile e per ogni intervallo. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati. L'API Aggregate Series consente di ottenere una riduzione usando variabili archiviate nel modello di serie temporale o nella funzione inline specificata per i dati aggregati o di esempio.

  Tipi aggregati supportati: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sull' [archiviazione e](./time-series-insights-update-storage-ingress.md) sul traffico in ingresso nella Azure Time Series Insights anteprima.

- Vedere l'articolo sulla [modellazione dei dati](./time-series-insights-update-tsm.md) di Time Series Insights Preview.

- Individuare le [procedure consigliate per la scelta di un ID di serie temporale](./time-series-insights-update-how-to-id.md).
