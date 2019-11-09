---
title: Elaborazione dei dati e funzioni definite dall'utente-dispositivi gemelli digitali di Azure | Microsoft Docs
description: Panoramica dell'elaborazione dei dati, dei matcher e delle funzioni definite dall'utente con Gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 0708b1dd2d272757949d014d768c1da649b50146
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889689"
---
# <a name="data-processing-and-user-defined-functions"></a>Elaborazione dati e funzioni definite dall'utente

Gemelli digitali di Azure offre funzionalità di calcolo avanzate. Gli sviluppatori possono definire ed eseguire funzioni personalizzate su messaggi di telemetria in ingresso per inviare eventi a endpoint predefiniti.

## <a name="data-processing-flow"></a>Flusso di elaborazione dei dati

Quando i dispositivi inviano dati di telemetria a Gemelli digitali di Azure, gli sviluppatori possono elaborare i dati in quattro fasi: *convalida*, *associazione*, *calcolo* e *invio*.

[![flusso di elaborazione dati dei gemelli digitali di Azure](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. La fase di convalida trasforma il messaggio di telemetria in ingresso in un formato di [oggetto di trasferimento dati](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) comunemente riconoscibile. Questa fase esegue anche la convalida di dispositivi e sensori.
1. La fase di associazione trova le funzioni definite dall'utente appropriate da eseguire. I matcher predefiniti trovano le funzioni definite dall'utente in base alle informazioni su dispositivi, sensori e spazi dal messaggio di telemetria in ingresso.
1. La fase di calcolo esegue le funzioni definite dall'utente associate nella fase precedente. Queste funzioni possono leggere e aggiornare i valori calcolati nei nodi del grafico spaziale e possono emettere notifiche personalizzate.
1. La fase di invio instrada tutte le notifiche personalizzate dalla fase di calcolo agli endpoint definiti nel grafico.

## <a name="data-processing-objects"></a>Oggetti di elaborazione dei dati

L'elaborazione dei dati in Gemelli digitali di Azure consiste nella definizione di tre oggetti: *matcher*, *funzioni definite dall'utente* e *assegnazioni di ruolo*.

[![oggetti di elaborazione dati di dispositivi gemelli digitali di Azure](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matcher

I matcher definiscono un set di condizioni che valutano quali azioni vengono eseguite in base alla telemetria dei sensori in ingresso. Le condizioni per determinare l'associazione possono includere proprietà del sensore, del dispositivo padre del sensore e dello spazio padre del sensore. Le condizioni sono espresse come confronti rispetto a un [percorso JSON](https://jsonpath.com/), come mostrato in questo esempio:

- Tutti i sensori di tipo di dati **Temperatura** rappresentati dal valore stringa preceduto da un carattere di escape `\"Temperature\"`
- Con `01` nella rispettiva porta
- Che appartengono a dispositivi con la chiave di proprietà estesa **Manufacturer** impostata sul valore stringa preceduto da un carattere di escape `\"Contoso\"`
- Che appartengono agli spazi del tipo specificato dalla stringa preceduta da un carattere di escape `\"Venue\"`
- Che sono discendenti dell'elemento **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` padre

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
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
> - Il payload JSON corrisponde al payload restituito da:
>   - `/sensors/{id}?includes=properties,types` per il sensore.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` per il dispositivo padre del sensore.
>   - `/spaces/{id}?includes=properties,types,location,timezone` per lo spazio padre del sensore.
> - I confronti non fanno distinzione tra maiuscole e minuscole.

### <a name="user-defined-functions"></a>Funzioni definite dall'utente

Una funzione definita dall'utente è una funzione personalizzata eseguita all'interno di un ambiente di Gemelli digitali di Azure isolato. Le funzioni definite dall'utente hanno accesso al messaggio di telemetria del sensore non elaborato al momento della ricezione. Hanno inoltre accesso al grafico spaziale e al servizio dispatcher. Dopo che la funzione definita dall'utente viene registrata in un grafico, deve venire creato un matcher (descritto in dettaglio [sopra](#matchers)) per specificare quando viene eseguita la funzione. Ad esempio, quando Gemelli digitali di Azure riceve nuovi dati di telemetria da un sensore specifico, la funzione definita dall'utente associata può calcolare una media mobile delle ultime letture del sensore.

Le funzioni definite dall'utente possono essere scritte in JavaScript. I metodi helper interagiscono con il grafico nell'ambiente di esecuzione definito dall'utente. Gli sviluppatori possono eseguire frammenti di codice personalizzati sui messaggi di telemetria dei sensori. Tra gli esempi sono inclusi:

- Impostare la lettura del sensore direttamente nell'oggetto del sensore all'interno del grafico.
- Eseguire un'azione in base a letture del sensore diverse all'interno di uno spazio nel grafico.
- Creare una notifica quando vengono soddisfatte determinate condizioni per una lettura del sensore in ingresso.
- Collegare metadati del grafico alla lettura del sensore prima di inviare una notifica.

Per altre informazioni, vedere [Come usare le funzioni definite dall'utente](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Esempi

Il [repository GitHub per l'esempio C# di Gemelli digitali](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contiene alcuni esempi delle funzioni definite dall'utente:
- [Questa funzione](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) cerca i valori relativi ad anidride carbonica, movimento e temperatura per determinare se una stanza è disponibile con questi valori compresi nell'intervallo. Le [esercitazioni per Gemelli digitali](tutorial-facilities-udf.md) illustrano più in dettaglio questa funzione. 
- [Questa funzione](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) cerca i dati di più sensori di movimento e determina che lo spazio è disponibile se nessuno di essi rileva movimenti. È possibile sostituire facilmente la funzione definita dall'utente usata nell'argomento di [avvio rapido](quickstart-view-occupancy-dotnet.md) o nelle [esercitazioni](tutorial-facilities-setup.md), apportando le modifiche indicate nella sezione del file relativa ai commenti. 

### <a name="role-assignment"></a>Assegnazione di ruolo

Le azioni di una funzione definita dell'utente sono soggette al [controllo degli accessi in base al ruolo](./security-role-based-access-control.md) di Gemelli digitali di Azure per proteggere i dati all'interno del servizio. Le assegnazioni di ruolo definiscono quali funzioni definite dall'utente possiedono le autorizzazioni appropriate per interagire con il grafico spaziale e le relative entità. Ad esempio, una funzione definita dall'utente potrebbe avere la capacità e l'autorizzazione per *CREARE*, *LEGGERE*, *AGGIORNARE* o *ELIMINARE* i dati del grafico in uno spazio specificato. Il livello di accesso di una funzione definita dell'utente viene controllato quando la funzione definita dall'utente chiede dati al grafico o tenta un'operazione. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](./security-create-manage-role-assignments.md).

È possibile che un matcher attivi una funzione definita dall'utente priva di assegnazioni di ruolo. In questo caso, la funzione definita dall'utente non riuscirà a leggere alcun dato dal grafico.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come instradare eventi e messaggi di telemetria ad altri servizi di Azure, vedere [Routing di eventi e messaggi](./concepts-events-routing.md).

- Per altre informazioni su come creare matcher, funzioni definite dall'utente e assegnazioni di ruolo, vedere [Guide for using user-defined functions](./how-to-user-defined-functions.md) (Guida per l'uso di funzioni definite dall'utente).

- Esaminare la [documentazione di riferimento della libreria client della funzione definita dall'utente](./reference-user-defined-functions-client-library.md).
