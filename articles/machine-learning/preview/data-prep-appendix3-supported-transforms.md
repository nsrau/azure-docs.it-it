---
title: Trasformazioni supportate disponibili per la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutte le trasformazioni disponibili per la preparazione dei dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fd3f8157e27847b99e59465063111a6d6c8c713d
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="supported-transforms-for-this-release"></a>Trasformazioni supportate per questa versione
L'elenco e i riepiloghi seguenti indicano le trasformazioni disponibili per questa versione. 

Le trasformazioni supportate per questa versione sono descritte di seguito.

## <a name="column-selection"></a>Selezione di colonne 
Molte trasformazioni elencate di seguito operano su una o su più colonne. Per selezionare più colonne, usare Ctrl+clic su ogni colonna o Maiusc+clic su un intervallo di colonne.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Trasformazioni dal menu principale e/o dall'intestazione della griglia 
È possibile accedere alle trasformazioni dall'opzione Transforms (Trasformazioni) del menu principale. Le trasformazioni possono essere selezionate anche facendo clic sul nome della colonna nella griglia dati. Se sono selezionate più colonne, facendo clic su una qualsiasi di esse viene visualizzato un menu Transforms (Trasformazioni).

Sono disponibili solo le trasformazioni valide per il tipo di dati selezionato nel menu di scelta rapida; il menu principale offre tutte le trasformazioni ma le trasformazioni non rilevanti per le colonne selezionate sono disattivate.

Un piccolo subset delle trasformazioni contestuali è disponibile facendo clic con il pulsante destro del mouse su una cella. Queste trasformazioni sono quelle relative alla copia, alla sostituzione e al filtro; queste trasformazioni riconoscono il tipo di dati e quindi le opzioni per una colonna numerica sono diverse rispetto a quella di una colonna contenente stringhe.

## <a name="derive-column-by-example"></a>Derive Column by Example (Deriva colonna in base all'esempio)
Questa trasformazione consente di creare una nuova colonna come derivata da uno o più colonne esistenti. Vengono esaminati le colonne di input (selezionate) e l'esempio fornito per determinare l'output desiderato nella nuova colonna. 

Per usarla, selezionare una o più colonne. Aggiungere una nuova colonna derivata (vuota) per l'esempio. Digitare un esempio di ciò che si vuole visualizzare nella colonna derivata, presumendo che derivi da altre colonne, e la tecnologia "By Example" tenta di compilare tutte le altre celle nella colonna. 

Per esempi complessi potrebbe essere necessario fornire più esempi, selezionare un'altra cella e digitare un altro esempio.

La tecnologia "By Example" usa le colonne selezionate per tentare di determinare il significato di un esempio. Se non sono selezionate colonne quando viene richiamata questa trasformazione, vengono usate tutte le celle per la riga corrente. La selezione solo delle colonne necessarie implica risultati più accurati.

È possibile selezionare le colonne prima di richiamare la trasformazione. Dopo avere avviato l'editor di trasformazione, è possibile visualizzare le colonne selezionate come input tramite le caselle di controllo nella parte superiore di ogni colonna. L'aggiunta e la rimozione di colonne dalla selezione possono essere completate usando le caselle di controllo nelle intestazioni di colonna.

Per una spiegazione più dettagliata della trasformazione **Derive Column By Example** (Deriva colonna in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Derive Column by Example (Deriva colonna in base all'esempio)](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Split Column by Example (Dividi colonna in base all'esempio)
Usa una colonna esistente e tenta di dividere questa colonna in altre n colonne usando il motore "By Example". È possibile eseguire la divisione automatica per le colonne generate successive.

Per una spiegazione più dettagliata della trasformazione **Split Column By Example** (Dividi colonna in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Split Column By Example (Dividi colonna in base all'esempio)](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>Expand JSON (Espandi JSON)

La trasformazione **Expand JSON** (Espandi JSON) consente agli utenti di aggiungere più colonne espandendo una colonna con testo JSON valido.

Per una spiegazione più dettagliata della trasformazione **Expand JSON** (Espandi JSON) e per altri esempi, vedere il [documento di riferimento per la trasformazione Expand JSON (Espandi JSON)](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Combine Columns by Example (Combina colonne in base all'esempio)

Consente all'utente di aggiungere una nuova colonna combinando i valori di più colonne. 

Per una spiegazione più dettagliata della trasformazione **Combine Columns By Example** (Combina colonne in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Combine Columns by Example Reference (Combina colonne in base all'esempio)](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Duplicate Column (Duplica colonna)
Questa trasformazione esegue una copia esatta di una o più colonne e assegna un nuovo nome derivato dal nome o dai nomi delle colonne originali. Questa trasformazione supporta uno o più colonne.

## <a name="text-clustering"></a>Text Clustering (Clustering di testo) 
Questa trasformazione è progettata per accettare valori non coerenti che dovrebbero essere gli stessi e raggrupparli insieme.  

Durante l'esecuzione di questa trasformazione, vengono analizzati i valori in una singola colonna per individuarne la somiglianza. Vengono quindi raggruppati in cluster. Per ogni cluster esiste un valore canonico, ovvero il valore che sostituisce tutte le istanze del cluster e i valori delle istanze. È possibile rimuovere cluster completi; è possibile modificare il valore canonico. Le istanze possono essere rimosse da un cluster specifico. Inoltre è possibile modificare il filtro di soglia del punteggio di somiglianza usato per raggruppare le istanze in un cluster.

Per impostazione predefinita questa trasformazione sostituisce tutti i valori dell'istanza del cluster con il valore canonico per tale cluster, creando una nuova colonna per contenere i nuovi valori. È possibile anche aggiungere il punteggio di somiglianza, per ogni istanza, a una nuova colonna (che può essere denominata) per consentire l'uso in un secondo momento nel flusso di dati.

## <a name="replace-values"></a>Replace Values (Sostituisci valori)
Questa trasformazione consente la sostituzione di una stringa con un'altra; la stringa di origine può essere una stringa parziale o una cella completa. La sostituzione può essere applicata a una singola colonna o a numerose colonne. La stringa di ricerca supporta la ricerca di caratteri speciali e di caratteri normali. 

## <a name="replace-na-values"></a>Replace NA Values (Sostituisci valori NA)
Consente di sostituire le varie forme diverse di NA (N/A, NA, null, NaN e così via) o le stringhe vuote con un singolo valore in modo da renderle coerenti. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="replace-missing-values"></a>Replace Missing Values (Sostituisci valori mancanti)
Esegue la sostituzione dei dati mancanti con un singolo valore. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="replace-error-values"></a>Replace Error Values (Sostituisci valori di errore)
Esegue la sostituzione degli errori con un singolo valore. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="trim-string"></a>Trim String (Taglia stringa)
Rimuovere i caratteri "spazio" iniziali e finali, tra cui spazi e tabulazioni, da una o più colonne.

## <a name="adjust-precision"></a>Adjust Precision (Regola precisione)
Consente di impostare le posizioni decimali per una colonna numerica

## <a name="rename-column"></a>Rename Column (Rinomina colonna)
Modifica il nome della colonna; questa trasformazione può anche essere richiamata inline nell'intestazione di colonna facendo clic sul nome della colonna.

## <a name="remove-column"></a>Remove Column (Rimuovi colonna)
Rimuove la colonna o le colonne selezionate; questa trasformazione opera su una o più colonne. 

## <a name="keep-column"></a>Keep Column (Mantieni colonna)
Mantiene solo la colonna o le colonne selezionate; questa trasformazione opera su una o più colonne.

## <a name="handle-path-column"></a>Handle Path Column (Gestisci colonna percorso)
Durante l'importazione di un file viene aggiunta automaticamente del percorso al set di dati dalla procedura guidata. Contiene il nome file completo che costituisce il percorso del set di dati. Questa trasformazione aggiunge o rimuove la colonna aggiuntiva dal set di dati.

## <a name="convert-field-type-to-numeric"></a>Convert Field Type to Numeric (Converti tipo di campo in numerico)
Cambiare il tipo di colonna in numerico. Se sono presenti dati non interi, specificare il separatore. Per impostazione predefinita questa trasformazione non visualizza una richiesta per il separatore; usare la voce di menu **Edit** (Modifica) per richiamare l'editor. Questa trasformazione opera su una o più colonne.

## <a name="convert-field-type-to-date"></a>Convert Field Type to Date (Converti tipo di campo in data)
Cambiare il tipo di colonna in data. Viene usato un formato di data/ora predefinito, ma può essere sottoposto a override usando le direttive `strftime`. È possibile anche anteporre i valori dell'ora con una data fissa.

Per impostazione predefinita questa trasformazione non visualizza una richiesta per il formato; usare la voce di menu **Edit** (Modifica) sul passaggio risultante per richiamare l'editor. Questa trasformazione opera su una o più colonne.

Solo le date tra 9-22-1677 e 4-11-2262 possono essere convertite nel tipo data.

## <a name="convert-field-type-to-boolean"></a>Convert Field Type to Boolean (Converti tipo di campo in booleano)
Cambiare il tipo di colonna in true/false. Questa trasformazione supporta il mapping di più valori a true o false; è possibile modificare questi mapping. Supporta anche una costante per eseguire il mapping dei valori a valori non presenti nelle tabelle di mapping true/false. Questa trasformazione opera su una o più colonne.

## <a name="convert-field-type-to-string"></a>Convert Field Type to String (Converti tipo di campo in stringa)
Cambiare il tipo di colonna in stringa. Questa trasformazione opera su una o più colonne.

## <a name="convert-unix-timestamp-to-datetime"></a>Convert Unix Timestamp to DateTime (Converti timestamp Unix in DateTime)
Questa trasformazione riconosce il formato di timestamp Unix, anche se viene rappresentato sotto forma di stringa nei dati, e lo converte correttamente in un tipo di data nella preparazione dei dati.

## <a name="filter"></a>Filtro
Questa trasformazione supporta l'applicazione di filtri delle righe in base ai valori in una o più colonne; le condizioni del filtro dipendono dal tipo di dati di ogni colonna. È quindi possibile filtrare le colonne di stringhe per "contains" o "does not contain", ma non per i dati numerici. Tuttavia le condizioni "greater than" "less than" si applicano per le colonne di tipo numerico ma non per le colonne con stringhe.

Nel caso in cui questa trasformazione venga chiamata dal menu principale o facendo clic con il pulsante destro del mouse sull'intestazione di una colonna, è disponibile l'opzione per dividere le righe con errore in un altro flusso di dati. In questo modo il flusso di dati principale continua con le righe **in** filtrate e viene creato un nuovo flusso di dati che contiene tutte le righe **out** filtrate.

## <a name="use-first-row-as-headers"></a>Use first row as headers (Usa prima riga come intestazioni)
Questa trasformazione alza di livello la prima riga dal set di dati in modo che diventi nome di colonne; se nella prima riga di alcune colonne non sono presenti dati, viene generato automaticamente un nome. L'uso di questa trasformazione implica che l'importazione dei dati inizia non prima della riga 2. A seconda dell'impostazione che consente di ignorare righe, l'importazione potrebbe iniziare ancora più in basso nel set di dati. Può essere usata anche per rimuovere l'innalzamento di livello e usare solo nomi generati automaticamente.

## <a name="join"></a>Join
Questa trasformazione viene usata per creare un join tra due flussi di dati. Consente di selezionare l'output del join che deve essere il risultato: le righe con esito positivo dal join, le righe con errore a "sinistra" o le righe con errore a "destra" generate dal join.

La procedura guidata per la creazione del join viene avviata da un singolo flusso di dati e seleziona questo flusso di dati come uno dei lati del join; viene quindi richiesto all'utente un altro flusso di dati che rappresenti l'altro lato del join. Dopo avere selezionato i due flussi, viene richiesto di selezionare una singola colonna su ciascun lato del join in base a cui creare il join. Se il join richiede più di una colonna, creare una colonna derivata prima di avviare la procedura guidata per creare una nuova colonna (concatenata) da usare solo per il join. La procedura guidata tenta di suggerire le chiavi del join e, se possibile, di generare automaticamente la colonna derivata.

Dopo il completamento del join, viene presentata una vista del join usando il diagramma di Sankey; la larghezza delle linee di relazione reciproca è rappresentativa del numero di righe che si spostano all'interno di tale parte del flusso di join. Il pannello a destra consente di selezionare le righe con esito positivo, con esito negativo a sinistra, con esito negativo a destra in modo esclusivo o è possibile scegliere solo un ramo.

## <a name="append-rows"></a>Append Rows (Accoda righe)
Questa trasformazione accoda i dati da un altro flusso di dati a quello corrente; esegue il mapping di colonne in base alla posizione per aggiungere le nuove righe alla fine.

## <a name="append-columns"></a>Append Columns (Accoda colonne)
Questa trasformazione aggiunge nuove colonne da un altro flusso di dati a quello corrente, aggiunge le nuove colonne a destra, non tenta di allineare i dati nelle righe.

## <a name="summarize"></a>Summarize (Riepiloga)
Selezionare una o più colonne e calcolare le aggregazioni per questa combinazione di valori univoci.

Aggregazioni supportate: COUNT, SUM, MIN, MAX, MEAN, VARIANCE, STANDARD DEVIATION. L'elenco di aggregazioni per una determinata colonna viene filtrato in modo da indicare l'unico che si applica a questo tipo di dati. Le aggregazione non applicabili sono disattivate. Ad esempio non è possibile calcolare la media di una colonna di tipo stringa, ma è possibile calcolare il valore min e max.

È simile a una clausola GROUP BY ANSI-SQL

Dopo che l'editor è diventato disponibile, eseguire il trascinamento dall'intestazione di colonna verso l'alto nel pannello in alto a sinistra; le colonne da aggregare vengono visualizzate qui. Questo pannello è gerarchico ed è quindi possibile eseguire aggregazioni annidate. Il pannello dell'editor in alto a destra viene usato per selezionare l'aggregazione da applicare a una colonna. Una singola colonna può essere aggregata una o più volte. Dopo avere selezionato almeno un'aggregazione, la griglia in basso a destra visualizza in anteprima i dati in forma aggregata. 

## <a name="remove-duplicates"></a>Remove Duplicates (Rimuovi duplicati)
Questa trasformazione rimuove l'intera riga in cui sono presenti valori duplicati. I valori duplicati vengono definiti da una o più colonne selezionate nella chiamata della trasformazione o avviando l'editor. Se non vengono selezionate colonne, le uniche righe rimosse sono quelle in tutti i valori di colonna coincidono.

## <a name="sort"></a>Ordina
Questa trasformazione consente di ordinare i dati, può essere eseguita in base a una singola o a molte colonne, ogni colonna può essere ordinata in modo crescente (impostazione predefinita) o decrescente (modificabile dall'editor).

Simile a una clausola ORDER BY ANSI-SQL

## <a name="output-transforms"></a>Trasformazioni dell'output
Le seguenti trasformazioni producono l'output dei dati. È possibile disporre di più blocchi di scrittura in un singolo flusso per poter scrivere i dati in punti diversi.

### <a name="write-to-csv"></a>Write To CSV (Scrivi in Excel)
Questa trasformazione scrive i dati in formato CSV dal punto corrente nel flusso di dati.  Consente il controllo della posizione (locale o remota) e delle varie impostazioni relative al file.

### <a name="write-to-parquet"></a>Write to Parquet (Scrivi in Parquet)
Questa trasformazione scrive i dati in formato Parquet dal punto corrente nel flusso di dati. Consente il controllo della posizione (locale o remota) e delle varie impostazioni relative al file.

## <a name="script-based-transforms"></a>Trasformazioni basate su script
Tutte le trasformazioni seguenti usano script (Python) per eseguire le funzionalità mancanti nel prodotto principale. 
[Prima di usare una di queste trasformazioni, leggere il documento sull'estendibilità qui](data-prep-python-extensibility-overview.md):

### <a name="add-column-script"></a>Add Column - Script (Aggiungi colonna - Script)
Consente di aggiungere una colonna ai dati usando un'espressione Python; [vedere l'Appendice 10 per altre informazioni](data-prep-appendix10-sample-custom-column-transforms-python.md)

### <a name="advanced-filter-script"></a>Advanced Filter - Script (Filtro avanzato - Script)
Consente di scrivere un filtro a livello di riga Python; [vedere l'Appendice 6 per altre informazioni](data-prep-appendix6-sample-filter-expressions-python.md)

### <a name="transform-dataflow-script"></a>Transform Dataflow - Script (Trasforma flusso di dati - Script)
Consente di applicare Python all'intero set di dati

[Per altre informazioni, vedere l'Appendice 7](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="transform-dataflow-script"></a>Transform Dataflow - Script (Trasforma flusso di dati - Script)
Consente di applicare Python a un'intera partizione di dati

[Per altre informazioni, vedere l'Appendice 7](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="write-dataflow-script"></a>Write Dataflow - Script (Scrivi flusso di dati - Script)
Consente di applicare Python per scrivere un intero set di dati; [vedere l'Appendice 10 per altre informazioni](data-prep-appendix9-sample-destination-connections-python.md)




