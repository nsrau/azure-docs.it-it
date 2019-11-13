---
title: Esecuzione di query sui dati in anteprima-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle query di Azure Time Series Insights anteprima dei dati.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: f44aa5c0a412dc53f4b1a5f127887257a1b0b550
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006318"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Esecuzione di query sui dati in Azure Time Series Insights anteprima

La versione di anteprima di Azure Time Series Insights consente di eseguire query sui dati per eventi e metadati archiviati nell'ambiente tramite API di area pubblica. Tali API vengono usate anche nell'[anteprima di Time Series Insights Explorer](./time-series-insights-update-explorer.md).

In Time Series Insights sono disponibili tre categorie di API principali.

* **API dell'ambiente**: queste API consentono di eseguire query sull'ambiente di Time Series Insights stesso. Esempi di query sono l'elenco di ambienti a cui il chiamante può accedere e i metadati di ambiente.
* **Modello Time Series-API query (TSM-Q)** : Abilita le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sui metadati archiviati nella parte relativa all'ambiente del modello Time Series. Esempi sono costituiti da istanze, tipi e gerarchie.
* **API TSQ (Time Series query)** : Abilita il recupero di dati di telemetria o di eventi durante la registrazione dal provider di origine o la riduzione dei dati tramite funzioni scalari e di aggregazione archiviate parte delle variabili. Queste API possono eseguire operazioni per trasformare, combinare e applicare calcoli sui dati delle serie temporali.

In Time Series Insights viene utilizzato un linguaggio di espressioni basato su stringa, [TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx), per l'espressione di calcoli.

## <a name="azure-time-series-insights-preview-core-apis"></a>API principali dell'anteprima di Azure Time Series Insights

Sono supportate le API principali seguenti.

[Panoramica delle query su serie temporali ![](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

Sono disponibili le API di ambiente seguenti.

* [Get Environment API](/rest/api/time-series-insights/management/environments/get): restituisce l'elenco di ambienti a cui il chiamante è autorizzato ad accedere.
* [Get Environment Availability API](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): restituisce la distribuzione del numero di eventi sul timestamp dell'evento `$ts`. Tale API consente di stabilire se sono presenti eventi nel timestamp restituendo il numero di eventi, se presenti.
* [Get Event schema API](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): restituisce i metadati dello schema dell'evento per un intervallo di ricerca specificato. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

Sono disponibili le API di query del modello Time Series seguenti. La maggior parte di queste API supporta l'operazione di esecuzione batch per abilitare le operazioni CRUD batch su più entità del modello Time Series:

* [API delle impostazioni del modello](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Abilita *Get* e *patch* per il tipo predefinito e il nome del modello dell'ambiente.
* [API dei tipi](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Abilita CRUD sui tipi Time Series e sulle variabili associate.
* [API gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Abilita CRUD nelle gerarchie della serie temporale e nei percorsi dei campi associati.
* [API istanze](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Abilita CRUD nelle istanze della serie temporale e nei campi di istanza associati. Inoltre, l'API instances supporta le operazioni seguenti:
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.
  * [Suggerisci](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Cerca e suggerisce un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Sono disponibili le API di query della serie temporale seguenti. Queste API sono disponibili in tutte le archiviazioni a più livelli supportate in Time Series Insights. I parametri URL query vengono utilizzati per specificare il [tipo di archivio](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) in cui deve essere eseguita la query:

* [Get Events API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): consente la query e il recupero dei dati di Time Series Insights dagli eventi quando vengono registrati nel Time Series Insights dal provider di origine. Questa API consente il recupero di eventi non elaborati per un determinato ID della serie temporale e un intervallo di ricerca. Questa API supporta la paginazione per recuperare il set di dati completo per l'input selezionato. 

* [Ottenere l'API della serie](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): consente la query e il recupero dei dati di Time Series Insights dagli eventi acquisiti usando i dati registrati in transito. I valori restituiti si basano sulle variabili definite nel modello o nella funzione inline specificata. Questa API supporta la paginazione per recuperare il set di dati completo per l'input selezionato. Questa API consente di definire le proprietà o le colonne calcolate.

    >[!NOTE]
    > La clausola di aggregazione viene ignorata anche se è specificata in un modello oppure in una funzione inline specificata.

  L'API Get Series restituisce un modello di serie temporale per ogni variabile e per ogni intervallo. Un valore di serie temporali è un formato che Time Series Insights USA per l'output JSON di una query. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati.

* [API della serie aggregata](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): consente la query e il recupero dei dati di Time Series Insights dagli eventi acquisiti tramite il campionamento e l'aggregazione dei dati registrati. Questa API supporta l'esecuzione continua con i [token di continuazione](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  L'API Aggregate Series restituisce un valore di serie temporale per ogni variabile e per ogni intervallo. I valori restituiti si basano sull'ID di serie temporale e sul set di variabili specificati. L'API Aggregate Series consente di ottenere una riduzione usando variabili archiviate nel modello di serie temporale o nella funzione inline specificata per i dati aggregati o di esempio.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [archiviazione e ingresso](./time-series-insights-update-storage-ingress.md) in Azure Time Series Insights Preview.
- Vedere l'articolo sulla [modellazione dei dati](./time-series-insights-update-tsm.md) di Time Series Insights Preview.
- Individuare le [procedure consigliate per la scelta di un ID di serie temporale](./time-series-insights-update-how-to-id.md).
