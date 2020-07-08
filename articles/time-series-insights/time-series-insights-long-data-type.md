---
title: Aggiunta del supporto per il tipo di dati Long | Microsoft Docs
description: Supporto per il tipo di dati Long
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: ebb62b67b56134902f2752b43dd25fb0a7c6ccd4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045774"
---
# <a name="adding-support-for-long-data-type"></a>Aggiunta del supporto per il tipo di dati Long

Queste modifiche verranno applicate solo agli ambienti di anteprima (PAYG). Se si dispone di uno SKU standard (S), è possibile ignorare queste modifiche.

Sono state apportate modifiche alla modalità di archiviazione e di indicizzazione dei dati numerici in Azure Time Series Insights anteprima che potrebbero influisca sull'utente. Se si è interessati da uno dei casi riportati di seguito, apportare le modifiche necessarie il prima possibile. I dati inizieranno a essere indicizzati per un periodo di tempo compreso tra il 29 giugno e il 30 giugno 2020, a seconda dell'area geografica. In caso di domande o dubbi su questa modifica, inviare un ticket di supporto tramite il portale di Azure e menzionare questa comunicazione.

Questa modifica ha un effetto sui casi seguenti:

1. Se si usano attualmente le variabili del modello Time Series e si inviano solo tipi di dati integrali nei dati di telemetria.
1. Se si usano attualmente variabili di modello Time Series e si inviano tipi di dati integrali e non integrali nei dati di telemetria.
1. Se si usano variabili categoriche per eseguire il mapping di valori integer a categorie.
1. Se si usa JavaScript SDK per compilare un'applicazione front-end personalizzata.
1. Se si sta per avvicinarsi al limite del nome della proprietà 1.000 in warm Store (WS) e inviare dati sia integrali che non integrali, il conteggio delle proprietà può essere visualizzato come metrica nel [portale di Azure](https://portal.azure.com/).

Se uno dei casi precedenti si applica all'utente, è necessario apportare modifiche al modello per adattarsi a questa modifica. Aggiornare l'espressione Time Series nella definizione di variabile sia in TSI Explorer che in qualsiasi client personalizzato usando le API con le modifiche consigliate. Per informazioni dettagliate, vedi di seguito.

A seconda della soluzione e dei vincoli dell'it, è possibile che non si disponga della visibilità dei dati inviati all'ambiente PAYG di TSI. Se non si è certi che i dati siano integrali o integrali e non integrali, sono disponibili alcune opzioni. È possibile attendere che la funzionalità venga rilasciata e quindi esplorare gli eventi non elaborati nell'interfaccia utente di Explorer per comprendere quali proprietà sono state salvate in due colonne separate. È possibile apportare le modifiche in modo preventivo per tutti i tag numerici o instradare temporaneamente un subset di eventi alla risorsa di archiviazione per comprendere meglio ed esplorare lo schema. Per archiviare gli eventi, attivare l' [acquisizione](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) di eventi per gli hub eventi o eseguire il [routing](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) dall'hub delle cose all'archivio BLOB di Azure. I dati possono essere osservati anche tramite [Esplora Hub eventi](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)o tramite l' [host processore di eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Se si usa l'hub Internet delle cose, vedere la documentazione [qui](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) per informazioni su come accedere all'endpoint predefinito.

Si noti che se queste modifiche sono interessate da queste modifiche e non sono in grado di impostarle in base alle date precedenti, è possibile che si verifichi un'interferenza in cui le variabili della serie temporale interessate a cui si accede tramite le API di query o Time Series Insights Explorer restituiscano *null* (ad esempio non vengono visualizzati dati nella finestra di esplorazione).

## <a name="recommended-changes"></a>Modifiche consigliate

Caso 1 & 2: **uso delle variabili del modello Time Series e invio solo di tipi di dati integrali o invio di tipi integrali e non integrali nei dati di telemetria.**

Se si inviano attualmente dati di telemetria Integer, i dati verranno divisi in due colonne: "propertyValue_double" e "propertyValue_long".

I dati di tipo integer verranno scritti in "propertyValue_long" quando le modifiche vengono applicate e i dati numerici inseriti in precedenza (e inseriti successivamente) in "propertyValue_double" non verranno copiati.

Se si desidera eseguire una query sui dati in queste due colonne per la proprietà "propertyValue", sarà necessario utilizzare la funzione scalare *COALESCE ()* nel TSX. La funzione accetta argomenti dello stesso tipo di dati e restituisce il primo valore non null nell'elenco di argomenti. per altre informazioni sull'utilizzo, vedere [qui](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Definizione di variabile in Esplora serie temporali-numerico

*Definizione di variabile precedente:*

[![Definizione di variabile precedente](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nuova definizione di variabile:*

[![Nuova definizione di variabile](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

È anche possibile usare *"COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))"* come [espressione della serie temporale personalizzata.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Definizione di variabile inline usando le API di query della serie temporale-numeric

*Definizione di variabile precedente:*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*Nuova definizione di variabile:*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

È anche possibile usare *"COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))"* come [espressione della serie temporale personalizzata.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Si consiglia di aggiornare queste variabili in tutte le posizioni che potrebbero essere usate (modello Time Series, query salvate, Power BI query del connettore).

Caso 3: **uso di variabili categoriche per eseguire il mapping di valori integer a categorie**

Se attualmente si usano variabili categoriche che eseguono il mapping di valori integer a categorie, è probabile che si usi la funzione tolong per convertire i dati da un tipo Double a un tipo Long. Come nei casi precedenti, è necessario unire le colonne del tipo di dati Double e Long.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definizione di variabile in Esplora serie temporali-categorico

*Definizione di variabile precedente:*

[![Definizione di variabile precedente](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nuova definizione di variabile:*

[![Nuova definizione di variabile](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

È anche possibile usare *"COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))"* come [espressione della serie temporale personalizzata.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

Le variabili categoriche richiedono ancora un valore di tipo Integer. Il tipo di dati di tutti gli argomenti in COALESCE () deve essere di tipo Long nell' [espressione della serie temporale personalizzata.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Definizione di variabile inline usando le API di query della serie temporale-categorico

*Definizione di variabile precedente:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "tolong($event.propertyValue.Double)"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

*Nuova definizione di variabile:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

Le variabili categoriche richiedono ancora un valore di tipo Integer. Il tipo di dati di tutti gli argomenti in COALESCE () deve essere di tipo Long nell' [espressione della serie temporale personalizzata.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Si consiglia di aggiornare queste variabili in tutte le posizioni che potrebbero essere usate (modello Time Series, query salvate, Power BI query del connettore).

Caso 4: **uso di JavaScript SDK per creare un'applicazione front-end personalizzata**

Se si è interessati dai casi 1-3 precedenti e si creano applicazioni personalizzate, è necessario aggiornare le query per usare la funzione *COALESCE ()* , come illustrato negli esempi precedenti.

Caso 5: **prossimità del limite della proprietà 1.000 dello Store warm**

Se si è un utente del negozio a caldo con un numero elevato di proprietà e si ritiene che questa modifica comporterebbe il push dell'ambiente al limite dei nomi di proprietà di 1.000 WS, inviare un ticket di supporto tramite il portale di Azure e menzionare questa comunicazione.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [questo](concepts-supported-data-types.md) articolo per visualizzare l'elenco completo dei tipi di dati supportati.
