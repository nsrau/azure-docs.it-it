---
title: Notebook di sinapsi Studio
description: Questo articolo illustra come creare e sviluppare notebook di Azure Synapse Studio (anteprima) per la preparazione e la visualizzazione dei dati.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 0f6f193f531be746d3ef4920b86855ffa49efda2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260445"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Creare, sviluppare e gestire i notebook di sinapsi Studio (anteprima) in Azure sinapsi Analytics

Un notebook di sinapsi Studio (anteprima) è un'interfaccia Web che consente di creare file che contengono codice in tempo reale, visualizzazioni e testo descrittivo. I notebook possono essere usati per convalidare idee ed eseguire esperimenti rapidi per ottenere informazioni cognitive dettagliate dai dati. I notebook sono anche ampiamente usati per la preparazione e la visualizzazione dei dati, l'apprendimento automatico e altri scenari di Big Data.

Con un notebook di Azure Synapse Studio è possibile:

* Iniziare senza attività di configurazione.
* Mantenere i dati protetti con le funzionalità di sicurezza aziendali predefinite.
* Analizzare i dati in formati non elaborati (CSV, txt, JSON e così via), formati di file elaborati (parquet, Delta Lake, ORC e così via) e file di dati tabulari SQL in Spark e SQL.
* Ottenere produttività con funzionalità avanzate di creazione e visualizzazione dei dati predefinite.

Questo articolo descrive come usare i notebook in Azure Synapse Studio.

## <a name="create-a-notebook"></a>Creare un notebook

È possibile creare un notebook in due modi. È possibile creare un nuovo notebook o importarne uno esistente in un'area di lavoro Azure Synapse da **Esplora oggetti**. I notebook di Azure Synapse Studio sono in grado di riconoscere i file con estensione ipynb standard di Jupyter Notebook.

![Crea notebook di importazione](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Sviluppare i notebook

I notebook sono costituiti da celle, ovvero singoli blocchi di codice o testo che possono essere eseguiti in modo indipendente o come gruppo.

### <a name="add-a-cell"></a>Aggiungere una cella

Esistono diversi modi per aggiungere una nuova cella al notebook.

1. Espandere il pulsante in alto a sinistra **+ Cella**, quindi selezionare **Aggiungi cella di codice** o **Aggiungi cella di testo**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passare con il mouse sullo spazio tra due celle e selezionare **Aggiungi codice** o **Aggiungi testo**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **A** per inserire una cella al di sopra della cella corrente. Premere **B** per inserire una cella sotto la cella corrente.

### <a name="set-a-primary-language"></a>Impostare il linguaggio primario

I notebook di Azure Synapse Studio supportano quattro linguaggi Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET per Apache Spark (C#)

È possibile impostare il linguaggio primario per le nuove celle aggiunte dall'elenco a discesa nella barra dei comandi superiore.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Usare più linguaggi

È possibile usare più linguaggi in un notebook specificando il comando magic corretto per il linguaggio all'inizio di una cella. Nella tabella seguente sono elencati i comandi magic per passare da un linguaggio all'altro nelle celle.

|Comando magic |Linguaggio | Descrizione |  
|---|------|-----|
|%%pyspark| Python | Eseguire una query **Python** sul contesto Spark.  |
|%%spark| Scala | Eseguire una query **Scala** sul contesto Spark.  |  
|%%sql| SparkSQL | Eseguire una query **SparkSQL** sul contesto Spark.  |
|%%csharp | .NET per Spark C# | Eseguire una query **.NET per Spark C#** sul contesto Spark. |

L'immagine seguente è un esempio di come è possibile scrivere una query PySpark usando il comando magic **%% PySpark** o una query SparkSQL con il comando magic **%% SQL** in un notebook **Spark(Scala)** . Si noti che il linguaggio primario per il notebook è impostato su pySpark.

   ![Comandi Magic di sinapsi Spark](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usare tabelle temporanee per fare riferimento ai dati tra linguaggi

Non è possibile fare riferimento a dati o variabili direttamente tra linguaggi diversi in un notebook di Synapse Studio. In Spark è possibile fare riferimento a una tabella temporanea tra i vari linguaggi. Di seguito è riportato un esempio di come leggere un DataFrame `Scala` in `PySpark` e `SparkSQL` usando una tabella temporanea di Spark come soluzione alternativa.

1. Nella cella 1 leggere un DataFrame dal connettore del pool SQL usando Scala e creare una tabella temporanea.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Nella cella 2 eseguire una query sui dati usando Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Nella cella 3 usare i dati in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense di tipo IDE

I notebook di Azure Synapse Studio sono integrati nell'editor Monaco e consentono di aggiungere la funzionalità IntelliSense di tipo IDE all'editor di celle. L'evidenziazione della sintassi, il marcatore di errore e i completamenti automatici del codice consentono di scrivere codice e identificare più rapidamente i problemi.

Le funzionalità di IntelliSense hanno livelli di maturità diversi per i diversi linguaggi. Usare la tabella seguente per visualizzare gli elementi supportati.

|Languages| Evidenziazione della sintassi | Generatore di errori della sintassi  | Completamento del codice della sintassi | Completamento del codice della variabile| Completamento del codice della funzione di sistema| Completamento del codice della funzione utente| Rientro automatico | Riduzione del codice|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Spark (Scala)|Sì|Sì|Sì|Sì|-|-|-|Sì|
|SparkSQL|Sì|Sì|-|-|-|-|-|-|
|.NET per Spark (C#)|Sì|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formattare la cella di testo con i pulsanti della barra degli strumenti

È possibile usare i pulsanti di formato nella barra degli strumenti delle celle di testo per eseguire azioni di markdown comuni, tra cui l'applicazione di grassetto o corsivo al testo, l'inserimento di frammenti di codice, l'inserimento di elenchi non ordinati, l'inserimento di elenchi ordinati e l'inserimento di immagini dall'URL.

  ![Barra degli strumenti cella testo sinapsi](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Annullare operazioni sulle celle
Selezionare il pulsante **Annulla** oppure premere **CTRL + Z** per revocare l'operazione più recente sulle celle. A questo punto è possibile annullare le ultime 20 azioni cronologiche sulle celle. 

   ![Celle di annullamento della sinapsi](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Spostare una cella

Selezionare i puntini di sospensione (...) per accedere al menu aggiuntivo delle azioni sulle celle all'estrema destra. Selezionare quindi **Sposta la cella in alto** o **Sposta la cella in basso** per spostare la cella corrente. 

È anche possibile usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **CTRL + ALT + ↑** per spostare verso l'alto la cella corrente. Premere **CTRL + ALT + ↓** per spostare verso il basso la cella corrente.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Eliminare una cella

Per eliminare una cella, selezionare i puntini di sospensione (...) per accedere al menu aggiuntivo delle azioni sulle celle all'estrema destra e selezionare **Elimina cella**. 

È anche possibile usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **D,D** per eliminare la cella corrente.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Comprimere l'input di una cella
Selezionare il pulsante freccia nella parte inferiore della cella corrente per comprimerlo. Per espanderlo, selezionare il pulsante freccia mentre la cella è compressa.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Comprimere l'output di una cella

Selezionare il pulsante **Comprimi output** nella parte superiore sinistra dell'output della cella corrente per comprimerlo. Per espanderlo, selezionare **Mostra output celle** mentre l'output della cella è compresso.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Eseguire i notebook

È possibile eseguire le celle di codice nel notebook singolarmente o tutte insieme. Lo stato e l'avanzamento di ogni cella sono rappresentati nel notebook.

### <a name="run-a-cell"></a>Eseguire una cella

Esistono diversi modi per eseguire il codice in una cella.

1. Passare con il puntatore del mouse sulla cella che si desidera eseguire e selezionare il pulsante **Esegui cella** oppure premere **CTRL + INVIO**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Selezionare i puntini di sospensione **(...)** all'estrema destra per accedere al menu aggiuntivo delle azioni sulle celle. Selezionare quindi **Esegui cella**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **MAIUSC + INVIO** per eseguire la cella corrente e selezionare la cella al di sotto. Premere **ALT + INVIO** per eseguire la cella corrente e inserire una nuova cella al di sotto.


### <a name="run-all-cells"></a>Eseguire tutte le celle
Selezionare il pulsante **Esegui tutto** per eseguire tutte le celle del notebook corrente in sequenza.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Eseguire tutte le celle al di sopra o al di sotto

Selezionare i puntini di sospensione **(...)** all'estrema destra per accedere al menu aggiuntivo delle azioni sulle celle. Selezionare quindi **Esegui celle sopra** per eseguire tutte le celle sopra quella corrente in sequenza. Selezionare **Esegui celle sotto** per eseguire tutte le celle sotto quella corrente in sequenza.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Annulla tutte le celle in esecuzione
Selezionare il pulsante **Annulla tutto** per annullare le celle o le celle in attesa nella coda. 
   ![Annulla-tutte le celle](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Indicatore di stato delle celle

Sotto la cella viene visualizzato il relativo stato di esecuzione dettagliato, che indica lo stato di avanzamento corrente. Al termine dell'esecuzione della cella, viene visualizzato un riepilogo dell'esecuzione con la durata totale e l'ora di fine, informazioni che verranno mantenute per riferimento futuro.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicatore di avanzamento Spark

Il notebook di Azure Synapse Studio è basato esclusivamente su Spark. Le celle di codice vengono eseguite in remoto nel pool Spark. Viene fornito un indicatore di stato del processo Spark con una barra di avanzamento in tempo reale che consente di comprendere lo stato di esecuzione del processo.
Il numero di attività per ogni processo o fase consente di identificare il livello parallelo del processo Spark. È anche possibile esaminare più a fondo l'interfaccia utente di Spark di un processo specifico o di una fase mediante la selezione del collegamento al nome del processo o della fase.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configurazione della sessione Spark

È possibile specificare la durata del timeout, il numero e le dimensioni degli executor da assegnare alla sessione Spark corrente in **Configura sessione**. Riavviare la sessione di Spark per rendere effettive le modifiche alla configurazione. Tutte le variabili del notebook memorizzate nella cache vengono cancellate.

[![gestione della sessione](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Un Consiglio di sessione Spark è ora disponibile nel pannello di configurazione della sessione Spark. È possibile selezionare un pool Spark direttamente dal pannello configurazione della sessione e verificare il numero di nodi che usano e il numero di esecutori rimanenti disponibili. Queste informazioni possono essere utili per impostare le dimensioni della sessione appropriate anziché modificarle.

![sessione-consigli](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Importare i dati in un notebook

È possibile caricare i dati da Archiviazione BLOB di Azure, Azure Data Lake Store Gen 2 e dal pool SQL, come illustrato negli esempi di codice riportati di seguito.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leggere un CSV da Azure Data Lake Store Gen2 come un DataFrame di Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leggere un CSV da Archiviazione BLOB di Azure come un DataFrame di Spark

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Leggere i dati dall'account di archiviazione primario

È possibile accedere direttamente ai dati nell'account di archiviazione primario. Non è necessario fornire le chiavi private. In Esplora dati fare clic con il pulsante destro del mouse su un file e selezionare **Nuovo notebook** per visualizzare un nuovo notebook con l'estrazione dati generata automaticamente.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizzare i dati in un notebook

### <a name="produce-rendered-table-view"></a>Genera visualizzazione tabella sottoposta a rendering

Viene fornita una visualizzazione tabulare dei risultati con l'opzione per creare un grafico a barre, un grafico a linee, un grafico a torta, un grafico a dispersione e un grafico ad area. È possibile visualizzare i dati senza dover scrivere codice. I grafici possono essere personalizzati nelle **Opzioni del grafico**. 

Per impostazione predefinita, l'output dei comandi magic **%%sql** appare nella visualizzazione tabella di cui è stato eseguito il rendering. <code>display(df)</code>Per produrre la visualizzazione della tabella di cui è stato eseguito il rendering, è possibile chiamare sui frame di frame di Spark, i Dataframe Pandas, l'elenco o la funzione RDD (Resilient Distributed DataSets).

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Visualizza i grafici predefiniti dal set di dati su larga scala 

Per impostazione predefinita, la <code>display(df)</code> funzione utilizzerà solo le prime 1000 righe dei dati per eseguire il rendering dei grafici. Controllare l' **aggregazione su tutti i risultati** e selezionare **applica** pulsante. la generazione del grafico viene applicata dall'intero set di dati. Un processo Spark viene attivato quando viene modificata l'impostazione del grafico, il completamento del calcolo e il rendering del grafico richiedono un po' di tempo. 
    [![Builtin-grafici-aggregazione-tutti](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)



### <a name="visualize-data-statistic-information"></a>Visualizzare le informazioni statistiche sui dati
È possibile usare <code>display(df, summary = True)</code> per controllare il riepilogo delle statistiche di un frame di dati Spark specifico che include il nome della colonna, il tipo di colonna, i valori univoci e i valori mancanti per ogni colonna. È anche possibile selezionare una colonna specifica per visualizzare il valore minimo, il valore massimo, il valore medio e la deviazione standard.
    [![Builtin-grafici-riepilogo ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>Eseguire il rendering di HTML o di librerie interattive

È possibile eseguire il rendering del codice HTML, incluse le librerie JavaScript, CSS, D3 o interattive, ad esempio **bokeh**, usando **displayHTML ()**.

L'immagine seguente è un esempio di glifi tracciati su una mappa che usa **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Eseguire questo codice di esempio per disegnare l'immagine precedente.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Salvare i notebook

È possibile salvare un singolo notebook o tutti i notebook nell'area di lavoro.

1. Per salvare le modifiche apportate a un singolo notebook, selezionare il pulsante **Pubblica** sulla barra dei comandi del notebook.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Per salvare tutti i notebook nell'area di lavoro, selezionare il pulsante **Pubblica tutti i** sulla barra dei comandi dell'area di lavoro. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nelle proprietà del notebook è possibile specificare se includere l'output della cella durante il salvataggio.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandi magic
Nei notebook di Azure sinapsi studio è possibile usare i comandi Magic Jupyter noti. Esaminare l'elenco seguente come i comandi Magic disponibili correnti. Inviaci [i tuoi casi d'uso su GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) per poter continuare a sviluppare più comandi magici per soddisfare le tue esigenze.

Magic line disponibili: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% tempo](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% tempo](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Comandi magic disponibili per le celle: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>Orchestrazione notebook

### <a name="add-a-notebook-to-a-pipeline"></a>Aggiungere un notebook a una pipeline

Selezionare il pulsante **Aggiungi a pipeline** nell'angolo superiore destro per aggiungere un notebook a una pipeline esistente o creare una nuova pipeline.

![Aggiungi notebook alla pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designare una cella parametri

Per parametrizzare il notebook, selezionare i puntini di sospensione (...) per accedere al menu delle azioni delle celle aggiuntivo all'estrema destra. Selezionare quindi **Imposta/Rimuovi cella parametro** per indicare la cella come cella parametri.

![interruttore-parametro](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory cerca la cella Parameters e considera questa cella come impostazioni predefinite per i parametri passati in fase di esecuzione. Il motore di esecuzione aggiungerà una nuova cella sotto la cella Parameters con i parametri di input per sovrascrivere i valori predefiniti. Quando non viene designata una cella dei parametri, la cella inserita viene inserita nella parte superiore del notebook.

### <a name="assign-parameters-values-from-a-pipeline"></a>Assegnare i valori dei parametri da una pipeline

Dopo aver creato un notebook con i parametri, è possibile eseguirlo da una pipeline con l'attività di Azure sinapsi notebook. Dopo aver aggiunto l'attività nell'area di disegno della pipeline, sarà possibile impostare i valori dei parametri nella sezione **parametri di base** della scheda **Impostazioni** . 

![Assegnare un parametro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Quando si assegnano valori di parametro, è possibile usare il [linguaggio delle espressioni della pipeline](../../data-factory/control-flow-expression-language-functions.md) o le variabili di [sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Combinazioni di tasti

Analogamente a Jupyter Notebook, i notebook di Azure Synapse Studio hanno un'interfaccia utente modale. La tastiera esegue diverse operazioni a seconda della modalità in cui si trova la cella del notebook. I notebook di Synapse Studio supportano le due modalità seguenti per una cella di codice specificata, ovvero la modalità di comando e la modalità di modifica.

1. Una cella è in modalità di comando quando non è presente un cursore di testo che richiede di digitare. Quando una cella è in modalità di comando, è possibile modificare il notebook nel suo complesso, ma non digitare in singole celle. Immettere la modalità comando premendo `ESC` o usando il mouse per selezionare all'esterno dell'area dell'editor di una cella.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. La modalità di modifica è indicata da un cursore di testo che richiede di digitare nell'area dell'editor. Quando una cella è in modalità di modifica, è possibile digitare nella cella. Per passare alla modalità di modifica, premere `Enter` o usare il mouse per selezionare l'area dell'editor di una cella.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Combinazione di tasti in modalità comando

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di Azure Synapse.

| Azione |Scelte rapide del notebook di Synapse Studio  |
|--|--|
|Eseguire la cella corrente e selezionare in basso | MAIUSC+INVIO |
|Eseguire la cella corrente e inserire in basso | ALT+INVIO |
|Selezionare la cella in alto| Su |
|Selezionare la cella in basso| Giù |
|Inserire la cella in alto| Una |
|Inserire la cella in basso| b |
|Estendere le celle selezionate in alto| MAIUSC+freccia SU |
|Estendere le celle selezionate in basso| Shift+Down|
|Spostare la cella in alto| CTRL + ALT + ↑ |
|Spostare la cella in basso| CTRL+Alt+↓ |
|Eliminare le celle selezionate| D, D |
|Passare alla modalità di modifica| Immettere |

### <a name="shortcut-keys-under-edit-mode"></a>Tasti di scelta rapida in modalità di modifica

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di Azure Synapse in modalità di modifica.

| Azione |Scelte rapide del notebook di Synapse Studio  |
|--|--|
|Spostare il cursore in alto | Su |
|Spostare in cursore in basso|Giù|
|Annullamento|CTRL + Z|
|Ripristinare|CTRL + Y|
|Inserimento/Rimozione di commenti|CTRL + /|
|Eliminare la parola prima|CTRL + BACKSPACE|
|Eliminare la parola dopo|CTRL + CANC|
|Andare all'inizio della cella|CTRL + Home|
|Andare alla fine della cella |CTRL + Fine|
|Andare a sinistra di una parola|CTRL + freccia sinistra|
|Andare a destra di una parola|CTRL + freccia destra|
|Selezionare tutto|CTRL + A|
|Impostare un rientro| CTRL + ]|
|Annullare l'impostazione di un rientro|CTRL + [|
|Passare alla modalità comandi| ESC |

## <a name="next-steps"></a>Passaggi successivi
- [Estrai notebook di esempio sinapsi](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Avvio rapido: Creare un pool di Apache Spark (anteprima) in Azure Synapse Analytics con gli strumenti Web](../quickstart-apache-spark-notebook.md)
- [Che cos'è Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Usare .NET per Apache Spark con Azure Synapse Analytics](spark-dotnet.md)
- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
