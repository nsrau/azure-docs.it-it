---
title: Esecuzione di query sui dati in anteprima-Azure Time Series Insights | Microsoft Docs
description: Cenni preliminari sulle query sui dati e sull'API REST HTTP in Azure Time Series Insights Preview.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284892"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Esecuzione di query sui dati in Azure Time Series Insights anteprima

Azure Time Series Insights consente l'esecuzione di query sui dati su eventi e metadati archiviati nell'ambiente tramite le API di superficie pubblica. Queste API vengono usate anche da [esplora Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

In Time Series Insights sono disponibili tre categorie di API principali.

* **API dell'ambiente**: queste API consentono di eseguire query sull'ambiente di Time Series Insights stesso. Questi possono essere usati per raccogliere l'elenco di ambienti a cui il chiamante ha accesso e i metadati dell'ambiente.
* **Modello Time Series-API query (TSM-Q)**: Abilita le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sui metadati archiviati nel modello Time Series dell'ambiente. Questi possono essere usati per accedere e modificare le istanze, i tipi e le gerarchie.
* **API TSQ (Time Series query)**: consente il recupero di dati di telemetria o di eventi durante la registrazione dal provider di origine e consente di calcolare e aggregazioni efficienti sui dati utilizzando funzioni scalari e di aggregazione avanzate.

In Time Series Insights viene utilizzato un linguaggio di espressioni basato su stringa, [TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), per l'espressione di calcoli.

## <a name="azure-time-series-insights-core-apis"></a>API di Azure Time Series Insights Core

Sono supportate le API principali seguenti.

[![Panoramica delle query su serie temporali](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

* [Get environments API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): restituisce l'elenco di ambienti a cui il chiamante è autorizzato ad accedere.
* [Get environments Availability API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): restituisce la distribuzione del numero di eventi sul timestamp `$ts`dell'evento. Questa API consente di determinare se sono presenti eventi nell'ambiente restituendo il numero di eventi suddivisi in intervalli di tempo, se presenti.
* [Get Event schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): restituisce i metadati dello schema dell'evento per un intervallo di ricerca specificato. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

La maggior parte di queste API supporta l'operazione di esecuzione batch per abilitare le operazioni CRUD batch su più entità del modello Time Series:

* [API delle impostazioni del modello](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): Abilita *Get* e *patch* per il tipo predefinito e il nome del modello dell'ambiente.
* [API dei tipi](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): Abilita CRUD sui tipi Time Series e sulle variabili associate.
* [API gerarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): Abilita CRUD nelle gerarchie della serie temporale e nei percorsi dei campi associati.
* [API istanze](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): Abilita CRUD nelle istanze della serie temporale e nei campi di istanza associati. Inoltre, l'API instances supporta le operazioni seguenti:
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.
  * [Suggerisci](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Cerca e suggerisce un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Queste API sono disponibili in tutti i punti vendita della soluzione di archiviazione a più livelli in Time Series Insights. I parametri URL query vengono utilizzati per specificare il [tipo di archivio](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) in cui deve essere eseguita la query:

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): consente la query e il recupero di eventi non elaborati e dei timestamp degli eventi associati Man mano che vengono registrati nel Time Series Insights dal provider di origine. Questa API consente il recupero di eventi non elaborati per un determinato ID della serie temporale e un intervallo di ricerca. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato. 

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): consente di eseguire query e recuperare i valori calcolati e i timestamp degli eventi associati applicando calcoli definiti dalle variabili in eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato. 

* [API della serie aggregata](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): consente l'esecuzione di query e il recupero di valori aggregati e dei timestamp di intervallo associati applicando calcoli definiti dalle variabili in eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato. 
  
  Per un intervallo e un intervallo di ricerca specificati, questa API restituisce una risposta aggregata per ogni variabile per ogni intervallo per un ID di serie temporale. Il numero di intervalli nel set di dati della risposta viene calcolato contando i segni di graduazione dell'epoca (il numero di millisecondi trascorsi dal 1 ° gennaio 1970) e dividendo i segni di graduazione in base alle dimensioni dell'intervallo specificato nella query.

  I timestamp restituiti nel set di risposte sono i limiti dell'intervallo sinistro, non gli eventi campionati dall'intervallo. 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle diverse variabili che possono essere definite nel [modello Time Series](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Scopri di più su come eseguire query sui dati da [esplora Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
