---
title: Aggregazioni JavaScript definite dall'utente in Analisi di flusso di Azure | Microsoft Docs
description: Eseguire i meccanismi di query avanzate con aggregazioni JavaScript definite dall'utente
keywords: javascript, aggregazioni definite dall'utente, uda
services: stream-analytics
author: minhe-msft
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/28/2017
ms.author: minhe
ms.openlocfilehash: b3863a34ed146e54c6d60e035957b942a1976ff9
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Aggregazioni JavaScript definite dall'utente in Analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta le aggregazioni definite dall'utente (UDA) scritte in JavaScript e ciò consente di implementare la logica di business con stato complessa. Le aggregazioni definite dall'utente garantiscono il controllo completo su strutture di dati sullo stato, accumulo di stati, decumulazione di stati e calcolo dei risultati dell'aggregazione. Nell'articolo sono descritte le due diverse interfacce JavaScript per aggregazioni definite dall'utente, le procedure per creare un'aggregazione definita dall'utente e come utilizzare questo tipo di aggregazione con operazioni basate su finestre temporali in query di Analisi di flusso di Azure.

## <a name="javascript-user-defined-aggregates"></a>aggregazioni JavaScript definite dall'utente

Un'aggregazione definita dall'utente viene usata su una finestra temporale specifica per aggregare gli eventi in tale finestra e generare un unico valore di risultato. Esistono due tipi di interfacce per l'aggregazione definita dall'utente attualmente supportati in Analisi di flusso di Azure, ovvero AccumulateOnly e AccumulateDeaccumulate. Entrambi i tipi di aggregazione definita dall'utente possono essere usati da Finestra a cascata, Finestra di salto e Finestra temporale scorrevole. L'aggregazione definita dall'utente di tipo AccumulateDeaccumulate ha prestazioni migliori rispetto a quella di tipo AccumulateOnly quando viene usata assieme a Finestra di salto e Finestra temporale scorrevole. Scegliere uno dei due tipi in base all'algoritmo usato.

### <a name="accumulateonly-aggregates"></a>Aggregazioni di tipo AccumulateOnly

Le aggregazioni di tipo AccumulateOnly possono solo accumulare nuovi eventi in base al relativo stato. L'algoritmo non consente la decumulazione dei valori. Scegliere questo tipo di aggregazione quando risulta impossibile implementare la decumulazione delle informazioni di un evento dal valore di stato. Di seguito è illustrato il modello JavaScript per le aggregazioni di tipo AccumulatOnly:

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### <a name="accumulatedeaccumulate-aggregates"></a>Aggregazioni di tipo AccumulateDeaccumulate

Le aggregazioni di tipo AccumulateDeaccumulate consentono la decumulazione di un valore precedentemente accumulato dallo stato, ad esempio la rimozione di una coppia chiave-valore da un elenco di valori di evento o la sottrazione di un valore da uno stato di somma di aggregazioni. Di seguito è illustrato il modello JavaScript per le aggregazioni di tipo AccumulateDeaccumulate:

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## <a name="uda---javascript-function-declaration"></a>Aggregazione definita dall'utente - Dichiarazione della funzione JavaScript

Ogni aggregazione JavaScript definita dall'utente viene definita dalla dichiarazione di un oggetto Function. Di seguito sono descritti i principali elementi di una definizione di aggregazione definita dall'utente.

### <a name="function-alias"></a>Alias di funzione

L'alias di funzione rappresenta l'identificatore dell'aggregazione definita dall'utente. Quando viene chiamato in una query di Analisi di flusso di Azure, usare sempre l'alias dell'aggregazione definita dall'utente con un prefisso "uda" .

### <a name="function-type"></a>Tipo di funzione

Per l'aggregazione definita dall'utente, il tipo di funzione deve essere **UDA JavaScript**.

### <a name="output-type"></a>Tipo di output

Tipo specifico supportato dal processo di Analisi di flusso di Azure oppure "Any" se si desidera gestire il tipo nella query.

### <a name="function-name"></a>Nome della funzione

Nome dell'oggetto Function corrente. Il nome della funzione deve corrispondere esattamente all'alias dell'aggregazione definita dall'utente (anticipazione: si sta valutando la possibilità di supporto della funzione anonima in GA).

### <a name="method---init"></a>Metodo init()

Il metodo init() inizializza lo stato dell'aggregazione. Questo metodo viene chiamato all'inizio della finestra temporale.

### <a name="method--accumulate"></a>Metodo accumulate()

Il metodo accumulate() calcola lo stato dell'aggregazione definita dall'utente in base allo stato precedente e ai valori di evento correnti. Questo metodo viene chiamato quando l'evento entra in una finestra temporale (TUMBLINGWINDOW, HOPPINGWINDOW o SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metodo deaccumulate()

Il metodo deaccumulate() ricalcola lo stato in base allo stato precedente e ai valori di evento correnti. Questo metodo viene chiamato quando un evento abbandona una finestra di tipo SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metodo deaccumulateState()

Il metodo deaccumulateState() ricalcola lo stato in base allo stato precedente e allo stato di un hop. Questo metodo viene chiamato quando un set di eventi abbandona una finestra di tipo HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metodo computeResult()

Il metodo computeResult() restituisce il risultato dell'aggregazione in base allo stato corrente. Questo metodo viene chiamato alla fine di una finestra temporale (TUMBLINGWINDOW, HOPPINGWINDOW e SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Tipi di dati di input e output supportati dall'aggregazione JavaScript definita dall'utente
Per i tipi di dati delle aggregazioni JavaScript definite dall'utente, vedere la sezione **Conversione dei tipi di Analisi di flusso di Azure e JavaScript** in [Integrare funzioni UDF di JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Aggiunta di un'aggregazione JavaScript definita dall'utente dal portale di Azure

Di seguito viene descritto il processo di creazione di un'aggregazione definita dall'utente dal portale. In questo esempio vengono calcolate caso è calcolare le medie ponderate nel tempo.

A questo punto viene creata un'aggregazione JavaScript definita dall'utente in un processo ASA esistente eseguendo i passaggi seguenti.

1. Accedere al portale di Azure e individuare il processo di Analisi di flusso di Azure esistente.
1. Fare clic sul collegamento alle funzioni in **PROCESSO TOPOLOGIA**.
1. Fare clic sull'icona **Aggiungi** per aggiungere una nuova funzione.
1. Nella visualizzazione Nuova funzione selezionare **UDA JavaScript** nel campo Tipo di funzione. Nell'editor verrà visualizzato un modello UDA predefinito.
1. Immettere "TWA" come alias dell'aggregazione definita dall'utente e modificare l'implementazione della funzione come indicato di seguito:

    ````JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ````

1. Dopo aver fatto clic sul pulsante "Salva", l'aggregazione definita dall'utente verrà visualizzata nell'elenco di funzioni.

1. Fare clic sulla nuova funzione "TWA" per verificare la definizione della funzione.

## <a name="calling-javascript-uda-in-asa-query"></a>Chiamata di un'aggregazione JavaScript definita dall'utente in una query ASA

Nel portale di Azure aprire il processo, modificare la query e chiamare la funzione TWA() con un prefisso obbligatorio "uda", ad esempio:

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## <a name="testing-query-with-uda"></a>Test della query mediante un'aggregazione definita dall'utente

Creare un file JSON locale con il contenuto riportato di seguito, caricare il file nel processo di Analisi di flusso di Azure ed eseguire il test della query illustrata in precedenza.

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
