---
title: Gemelli digitali e grafo dei gemelli
titleSuffix: Azure Digital Twins
description: Comprendere il concetto di dispositivo gemello digitale e il modo in cui le relazioni costituiscono un grafo.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6b6da2a15441564ef0b67e76ee5a0e0c85839a63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609317"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Comprendere i dispositivi gemelli digitali e i relativi grafici gemelli

In una soluzione di dispositivi gemelli digitali di Azure, le entità nell'ambiente sono rappresentate dai dispositivi **gemelli digitali**di Azure. Un dispositivo gemello digitale è un'istanza di uno dei [modelli](concepts-models.md)personalizzati definiti. Può essere connessa ad altri dispositivi gemelli digitali tramite **relazioni** per formare un **grafico a due gemelli**: questo grafico a due è la rappresentazione dell'intero ambiente.

> [!TIP]
> "I dispositivi gemelli digitali di Azure" si riferiscono a questo servizio di Azure nel suo complesso. "I gemelli digitali" o solo i "gemelli" si riferiscono ai singoli nodi gemelli all'interno dell'istanza del servizio.

## <a name="creating-digital-twins"></a>Creazione di gemelli digitali

Prima di poter creare un dispositivo gemello digitale nell'istanza di Azure Digital gemelli, è necessario che nel servizio sia caricato un *modello* . Un modello descrive il set di proprietà, i messaggi di telemetria e le relazioni che un particolare gemello può avere, tra le altre cose. Per informazioni sui tipi definiti in un modello, vedere [concetti: modelli personalizzati](concepts-models.md).

Dopo la creazione e il caricamento di un modello, l'app client può creare un'istanza del tipo. si tratta di un dispositivo gemello digitale. Ad esempio, dopo aver creato un modello di *floor*, è possibile creare uno o più dispositivi gemelli digitali che usano questo tipo, ad esempio un gemello di tipo *floor*denominato *pianterreno*, un altro denominato *floor2*e così via. 

Di seguito è riportato un frammento di codice client che usa le [API DigitalTwins](how-to-use-apis-sdks.md) per creare un'istanza di un gemello di tipo *room*.

Nell'anteprima corrente dei dispositivi gemelli digitali di Azure, tutte le proprietà di un dispositivo gemello devono essere inizializzate prima che il dispositivo gemello possa essere creato. Questa operazione viene eseguita creando un documento JSON che fornisce i valori di inizializzazione necessari.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

## <a name="relationships-creating-a-graph-of-digital-twins"></a>Relazioni: creazione di un grafico di gemelli digitali

I dispositivi gemelli sono connessi in un grafo gemello dalle relazioni. Le relazioni che possono avere un gemello sono definite come parte del modello.  

Ad esempio, il *piano* del modello potrebbe definire una relazione *Contains* destinata ai gemelli di tipo *room*. Con questa definizione *, i dispositivi* gemelli digitali di Azure consentiranno *di creare relazioni tra i* dispositivi gemelli *e i dispositivi gemelli* , inclusi i sottotipi di *chat* . 

Di seguito è riportato un esempio di codice client che usa le [API DigitalTwins](how-to-use-apis-sdks.md) per creare una relazione tra un gemello digitale di tipo *floor*denominato *pianterreno* e un gemello digitale di tipo *room*chiamato *Cafe*.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

Il risultato di questo processo è un set di nodi (i gemelli digitali) connessi tramite bordi (le rispettive relazioni) in un grafico.

## <a name="json-representations-of-graph-elements"></a>Rappresentazioni JSON degli elementi del grafico

I dati del dispositivo gemello digitale e i dati delle relazioni sono archiviati in formato JSON. Ciò significa che quando si [esegue una query sul grafico a due](how-to-query-graph.md) gemelli nell'istanza di Azure Digital Twins, il risultato sarà una rappresentazione JSON dei gemelli digitali e delle relazioni create.

### <a name="digital-twin-json-format"></a>Formato JSON digitale gemello

Quando viene rappresentato come oggetto JSON, un dispositivo gemello digitale Visualizza i campi seguenti:

| Nome campo | Descrizione |
| --- | --- |
| `$dtId` | Stringa fornita dall'utente che rappresenta l'ID del dispositivo gemello digitale |
| `$conformance` | Enumerazione contenente lo stato di conformità del dispositivo gemello*digitale (conforme,* *non conforme*, *sconosciuto*) |
| `{propertyName}` | Valore di una proprietà in JSON ( `string` , tipo numerico o oggetto) |
| `$relationships` | URL del percorso della raccolta di relazioni. Questo campo è assente se il gemello digitale non ha bordi di relazione in uscita. |
| `$metadata.$model` | Opzionale ID dell'interfaccia del modello che caratterizza il gemello digitale |
| `$metadata.{propertyName}.desiredValue` | [Solo per le proprietà scrivibili] Valore desiderato della proprietà specificata. |
| `$metadata.{propertyName}.desiredVersion` | [Solo per le proprietà scrivibili] Versione del valore desiderato |
| `$metadata.{propertyName}.ackVersion` | La versione riconosciuta dall'app per dispositivi che implementa il dispositivo gemello digitale |
| `$metadata.{propertyName}.ackCode` | [Solo per le proprietà scrivibili] `ack`Codice restituito dall'app per dispositivi che implementa il dispositivo gemello digitale |
| `$metadata.{propertyName}.ackDescription` | [Solo per le proprietà scrivibili] `ack`Descrizione restituita dall'app per dispositivo che implementa il gemello digitale |
| `{componentName}` | Oggetto JSON che contiene i valori e i metadati della proprietà del componente, simili a quelli dell'oggetto radice. Questo oggetto esiste anche se il componente non dispone di proprietà. |
| `{componentName}.{propertyName}` | Il valore della proprietà del componente in JSON ( `string` , tipo numerico o oggetto) |
| `{componentName}.$metadata` | Informazioni sui metadati per il componente, in modo analogo al livello radice`$metadata` |

Di seguito è riportato un esempio di un dispositivo gemello digitale formattato come oggetto JSON:

```json
{
  "$dtId": "Cafe",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Formato JSON relazione

Quando rappresentata come oggetto JSON, una relazione da un dispositivo gemello digitale visualizzerà i campi seguenti:

| Nome campo | Descrizione |
| --- | --- |
| `$relationshipId` | Stringa fornita dall'utente che rappresenta l'ID di questa relazione. Questa stringa è univoca nel contesto del dispositivo gemello digitale di origine, che significa anche che `sourceId`  +  `relationshipId` è univoco nel contesto dell'istanza di Azure Digital gemelli. |
| `$sourceId` | ID del dispositivo gemello digitale di origine |
| `$targetId` | ID del dispositivo gemello digitale di destinazione |
| `$relationshipName` | Nome della relazione. |
| `{propertyName}` | Opzionale Valore di una proprietà di questa relazione, in JSON ( `string` , tipo numerico o oggetto) |

Di seguito è riportato un esempio di una relazione formattata come oggetto JSON:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Passaggi successivi

Vedere How to Manage Graph elements with Azure Digital Twin API:
* [Procedura: gestire i dispositivi gemelli digitali](how-to-manage-twin.md)
* [Procedura: gestire il grafo gemello con relazioni](how-to-manage-graph.md)

In alternativa, informazioni sull'esecuzione di query sul grafico gemello di Azure Digital gemelli per informazioni:
* [Concetti: linguaggio di query](concepts-query-language.md)