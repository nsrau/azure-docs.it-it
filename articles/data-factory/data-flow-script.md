---
title: Mapping dello script del flusso di dati
description: Panoramica del linguaggio code-behind dello script del flusso di dati di Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: 251507c5740af69bd0818391dd2e8f857338b6cf
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313921"
---
# <a name="data-flow-script-dfs"></a>Script flusso di dati (DFS)

Lo script del flusso di dati (DFS) è il metadati sottostante, simile a un linguaggio di codifica, utilizzato per eseguire le trasformazioni incluse in un flusso di dati di mapping. Ogni trasformazione è rappresentata da una serie di proprietà che forniscono le informazioni necessarie per eseguire correttamente il processo. Lo script è visibile e modificabile da ADF facendo clic sul pulsante "script" nella barra multifunzione superiore dell'interfaccia utente del browser.

![Pulsante Script](media/data-flow/scriptbutton.png "Pulsante Script")

Ad esempio, `allowSchemaDrift: true,` in una trasformazione di origine indica al servizio di includere tutte le colonne del set di dati di origine nel flusso di dati anche se non sono incluse nella proiezione dello schema.

## <a name="use-cases"></a>Casi d'uso
Il DFS viene prodotto automaticamente dall'interfaccia utente. È possibile fare clic sul pulsante Script per visualizzare e personalizzare lo script. È inoltre possibile generare script all'esterno dell'interfaccia utente di ADF e quindi passarlo nel cmdlet PowerShell. Quando si esegue il debug di flussi di dati complessi, potrebbe risultare più semplice analizzare il code-behind dello script anziché eseguire la scansione della rappresentazione del grafico dell'interfaccia utente dei flussi.

Di seguito sono riportati alcuni casi d'uso di esempio:Here are a few example use cases:
- Produrre a livello di programmazione molti flussi di dati abbastanza simili, ovvero flussi di dati "timbratura".
- Espressioni complesse difficili da gestire nell'interfaccia utente o che causano problemi di convalida.
- Debug e migliore comprensione di vari errori restituiti durante l'esecuzione.

Quando si compila uno script del flusso di dati da usare con PowerShell o un'API, è necessario comprimere il testo formattato in un'unica riga. È possibile mantenere le schede e le nuove righe come caratteri di escape. Ma il testo deve essere formattato per adattarsi a una proprietà JSON. C'è un pulsante nell'interfaccia utente dell'editor di script nella parte inferiore che formatterà lo script come una singola riga per te.

![Pulsante Copia](media/data-flow/copybutton.png "Pulsante Copia")

## <a name="how-to-add-transforms"></a>Come aggiungere trasformazioni
L'aggiunta di trasformazioni richiede tre passaggi di base: aggiunta dei dati di trasformazione di base, reindirizzamento del flusso di input e quindi instradamento del flusso di output. Questo può essere visto più facile in un esempio.
Let's say we start with a simple source to sink data flow like the following:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se decidiamo di aggiungere una trasformazione derivazione, in primo luogo è necessario creare il `upperCaseTitle`testo di trasformazione di base, che ha un'espressione semplice per aggiungere una nuova colonna maiuscola chiamata :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Quindi, prendiamo il DFS esistente e aggiungiamo la trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

E ora reindirizziamo il flusso in entrata identificando quale trasformazione vogliamo che la nuova trasformazione venga dopo (in questo caso, `source1`) e copiando il nome del flusso nella nuova trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Infine identifichiamo la trasformazione che vogliamo venire dopo questa nuova `sink1`trasformazione e sostituiamo il suo flusso di input (in questo caso, ) con il nome del flusso di output della nostra nuova trasformazione:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Nozioni fondamentali su DFS
Il DFS è composto da una serie di trasformazioni connesse, tra cui origini, sink e vari altri che possono aggiungere nuove colonne, filtrare i dati, unire i dati e molto altro ancora. In genere, lo script con inizia con una o più origini seguite da molte trasformazioni e termina con uno o più sink.

Le fonti hanno tutte la stessa costruzione di base:
```
source(
  source properties
) ~> source_name
```

Ad esempio, una semplice origine con tre colonne (movieId, title, generi) sarebbe:For esempio, a simple source with three columns (movieId, title, genres) would be:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Tutte le trasformazioni diverse dalle origini hanno la stessa costruzione di base:All transformations other than sources have the same basic construction:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Ad esempio, una semplice trasformazione derivazione che accetta una colonna (titolo) e la sovrascrive con una versione maiuscola sarebbe la seguente:For example, a simple derive transformation that takes a column (title) and overwrites it with an uppercase version would be as follows:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E un lavandino senza schema sarebbe semplicemente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Frammenti di script

I frammenti di script sono codice condistibile di Script flusso di dati che è possibile usare per condividere tra flussi di dati. Questo video seguente illustra come usare i frammenti di script e usare Lo script del flusso di dati per copiare e incollare parti dello script dietro i grafici del flusso di dati:This video below talks about how to use script snippets and using Data Flow Script to copy and paste portions of the script behind your data flow graphs:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Statistiche di riepilogo aggregate
Aggiungere una trasformazione Aggregazione al flusso di dati denominata "SummaryStats" e quindi incollare questo codice seguente per la funzione di aggregazione nello script, sostituendo summaryStats esistente. Questo fornirà un modello generico per le statistiche di riepilogo del profilo dati.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
È inoltre possibile utilizzare l'esempio seguente per contare il numero di righe univoche e il numero di righe distinte nei dati. L'esempio seguente può essere incollato in un flusso di dati con la trasformazione Aggregazione denominata ValueDistAgg.The example below can be pasted into a data flow with Aggregate transformation called ValueDistAgg. In questo esempio viene utilizzata una colonna denominata "title". Assicurarsi di sostituire "title" con la colonna stringa nei dati che si desidera utilizzare per ottenere i conteggi dei valori.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Includere tutte le colonne in un'aggregazioneInclude all columns in an aggregate
Si tratta di un modello di aggregazione generico che illustra come mantenere le colonne rimanenti nei metadati di output quando si compilano aggregazioni. In questo caso, ```first()``` usiamo la funzione per scegliere il primo valore in ogni colonna il cui nome non è "film". Per usare questa opzione, creare una trasformazione Aggregate denominata DistinctRows e quindi incollarla nello script sopra lo script di aggregazione DistinctRows esistente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Creare l'impronta digitale dell'hash di rigaCreate row hash fingerprint 
Usare questo codice nello script del flusso di ```DWhash``` dati ```sha1``` per creare una nuova colonna derivata denominata che produce un hash di tre colonne.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare i flussi di dati iniziando con [l'articolo Panoramica dei flussi di datiExplore](concepts-data-flow-overview.md) Data Flows by starting with the data flows overview article
