---
title: Funzioni JavaScript definite dall&quot;utente in Analisi di flusso di Azure | Microsoft Docs
description: Eseguire i meccanismi di query avanzate con funzioni JavaScript definite dall&quot;utente
keywords: javascript, funzioni definite dall&quot;utente, udf
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a8334d146877ccc3988784c7a74a1c887dba68ab
ms.openlocfilehash: 57027b97cebf8accccd91f135a13825047fd211e

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure
L'Analisi di flusso di Azure supporta le funzioni definite dall'utente nel linguaggio JavaScript. Con il vasto set di metodi **String**, **RegExp**, **Math**, **Array** e **Date** offerti da JavaScript, risulta più facile creare trasformazioni di dati complessi con processi di Analisi di flusso.

## <a name="javascript-user-defined-functions"></a>Funzioni JavaScript definite dall'utente
Le funzioni JavaScript definite dall'utente supportano funzioni senza stato e di solo calcolo che non richiedono connettività esterna. Il valore restituito di una funzione può essere solo un valore scalare singolo. Dopo aver aggiunto una funzione JavaScript definita dall'utente a un processo, è possibile utilizzare la funzione in un punto qualsiasi nella query, ad esempio una funzione scalare incorporata.

Ecco alcuni scenari in cui potrebbero essere utili le funzioni JavaScript definite dall'utente:
* Analisi e manipolazione delle stringhe con funzioni di espressione regolare, ad esempio **Regexp_Replace()** e **Regexp_Extract()**
* Decodifica e codifica dati, ad esempio conversione dal formato binario al formato esadecimale
* Esecuzione di calcoli matematici con funzioni JavaScript **Math**
* Esecuzione di operazioni di matrice come ordinamento, aggiunta, ricerca e riempimento

Ecco alcune operazioni non eseguibili con una funzione JavaScript definita dall'utente in Analisi di flusso:
* Chiamate agli endpoint REST esterni, ad esempio per l'esecuzione di una ricerca inversa degli indirizzi IP o per la raccolta di dati di riferimento da un'origine esterna
* Esecuzione della serializzazione o deserializzazione negli input e output di un formato evento personalizzato
* Creazione di aggregazioni personalizzate

Sebbene le funzioni come **Date.GetDate()** o **Math.random()** non sono bloccate nella definizione delle funzioni, è consigliabile evitare di utilizzarle. Queste funzioni, infatti, **non** restituiscono lo stesso risultato ogni volta che vengono chiamate e Analisi di flusso di Azure non conserva un giornale di chiamate di funzione e dei risultati restituiti. Se una funzione restituisce risultati diversi per gli stessi eventi, non viene garantita la ripetibilità in caso di processo riavviato dall'utente o dal servizio di Analisi di flusso.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Aggiungere una funzione JavaScript definita dall'utente nel portale di Azure
I passaggi seguenti illustrano come creare una semplice funzione JavaScript definita dall'utente in un processo esistente di Analisi di flusso:

1.    Nel portale di Azure individuare il processo di Analisi di flusso.
2.  In **TOPOLOGIA PROCESSO** selezionare la funzione. Viene visualizzato un elenco vuoto di funzioni.
3.    Per creare una nuova funzione definita dall'utente, selezionare **Aggiungi**.
4.    Sul pannello **Nuova funzione** selezionare **JavaScript** per **Tipo funzione**. Nell'editor viene visualizzato un modello di funzione predefinita.
5.    Per l'**alias della funzione definita dall'utente**, inserire **hex2Int** e modificare l'implementazione della funzione come indicato di seguito:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.    Selezionare **Salva**. La funzione viene visualizzata nell'elenco delle funzioni.
7.    Selezionare la nuova funzione **hex2Int** e controllare la definizione di funzione. Per ogni funzione, all'alias della funzione viene aggiunto un prefisso della **funzione definita dall'utente**. È necessario *includere il prefisso* quando si chiama la funzione nella query Analisi di flusso. In questo caso, si chiama **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chiamare una funzione JavaScript definita dall'utente nella query

1. Nell'editor di query, in **TOPOLOGIA PROCESSO**, selezionare **Query**.
2.    Modificare la query e quindi chiamare la funzione definita dall'utente, simile alla seguente:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.    Per caricare un file di dati di esempio, fare clic con il tasto destro del mouse sull'input del processo.
4.    Per testare la query, selezionare **Test**.


## <a name="supported-javascript-objects"></a>Oggetti JavaScript supportati
Le funzioni JavaScript definite dall'utente in Analisi di flusso di Azure supportano oggetti JavaScript standard e incorporati. Per un elenco di questi oggetti, vedere [Oggetti globali](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversione dei tipi di Analisi di flusso e JavaScript

Esistono delle differenze tra i tipi supportati da JavaScript e dal linguaggio di query di Analisi di flusso. Questa tabella elenca i mapping di conversione tra i due:

Analisi dei flussi | JavaScript
--- | ---
bigint | Numero (per la precisione, JavaScript può rappresentare solo numeri interi fino a 2^53)
DateTime | Data (JavaScript supporta solo millisecondi)
double | Number
nvarchar(MAX) | string
Record | Oggetto
Array | Array
NULL | Null


Ecco le conversioni da JavaScript ad Analisi di flusso:


JavaScript | Analisi dei flussi
--- | ---
Number | Bigint (se il numero è arrotondato e compreso tra long.MinValue e long.MaxValue, in caso contrario è doppio)
Date | DateTime
String | nvarchar(MAX)
Oggetto | Record
Array | Array
Null, Undefined | NULL
Qualsiasi altro tipo (ad esempio, una funzione o un errore) | Non supportato (risultati nell'errore di runtime)

## <a name="troubleshooting"></a>Risoluzione dei problemi
Gli errori di runtime in JavaScript sono considerati irreversibili ed esposti tramite il log attività. Per recuperare il log, nel portale di Azure passare al processo e selezionare **Log attività**.


## <a name="other-javascript-user-defined-function-patterns"></a>Altri modelli della funzione JavaScript definita dall'utente

### <a name="write-nested-json-to-output"></a>Scrivere una stringa JSON nidificata in output
In caso di passaggi di elaborazione successivi che utilizzano un output di un processo di Analisi di flusso come input e l'output richiede un formato JSON, è possibile scrivere una stringa JSON in output. L'esempio successivo chiama la funzione **JSON.stringify()** per raccogliere tutte le coppie nome/valore dell'input e quindi scriverle come valore di stringa singola nell'output.

**Definizione della funzione JavaScript definita dall'utente:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Query di esempio:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



<!--HONumber=Feb17_HO2-->


