---
title: Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure
description: Questo articolo è un'introduzione alle funzioni JavaScript definite dall'utente in Analisi di flusso.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-javascript
ms.date: 06/16/2020
ms.openlocfilehash: 6540b35925a92ebd6a8bcced427b5457785603db
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056908"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure
 
L'Analisi di flusso di Azure supporta le funzioni definite dall'utente nel linguaggio JavaScript. Con il vasto set di metodi **String**, **RegExp**, **Math**, **Array** e **Date** offerti da JavaScript, risulta più facile creare trasformazioni di dati complessi con processi di Analisi di flusso.

## <a name="overview"></a>Panoramica

Le funzioni JavaScript definite dall'utente supportano funzioni scalari di solo calcolo senza stato che non richiedono connettività esterna. Il valore restituito di una funzione può essere solo un valore scalare singolo. Dopo aver aggiunto una funzione JavaScript definita dall'utente a un processo, è possibile utilizzare la funzione in un punto qualsiasi nella query, ad esempio una funzione scalare incorporata.

Ecco alcuni scenari in cui potrebbero essere utili le funzioni JavaScript definite dall'utente:
* Analisi e manipolazione delle stringhe con funzioni di espressione regolare, ad esempio **Regexp_Replace()** e **Regexp_Extract()**
* Decodifica e codifica dati, ad esempio conversione dal formato binario al formato esadecimale
* Calcoli matematici con funzioni **matematiche** di JavaScript
* Esecuzione di operazioni di matrice come ordinamento, aggiunta, ricerca e riempimento

Ecco alcune operazioni non eseguibili con una funzione JavaScript definita dall'utente in Analisi di flusso:
* Chiamate agli endpoint REST esterni, ad esempio per l'esecuzione di una ricerca inversa degli indirizzi IP o per la raccolta di dati di riferimento da un'origine esterna
* Esecuzione della serializzazione o deserializzazione negli input e output di un formato evento personalizzato
* Creazione di aggregazioni personalizzate

Sebbene le funzioni come **Date.GetDate()** o **Math.random()** non sono bloccate nella definizione delle funzioni, è consigliabile evitare di usarle. Queste funzioni, infatti, **non** restituiscono lo stesso risultato ogni volta che vengono chiamate e Analisi di flusso di Azure non conserva un giornale di registrazione delle chiamate di funzione e dei risultati restituiti. Se una funzione restituisce risultati diversi per gli stessi eventi, non viene garantita la ripetibilità in caso di processo riavviato dall'utente o dal servizio di Analisi di flusso.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Eseguire una funzione JavaScript definita dall'utente al processo

> [!NOTE]
> Questi passaggi funzionano sui processi di Analisi di flusso configurati per l'esecuzione nel cloud. Se il processo di Analisi di flusso è configurato per l'esecuzione su Azure IoT Edge, usare invece Visual Studio e [scrivere la funzione definita dall'utente usando C#](stream-analytics-edge-csharp-udf.md).

Per creare una funzione JavaScript definita dall'utente nel processo di Analisi di flusso, selezionare **Funzioni** in **Topologia processo**. Selezionare quindi **UDF JavaScript** dal menu a discesa **+ Aggiungi**. 

![Aggiungere la funzione definita dall'utente JavaScript](./media/javascript/stream-analytics-jsudf-add.png)

È quindi necessario specificare le proprietà seguenti e selezionare **Salva**.

|Proprietà|Descrizione|
|--------|-----------|
|Alias di funzione|Immettere un nome per richiamare la funzione nella query.|
|Tipo di output|Tipo che verrà restituito dalla funzione JavaScript definita dall'utente alla query di Analisi di flusso.|
|Definizione di funzione|Implementazione della funzione JavaScript che verrà eseguita ogni volta che la funzione definita dall'utente viene richiamata dalla query.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testare e risolvere i problemi delle funzioni definite dall'utente JavaScript 

È possibile testare ed eseguire il debug della logica delle funzioni definite dall'utente JavaScript in qualsiasi browser. Il debug e il test della logica di queste funzioni definite dall'utente non sono attualmente supportati nel portale di Analisi di flusso. Quando la funzione si comporta come previsto, è possibile aggiungerla al processo di analisi di flusso come indicato in precedenza e quindi richiamarla direttamente dalla query. È possibile testare la logica di query con la funzione definita dall'utente JavaScript usando gli [strumenti di Analisi di flusso di Azure per Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

Gli errori di runtime in JavaScript sono considerati irreversibili ed esposti tramite il log attività. Per recuperare il log, nel portale di Azure passare al processo e selezionare **Log attività**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chiamare una funzione JavaScript definita dall'utente nella query

È possibile richiamare facilmente la funzione JavaScript nella query usando l'alias di funzione con il prefisso **udf**. Di seguito è riportato un esempio di una funzione definita dall'utente JavaScript che converte i valori esadecimali in valori integer richiamati in una query di Analisi di flusso.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Oggetti JavaScript supportati

Le funzioni JavaScript definite dall'utente in Analisi di flusso di Azure supportano oggetti JavaScript standard e incorporati. Per un elenco di questi oggetti, vedere [Oggetti globali](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversione dei tipi di Analisi di flusso e JavaScript

Esistono delle differenze tra i tipi supportati da JavaScript e dal linguaggio di query di Analisi di flusso. Questa tabella elenca i mapping di conversione tra i due:

Analisi di flusso | JavaScript
--- | ---
bigint | Numero (per la precisione, JavaScript può rappresentare solo numeri interi fino a 2^53)
Datetime | Data (JavaScript supporta solo millisecondi)
double | Number
nvarchar(MAX) | string
Record | Oggetto
Array | Array
NULL | Null

Ecco le conversioni da JavaScript ad Analisi di flusso:

JavaScript | Analisi di flusso
--- | ---
Number | Bigint (se il numero è arrotondato e compreso tra long.MinValue e long.MaxValue, in caso contrario è doppio)
Data | Datetime
string | nvarchar(MAX)
Oggetto | Record
Array | Array
Null, Undefined | NULL
Qualsiasi altro tipo (ad esempio, una funzione o un errore) | Non supportato (risultati nell'errore di runtime)

Il linguaggio JavaScript distingue tra maiuscole/minuscole e le maiuscole e le minuscole dei campi oggetto nel codice JavaScript devono corrispondere alle maiuscole e alle minuscole dei campi nei dati in ingresso. I processi con livello di compatibilità 1.0 convertiranno i campi dall'istruzione SQL SELECT affinché siano minuscoli. Nel livello di compatibilità 1.1 e superiore, i campi dall'istruzione SELECT avranno le stesse maiuscole e minuscole come specificate nella query SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Altri modelli della funzione JavaScript definita dall'utente

### <a name="write-nested-json-to-output"></a>Scrivere una stringa JSON nidificata in output

In caso di passaggi di elaborazione successivi che utilizzano un output di un processo di Analisi di flusso come input e l'output richiede un formato JSON, è possibile scrivere una stringa JSON in output. L'esempio successivo chiama la funzione **JSON.stringify()** per raccogliere tutte le coppie nome/valore dell'input e quindi scriverle come valore di stringa singola nell'output.

**Definizione della funzione JavaScript definita dall'utente:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Query di esempio:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>eseguire il cast della stringa all'oggetto JSON da elaborare

Se è presente un campo stringa JSON e si vuole convertirlo in un oggetto JSON per l'elaborazione in una funzione definita dall'utente JavaScript, è possibile usare la funzione **JSON.parse()** per creare un oggetto JSON da usare a questo scopo.

**Definizione della funzione JavaScript definita dall'utente:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Query di esempio:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>usare try/catch per la gestione degli errori

I blocchi try/catch consentono di identificare i problemi relativi a dati di input in formato non valido passati in una funzione definita dall'utente JavaScript.

**Definizione della funzione JavaScript definita dall'utente:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Query di esempio: passare l'intero record come primo parametro in modo che possa essere restituito se si verifica un errore.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

## <a name="next-steps"></a>Passaggi successivi

* [Funzione definita dall'utente di Machine Learning](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [Funzione definita dall'utente in C#](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
