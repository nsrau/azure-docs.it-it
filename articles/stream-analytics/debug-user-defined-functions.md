---
title: Eseguire il debug di funzioni definite dall'utente in analisi di flusso di Azure
description: Questo articolo descrive come eseguire il debug di funzioni definite dall'utente in analisi di flusso di Azure.
author: jenssuessmeyer
ms.author: jenss
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 567def7abaff3cd8d70eb56f0aa117d1eeb52a13
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300429"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Eseguire il debug di funzioni definite dall'utente in analisi di flusso di Azure 

Quando le funzioni definite dall'utente (UDF) non funzionano come previsto, è necessario eseguirne il debug per individuare il problema. È possibile eseguire il debug di funzioni definite dall'utente per i processi di analisi di flusso quando si eseguono i processi localmente usando [Visual Studio Code](visual-studio-code-local-run-live-input.md) o [Visual Studio](stream-analytics-vs-tools-local-run.md).

Quando si esegue il processo su un flusso di input Live localmente, viene simulata solo l'esecuzione del motore di analisi di flusso di Azure cloud in un nodo. I test locali dei dati Live non possono sostituire i test di prestazioni e scalabilità eseguiti nel cloud, ma si risparmia tempo durante i test funzionali senza dover inviare il processo al cloud ogni volta che si vuole eseguire il test. Inoltre, i criteri temporali vengono disabilitati per l'esecuzione locale con dati locali o di esempio, ma i criteri temporali sono supportati per il testing dei dati Live.

## <a name="pick-your-language"></a>Scegli la tua lingua

È possibile scrivere funzioni definite dall'utente per analisi di flusso di Azure tramite .NET (C#) o JavaScript. 

### <a name="functions-in-c"></a>Funzioni in C # 

Quando si [scrivono funzioni definite dall'utente .NET con Visual Studio](stream-analytics-edge-csharp-udf-methods.md), si ottiene lo stesso livello di supporto usato per qualsiasi progetto di classe .NET. Questo supporto include:

* Supporto della compilazione, ad esempio il controllo della sintassi e il supporto del compilatore.

* Possibilità di aggiungere, compilare e fare riferimento a un progetto C# ed elementi nella soluzione di analisi di flusso. 

* Facile riutilizzo del codice incapsulato in un progetto condivisibile. 

* Eseguire il debug del supporto direttamente in Visual Studio. Impostare il progetto di analisi di flusso come avviare il progetto e impostare i punti di interruzione nel codice C#. Premere quindi **F5** per eseguire il debug del codice c# come per qualsiasi altro progetto c#. 

### <a name="functions-in-javascript"></a>Funzioni in JavaScript

JavaScript è un'altra opzione per la creazione di funzioni in analisi di flusso. Il codice JavaScript viene inserito direttamente nell'area funzione del progetto di analisi di flusso, che rende più difficile la condivisione tra più progetti.

La compilazione si verifica quando il progetto di analisi di flusso viene compilato o eseguito. La possibilità di trovare un problema solo in fase di esecuzione è superiore. Non è disponibile alcun supporto per il debug di funzioni JavaScript direttamente in analisi di flusso.

## <a name="debug-options-for-javascript"></a>Opzioni di debug per JavaScript

Poiché non è disponibile alcun supporto per il debug delle funzioni JavaScript in analisi di flusso direttamente, è possibile eseguire il debug incapsulando la funzione in un sito HTML e ottenendo l'output da tale posizione.

Nell'esempio seguente viene illustrato come eseguire il debug di funzioni definite dall'utente JavaScript con alcune limitazioni in un ambiente di runtime integrato in [Visual Studio Code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi che nel progetto di analisi di flusso di Azure siano presenti gli elementi seguenti:

* Input 
* Un output 
* Una query di analisi di flusso (con estensione asaql) 
* Configurazione di un processo di analisi di flusso (JobConfig.json)
* Una funzione definita dall'utente JavaScript

### <a name="prepare-files"></a>Preparare i file

Nell'immagine seguente il file di query con *estensione asaql* include solo la chiamata alla funzione definita dall'utente, *fxcharCount*. Questa modifica garantisce che sia ancora possibile compilare il progetto dopo che sono state apportate modifiche.

> [!div class="mx-imgBorder"]
> ![File di query di test di analisi di flusso](./media/debug-user-defined-functions/asaql-file.png)

Creare una cartella aggiuntiva nei **test** per ospitare il file di test, che viene chiamato per eseguire il test con la funzione JavaScript. In questo esempio, il nome della cartella è *fxcharCount* e il nome del test viene *Test_UDF.js*. 

L'immagine seguente mostra il codice nel file di test, che carica il file di funzione ed esegue la funzione. Questo esempio è semplice, ma è possibile caricare altri file di dati di test ed eseguire un ciclo attraverso altri test per ottenere l'output. La notazione della chiamata di funzione è leggermente diversa dalle chiamate comuni perché si fa riferimento al file e non viene caricato nel runtime, rendendo possibile il debug. 

> [!div class="mx-imgBorder"]
> ![File di test di analisi di flusso](./media/debug-user-defined-functions/test-file.png)

Nella funzione aggiungere le righe di codice seguenti al file per esporre i metodi. Non influiscono sulla possibilità di compilare il codice in Visual Studio Code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![UDF JavaScript di analisi di flusso](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Installare il supporto per il debug

Per eseguire il debug, è necessario [scaricare](https://nodejs.org/en/download/) e installare **node.js**. Installare la versione corretta in base alla piattaforma in uso. Al termine dell'installazione del runtime di node.js, riavviare Visual Studio Code per implementare le modifiche. 

Selezionare **Esegui e debug** o premere **CTRL + MAIUSC + D** per avviare il debug. Viene visualizzata una casella combinata in cui è possibile selezionare **node.js** come runtime. Se è stato installato solo node.js, viene usato per impostazione predefinita. Si dovrebbe essere in grado di eseguire il codice e nel file satellite se necessario con F11. 

> [!div class="mx-imgBorder"]
> ![Esecuzione e debug UDF di analisi di flusso](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Eseguire il debug di funzioni di aggregazione definite dall'utente 

Per eseguire il debug di funzioni di aggregazione definite dall'utente (UDA), è possibile usare il metodo debug per UDF JavaScript. In questo esempio, un'aggregazione definita dall'utente viene aggiunta al file di query con *estensione asaql* e al file di test.

Come per la funzione definita dall'utente, è necessario includere una chiamata a UDA per garantire che il progetto venga compilato dopo che sono state apportate modifiche. 

> [!div class="mx-imgBorder"]
> ![Aggiungere Uda a asaql](./media/debug-user-defined-functions/asaql-uda.png)

Nel file di *Test_UDA.js* si fa riferimento al file Uda come è stato fatto con la funzione definita dall'utente. Inoltre, è possibile chiamare `main()` , `init()` e `accumulate()` . Il `accumulate()` metodo viene chiamato in un ciclo per inserire i valori nello stack di stato. Il `computeresult()` metodo viene chiamato per comporre la query finale. 

> [!div class="mx-imgBorder"]
> ![File di test UDA](./media/debug-user-defined-functions/uda-test.png)

Come nell'esempio della funzione definita dall'utente, è necessario aggiungere parte del codice all'UDA stessa per esporre i metodi pertinenti.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Codice aggiunto ad UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Selezionare **Esegui e debug** o premere **CTRL + MAIUSC + D** per avviare il debug. Viene visualizzata una casella combinata in cui è possibile selezionare **node.js** come runtime. Se è stato installato solo node.js, viene usato per impostazione predefinita. Si dovrebbe essere in grado di eseguire il codice e nel file satellite se necessario con F11.

> [!div class="mx-imgBorder"]
> ![Esecuzione e debug di analisi di flusso Uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Passaggi successivi

* [Sviluppare ed eseguire il debug di processi di analisi di flusso di Azure localmente](develop-locally.md)
* [Eseguire il debug di query di analisi di flusso di Azure localmente usando il diagramma processi in Visual Studio](debug-locally-using-job-diagram.md)
* [Funzioni definite dall'utente in analisi di flusso di Azure](functions-overview.md)
 
