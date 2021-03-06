---
title: Script del flusso di dati di mapping
description: Panoramica del linguaggio code-behind degli script del flusso di dati Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 69cc835b37d2405e15638d85309dc89d51c6d043
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "93360276"
---
# <a name="data-flow-script-dfs"></a>Script del flusso di dati (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Lo script del flusso di dati (DFS) è costituito dai metadati sottostanti, analogamente a un linguaggio di codifica, utilizzato per eseguire le trasformazioni incluse in un flusso di dati di mapping. Ogni trasformazione è rappresentata da una serie di proprietà che forniscono le informazioni necessarie per eseguire correttamente il processo. Lo script è visibile e modificabile da ADF facendo clic sul pulsante "script" nella barra multifunzione superiore dell'interfaccia utente del browser.

![Pulsante script](media/data-flow/scriptbutton.png "Pulsante script")

Ad esempio, `allowSchemaDrift: true,` in una trasformazione origine indica al servizio di includere tutte le colonne del set di dati di origine nel flusso di dati anche se non sono incluse nella proiezione dello schema.

## <a name="use-cases"></a>Casi d'uso
Il DFS viene prodotto automaticamente dall'interfaccia utente. È possibile fare clic sul pulsante script per visualizzare e personalizzare lo script. È anche possibile generare script all'esterno dell'interfaccia utente di ADF, quindi passarli al cmdlet di PowerShell. Quando si esegue il debug di flussi di dati complessi, può risultare più semplice analizzare il code-behind dello script anziché analizzare la rappresentazione grafica dei flussi dell'interfaccia utente.

Di seguito sono riportati alcuni casi d'uso di esempio:
- A livello produce molti flussi di dati abbastanza simili, ad esempio i flussi di dati di "contrassegno".
- Espressioni complesse difficili da gestire nell'interfaccia utente o che causano problemi di convalida.
- Debug e migliore comprensione di diversi errori restituiti durante l'esecuzione.

Quando si compila uno script del flusso di dati da usare con PowerShell o un'API, è necessario comprimere il testo formattato in una sola riga. È possibile tenere sotto controllo le schede e le nuove righe come caratteri di escape. Ma il testo deve essere formattato per adattarsi a una proprietà JSON. È presente un pulsante nell'interfaccia utente dell'editor di script nella parte inferiore che formatterà lo script come una singola riga.

![Pulsante Copia](media/data-flow/copybutton.png "Pulsante Copia")

## <a name="how-to-add-transforms"></a>Come aggiungere trasformazioni
Per aggiungere trasformazioni sono necessari tre passaggi di base: aggiungere i dati di trasformazione principali, reindirizzare il flusso di input e quindi reindirizzare il flusso di output. Questa operazione può essere considerata più semplice in un esempio.
Si inizierà con una semplice origine per eseguire il sink di flusso di dati come il seguente:

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

Se si decide di aggiungere una trasformazione derivazione, è necessario innanzitutto creare il testo della trasformazione principale, che include un'espressione semplice per aggiungere una nuova colonna maiuscola denominata `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Quindi, si accetta il DFS esistente e si aggiunge la trasformazione:
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

A questo punto, il flusso in ingresso viene reindirizzato identificando la trasformazione che si desidera venga successiva alla nuova trasformazione, in questo caso, `source1` e copiando il nome del flusso nella nuova trasformazione:
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

Infine, identifichiamo la trasformazione che desideriamo dopo la nuova trasformazione e sostituisco il relativo flusso di input (in questo caso `sink1` ) con il nome del flusso di output della nuova trasformazione:
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
Il DFS è costituito da una serie di trasformazioni connesse, tra cui origini, sink e diverse altre, che possono aggiungere nuove colonne, filtrare dati, unire dati e molto altro ancora. In genere, lo script inizia con una o più origini seguite da numerose trasformazioni e termina con uno o più sink.

Le origini hanno tutti la stessa costruzione di base:
```
source(
  source properties
) ~> source_name
```

Ad esempio, un'origine semplice con tre colonne (movieId, title, genres) sarà:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Tutte le trasformazioni diverse dalle origini hanno la stessa costruzione di base:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Ad esempio, una semplice trasformazione derivata che accetta una colonna (titolo) e la sovrascrive con una versione maiuscola è la seguente:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E un sink senza schema sarebbe semplicemente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Frammenti di script

I frammenti di script sono codice condivisibile di script del flusso di dati che è possibile usare per la condivisione tra flussi di dati. Il video seguente illustra come usare i frammenti di script e usare lo script del flusso di dati per copiare e incollare parti dello script dietro i grafici del flusso di dati:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Statistiche di riepilogo aggregate
Aggiungere una trasformazione aggregazione al flusso di dati denominato "SummaryStats" e quindi incollare il codice seguente per la funzione di aggregazione nello script, sostituendo il SummaryStats esistente. Verrà fornito un modello generico per le statistiche di riepilogo del profilo dati.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
È anche possibile usare l'esempio seguente per contare il numero di righe univoche e il numero di righe distinte nei dati. L'esempio seguente può essere incollato in un flusso di dati con una trasformazione aggregazione denominata ValueDistAgg. In questo esempio viene utilizzata una colonna denominata "title". Assicurarsi di sostituire "title" con la colonna stringa nei dati che si vuole usare per ottenere i conteggi dei valori.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Includi tutte le colonne in un'aggregazione
Si tratta di un modello di aggregazione generico che dimostra come è possibile mantenere le colonne rimanenti nei metadati di output quando si compilano aggregazioni. In questo caso, viene usata la ```first()``` funzione per scegliere il primo valore in ogni colonna il cui nome non è "Movie". Per usarlo, creare una trasformazione aggregazione denominata DistinctRows, quindi incollarla nello script sopra lo script di aggregazione DistinctRows esistente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Crea impronta digitale hash riga 
Utilizzare questo codice nello script del flusso di dati per creare una nuova colonna derivata denominata ```DWhash``` che produce un ```sha1``` hash di tre colonne.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

È anche possibile usare lo script seguente per generare un hash di riga usando tutte le colonne presenti nel flusso, senza dover assegnare un nome a ogni colonna:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>Equivalente String_agg
Questo codice agirà come la funzione T-SQL ```string_agg()``` e aggrega i valori stringa in una matrice. È quindi possibile eseguire il cast di tale matrice in una stringa da usare con le destinazioni SQL.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Conteggio numero di aggiornamenti, Upsert, inserimenti, eliminazioni
Quando si utilizza una trasformazione alter Row, è possibile che si desideri contare il numero di aggiornamenti, Upsert, inserimenti ed eliminazioni risultanti dai criteri alter Row. Aggiungere una trasformazione aggregazione dopo l'istruzione ALTER Row e incollare lo script del flusso di dati nella definizione di aggregazione per questi conteggi.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Riga DISTINCT con tutte le colonne
Questo frammento di codice consente di aggiungere una nuova trasformazione aggregazione al flusso di dati che accetta tutte le colonne in ingresso, genera un hash utilizzato per il raggruppamento per eliminare i duplicati, quindi fornisce la prima occorrenza di ogni duplicato come output. Non è necessario denominare in modo esplicito le colonne, che verranno generate automaticamente dal flusso di dati in ingresso.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Verificare la presenza di valori NULL in tutte le colonne
Si tratta di un frammento che è possibile incollare nel flusso di dati per controllare in modo generico tutte le colonne per i valori NULL. Questa tecnica sfrutta la deriva dello schema per esaminare tutte le colonne in tutte le righe e usa una suddivisione condizionale per separare le righe con valori NULL dalle righe prive di valori NULL. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Spostamento dello schema AutoMap con selezione
Quando è necessario caricare uno schema di database esistente da un set sconosciuto o dinamico di colonne in ingresso, è necessario eseguire il mapping delle colonne di destra nella trasformazione sink. Questa operazione è necessaria solo quando si carica una tabella esistente. Aggiungere questo frammento di codice prima del sink per creare un oggetto Select che esegue il mapping automatico delle colonne. Lasciare il mapping del sink per eseguire il mapping automatico.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare i flussi di dati iniziando dall' [articolo Panoramica dei flussi di dati](concepts-data-flow-overview.md)
