---
title: Elaborazione dei dati e funzioni definite dall'utente con Gemelli digitali di Azure | Microsoft Docs
description: Panoramica dell'elaborazione dei dati, dei matcher e delle funzioni definite dall'utente con Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210126"
---
# <a name="data-processing-and-user-defined-functions"></a>Elaborazione dati e funzioni definite dall'utente

Gemelli digitali di Azure offre funzionalità di calcolo avanzate. Gli sviluppatori possono definire ed eseguire funzioni personalizzate su messaggi di telemetria in ingresso per inviare eventi a endpoint predefiniti.

## <a name="data-processing-flow"></a>Flusso di elaborazione dei dati

Quando i dispositivi inviano dati di telemetria a Gemelli digitali, gli sviluppatori possono elaborare i dati in quattro fasi: _convalida_, _associazione_, _calcolo_ e _invio_:

![Flusso di elaborazione dei dati di Gemelli digitali][1]

1. La fase di _convalida_ trasforma il messaggio di telemetria in ingresso in un formato [**oggetto di trasferimento dati**](https://en.wikipedia.org/wiki/Data_transfer_object) facilmente riconoscibile. Questa fase esegue anche la convalida di dispositivi e sensori.
1. La fase di _associazione_ trova la o le funzioni definite dall'utente appropriate da eseguire. I matcher predefiniti trovano le funzioni definite dall'utente in base alle informazioni su dispositivi, sensori e spazi dal messaggio di telemetria in ingresso.
1. La fase di _calcolo_ esegue le funzioni definite dall'utente associate nella fase precedente. Queste funzioni possono leggere e aggiornare i valori calcolati nei nodi del grafico spaziale e possono emettere notifiche personalizzate.
1. La fase di _invio_ instrada tutte le notifiche personalizzate dalla fase di calcolo agli endpoint definiti nel grafico.

## <a name="data-processing-objects"></a>Oggetti di elaborazione dei dati

L'elaborazione dei dati in Gemelli digitali di Azure consiste nella definizione di tre oggetti: _matcher_, _funzioni definite dall'utente_ e _assegnazioni di ruolo_:

![Flusso di elaborazione dei dati di Gemelli digitali][2]

### <a name="matchers"></a>Matcher

I _matcher_ definiscono un set di condizioni che valutano quali azioni verranno eseguite in base alla telemetria dei sensori in ingresso. Queste condizioni per determinare l'associazione possono includere proprietà del sensore, del dispositivo padre del sensore e dello spazio padre del sensore. Le condizioni sono espresse come confronti rispetto a un [percorso JSON](http://jsonpath.com/), come mostrato nell'esempio seguente:

- Tutti i sensori del tipo di dati **Temperature**.
- Con `01` nella rispettiva porta.
- Che appartengono a dispositivi con la chiave di proprietà estesa **Manufacturer** impostata sul valore `"GoodCorp"`.
- Che appartengono a spazi di tipo `"Venue"`.
- Che sono discendenti dell'elemento **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` padre.

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - I percorsi JSON fanno distinzione tra maiuscole e minuscole.
> - Il payload JSON è lo stesso del payload che verrebbe restituito da:
>   - `/sensors/{id}?includes=properties,types` per il sensore.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` per il dispositivo padre del sensore.
>   - `/spaces/{id}?includes=properties,types,location,timezone` per lo spazio padre del sensore.
> - I confronti non fanno distinzione tra maiuscole e minuscole.

### <a name="user-defined-functions"></a>Funzioni definite dall'utente

Una _funzione definita dall'utente_ o _UDF_ è una funzione personalizzata che viene eseguita all'interno di un ambiente isolato in Gemelli digitali di Azure. Le funzioni definite dall'utente hanno accesso sia al messaggio di telemetria del sensore non elaborato al momento della ricezione sia al grafico spaziale e al servizio dispatcher. Dopo che la funzione definita dall'utente viene registrata nel grafico, deve essere creato un matcher (descritto in dettaglio sopra) per specificare quando eseguire la funzione definita dall'utente. Quando Gemelli digitali riceve nuova telemetria da un sensore specifico, la funzione definita dall'utente associata può calcolare una media mobile, ad esempio, delle ultime letture del sensore.

Le funzioni definite dall'utente possono essere scritte in JavaScript e permettono agli sviluppatori di eseguire frammenti di codice personalizzati in base ai messaggi di telemetria del sensore. Sono anche disponibili metodi helper per interagire con il grafico nell'ambiente di esecuzione definito dall'utente. Con una funzione definita dall'utente gli sviluppatori possono:

- Impostare la lettura del sensore direttamente nell'oggetto del sensore all'interno del grafico.
- Eseguire un'azione in base a letture del sensore diverse all'interno di uno spazio nel grafico.
- Creare una notifica quando vengono soddisfatte determinate condizioni per una lettura del sensore in ingresso.
- Collegare metadati del grafico alla lettura del sensore prima di inviare una notifica.

Per altre informazioni, fare riferimento a [How to Use User-Defined Functions](how-to-user-defined-functions.md) (Come usare funzioni definite dall'utente).

### <a name="role-assignment"></a>Assegnazione di ruolo

Le operazioni di una funzione definita dell'utente sono soggette al controllo degli accessi in base al ruolo di Gemelli digitali per proteggere i dati all'interno del servizio. Le assegnazioni di ruolo fanno sì che una funzione definita dall'utente specifica abbia le autorizzazioni necessarie per interagire con il grafico spaziale. Ad esempio, una funzione definita dall'utente potrebbe tentare di creare, leggere, aggiornare o eliminare i dati del grafico in un determinato spazio. Il livello di accesso di una funzione definita dell'utente viene controllato quando la funzione definita dall'utente chiede dati al grafico o tenta un'operazione. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](security-create-manage-role-assignments.md).

È possibile che un matcher attivi una funzione definita dall'utente priva di assegnazioni di ruolo. In questo caso, la funzione definita dall'utente non riuscirà a leggere alcun dato dal grafico.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come instradare eventi e messaggi di telemetria ad altri servizi di Azure, vedere [Routing di eventi e messaggi](concepts-events-routing.md).

Per altre informazioni su come creare matcher, funzioni definite dall'utente e assegnazioni di ruolo, vedere [Guide for using user-defined functions](how-to-user-defined-functions.md) (Guida per l'uso di funzioni definite dall'utente).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
