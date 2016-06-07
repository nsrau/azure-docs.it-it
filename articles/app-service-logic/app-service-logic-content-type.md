<properties
   pageTitle="Gestione del tipo di contenuto nelle app per la logica | Microsoft Azure"
   description="Informazioni sul modo in cui App per la logica gestisce i tipi di contenuto in fase di progettazione e di runtime"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/03/2016"
   ms.author="jehollan"/>

# Gestione del tipo di contenuto nelle app per la logica

Esistono molti tipi diversi di contenuto che possono attraversare un'app per la logica, tra cui JSON, XML, file flat e dati binari. Benché siano supportati tutti i tipi di contenuto, alcuni sono vengono riconosciuti in modo nativo dal motore delle app per la logica e altri possono richiedere il cast o la conversione, secondo le esigenze. L'articolo seguente descrive il modo in cui il motore gestisce diversi tipi di contenuto e come gestire correttamente i contenuti in base alle esigenze.

## Intestazione Content-Type

Per semplicità, esaminiamo due valori `Content-Types` che non richiedono alcuna conversione o cast da usare nei tipi di contenuto `application/json` e `text/plain` di un'app per la logica.

### Application/json

Il motore del flusso di lavoro si basa sull'intestazione `Content-Type` delle chiamate HTTP per determinare la gestione appropriata. Qualsiasi richiesta con tipo di contenuto `application/json` verrà archiviata e gestita come un oggetto JSON. Per impostazione predefinita, il contenuto JSON può anche essere analizzato senza che sia necessario alcun cast. Pertanto, una richiesta con l'intestazione content-type `application/json ` come questa:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

può essere analizzata in un flusso di lavoro con un'espressione come `@body('myAction')['foo'][0]` per ottenere un valore, in questo caso, `bar`. Non è necessario alcun cast aggiuntivo. Se si lavora con dati JSON ma non è stata specificata un'intestazione, è possibile impostare manualmente i dati su JSON usando la funzione `@json()` (ad esempio: `@json(triggerBody())['foo']`).

### Text/plain

Come per `application/json`, i messaggi HTTP ricevuti con l'intestazione `Content-Type` di `text/plain` verranno archiviati nel relativo formato non elaborato. Se inclusa in azioni successive senza cast, la richiesta verrà inviata anche con un'intestazione `Content-Type`: `text/plain`. Ad esempio, se si lavora con un file flat, si può ricevere il contenuto HTTP seguente:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

come `text/plain`. Se nell'azione successiva si invia il contenuto come corpo di un'altra richiesta (`@body('flatfile')`), la richiesta avrà un'intestazione Content-Type `text/plain`. Se si lavora con dati che sono testo normale ma non è stata specificata un'intestazione, è possibile impostare manualmente i dati come testo usando la funzione `@string()` (ad esempio: `@string(triggerBody())`).

### Application/xml, Application/octet-stream e funzioni del convertitore

Il motore dell'app per la logica manterrà sempre il valore `Content-Type` che è stato ricevuto per la richiesta o risposta HTTP. Questo significa che se un contenuto viene ricevuto con `Content-Type` `application/octet-stream`, l'inserimento dello stesso in un'azione successiva senza cast genererà una richiesta in uscita con `Content-Type`: `application/octet-stream`. In questo modo il motore è in grado di garantire che i dati non vadano perduti mentre attraversano il flusso di lavoro. Tuttavia, lo stato dell'azione, ovvero gli input e gli output, viene memorizzato in un oggetto JSON mentre attraversa il flusso di lavoro. Ciò significa che per mantenere alcuni tipi di dati, il motore convertirà il contenuto in una stringa binaria con codifica Base64, con i metadati appropriati per conservare sia `$content` che `$content-type`, che verrà automaticamente convertita. È anche possibile convertire manualmente i tipi di contenuto usando le funzioni del convertitore incorporato:

* `@json()`: esegue il cast dei dati in `application/json`
* `@xml()`: esegue il cast dei dati in `application/xml`
* `@binary()`: esegue il cast dei dati in `application/octet-stream`
* `@string()`: esegue il cast dei dati in `text/plain`
* `@base64()`: converte il contenuto in una stringa Base64
* `@base64toString()`: converte una stringa Base64 codificata in `text/plain`
* `@base64toBinary()`: converte una stringa Base64 codificata in `application/octet-stream`
* `@encodeDataUri()`: codifica una stringa come matrice di byte dataUri
* `@decodeDataUri()`: decodifica un dataUri in una matrice di byte

Ad esempio, se si riceve una richiesta HTTP con `Content-Type`: `application/xml` di:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Si potrebbe eseguire il cast e usarlo in un secondo tempo con un elemento simile a `@xml(triggerBody())` o all'interno di una funzione come `@xpath(xml(triggerBody()), '/CustomerName')`.

### Altri tipi di contenuto

Esistono altri tipi di contenuto che sono supportati e funzionano con le app per la logica, ma potrebbero richiedere il recupero manuale del corpo del messaggio con la decodifica di `$content`. Ad esempio, se si attiva una richiesta `application/x-www-url-formencoded` simile alla seguente:

```
CustomerName=Frank&Address=123+Avenue
```

poiché i dati non sono testo normale o JSON verranno memorizzati nell'azione come:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Dove `$content` rappresenta il payload codificato come stringa Base64 per mantenere tutti i dati. Poiché al momento non esiste una funzione nativa per dati del modulo, questi dati possono essere usati all'interno di un flusso di lavoro eseguendo manualmente l'accesso ai dati con una funzione come `@string(body('formdataAction'))`. Se la richiesta in uscita deve avere anche l'intestazione content-type `application/x-www-url-formencoded`, è sufficiente aggiungerla al corpo dell'azione senza cast come `@body('formdataAction')`. Questa operazione tuttavia funziona solo se il corpo è l'unico parametro nell'input `body`. Se si tenta di eseguire `@body('formdataAction')` all'interno di una richiesta `application/json` si riceve un errore di runtime perché viene inviato il corpo codificato.

<!---HONumber=AcomDC_0525_2016-->