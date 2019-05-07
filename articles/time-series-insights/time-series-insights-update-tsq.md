---
title: Esecuzione di query sui dati nell'anteprima di Azure Time Series Insights | Microsoft Docs
description: Esecuzione di query sui dati nell'anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: d28cb35b1e8e4eaa86418da632c6fcabb636e643
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205023"
---
# <a name="data-querying"></a>Esecuzione di query sui dati

La versione di anteprima di Azure Time Series Insights consente di eseguire query sui dati per eventi e metadati archiviati nell'ambiente tramite API di area pubblica. Tali API vengono usate anche nell'[anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).

In Time Series Insights sono disponibili tre categorie di API principali.

* **API di ambiente**: consentono di eseguire query dell'ambiente Time Series Insights. Esempi di query sono l'elenco di ambienti a cui il chiamante può accedere e i metadati di ambiente.

* **API TSM-Q (Time Series Model-Query)**: consentono di creare, leggere, aggiornare ed eliminare operazioni sui metadati archiviati nella parte di ambiente del modello di serie temporali. Esempi sono costituiti da istanze, tipi e gerarchie.

* **API TSQ (Time Series Query)**: consentono di recuperare dati di eventi come vengono registrati dal provider di origine. Tali API possono eseguire operazioni per trasformare, combinare ed eseguire calcoli sui dati delle serie temporali.

Il [linguaggio TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) è una quarta categoria potente. Tale linguaggio usa i modelli di serie temporali per abilitare la composizione di calcoli avanzati.

## <a name="azure-time-series-insights-preview-core-apis"></a>API principali dell'anteprima di Azure Time Series Insights

Sono supportate le API principali seguenti.

[![Panoramica delle Query di serie ora](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

Sono disponibili le API di ambiente seguenti.

* [API Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): restituisce l'elenco degli ambienti a cui il chiamante è autorizzato ad accedere.
* [API Get Environment Availability](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): restituisce la distribuzione del numero di eventi rispetto al timestamp di eventi `$ts`. Tale API consente di stabilire se sono presenti eventi nel timestamp restituendo il numero di eventi, se presenti.
* [API Get Event Schema](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): restituisce i metadati dello schema di eventi per un intervallo di ricerca specificato. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

Sono disponibili le API TSM-Q (Time Series Model-Query) seguenti.

* [API Model Settings](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): consente di ottenere il tipo predefinito e il nome di modello dell'ambiente e di applicarvi una patch.
* [API Types](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): abilita le operazioni CRUD sui tipi di serie temporali e sulle variabili associate.
* [API Hierarchies](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): abilita le operazioni CRUD sulle gerarchie di serie temporali e sui percorsi di campo associati.
* [API Instances](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): abilita le operazioni CRUD sulle istanze di serie temporali e sui campi delle istanze associate.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Sono disponibili le API TSQ (Time Series Query) seguenti.

* [API Get Events](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): consente di eseguire query sui dati di Time Series Insights e di recuperarli da eventi registrati in Time Series Insights dal provider di origine.

* [API Get Series](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): consente di eseguire query sui dati di Time Series Insights e di recuperarli da eventi acquisiti usando dati registrati in transito. I valori restituiti si basano sulle variabili definite nel modello o nella funzione inline specificata.

    >[!NOTE]
    > La clausola di aggregazione viene ignorata anche se è specificata in un modello oppure in una funzione inline specificata.

  L'API Get Series restituisce un modello di serie temporale per ogni variabile e per ogni intervallo. Un valore di serie temporale è un formato usato da Time Series Insights per restituire l'output JSON da una query. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati.

* [API Aggregate Series](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): consente di eseguire query sui dati di Time Series Insights e di recuperarli da eventi acquisiti tramite il campionamento e l'aggregazione di dati registrati.

  L'API Aggregate Series restituisce un valore di serie temporale per ogni variabile e per ogni intervallo. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati. L'API Aggregate Series consente di ottenere una riduzione usando variabili archiviate nel modello di serie temporale o nella funzione inline specificata per i dati aggregati o di esempio.

  Tipi aggregati supportati: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [in ingresso e in archiviazione](./time-series-insights-update-storage-ingress.md) nell'anteprima di Insights di Azure ora serie.

- Leggere la versione di anteprima di tempo Series Insights [modellazione dei dati](./time-series-insights-update-tsm.md) articolo.

- Scopri [procedure consigliate quando si sceglie un ID di serie ora](./time-series-insights-update-how-to-id.md).
