---
title: Generatore di espressioni nel flusso di dati di mapping
description: Compilazione di espressioni tramite Generatore di espressioni nel mapping di flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605371"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Espressioni di compilazione nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nel flusso di dati del mapping, molte proprietà di trasformazione vengono immesse come espressioni. Queste espressioni sono costituite da valori di colonna, parametri, funzioni, operatori e valori letterali che restituiscono un tipo di dati Spark in fase di esecuzione.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Apri generatore di espressioni

L'interfaccia di modifica delle espressioni nell'esperienza utente Azure Data Factory è nota come generatore di espressioni. Quando si immette la logica dell'espressione, Data Factory usa il completamento del codice [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) per l'evidenziazione, il controllo della sintassi e il completamento automatico.

![Generatore di espressioni](media/data-flow/xpb1.png "Generatore di espressioni")

Nelle trasformazioni, ad esempio la colonna derivata e il filtro, in cui le espressioni sono obbligatorie, aprire Generatore di espressioni selezionando la casella espressione blu.

![Casella espressione blu](media/data-flow/expressionbox.png "Generatore di espressioni")

Quando si fa riferimento a colonne in una condizione di corrispondenza o di raggruppamento, un'espressione può estrarre valori dalle colonne. Per creare un'espressione, selezionare **colonna calcolata**.

![Opzione della colonna calcolata](media/data-flow/computedcolumn.png "Generatore di espressioni")

Nei casi in cui un'espressione o un valore letterale sono input validi, selezionare **Aggiungi contenuto dinamico** per compilare un'espressione che restituisca un valore letterale.

![Opzione Aggiungi contenuto dinamico](media/data-flow/add-dynamic-content.png "Generatore di espressioni")

## <a name="expression-language-reference"></a>Riferimento al linguaggio delle espressioni

Il mapping di flussi di dati include funzioni e operatori predefiniti che possono essere utilizzati nelle espressioni. Per un elenco delle funzioni disponibili, vedere [funzioni di espressione nel flusso di dati di mapping](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nomi di colonna con caratteri speciali

Quando sono presenti nomi di colonna che includono caratteri speciali o spazi, racchiudere il nome tra parentesi graffe per farvi riferimento in un'espressione.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Risultati espressione anteprima

Se la [modalità di debug](concepts-data-flow-debug-mode.md) è attivata, è possibile usare il cluster Spark in tempo reale per visualizzare un'anteprima in corso della valutazione dell'espressione. Quando si compila la logica, è possibile eseguire il debug dell'espressione in tempo reale. 

![Anteprima in corso](media/data-flow/exp4b.png "Anteprima dati espressione")

Selezionare **Aggiorna** per aggiornare i risultati dell'espressione rispetto a un campione Live dell'origine.

![Pulsante Aggiorna](media/data-flow/exp5.png "Anteprima dati espressione")

## <a name="string-interpolation"></a>Interpolazione di stringhe

Utilizzare le virgolette per racchiudere il testo di stringa letterale insieme alle espressioni. È possibile includere funzioni di espressione, colonne e parametri. L'interpolazione di stringhe è utile per evitare l'utilizzo estensivo della concatenazione di stringhe quando i parametri sono inclusi nelle stringhe di query. Per utilizzare la sintassi delle espressioni, racchiuderla tra parentesi graffe,

Alcuni esempi di interpolazione di stringhe:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Espressioni di commento

Aggiungere commenti alle espressioni usando la sintassi di commento a riga singola e a più righe.

![Sintassi per commenti a riga singola e a più righe](media/data-flow/comments.png "Commenti")

Gli esempi seguenti sono commenti validi:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se si inserisce un commento all'inizio dell'espressione, questo viene visualizzato nella casella di testo trasformazione per documentare le espressioni di trasformazione.

![Commento nella casella di testo trasformazione](media/data-flow/comments2.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Molte funzioni del linguaggio delle espressioni usano la sintassi delle espressioni regolari. Quando si usano le funzioni di espressione regolare, il generatore di espressioni tenta di\\interpretare una barra rovesciata () come sequenza di caratteri di escape. Quando si usano le barre rovesciate nell'espressione regolare, racchiudere l'intera espressione regolare in apice inverso (\`) o usare una doppia barra rovesciata.

Esempio che usa i segni di inverso:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Esempio che usa le barre doppie:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indici di matrici di indirizzi

Con le funzioni di espressione che restituiscono matrici, usare le parentesi quadre ([]) per indirizzare gli indici specifici all'interno di che restituiscono oggetti matrice. La matrice è basata su quelle.

![Matrice generatore di espressioni](media/data-flow/expb2.png "Anteprima dati espressione")

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

* CTRL + K CTRL + C: commento riga intera.
* CTRL + K CTRL + U: rimuovere il commento.
* F1: specificare i comandi della Guida dell'editor.
* ALT + tasto freccia giù: Sposta giù riga corrente.
* ALT + tasto freccia su: sposta su riga corrente.
* CTRL + barra spaziatrice: Mostra la guida del contesto.

## <a name="convert-to-dates-or-timestamps"></a>Converti in date o timestamp

Per includere valori letterali stringa nell'output timestamp, eseguire il wrapping della ```toString()```conversione in.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Per convertire i millisecondi da Epoch a date o timestamp, usare `toTimestamp(<number of milliseconds>)`. Se il tempo è in secondi, moltiplicare per 1.000.

```toTimestamp(1574127407*1000l)```

La "l" finale alla fine dell'espressione precedente indica la conversione in un tipo long come sintassi inline.

## <a name="next-steps"></a>Passaggi successivi

[Inizia a creare espressioni di trasformazione dei dati](data-flow-expression-functions.md)
