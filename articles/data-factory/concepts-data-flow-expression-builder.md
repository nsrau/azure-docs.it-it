---
title: Generatore di espressioni nel flusso di dati di mapping
description: Espressioni di compilazione che usano il generatore di espressioni nel mapping di flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969367"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Compilazione di espressioni nel flusso di dati di mapping

Nel flusso di dati del mapping, molte proprietà di trasformazione vengono immesse come espressioni. Queste espressioni sono costituite da valori di colonna, parametri, funzioni, operatori e valori letterali che restituiscono un tipo di dati Spark in fase di esecuzione.

## <a name="opening-the-expression-builder"></a>Apertura del generatore di espressioni

L'interfaccia di modifica delle espressioni nel data factory UX è noto come **Generatore di espressioni**. Quando si immette nella logica dell'espressione, data factory USA il completamento del codice [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) per l'evidenziazione, il controllo della sintassi e il completamento automatico.

![Generatore di espressioni](media/data-flow/xpb1.png "Generatore di espressioni")

Nelle trasformazioni, ad esempio la colonna derivata e il filtro, in cui le espressioni sono obbligatorie, aprire il generatore di espressioni facendo clic sulla casella espressione blu.

![Generatore di espressioni](media/data-flow/expressionbox.png "Generatore di espressioni")

Quando si fa riferimento a colonne in una condizione Group by o corrispondente, un'espressione può estrarre valori dalle colonne. Per creare un'espressione, selezionare l'opzione "colonna calcolata".

![Generatore di espressioni](media/data-flow/computedcolumn.png "Generatore di espressioni")

Nei casi in cui un'espressione o un valore letterale sono input validi,' Aggiungi contenuto dinamico ' consente di compilare un'espressione che restituisce un valore letterale.

![Generatore di espressioni](media/data-flow/add-dynamic-content.png "Generatore di espressioni")

## <a name="expression-language-reference"></a>Riferimento al linguaggio delle espressioni

Il mapping di flussi di dati include funzioni e operatori predefiniti che possono essere utilizzati nelle espressioni. Un elenco delle funzioni disponibili si trova nella pagina di riferimento del [linguaggio espressione flusso di dati mapping](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Nomi di colonna con caratteri speciali

Quando sono presenti nomi di colonna che includono caratteri speciali o spazi, racchiudere il nome tra parentesi graffe per farvi riferimento in un'espressione.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Visualizzazione in anteprima dei risultati delle espressioni

Se la [modalità di debug](concepts-data-flow-debug-mode.md) è attivata, è possibile usare il cluster Spark attivo per visualizzare un'anteprima in corso delle valutazioni dell'espressione. Quando si compila la logica, è possibile eseguire il debug dell'espressione in tempo reale. 

![Generatore di espressioni](media/data-flow/exp4b.png "Anteprima dati espressione")

Fare clic sul pulsante Aggiorna per aggiornare i risultati dell'espressione rispetto a un campione Live dell'origine.

![Generatore di espressioni](media/data-flow/exp5.png "Anteprima dati espressione")

## <a name="string-interpolation"></a>Interpolazione di stringhe

Utilizzare le virgolette doppie per racchiudere il testo di stringa letterale insieme alle espressioni. È possibile includere funzioni di espressione, colonne e parametri. L'interpolazione di stringhe è utile per evitare l'utilizzo estensivo della concatenazione di stringhe quando si includono parametri nelle stringhe di query. Per utilizzare la sintassi delle espressioni, racchiuderla tra parentesi graffe,

Alcuni esempi di interpolazione di stringhe:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Espressioni di commento

Aggiungere commenti per le espressioni che usano sintassi dei commenti su una riga singola e su righe multiple:

![Commenti](media/data-flow/comments.png "Commenti")

Di seguito sono riportati alcuni esempi di commenti validi:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se si inserisce un commento all'inizio dell'espressione, questo verrà visualizzato nella casella di testo trasformazione per documentare le espressioni di trasformazione:

![Commenti](media/data-flow/comments2.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Molte funzioni del linguaggio delle espressioni usano la sintassi delle espressioni regolari. Quando si utilizzano le funzioni di espressione regolare, il generatore di espressioni tenterà di interpretare la barra rovesciata (\\) come sequenza di caratteri di escape. Quando si usano le barre rovesciate nell'espressione regolare, racchiudere l'intera espressione regolare in cicli (\`) o usare una doppia barra rovesciata.

Esempio di utilizzo di tick

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

o della barra doppia

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Indirizzamento degli indici della matrice

Con funzioni per le espressioni che restituiscono matrici, usare le parentesi quadre [] per indicare indici specifici all'interno di tale oggetto di restituzione della matrice. La matrice è basata sulle unità.

![Matrice generatore di espressioni](media/data-flow/expb2.png "Anteprima dati espressione")

## <a name="keyboard-shortcuts"></a>Scelte rapide da tastiera

* ```Ctrl-K Ctrl-C```: intera riga Commenti
* ```Ctrl-K Ctrl-U```: rimuovere il commento
* ```F1```: specificare i comandi della Guida dell'editor
* ```Alt-Down Arrow```: Sposta giù riga corrente
* ```Alt-Up Arrow```: sposta su riga corrente
* ```Cntrl-Space```: Mostra la guida del contesto

## <a name="convert-to-dates-or-timestamps"></a>Converti in date o timestamp

Per includere valori letterali stringa nell'output timestamp, è necessario eseguire il wrapping della conversione in ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Per convertire i millisecondi da Epoch a date o timestamp, usare `toTimestamp(<number of milliseconds>)`. Se il tempo è in secondi, moltiplicare per 1000.

```toTimestamp(1574127407*1000l)```

La "l" finale alla fine dell'espressione precedente indica la conversione in un tipo long come sintassi inline.

## <a name="next-steps"></a>Passaggi successivi

[Inizia a creare espressioni di trasformazione dei dati](data-flow-expression-functions.md)
