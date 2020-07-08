---
title: Usare i metodi diretti in analisi video in tempo reale su IoT Edge-Azure
description: Analisi video in tempo reale su IoT Edge espone diversi metodi diretti. I metodi diretti sono basati sulle convenzioni descritte in questo argomento.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: b87452de6b12b0335afca5e28abb3ef6adb29157
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260804"
---
# <a name="direct-methods"></a>Metodi diretti

Analisi video in tempo reale su IoT Edge espone diversi metodi diretti che possono essere richiamati dall'hub Internet. I metodi diretti rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, dato che dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente. Questo approccio è utile per gli scenari in cui la linea di condotta immediata è diversa a seconda che il dispositivo sia in grado di rispondere o meno, Per altre informazioni, vedere [comprendere e richiamare metodi diretti dall'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)Internet.

In questo argomento vengono descritti questi metodi e convenzioni.

## <a name="conventions"></a>Convenzioni

I metodi diretti si basano sulle convenzioni seguenti:

1. I metodi diretti hanno una versione specificata in MAJOR. Formato secondario, come illustrato nell'esempio seguente:

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Una determinata versione di analisi video live sul modulo IoT Edge supporterà tutti i metodi diretti fino alla versione corrente. Ad esempio, il modulo versione 1,3 supporterà metodi diretti con le versioni 1,3, 1,2, 1,1 e 1,0.
3. Tutti i metodi diretti sono sincroni.
4. I risultati dell'errore seguono lo schema di errore OData.
5. I nomi devono osservare i vincoli seguenti:
    
    * Solo caratteri alfanumerici e trattini fino a quando non inizia e termina con un trattino
    * Nessun spazio
    * Massimo 32 caratteri

### <a name="example"></a>Esempio

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Codici di errore di primo livello     

|Stato |Codice   |Message|
|---|---|---|
|400|   BadRequest| Richiesta non valida.|
|400|   InvalidResource|    Risorsa non valida|
|400|   InvalidVersion| La versione dell'API non è valida|
|402|   ConnectivityRequired    |Per il corretto funzionamento del modulo Edge è necessaria la connettività cloud.|
|404|   NotFound    |La risorsa non è stata trovata|
|409|   Conflitto|   Conflitto di operazioni|
|500|   InternalServerError|    Errore interno del server|
|503|   ServerBusy| Server occupato|

### <a name="detailed-error-codes"></a>Codici di errore dettagliati

Vengono aggiunti come dettagli errore un errore di convalida dettagliato, ad esempio le convalide dei moduli Graph.

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Stato|    Codice dettagliato   |Descrizione|
|---|---|---|
|400|   GraphValidationError|   Errori generali del grafo, ad esempio cicli o partizionamento e così via.|
|400|   ModuleValidationError|  Errori di convalida specifici del modulo.|
|409|   GraphTopologyInUse| Una o più istanze di Graph fanno ancora riferimento alla topologia Graph.|
|409|   OperationNotAllowedInState| Non è possibile eseguire l'operazione richiesta nello stato corrente.|
|409|   ResourceValidationError|    La risorsa a cui si fa riferimento, ad esempio: asset, non è in uno stato valido.|

## <a name="details"></a>Dettagli  

### <a name="graphtopologyget"></a>GraphTopologyGet

Questo metodo diretto recupera una singola topologia Graph.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Entità trovata|  200 |N/D
|Errori generali dell'utente    |intervallo 400  ||
|Entità non trovata   |404        ||
|Errori generali del server| intervallo 500       ||

### <a name="graphtopologyset"></a>GraphTopologySet

Crea una singola topologia se non ne esiste già uno con il nome o gli aggiornamenti specificati ed esiste già uno con lo stesso nome.

Aspetti principali:

* La topologia può essere aggiornata liberamente perché non vi sono grafici che vi fanno riferimento.
* La topologia può essere aggiornata liberamente se tutti i grafici di riferimento vengono disattivati purché:

    * I parametri appena aggiunti hanno valori predefiniti
    * Nessun grafo fa riferimento ai parametri rimossi
* Gli aggiornamenti della topologia non sono consentiti se sono presenti grafici attivi

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
Entità esistente aggiornata |200|   N/D|
Nuova entità creata  |201|   N/D|
Errori generali dell'utente |intervallo 400  ||
Errori di convalida del grafo |400    |GraphValidationError|
Errori di convalida del modulo|   400 |ModuleValidationError|
Errori generali del server   |intervallo 500  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Elimina una singola topologia Graph.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Entità eliminata|    200|    N/D|
|Entità non trovata|  204|    N/D|
|Errori generali dell'utente|   intervallo 400   ||
|Una o più istanze di Graph fanno riferimento alla topologia del grafo| 409 |GraphTopologyInUse|
|Errori generali del server| intervallo 500   ||

### <a name="graphtopologylist"></a>GraphTopologyList

Recupera un elenco di tutte le topologie del grafico che corrispondono ai criteri di filtro.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Supporto del filtro

|Operazione      |Campo/i   |Operatori|
|---|---|---|
|$orderby|name  |ASC|


#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Operazione completata|   200 |N/D|
|Errori generali dell'utente|   intervallo 400   ||
|Errori generali del server| intervallo 500   ||

### <a name="graphinstanceget"></a>GraphInstanceGet

Recupera una singola istanza di Graph:

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Entità trovata   |200|   N/D|
|Errori generali dell'utente|   intervallo 400   ||
|Entità non trovata|  404 ||
|Errori generali del server| intervallo 500   ||

### <a name="graphinstanceset"></a>GraphInstanceSet

Crea una singola istanza di Graph se non ne esiste uno con il nome o gli aggiornamenti specificati ed esiste un oggetto con lo stesso nome.

Aspetti principali:

* L'istanza del grafo può essere aggiornata liberamente mentre si trova nello stato "disattivato".

    * Il grafo viene riconvalidato a ogni aggiornamento.
* Gli aggiornamenti dell'istanza Graph sono limitati parzialmente quando il grafico non si trova nello stato "inattivo".
* Gli aggiornamenti dell'istanza Graph non sono consentiti nei grafici attivi.

#### <a name="request"></a>Richiesta

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Risposta

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Entità esistente aggiornata    |200    |N/D|
|Nuova entità creata|    201 |N/D|
|Errori generali dell'utente|   intervallo 400   ||
|Errori di convalida del grafo    |400|   GraphValidationError|
|Errori di convalida del modulo|  400 |ModuleValidationError|
|Errori di convalida delle risorse |409    |ResourceValidationError|
|Errori generali del server  |intervallo 500||    

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Elimina una singola istanza di Graph.

Aspetti principali:

* È possibile eliminare solo i grafici disattivati.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Il grafico è stato eliminato|    200|    N/D|
|Il grafico non è stato trovato|   204|    N/D|
|Errori generali dell'utente    |intervallo 400  ||
|Il grafico non è nello stato "arrestato"    |409    |OperationNotAllowedInState|
|Errori generali del server| intervallo 500   ||

### <a name="graphinstancelist"></a>GraphInstanceList

Questa operazione è simile a GraphTopologyList. Consente l'uso di per enumerare le istanze del grafo.
Recupera un elenco di tutte le istanze dei grafici che corrispondono ai criteri di filtro.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Supporto del filtro

|Operazione  |   Campo/i|   Operatori|
|---|---|---|
|$orderby|  name|   ASC|

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Operazione completata    |200    |N/D|
|Errori generali dell'utente|   intervallo 400   ||
|Errori generali del server| intervallo 500   ||

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Attiva un'unica istanza di Graph. 

Aspetti principali

* Il metodo restituisce solo quando il grafico è attivato 
* Il grafico presuppone lo stato di attivazione durante l'attivazione.

    * Un'operazione List/Get sul grafico restituisce il grafo sullo stato appropriato.
* Idempotenza

    * L'avvio di un grafico nello stato "attivazione" si comporta allo stesso modo di quando il grafico è stato disattivato (ovvero i blocchi di chiamata fino all'attivazione del grafo)
    * L'attivazione di un grafico con stato "attivo" restituisce immediatamente un risultato.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Codici di stato

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Il grafico è stato attivato   |200    |N/D|
|Nuova entità creata |201|   N/D|
|Errori generali dell'utente    |intervallo 400  ||
|Errori di convalida del modulo   |400|   ModuleValidationError|
|Errori di convalida delle risorse|    409|    ResourceValidationError|
|Il grafico è in stato di disattivazione |409    |OperationNotAllowedInState|
|Errori generali del server| intervallo 500   ||

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Disattiva una singola istanza di Graph. La disattivazione di un grafico disattiva correttamente l'elaborazione del supporto e garantisce che tutti gli eventi e i supporti archiviati in modo temporaneo nel perimetro siano vincolati al cloud, quando applicabile.

Aspetti principali:

* Il metodo restituisce solo quando il grafico è disattivato
* Il grafico presuppone lo stato di disattivazione mentre viene disattivato.

    * Un'operazione List/Get sul grafico restituisce il grafo sullo stato appropriato.
    * Stop viene completato solo quando tutti i supporti sono stati caricati nel cloud.
* Idempotenza

    * La disattivazione di un grafico nello stato "disattivazione" si comporta allo stesso modo di quando il grafico è stato disattivato (ovvero i blocchi di chiamata fino alla disattivazione del grafo)
    * La disattivazione di un grafico sullo stato "inattivo" restituisce immediatamente un risultato.

#### <a name="request"></a>Richiesta

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Risposta

```
{
    "status": 200,
    "payload": null
}
```

|Condizione  |Codice di stato    |Codice di errore dettagliato|
|---|---|---|
|Il grafico è stato attivato   |200|   N/D|
|Nuova entità creata |201|   N/D|
|Errori generali dell'utente    |intervallo 400  ||
|Il grafico è nello stato di attivazione   |409|   OperationNotAllowedInState|
|Errori generali del server  |intervallo 500  ||

## <a name="next-steps"></a>Passaggi successivi

[Schema di configurazione del modulo gemello](module-twin-configuration-schema.md)
