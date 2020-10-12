---
title: Generatore di espressioni nel flusso di dati di mapping
description: Compilazione di espressioni tramite Generatore di espressioni nel mapping di flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 4297cc83ab3fa280e15480aefcd5aef8734c65ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531058"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Espressioni di compilazione nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nel flusso di dati del mapping, molte proprietà di trasformazione vengono immesse come espressioni. Queste espressioni sono costituite da valori di colonna, parametri, funzioni, operatori e valori letterali che restituiscono un tipo di dati Spark in fase di esecuzione. Il mapping dei flussi di dati offre un'esperienza dedicata per facilitare la creazione di queste espressioni, denominato **Generatore di espressioni**. Utilizzando il completamento del codice  [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) per l'evidenziazione, il controllo della sintassi e il completamento automatico, il generatore di espressioni è progettato per semplificare la creazione di flussi di dati. In questo articolo viene illustrato come utilizzare il generatore di espressioni per compilare efficacemente la logica di business.

![Generatore di espressioni](media/data-flow/expresion-builder.png "Generatore di espressioni")

## <a name="open-expression-builder"></a>Apri generatore di espressioni

Per aprire il generatore di espressioni sono presenti più punti di ingresso. Tutti questi oggetti dipendono dal contesto specifico della trasformazione del flusso di dati. Il caso d'uso più comune è costituito da trasformazioni come [colonna derivata](data-flow-derived-column.md) e [aggregazioni](data-flow-aggregate.md) in cui gli utenti creano o aggiornano colonne utilizzando il linguaggio delle espressioni del flusso di dati. È possibile aprire il generatore di espressioni selezionando **Apri generatore di espressioni** sopra l'elenco di colonne. È anche possibile fare clic su un contesto di colonna e aprire il generatore di espressioni direttamente nell'espressione.

![Il generatore di espressioni Open deriva](media/data-flow/open-expression-builder-derive.png "Il generatore di espressioni Open deriva")

In alcune trasformazioni come [filtro](data-flow-filter.md), facendo clic su una casella di testo espressione blu, viene aperto il generatore di espressioni. 

![Casella espressione blu](media/data-flow/expressionbox.png "Generatore di espressioni")

Quando si fa riferimento a colonne in una condizione di corrispondenza o di raggruppamento, un'espressione può estrarre valori dalle colonne. Per creare un'espressione, selezionare **colonna calcolata**.

![Opzione della colonna calcolata](media/data-flow/computedcolumn.png "Generatore di espressioni")

Nei casi in cui un'espressione o un valore letterale sono input validi, selezionare **Aggiungi contenuto dinamico** per compilare un'espressione che restituisca un valore letterale.

![Opzione Aggiungi contenuto dinamico](media/data-flow/add-dynamic-content.png "Generatore di espressioni")

## <a name="expression-elements"></a>Elementi Expression

Nei flussi di dati di mapping, le espressioni possono essere composte da valori di colonna, parametri, funzioni, variabili locali, operatori e valori letterali. Queste espressioni devono restituire un tipo di dati Spark, ad esempio stringa, valore booleano o Integer.

![Elementi Expression](media/data-flow/expression-elements.png "Elementi Expression")

### <a name="functions"></a>Funzioni

Il mapping di flussi di dati include funzioni e operatori predefiniti che possono essere utilizzati nelle espressioni. Per un elenco delle funzioni disponibili, vedere [riferimento al linguaggio del flusso di dati di mapping](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Indici di matrici di indirizzi

Quando si gestiscono colonne o funzioni che restituiscono tipi di matrice, usare le parentesi quadre ([]) per accedere a un elemento specifico. Se l'indice non esiste, l'espressione restituisce NULL.

![Matrice generatore di espressioni](media/data-flow/expression-array.png "Anteprima dati espressione")

> [!IMPORTANT]
> Nei flussi di dati di mapping, le matrici sono basate su un valore che indica che il primo elemento viene usato come riferimento dall'indice uno. La matrice [1], ad esempio, accederà al primo elemento di una matrice denominata ' matrice '.

### <a name="input-schema"></a>Schema di input

Se nel flusso di dati viene utilizzato uno schema definito in una delle relative origini, è possibile fare riferimento a una colonna in base al nome in molte espressioni. Se si usa la deriva dello schema, è possibile fare riferimento alle colonne in modo esplicito usando le `byName()` `byNames()` funzioni o o trovare una corrispondenza usando i modelli di colonna.

#### <a name="column-names-with-special-characters"></a>Nomi di colonna con caratteri speciali

Quando sono presenti nomi di colonna che includono caratteri speciali o spazi, racchiudere il nome tra parentesi graffe per farvi riferimento in un'espressione.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parametri

I parametri sono valori passati in un flusso di dati in fase di esecuzione da una pipeline. Per fare riferimento a un parametro, fare clic sul parametro nella visualizzazione **elementi espressione** o farvi riferimento con un segno di dollaro davanti al nome. Ad esempio, viene fatto riferimento a un parametro denominato parametro1 `$parameter1` . Per altre informazioni, vedere [parametrizzazione mapping di flussi di dati](parameters-data-flow.md).

### <a name="locals"></a>Variabili locali

Se si condivide la logica tra più colonne o si vuole compartimenti la logica, è possibile creare un locale all'interno di un column\. derivato Per fare riferimento a un oggetto locale, fare clic sul local dalla visualizzazione **elementi espressione** o farvi riferimento con i due punti davanti al nome. Ad esempio, è possibile fare riferimento a un local1 locale chiamato da `:local1` . Ulteriori informazioni sulle variabili locali sono disponibili nella [documentazione della colonna derivata](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Risultati espressione anteprima

Se la [modalità di debug](concepts-data-flow-debug-mode.md) è attivata, è possibile usare in modo interattivo il cluster di debug per visualizzare l'anteprima dell'espressione a cui viene restituita l'espressione. Selezionare **Aggiorna** accanto a Anteprima dati per aggiornare i risultati dell'anteprima dei dati. È possibile visualizzare l'output di ogni riga specificando le colonne di input.

![Anteprima in corso](media/data-flow/preview-expression.png "Anteprima dati espressione")

## <a name="string-interpolation"></a>Interpolazione di stringhe

Quando si creano stringhe lunghe che usano elementi Expression, usare l'interpolazione di stringhe per creare facilmente una logica di stringa complessa. L'interpolazione di stringhe evita un uso estensivo della concatenazione di stringhe quando i parametri sono inclusi nelle stringhe di query. Utilizzare le virgolette doppie per racchiudere il testo di stringa letterale insieme alle espressioni. È possibile includere funzioni di espressione, colonne e parametri. Per utilizzare la sintassi delle espressioni, racchiuderla tra parentesi graffe,

Alcuni esempi di interpolazione di stringhe:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

## <a name="commenting-expressions"></a>Espressioni di commento

Aggiungere commenti alle espressioni usando la sintassi di commento a riga singola e a più righe.

Gli esempi seguenti sono commenti validi:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Se si inserisce un commento all'inizio dell'espressione, questo viene visualizzato nella casella di testo trasformazione per documentare le espressioni di trasformazione.

![Commento nella casella di testo trasformazione](media/data-flow/comment-expression.png "Commenti")

## <a name="regular-expressions"></a>Espressioni regolari

Molte funzioni del linguaggio delle espressioni usano la sintassi delle espressioni regolari. Quando si usano le funzioni di espressione regolare, il generatore di espressioni tenta di interpretare una barra rovesciata ( \\ ) come sequenza di caratteri di escape. Quando si usano le barre rovesciate nell'espressione regolare, racchiudere l'intera espressione regolare in apice inverso ( \` ) o usare una doppia barra rovesciata.

Esempio che usa i segni di inverso:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Esempio che usa le barre doppie:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

Di seguito è riportato un elenco di tasti di scelta rapida disponibili nel generatore di espressioni. La maggior parte dei collegamenti IntelliSense è disponibile durante la creazione di espressioni.

* CTRL + K CTRL + C: commento riga intera.
* CTRL + K CTRL + U: rimuovere il commento.
* F1: specificare i comandi della Guida dell'editor.
* ALT + tasto freccia giù: Sposta giù riga corrente.
* ALT + tasto freccia su: sposta su riga corrente.
* CTRL + barra spaziatrice: Mostra la guida del contesto.

## <a name="commonly-used-expressions"></a>Espressioni di uso comune

### <a name="convert-to-dates-or-timestamps"></a>Converti in date o timestamp

Per includere valori letterali stringa nell'output timestamp, eseguire il wrapping della conversione in ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Per convertire i millisecondi da Epoch a date o timestamp, usare `toTimestamp(<number of milliseconds>)` . Se il tempo è in secondi, moltiplicare per 1.000.

```toTimestamp(1574127407*1000l)```

La "l" finale alla fine dell'espressione precedente indica la conversione in un tipo long come sintassi inline.

### <a name="find-time-from-epoch-or-unix-time"></a>Trovare l'ora da Epoch o UNIX Time

ToLong (IndicatoreDataOraCorrente ()-totimestamp (' 1970-01-01 00:00:00.000',' AAAA-MM-GG HH: mm: SS. SSS ')) * 1000L

## <a name="next-steps"></a>Passaggi successivi

[Inizia a creare espressioni di trasformazione dei dati](data-flow-expression-functions.md)
