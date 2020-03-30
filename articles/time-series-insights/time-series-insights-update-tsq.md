---
title: Esecuzione di query sui dati in Anteprima - Informazioni dettagliate sulle serie temporali di Azure Documenti Microsoft
description: Concetti relativi alle query sui dati e panoramica dell'API REST HTTP in Azure Time Series Insights Preview.Data querying concepts and HTTP REST API overview in Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284892"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Esecuzione di query sui dati nell'anteprima di Azure Time Series InsightsData querying in Azure Time Series Insights Preview

Azure Time Series Insights consente l'esecuzione di query sui dati sugli eventi e sui metadati archiviati nell'ambiente tramite API di superficie pubblica. Queste API vengono utilizzate anche da [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

In Time Series Insights sono disponibili tre categorie di API principali.

* **API**dell'ambiente: queste API abilitano le query sull'ambiente Time Series Insights stesso. Questi possono essere utilizzati per raccogliere l'elenco degli ambienti a cui il chiamante ha accesso e i metadati dell'ambiente.
* **Time Series Model-Query (TSM-Q) APIs**: Abilita le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sui metadati archiviati nel modello Time Series dell'ambiente. Questi possono essere utilizzati per accedere e modificare le istanze, tipi e gerarchie.
* **API TSQ (Time Series Query):** consente il recupero dei dati di telemetria o degli eventi registrati dal provider di origine e consente di eseguire calcoli e aggregazioni sui dati utilizzando funzioni scalari e di aggregazione avanzate.

Time Series Insights utilizza un linguaggio di espressione basato su stringa avanzato, [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), per esprimere i calcoli.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights core APIs

Sono supportate le API principali seguenti.

[![Panoramica della query Serie temporaliTime Series Query overview](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

* [API Get Environments](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Restituisce l'elenco degli ambienti a cui il chiamante è autorizzato ad accedere.
* [Get Environments Availability API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Restituisce la `$ts`distribuzione del conteggio degli eventi sul timestamp dell'evento. Questa API consente di determinare se sono presenti eventi nell'ambiente restituendo il numero di eventi suddivisi in intervalli di tempo, se presenti.
* [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Restituisce i metadati dello schema di eventi per un determinato intervallo di ricerca. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

La maggior parte di queste API supporta l'operazione di esecuzione batch per abilitare le operazioni CRUD batch su più entità del modello TimeSeries:Most of these APIs support batch execution operation to enable batch CRUD operations on multiple Time Series Model entities:

* [API delle impostazioni del](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)modello : Abilita *GET* e *PATCH* sul tipo predefinito e sul nome del modello dell'ambiente.
* [API dei tipi:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)abilita i tipi CRUD sulle serie temporali e le variabili associate.
* [API Gerarchie:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)abilita CRUD sulle gerarchie Time Series e sui percorsi dei campi associati.
* [API istanze](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): abilita CRUD sulle istanze Time Series e sui campi di istanza associati. Inoltre, l'API di istanze supporta le seguenti operazioni:
  * [Ricerca](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera un elenco parziale di hit nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.
  * [Suggerisci:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)cerca e suggerisce un elenco parziale di hit nella ricerca di istanze di serie temporali in base agli attributi di istanza.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Queste API sono disponibili in tutti i negozi della nostra soluzione di archiviazione multilivello in Time Series Insights. I parametri URL della query vengono utilizzati per specificare il [tipo di archivio](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) su cui eseguire la query:

* [API Get Events:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)abilita le query e il recupero degli eventi non elaborati e dei timestamp degli eventi associati quando vengono registrati in Time Series Insights dal provider di origine. Questa API consente il recupero di eventi non elaborati per un determinato ID Time Series e intervallo di ricerca. Questa API supporta l'impaginazione per recuperare il set di dati di risposta completo per l'input selezionato. 

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Abilita la query e il recupero dei valori calcolati e dei timestamp degli eventi associati applicando calcoli definiti dalle variabili sugli eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta l'impaginazione per recuperare il set di dati di risposta completo per l'input selezionato. 

* [API serie aggregate:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)abilita la query e il recupero di valori aggregati e i timestamp di intervallo associati applicando calcoli definiti dalle variabili sugli eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta l'impaginazione per recuperare il set di dati di risposta completo per l'input selezionato. 
  
  For a specified search span and interval, this API returns an aggregated response per variable per interval for a Time Series ID. Il numero di intervalli nel set di dati delle risposte viene calcolato contando i segni di graduazione (il numero di millisecondi trascorsi dall'epoca Unix - 1st, 1970) e dividendo i segni di graduazione per la dimensione dell'intervallo specificato nella query.

  I timestamp restituiti nel set di risposte sono dei limiti dell'intervallo sinistro, non degli eventi campionati dall'intervallo. 

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle diverse variabili che possono essere definite nel modello [Time Series](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Ulteriori informazioni su come eseguire query sui dati da [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
