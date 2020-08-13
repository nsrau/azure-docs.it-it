---
title: Modifiche imminenti alle regole di inserimento e Flat in Azure Time Series Insights Gen2 | Microsoft Docs
description: Modifiche alle regole di inserimento
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168202"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Modifiche imminenti alle regole di escape e Flat JSON per i nuovi ambienti

**Queste modifiche verranno applicate solo agli ambienti Azure Time Series Insights Gen2 *appena creati* . Queste modifiche non si applicano agli ambienti Gen1.**

Il Azure Time Series Insights ambiente Gen2 crea dinamicamente le colonne di archiviazione, seguendo un particolare set di convenzioni di denominazione. Quando si inserisce un evento, viene applicato un set di regole al payload JSON e ai nomi di proprietà. Le modifiche apportate al modo in cui i dati JSON sono bidimensionali e archiviati entreranno in vigore per i nuovi Azure Time Series Insights ambienti Gen2 nel 2020 luglio. Questa modifica ha un effetto sui casi seguenti:

* Se il payload JSON contiene oggetti annidati
* Se il payload JSON contiene matrici
* Se si usa uno dei quattro caratteri speciali seguenti in un nome di proprietà JSON: [\. '
* Se una o più delle proprietà dell'ID TS sono incluse in un oggetto annidato.

Se si crea un nuovo ambiente e uno o più dei casi precedenti si applicano al payload dell'evento, i dati verranno visualizzati in modo flat e archiviati in modo diverso. Di seguito è riportato un riepilogo delle modifiche:

| Regola corrente | Nuova regola | JSON di esempio | Nome precedente della colonna | Nuovo nome della colonna
|---|---| ---| ---|  ---|
| Il codice JSON annidato è bidimensionale usando un carattere di sottolineatura come delimitatore |JSON annidato è bidimensionale usando un punto come delimitatore  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| I caratteri speciali non sono preceduti da un carattere di escape | Nomi delle proprietà JSON che includono i caratteri speciali. [\ è sono preceduti da un carattere di escape con [' è]. In [' and '] è presente un ulteriore escape di virgolette singole e barre rovesciate. Una virgoletta singola verrà scritta come \' e una barra rovesciata verrà scritta come\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Le matrici di primitive vengono archiviate come stringa | Le matrici di tipi primitivi vengono archiviate come tipo dinamico  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Le matrici di oggetti sono sempre bidimensionali e producono più eventi | Se gli oggetti all'interno di una matrice non dispongono dell'ID TS o dei timestamp appropriati, la matrice di oggetti viene archiviata come tipo dinamico | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Modifiche consigliate per i nuovi ambienti

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se l'ID TS e/o la proprietà timestamp sono annidati all'interno di un oggetto

* Eventuali nuove distribuzioni dovranno corrispondere alle nuove regole di inserimento. Ad esempio, se l'ID TS è `telemetry_tagId` necessario aggiornare tutti i modelli di Azure Resource Manager (ARM) o gli script di distribuzione automatica per configurare `telemetry.tagId` come ID TS dell'ambiente. Questa modifica è necessaria anche per i timestamp dell'origine eventi nel file JSON annidato.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se il payload contiene codice JSON annidato o caratteri speciali e si automatizza la creazione di espressioni variabili di [modello Time Series](.\time-series-insights-update-tsm.md)

* Aggiornare il codice client eseguendo [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) in modo che corrisponda alle nuove regole di inserimento. Ad esempio, un' [espressione Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) precedente di `"value": {"tsx": "$event.series_value.Double"}` deve essere aggiornata a una delle opzioni seguenti:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Passaggi successivi

* Leggere [Azure Time Series Insights archiviazione Gen2 e](./time-series-insights-update-storage-ingress.md)il traffico in ingresso.

* Scopri di più su come eseguire query sui dati usando le [API di query di serie temporali](./concepts-query-overview.md).

* Scopri di più sulla [nuova sintassi delle espressioni Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).
