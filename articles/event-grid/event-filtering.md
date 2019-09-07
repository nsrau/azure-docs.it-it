---
title: Filtri eventi per Griglia di eventi di Azure
description: Questo articolo illustra come filtrare gli eventi quando si crea una sottoscrizione di Griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390184"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Informazioni sui filtri eventi per le sottoscrizioni di Griglia di eventi

Questo articolo descrive i diversi modi per filtrare gli eventi che vengono inviati all'endpoint. Quando si crea una sottoscrizione di eventi, sono disponibili tre opzioni di filtro:

* Tipi di eventi
* L'oggetto inizia o termina con
* Campi e operatori avanzati

## <a name="event-type-filtering"></a>Filtro in base al tipo di evento

Per impostazione predefinita, tutti i [tipi di evento](event-schema.md) dell'origine eventi vengono inviati all'endpoint. È possibile decidere di inviare all'endpoint solo determinati tipi di evento. È ad esempio possibile ricevere le notifiche degli aggiornamenti alle risorse, ma non quelle relative ad altre operazioni, come le eliminazioni. In tal caso, filtrare in base al tipo di evento `Microsoft.Resources.ResourceWriteSuccess`. Fornire una matrice con i tipi di evento o specificare `All` per ottenere tutti i tipi di evento per l'origine eventi.

La sintassi JSON per l'applicazione di un filtro in base al tipo di evento è la seguente:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtro in base all'oggetto

Per applicare un filtro semplice in base all'oggetto, specificare un valore iniziale o finale per l'oggetto. È ad esempio possibile specificare che l'oggetto termina con `.txt` per ottenere solo gli eventi correlati al caricamento di un file di testo in un account di archiviazione. È anche possibile specificare che l'oggetto inizia con `/blobServices/default/containers/testcontainer` per ottenere tutti gli eventi per il contenitore specificato, ma non per altri contenitori nell'account di archiviazione.

Quando si pubblicano eventi per argomenti personalizzati, creare oggetti per gli eventi che facilitino i sottoscrittori a capire se sono interessati nell'evento. I sottoscrittori usano la proprietà subject per filtrare e instradare gli eventi. È consigliabile aggiungere il percorso in cui si è verificato l'evento, in modo che i sottoscrittori possano filtrare in base ai segmenti di tale percorso. Il percorso consente ai sottoscrittori di filtrare gli eventi a seconda della dimensione. Se ad esempio si specifica un percorso di tre segmenti, come `/A/B/C` nell'oggetto, i sottoscrittori possono filtrare in base al primo segmento `/A` per ottenere un ampio set di eventi. Tali sottoscrittori ricevono eventi con oggetti come `/A/B/C` o `/A/D/E`. Altri sottoscrittori possono filtrare in base a `/A/B` per ottenere un set di eventi più ristretto.

La sintassi JSON per il filtro in base al soggetto è:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtro avanzato

Per filtrare i valori nei campi dati e specificare l'operatore di confronto, usare l'opzione di filtro avanzato. Quando si applica un filtro avanzato si specificano gli elementi seguenti:

* operator type: il tipo di confronto.
* key: il campo nei dati dell'evento che viene usato per il filtro. Può essere un numero, un valore booleano o una stringa.
* valore o valori: i valori da confrontare con la chiave.

Se si specifica un singolo filtro con più valori, viene eseguita un'operazione **o** , pertanto il valore del campo chiave deve essere uno di questi valori. Di seguito è fornito un esempio:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se si specificano più filtri diversi, viene eseguita un'operazione and, quindi è necessario soddisfare ogni condizione **di** filtro. Di seguito è fornito un esempio: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operator"></a>Operator

Gli operatori disponibili per i numeri sono:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

L'operatore disponibile per i valori booleani è: BoolEquals

Gli operatori disponibili per le stringhe sono:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Per tutti i confronti tra stringhe non viene fatta distinzione tra maiuscole e minuscole.

### <a name="key"></a>Chiave

Per gli eventi nello schema di Griglia di eventi, usare i valori seguenti per l'elemento key:

* id
* Argomento
* Subject
* Tipo di evento
* DataVersion
* Dati dell'evento (ad esempio, Data.key1)

Per gli eventi nello schema di Eventi cloud, usare i valori seguenti per l'elemento key:

* EventId
* Source
* Tipo di evento
* EventTypeVersion
* Dati dell'evento (ad esempio, Data.key1)

Per uno schema di input personalizzato, usare i campi dati degli eventi (ad esempio, Data.key1).

### <a name="values"></a>Valori

I valori possibili sono:

* number
* string
* boolean
* array

### <a name="limitations"></a>Limitazioni

I filtri avanzati presentano le limitazioni seguenti:

* Cinque filtri avanzati per ogni sottoscrizione di Griglia di eventi
* 512 caratteri per ogni valore stringa
* Cinque valori per gli operatori **in** e **not in**

È possibile usare l'elemento key in più filtri.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'applicazione di filtri agli eventi con PowerShell e l'interfaccia della riga di comando di Azure, vedere [Filtrare gli eventi per Griglia di eventi](how-to-filter-events.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
