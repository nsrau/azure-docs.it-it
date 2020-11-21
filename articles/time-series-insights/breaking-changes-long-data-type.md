---
title: Supporto per il tipo di dati Long in Azure Time Series Insights Gen2 | Microsoft Docs
description: Supporto per il tipo di dati Long in Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: dpalled
ms.openlocfilehash: 3460cd8a88733ede041f6c0635ba40797675ed03
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025328"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Aggiunta del supporto per il tipo di dati Long in Azure Time Series Insights Gen2

L'aggiunta del supporto per il tipo di dati Long influiscono sulla modalità di archiviazione e di indicizzazione dei dati numerici in Azure Time Series Insights solo ambienti Gen2. Se si dispone di un ambiente Gen1, è possibile ignorare queste modifiche.

A partire dal 29 giugno o dal 30 giugno 2020, a seconda dell'area geografica, i dati verranno indicizzati come **Long** e **Double**.  In caso di domande o dubbi su questa modifica, inviare un ticket di supporto tramite il portale di Azure e menzionare questa comunicazione.

Se si è interessati da uno dei seguenti casi, apportare le modifiche consigliate:

- **Caso 1**: si usano attualmente le variabili del modello Time Series e si inviano solo tipi di dati integrali nei dati di telemetria.
- **Caso 2**: si usano attualmente le variabili del modello Time Series e si inviano tipi di dati integrali e non integrali nei dati di telemetria.
- **Caso 3**: si utilizzano variabili categoriche per eseguire il mapping di valori integer a categorie.
- **Caso 4**: usare JavaScript SDK per creare un'applicazione front-end personalizzata.
- **Caso 5**: si sta per avvicinarsi al limite dei nomi di proprietà 1.000 in warm Store e inviare dati sia integrali che non integrali. Il numero di proprietà può essere visualizzato come metrica nel [portale di Azure](https://portal.azure.com/).

Se uno dei casi è applicabile, apportare modifiche al modello. Aggiornare l'espressione Time Series (TSX) nella definizione della variabile con le modifiche consigliate. Aggiornare entrambi:

- Strumento di esplorazione di Azure Time Series Insights
- Qualsiasi client personalizzato che usa le API

A seconda della soluzione e dei vincoli dell'it, è possibile che non si disponga della visibilità dei dati inviati all'ambiente di Azure Time Series Insights Gen2. Se non si è certi che i dati siano integrali o integrali e non integrali, sono disponibili alcune opzioni:

- È possibile attendere che la funzionalità venga rilasciata. Quindi, esplorare gli eventi non elaborati nell'interfaccia utente di Explorer per comprendere quali proprietà vengono salvate in due colonne separate.
- È possibile apportare preventivamente le modifiche consigliate per tutti i tag numerici.
- È possibile instradare temporaneamente un subset di eventi alla risorsa di archiviazione per comprendere meglio ed esplorare lo schema.

Per archiviare gli eventi, attivare l' [acquisizione](../event-hubs/event-hubs-capture-overview.md) di eventi per hub eventi di Azure o [instradare](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) dall'hub delle cose all'archivio BLOB di Azure.

I dati possono essere osservati anche tramite [Esplora Hub eventi](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)o tramite l' [host processore di eventi](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events).

Se si usa l'hub Internet delle cose, passare a [leggere i messaggi da dispositivo a cloud dall'endpoint predefinito per informazioni](../iot-hub/iot-hub-devguide-messages-read-builtin.md) su come accedere all'endpoint predefinito.

> [!NOTE]
> È possibile che si verifichino problemi se non si apportano le modifiche consigliate. Ad esempio, le variabili di Time Series Insights interessate a cui si accede tramite le API di query o Time Series Insights Explorer restituiranno **null** , ovvero non verranno visualizzati dati nella finestra di esplorazione.

## <a name="recommended-changes"></a>Modifiche consigliate

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Caso 1: uso di variabili di modello Time Series e invio di soli tipi di dati integrali nei dati di telemetria

Le modifiche consigliate per il caso 1 sono le stesse del caso 2. Seguire le istruzioni riportate nella sezione per il caso 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Caso 2: uso di variabili di modello Time Series e invio di tipi integrali e non integrali nei dati di telemetria

Se al momento si inviano dati di telemetria Integer, i dati verranno divisi in due colonne:

- **propertyValue_double**
- **propertyValue_long**

I dati integer vengono scritti in **propertyValue_long**. I dati numerici inseriti in precedenza (e inseriti successivamente) in **propertyValue_double** non vengono copiati.

Se si desidera eseguire una query sui dati in queste due colonne per la proprietà **PropertyValue** , è necessario utilizzare la funzione scalare **COALESCE ()** nel TSX. La funzione accetta argomenti dello stesso **tipo** di dati e restituisce il primo valore non null nell'elenco di argomenti. Per altre informazioni, vedere [Azure Time Series Insights concetti relativi all'accesso ai dati Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Definizione di variabile in TSX-numeric

*Definizione di variabile precedente:*

[![Screenshot mostra la finestra di dialogo Aggiungi nuova variabile per la variabile PropertyValue, numeric.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nuova definizione di variabile:*

[![Screenshot mostra la finestra di dialogo Aggiungi nuova variabile per la variabile PropertyValue con un valore personalizzato numerico.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

È anche possibile usare **COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))** come [espressione della serie temporale](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizzata.

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Definizione di variabile inline usando le API di query TSX-numeric

*Definizione di variabile precedente:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nuova definizione di variabile:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

È anche possibile usare **COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))** come [espressione della serie temporale](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizzata.

> [!NOTE]
> Si consiglia di aggiornare queste variabili in tutte le posizioni in cui possono essere utilizzate. Questi punti includono il modello Time Series, le query salvate e le query del connettore Power BI.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Caso 3: uso di variabili categoriche per eseguire il mapping di valori integer a categorie

Se attualmente si usano variabili categoriche che esegue il mapping di valori integer a categorie, è probabile che si usi la funzione **tolong** per convertire i dati da un tipo **Double** a un tipo **Long** . Analogamente ai casi 1 e 2, è necessario unire le colonne del **tipo** di dati **Double** e **Long** .

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definizione di variabile in Esplora serie temporali-categorico

*Definizione di variabile precedente:*

[![Screenshot mostra la finestra di dialogo Aggiungi nuova variabile per la variabile PropertyValue, categorico.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nuova definizione di variabile:*

[![Screenshot mostra la finestra di dialogo Aggiungi nuova variabile per la variabile PropertyValue con un valore personalizzato, categorico.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

È anche possibile usare **COALESCE ($Event. PropertyValue. Double, ToDouble ($Event. PropertyValue. Long))** come [espressione della serie temporale](/rest/api/time-series-insights/preview#time-series-expression-and-syntax)personalizzata.

Le variabili categoriche richiedono ancora un valore di tipo Integer. Il **tipo di dati di tutti** gli argomenti in **COALESCE ()** deve essere di tipo **Long** nell' [espressione della serie temporale personalizzata.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Definizione di variabile inline usando le API di query TSX-categorico

*Definizione di variabile precedente:*

```JSON
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
```

*Nuova definizione di variabile:*

```JSON
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
```

Le variabili categoriche richiedono ancora un valore di tipo Integer. Il **tipo di dati di tutti** gli argomenti in **COALESCE ()** deve essere di tipo **Long** nell' [espressione della serie temporale](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizzata.

> [!NOTE]
> Si consiglia di aggiornare queste variabili in tutte le posizioni in cui possono essere utilizzate. Questi punti includono il modello Time Series, le query salvate e le query del connettore Power BI.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Caso 4: uso di JavaScript SDK per creare un'applicazione front-end personalizzata

Se si è interessati da casi da 1 a 3 e si creano applicazioni personalizzate, è necessario aggiornare le query in modo da usare la funzione **COALESCE ()** , come illustrato negli esempi precedenti.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Caso 5: prossimità del limite della proprietà 1.000 dello Store warm

Se si è un utente di negozio a caldo con un numero elevato di proprietà e si ritiene che questa modifica possa comportare il push dell'ambiente sul limite dei nomi di proprietà dell'archivio caldo 1.000, inviare un ticket di supporto tramite il portale di Azure e menzionare questa comunicazione.

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'elenco completo dei [tipi di dati supportati](concepts-supported-data-types.md).