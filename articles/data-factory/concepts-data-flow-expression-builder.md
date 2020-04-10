---
title: Generatore di espressioni nel mapping del flusso di dati
description: Creare espressioni usando il Generatore di espressioni nel mapping dei flussi di dati in Azure Data FactoryBuild expressions by using Expression Builder in mapping data flows in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991706"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Creare espressioni nel mapping del flusso di datiBuild expressions in mapping data flow

Nel mapping del flusso di dati, molte proprietà di trasformazione vengono immesse come espressioni. Queste espressioni sono costituite da valori di colonna, parametri, funzioni, operatori e valori letterali che restituiscono un tipo di dati Spark in fase di esecuzione.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Apri Generatore di espressioni

L'interfaccia di modifica delle espressioni nell'esperienza utente di Azure Data Factory è nota come Generatore di espressioni. Quando si immette la logica dell'espressione, Data Factory usa il completamento del codice [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) per l'evidenziazione, il controllo della sintassi e il completamento automatico.

![Generatore di espressioni](media/data-flow/xpb1.png "Generatore di espressioni")

Nelle trasformazioni, ad esempio la colonna derivata e il filtro, in cui le espressioni sono obbligatorie, aprire Generatore di espressioni selezionando la casella di espressione blu.

![Casella di espressione blu](media/data-flow/expressionbox.png "Generatore di espressioni")

Quando si fa riferimento a colonne in una condizione corrispondente o di raggruppamento, un'espressione può estrarre valori dalle colonne. Per creare un'espressione, selezionare **Colonna calcolata**.

![Opzione Colonna calcolata](media/data-flow/computedcolumn.png "Generatore di espressioni")

Nei casi in cui un'espressione o un valore letterale sono input validi, selezionare **Aggiungi contenuto dinamico** per compilare un'espressione che restituisce un valore letterale.

![Opzione Aggiungi contenuto dinamico](media/data-flow/add-dynamic-content.png "Generatore di espressioni")

## <a name="expression-language-reference"></a>Informazioni di riferimento sul linguaggio delle espressioniExpression language reference

Il mapping dei flussi di dati include funzioni e operatori incorporati che possono essere utilizzati nelle espressioni. Per un elenco delle funzioni disponibili, vedere [Funzioni di espressione nel flusso di dati di mapping.](data-flow-expression-functions.md)

## <a name="column-names-with-special-characters"></a>Nomi di colonna con caratteri speciali

Quando si dispone di nomi di colonna che includono caratteri speciali o spazi, racchiudere il nome tra parentesi graffe per farvi riferimento in un'espressione.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Visualizzare in anteprima i risultati delle espressioni

Se la [modalità](concepts-data-flow-debug-mode.md) di debug è attivata, è possibile usare il cluster Spark attivo per visualizzare un'anteprima in corso di ciò che l'espressione restituisce. Durante la compilazione della logica, è possibile eseguire il debug dell'espressione in tempo reale. 

![Anteprima in corso](media/data-flow/exp4b.png "Anteprima dati espressione")

Selezionare **Aggiorna** per aggiornare i risultati dell'espressione rispetto a un campione attivo dell'origine.

![Pulsante Aggiorna](media/data-flow/exp5.png "Anteprima dati espressione")

## <a name="string-interpolation"></a>Interpolazione di stringhe

Utilizzare le virgolette per racchiudere il testo di una stringa letterale con le espressioni. È possibile includere funzioni di espressione, colonne e parametri. L'interpolazione di stringhe è utile per evitare un uso esteso della concatenazione di stringhe quando i parametri vengono inclusi nelle stringhe di query. Per utilizzare la sintassi dell'espressione, racchiuderla tra parentesi graffe,

Alcuni esempi di interpolazione di stringhe:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Espressioni di commento

Aggiungere commenti alle espressioni utilizzando la sintassi dei commenti a riga singola e su più righe.

![Sintassi dei commenti su riga singola e su più righe](media/data-flow/comments.png "Commenti")

I seguenti esempi sono commenti validi:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Se si inserisce un commento nella parte superiore dell'espressione, questo viene visualizzato nella casella di testo della trasformazione per documentare le espressioni di trasformazione.

![Commento nella casella di testo trasformazione](media/data-flow/comments2.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Molte funzioni del linguaggio delle espressioni utilizzano la sintassi delle espressioni regolari. Quando si utilizzano funzioni di espressione regolare,\\il Generatore di espressioni tenta di interpretare una barra rovesciata ( ) come sequenza di caratteri di escape. Quando si utilizzano le barre rovesciate nell'espressione regolare,\`racchiudere l'intera espressione regolare tra le barre rovesciate ( ) o utilizzare una doppia barra rovesciata.

Un esempio che usa le zecche a traverso:An example that uses backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Un esempio che usa le barre doppie:An example that uses double lashes:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indici della matrice di indirizziAddress array indexes

Con funzioni di espressione che restituiscono matrici, utilizzare le parentesi quadre ([]) per indirizzare indici specifici all'interno che restituiscono oggetti di matrice. L'array è basato su quelli.

![Matrice del Generatore di espressioni](media/data-flow/expb2.png "Anteprima dati espressione")

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

* Ctrl-K Ctrl-C: Commenta l'intera riga.
* Ctrl K Ctrl-U: Rimuovi commento.
* F1: Fornire i comandi della Guida dell'editor.
* Alt : freccia giù: consente di spostarsi verso il basso nella riga corrente.
* Alt: freccia su: consente di spostarsi verso l'alto nella riga corrente.
* Ctrl-Barra spaziatrice: consente di visualizzare la guida contestuale.

## <a name="convert-to-dates-or-timestamps"></a>Convertire in date o indicatori data e ora

Per includere valori letterali stringa nell'output ```toString()```timestamp, eseguire il wrapping della conversione in .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Per convertire millisecondi da epoca a una `toTimestamp(<number of milliseconds>)`data o a un timestamp, utilizzare . Se il tempo arriva in secondi, moltiplica per 1.000.

```toTimestamp(1574127407*1000l)```

La "l" finale alla fine dell'espressione precedente indica la conversione in un tipo long come sintassi inline.

## <a name="next-steps"></a>Passaggi successivi

[Iniziare a creare espressioni di trasformazione dei dati](data-flow-expression-functions.md)
