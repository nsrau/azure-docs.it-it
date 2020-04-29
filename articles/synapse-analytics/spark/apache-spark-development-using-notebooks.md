---
title: Creare, sviluppare e gestire notebook di Azure sinapsi Studio (anteprima)
description: Questo articolo illustra come creare e sviluppare notebook di Azure sinapsi Studio (anteprima) per la preparazione e la visualizzazione dei dati.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430227"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Creare, sviluppare e gestire notebook di Azure sinapsi Studio (anteprima)

Un notebook di Azure sinapsi Studio (anteprima) è un'interfaccia Web che consente di creare file che contengono codice in tempo reale, visualizzazioni e testo descrittivo. I notebook sono un posto ideale per convalidare le idee e usare esperimenti rapidi per ottenere informazioni dettagliate dai dati. I notebook sono anche ampiamente usati per la preparazione dei dati, la visualizzazione dei dati, l'apprendimento automatico e altri scenari di Big Data.

Con un notebook di Azure sinapsi studio è possibile:

* Inizia con zero attività di installazione.
* Proteggi i dati con le funzionalità di sicurezza aziendali predefinite.
* Analizza i dati in formati non elaborati (CSV, txt, JSON e così via), formati di file elaborati (parquet, Delta Lake, ORC e così via) e file di dati tabulari SQL in Spark e SQL.
* Produttività con funzionalità avanzate di creazione e visualizzazione dei dati incorporata.

Questo articolo descrive come usare i notebook in Azure sinapsi Studio.

## <a name="create-a-notebook"></a>Creare un notebook

Esistono due modi per creare un notebook. È possibile creare un nuovo notebook o importare un notebook esistente in un'area di lavoro di Azure sinapsi dalla **Esplora oggetti**. I notebook di Azure sinapsi studio sono in grado di riconoscere i file standard Jupyter Notebook IPYNB.

![sinapsi-creazione-importazione-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Sviluppare notebook

I notebook sono costituiti da celle, ovvero singoli blocchi di codice o testo che possono essere eseguiti in modo indipendente o come gruppo.

### <a name="add-a-cell"></a>Aggiungere una cella

Sono disponibili diversi modi per aggiungere una nuova cella al notebook.

1. Espandere il pulsante in alto a sinistra **+ cella** e selezionare **Aggiungi cella di codice** o **Aggiungi cella di testo**.

    ![Add-Cell-with-Cell-Button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passare il mouse sullo spazio tra due celle e selezionare **Aggiungi codice** o **Aggiungi testo**.

    ![Aggiungi-cella-tra-spazio](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Usare i [tasti di scelta rapida in modalità comando](#shortcut-keys-under-command-mode). Premere **un** per inserire una cella al di sopra della cella corrente. Premere **B** per inserire una cella sotto la cella corrente.

### <a name="set-a-primary-language"></a>Impostare una lingua primaria

I notebook di Azure sinapsi Studio supportano quattro lingue Spark:

* pyspark (Python)
* Spark (scala)
* sparkSQL
* Spark.NET (C#)

È possibile impostare la lingua primaria per le nuove celle aggiunte dall'elenco a discesa nella barra dei comandi superiore.

   ![default-sinapsi-Language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>USA più lingue

È possibile usare più lingue in un notebook specificando il corretto comando Language Magic all'inizio di una cella. Nella tabella seguente sono elencati i comandi Magic per cambiare le lingue delle celle.

|Comando Magic |Linguaggio | Descrizione |  
|---|------|-----|
|%% pyspark| Python | Eseguire una query **Python** su un contesto Spark.  |
|%% Spark| Scala | Eseguire una query **scala** sul contesto Spark.  |  
|%% SQL| SparkSQL | Eseguire una query **SparkSQL** sul contesto Spark.  |
|%% CSharp | Spark.NET C # | Eseguire una query **Spark.NET C#** sul contesto Spark. |

L'immagine seguente è un esempio di come è possibile scrivere una query PySpark usando il comando **%% PySpark** Magic o una query SparkSQL con il comando **%% SQL** Magic in un notebook **Spark (scala)** . Si noti che la lingua primaria per il notebook è impostata su scala.

   ![sinapsi-Spark-Magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usare le tabelle temporanee per fare riferimento ai dati tra lingue diverse

Non è possibile fare riferimento a dati o variabili direttamente in linguaggi diversi in un notebook di sinapsi Studio. In Spark è possibile fare riferimento a una tabella temporanea tra le varie lingue. Di seguito è riportato un esempio di come eseguire `Scala` una soluzione alternativa `PySpark` per `SparkSQL` leggere un frame di frame in e usare una tabella temporanea di Spark.

1. Nella cella 1 leggere un dataframe dal connettore del pool SQL usando scala e creare una tabella temporanea.

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

I notebook di Azure sinapsi studio sono integrati con l'editor di Monaco per portare IntelliSense in stile IDE nell'editor di celle. L'evidenziazione della sintassi, il creatore di errori e i completamenti automatici del codice consentono di scrivere codice e identificare più rapidamente i problemi.

Le funzionalità di IntelliSense hanno livelli di maturità diversi per le diverse lingue. Usare la tabella seguente per visualizzare gli elementi supportati.

|Languages| Evidenziazione della sintassi | Indicatore di errore di sintassi  | Completamento del codice di sintassi | Completamento codice variabile| Completamento del codice della funzione di sistema| Completamento del codice della funzione utente| Rientro automatico | Riduzione del codice|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Spark (scala)|Sì|Sì|Sì|Sì|-|-|-|Sì|
|SparkSQL|Sì|Sì|-|-|-|-|-|-|
|Spark.NET (C#)|Sì|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formattare la cella di testo con i pulsanti della barra degli strumenti

È possibile utilizzare i pulsanti formato nella barra degli strumenti celle testo per eseguire azioni Markdown comuni. Include testo in grassetto, testo corsivo, inserimento di frammenti di codice, inserimento di un elenco non ordinato, inserimento di elenchi ordinati e inserimento di immagini dall'URL.

  ![sinapsi-text-Cell-Toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Annulla operazioni celle
Fare clic sul pulsante **Annulla** oppure premere **CTRL + Z** per revocare l'operazione più recente sulle celle. A questo punto è possibile annullare le ultime 20 azioni della cella cronologica. 

   ![sinapsi-Undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Spostare una cella

Selezionare i puntini di sospensione (...) per accedere al menu delle azioni delle celle aggiuntivo all'estrema destra. Selezionare quindi **Sposta la cella in alto** o **Sposta la cella** verso il basso per spostare la cella corrente. 

È anche possibile usare i [tasti di scelta rapida in modalità comando](#shortcut-keys-under-command-mode). Premere **CTRL + ALT + ↑** per spostare verso l'alto la cella corrente. Premere **CTRL + ALT + ↓** per spostare la cella corrente verso il basso.

   ![Move-a-Cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Eliminare una cella

Per eliminare una cella, selezionare i puntini di sospensione (...) per accedere al menu azioni cella aggiuntiva all'estrema destra, quindi selezionare **Elimina cella**. 

È anche possibile usare i [tasti di scelta rapida in modalità comando](#shortcut-keys-under-command-mode). Premere **d, d** per eliminare la cella corrente.
  
   ![Elimina-a-cella](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Comprimi un input di cella
Fare clic sul pulsante freccia nella parte inferiore della cella corrente per comprimerlo. Per espanderlo, fare clic sul pulsante freccia mentre la cella è compressa.

   ![comprimere-cella-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Comprimi output di una cella

Fare clic sul pulsante **Comprimi output** nella parte superiore sinistra dell'output della cella corrente per comprimerlo. Per espanderlo, fare clic su **Mostra output celle** mentre l'output della cella è compresso.

   ![Comprimi-cella-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Eseguire notebook

È possibile eseguire le celle del codice nel notebook singolarmente o in una sola volta. Lo stato e lo stato di ogni cella sono rappresentati nel notebook.

### <a name="run-a-cell"></a>Eseguire una cella

Esistono diversi modi per eseguire il codice in una cella.

1. Passare il puntatore del mouse sulla cella che si vuole eseguire e selezionare il pulsante **Esegui cella** oppure premere **CTRL + INVIO**.

   ![cella di esecuzione-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Per accedere al menu delle azioni delle celle aggiuntivo all'estrema destra, selezionare i puntini di sospensione (**...**). Quindi selezionare **Run Cell**.

   ![Run-Cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Usare i [tasti di scelta rapida in modalità comando](#shortcut-keys-under-command-mode). Premere **MAIUSC + INVIO** per eseguire la cella corrente e selezionare la cella riportata di seguito. Premere **ALT + INVIO** per eseguire la cella corrente e inserire una nuova cella sotto.


### <a name="run-all-cells"></a>Esegui tutte le celle
Fare clic sul pulsante **Esegui tutto** per eseguire tutte le celle del notebook corrente in sequenza.

   ![Run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Eseguire tutte le celle sopra o sotto

Per accedere al menu delle azioni delle celle aggiuntivo all'estrema destra, selezionare i puntini di sospensione (**...**). Selezionare quindi **Esegui le celle sopra** per eseguire tutte le celle sopra l'oggetto corrente in sequenza. Selezionare **Esegui celle sotto** per eseguire tutte le celle sotto la sequenza corrente.

   ![Run-cells-sopra o-sotto](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicatore di stato delle celle

Sotto la cella viene visualizzato lo stato di esecuzione di una cella dettagliata che consente di visualizzare lo stato di avanzamento corrente. Al termine dell'esecuzione della cella, viene visualizzato un riepilogo di esecuzione con la durata e l'ora di fine totali, che verranno mantenuti per riferimento futuro.

![cella-stato](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicatore di stato Spark

Il notebook di Azure sinapsi studio è basato esclusivamente su Spark. Le celle di codice vengono eseguite in remoto nel pool Spark. Viene fornito un indicatore di stato del processo Spark con un indicatore di stato in tempo reale che consente di comprendere lo stato di esecuzione del processo.


![Spark-indicatore di stato](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configurazione della sessione Spark

È possibile specificare la durata del timeout, il numero e le dimensioni degli esecutori da assegnare alla sessione Spark corrente nella **sessione di configurazione**. Riavviare la sessione di Spark per rendere effettive le modifiche alla configurazione. Tutte le variabili del notebook memorizzati nella cache vengono cancellate.

![Gestione sessione](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Importare i dati in un notebook

È possibile caricare i dati dall'archiviazione BLOB di Azure, Azure Data Lake Store generazione 2 e dal pool SQL, come illustrato negli esempi di codice riportati di seguito.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leggi un volume CSV da Azure Data Lake Store Gen2 come frame di frame di Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leggere un volume CSV dall'archiviazione BLOB di Azure come frame di frame di Spark

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

È possibile accedere direttamente ai dati nell'account di archiviazione primario. Non è necessario fornire le chiavi segrete. In Esplora dati fare clic con il pulsante destro del mouse su un file e scegliere **nuovo notebook** per visualizzare un nuovo notebook con l'estrazione dati generata automaticamente.

![da dati a cella](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizzare i dati in un notebook

### <a name="display"></a>Visualizzazione ()

Viene fornita una visualizzazione dei risultati tabulari con l'opzione per creare un grafico a barre, un grafico a linee, un grafico a torta, un grafico a dispersione e un grafico ad area. È possibile visualizzare i dati senza dover scrivere codice. I grafici possono essere personalizzati nelle **Opzioni del grafico**. 

Per impostazione predefinita, l'output di **%% SQL** Magic Commands viene visualizzato nella visualizzazione tabella di cui è stato eseguito il rendering. Per produrre la visualizzazione della tabella di cui è stato eseguito il rendering, è possibile chiamare **display (`<DataFrame name>`)** in Spark dataframes o funzione RDD (Resilient Distributed DataSets).

   ![grafici incorporati](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

È possibile eseguire il rendering di HTML o di librerie interattive, ad esempio **bokeh**, usando **displayHTML ()**.

L'immagine seguente è un esempio di tracciato dei glifi su una mappa con **bokeh**.

   ![bokeh-esempio](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Eseguire il codice di esempio seguente per creare l'immagine sopra.

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

## <a name="save-notebooks"></a>Salva notebook

È possibile salvare un singolo notebook o tutti i notebook nell'area di lavoro.

1. Per salvare le modifiche apportate a un singolo notebook, selezionare il pulsante **pubblica** sulla barra dei comandi del notebook.

   ![Publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Per salvare tutti i notebook nell'area di lavoro, selezionare il pulsante **pubblica tutto** sulla barra dei comandi dell'area di lavoro. 

   ![pubblica tutto](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nelle proprietà del notebook è possibile specificare se includere l'output della cella durante il salvataggio.

   ![Notebook-proprietà](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandi Magic
È possibile usare i familiari comandi Jupyter Magic nei notebook di Azure sinapsi Studio. Controllare l'elenco seguente come i comandi Magic disponibili correnti. Inviaci i tuoi casi d'uso su GitHub per poter continuare a sviluppare più comandi magici per soddisfare le tue esigenze.

Magie della riga disponibili: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Funzionalità magiche disponibili per le celle: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Combinazione di tasti

Analogamente ai notebook di Jupyter, i notebook di Azure sinapsi studio hanno un'interfaccia utente modale. La tastiera esegue diverse operazioni a seconda della modalità in cui si trova la cella del notebook. I notebook di sinapsi Studio supportano le due modalità seguenti per una cella di codice specificata, ovvero la modalità di comando e la modalità di modifica.

1. Una cella è in modalità di comando quando non è presente un cursore di testo che richiede di digitare. Quando una cella è in modalità di comando, è possibile modificare il notebook nel suo complesso, ma non digitarlo in singole celle. Immettere la modalità comando premendo `ESC` o usando il mouse per fare clic all'esterno dell'area dell'editor di una cella.

   ![modalità di comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. La modalità di modifica è indicata da un cursore di testo che richiede di digitare nell'area dell'editor. Quando una cella si trova in modalità di modifica, è possibile digitare nella cella. Per passare alla modalità di `Enter` modifica, premere o usare il mouse per fare clic sull'area dell'editor di una cella.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Tasti di scelta rapida in modalità comando

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di sinapsi di Azure.

| Action |Collegamenti al notebook di sinapsi Studio  |
|--|--|
|Eseguire la cella corrente e selezionare sotto | MAIUSC+INVIO |
|Eseguire la cella corrente e inserire di seguito | ALT+INVIO |
|Seleziona cella sopra| Su |
|Selezionare la cella sotto| Giù |
|Inserisci cella sopra| Una |
|Inserisci cella sotto| b |
|Estendi le celle selezionate sopra| Maiusc+su |
|Estendi le celle selezionate sotto| Maiusc+giù.|
|Sposta cella in alto| CTRL + ALT + ↑ |
|Sposta cella giù| CTRL + ALT + ↓ |
|Elimina celle selezionate| D, D |
|Passa alla modalità di modifica| Immettere |

### <a name="shortcut-keys-under-edit-mode"></a>Tasti di scelta rapida in modalità di modifica

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di sinapsi di Azure in modalità di modifica.

| Action |Collegamenti al notebook di sinapsi Studio  |
|--|--|
|Sposta cursore su | Su |
|Sposta cursore in basso|Giù|
|Annullamento|CTRL+Z|
|Ripristinare|CTRL+Y|
|Inserimento/Rimozione di commenti|CTRL +/|
|Elimina parola prima|CTRL + BACKSPACE|
|Elimina parola dopo|CTRL + CANC|
|Vai a inizio cella|CTRL + Home|
|Vai alla fine della cella |CTRL + fine|
|Vai a sinistra di una parola|CTRL + freccia sinistra|
|Vai a destra di una parola|Ctrl + destra|
|Selezionare tutto|CTRL+A|
|Indent| CTRL +]|
|Riduci rientro|CTRL + [|
|Passa alla modalità di comando| ESC |

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
