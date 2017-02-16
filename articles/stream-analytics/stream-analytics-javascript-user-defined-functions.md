---
title: Funzioni JavaScript definite dall&quot;utente in Analisi di flusso di Azure| Microsoft Docs
description: Tag dei sensori IoT e flussi di dati con l&quot;elaborazione dei dati in tempo reale e l&quot;analisi di flusso
keywords: soluzione IoT, introduzione all&quot;IoT, strumenti
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
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure
L'Analisi di flusso di Azure supporta le funzioni definite dall'utente nel linguaggio JavaScript. Con la vasta gamma di metodi String, RegExp, Math, Array e Date offerti da JavaScript, risulta più facile creare trasformazioni di dati complessi con processo di Analisi di flusso.

## <a name="overview"></a>Panoramica
Le funzioni JavaScript definite dall'utente supportano funzioni senza stato e di solo calcolo che non richiedono connettività esterna. Il valore restituito di una funzione può essere solo un valore scalare singolo. Una volta aggiunta al processo, la funzione può essere usata ovunque nella query, come una funzione scalare predefinita.

Ecco alcuni scenari di esempio in cui risultano utili le funzioni JavaScript definite dall'utente:
* Analisi e manipolazione delle stringhe con funzioni di espressione regolare, ad esempio Regexp_Replace() e Regexp_Extract()
* Decodifica di una codifica dati, ad esempio la conversione dal formato binario al formato esadecimale
* Calcoli matematici con funzioni matematiche di JavaScript
* Operazioni di matrice come ordinamento, aggiunta, ricerca e riempimento

Di seguito sono elencate alcune operazioni non eseguibili con una funzione JavaScript definita dall'utente in Analisi di flusso:
* Chiamate agli endpoint REST esterni, ad esempio per la ricerca inversa degli indirizzi IP o per la raccolta di dati di riferimento da un'origine esterna
* Serializzazione o deserializzazione negli input e output di un formato evento personalizzato
* Aggregazioni personalizzate

Per quanto queste non vengano bloccate nella definizione delle funzioni, è consigliabile evitare di usare funzioni come Date.GetDate() o Math.random(). Queste funzioni, infatti, **non** restituiscono lo stesso risultato ogni volta che vengono chiamate e Analisi di flusso di Azure non conserva un giornale di chiamate di funzione e dei risultati restituiti. Pertanto, se una funzione restituisce risultati diversi per gli eventi stessi, non verrà garantita la ripetibilità in caso di processo riavviato dall'utente o dal servizio di Analisi di flusso.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Aggiunta di una funzione JavaScript definita dall'utente dal portale di Azure
I passaggi seguenti illustrano come creare una semplice funzione JavaScript definita dall'utente in un processo esistente di Analisi di flusso.

1.  Aprire il Portale di Azure.

2.  Individuare il processo di Analisi di flusso e fare clic sul collegamento alle funzioni in **TOPOLOGIA PROCESSO**.
 
3.  Verrà visualizzato un elenco vuoto di funzioni esistenti, fare clic sull'icona **Aggiungi** per aggiungere una nuova funzione definita dall'utente.

4.  Nel pannello **Nuova funzione** selezionare JavaScript come tipo di funzione; verrà visualizzato un modello di funzione predefinito nell'editor.
 
5.  Inserire _hex2Int_ come alias della funzione e modificarne l'implementazione come indicato di seguito

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Fare clic sul pulsante **Salva**; la funzione verrà visualizzata nell'elenco. 

7.  Fare clic sulla nuova funzione **hex2Int** per verificarne la definizione. Si noti che verrà aggiunto il prefisso "UDF" a tutti gli alias di funzione. Sarà necessario chiamare la funzione **con il prefisso** nella query di Analisi di flusso, in questo caso **UDF.hex2Int**.
 
## <a name="calling-javascript-udf-in-a-query"></a>Chiamata di una funzione JavaScript definita dall'utente in una query

1. Aprire l'editor di query facendo clic su **Query** in **TOPOLOGIA PROCESSO**. 

2.  Modificare la query e chiamare la funzione definita dall'utente appena aggiunta come indicato di seguito:

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Fare clic con il tasto destro del mouse sull'input del processo per caricare un file di dati di esempio 
 
4.  Fare clic su **Test** per testare la query.


## <a name="supported-javascript-objects"></a>Oggetti JavaScript supportati
La funzione JavaScript definita dall'utente in Analisi di flusso di Azure supporta gli oggetti predefiniti standard del linguaggio JavaScript. Per l'elenco di oggetti, fare riferimento al documento in [Oggetti globali](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversione dei tipi di Analisi di flusso e JavaScript

Vi sono differenze tra i tipi supportati nel linguaggio di query di Analisi di flusso e JavaScript. La tabella seguente elenca i mapping di conversione tra i due tipi.


---
Analisi dei flussi | JavaScript
--- | ---
bigint | Numero (per la precisione, JavaScript può rappresentare solo numeri interi fino a 2^53)
DateTime | Data (si noti che JavaScript supporta solo millisecondi) 
double | Number
nvarchar(MAX) | string
Record | Oggetto
Array | Array
NULL | Null


Sono elencate anche le conversioni da JavaScript ad Analisi di flusso di Azure.

---
JavaScript | Analisi dei flussi
--- | ---
Number | Bigint se il numero è arrotondato e compreso tra long.MinValue e long.MaxValue oppure doppio in caso contrario
Date | DateTime
String | nvarchar(MAX)
Oggetto | Record
Array | Array
Null, Undefined | NULL
qualsiasi altro tipo, ad esempio Function, Error e così via | Non supportato, errore di runtime

## <a name="troubleshooting"></a>Risoluzione dei problemi
Gli errori di runtime in JavaScript verranno considerati irreversibili ed esposti tramite il log attività. Per recuperare il log dal portale di Azure, passare al processo e fare clic su **Log attività**.
 

## <a name="other-javascript-udf-usage-patterns"></a>Altri modelli d'uso delle funzioni JavaScript definite dall'utente

### <a name="writing-nested-json-to-output"></a>Scrittura di una stringa JSON nidificata in output
La scrittura di una stringa JSON in output è un'attività comune quando si dispone di passaggi di elaborazione successivi che prendono l'output di un processo di Analisi di flusso come input. L'output richiede un formato JSON. Nell'esempio seguente viene chiamata la funzione JSON.stringify() per raccogliere tutte le coppie nome/valore dell'input e scriverle come valore di stringa singola nell'output. 

### <a name="javascript-udf-definition"></a>Definizione di una funzione JavaScript definita dall'utente:

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
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


