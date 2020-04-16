---
title: Creare, sviluppare e gestire blocchi appunti di Azure Synapse Studio (anteprima)Create, develop, and maintain Azure Synapse Studio (preview) notebook
description: In questo articolo viene illustrato come creare e sviluppare blocchi appunti di Azure Synapse Studio (anteprima) per eseguire la preparazione e la visualizzazione dei dati.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430227"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Creare, sviluppare e gestire blocchi appunti di Azure Synapse Studio (anteprima)Create, develop, and maintain Azure Synapse Studio (preview) notebook

Un blocco appunti di Azure Synapse Studio (anteprima) è un'interfaccia Web in cui è possibile creare file contenenti codice in tempo reale, visualizzazioni e testo narrativo. I notebook sono un buon posto per convalidare le idee e usare esperimenti rapidi per ottenere informazioni dettagliate dai tuoi dati. I blocchi appunti sono anche ampiamente utilizzati nella preparazione dei dati, nella visualizzazione dei dati, nell'apprendimento automatico e in altri scenari di Big Data.

Con un blocco appunti di Azure Synapse Studio è possibile:With an Azure Synapse Studio notebook, you can:

* Inizia con zero sforzo di configurazione.
* Proteggi i dati con le funzionalità di sicurezza aziendali integrate.
* Analizza i dati in formati non elaborati (CSV, txt, JSON e così via), i formati di file elaborati (parquet, Delta Lake, ORC e così via) e i file di dati tabulari SQL su Spark e SQL.
* Aumenta la produttività con funzionalità di creazione avanzate e visualizzazione dei dati integrata.

Questo articolo descrive come usare i blocchi appunti in Azure Synapse Studio.This article describes how to use notebooks in Azure Synapse Studio.

## <a name="create-a-notebook"></a>Creare un notebook

Esistono due modi per creare un blocco appunti. È possibile creare un nuovo blocco appunti o importare un blocco appunti esistente in un'area di lavoro di Azure Synapse da **Esplora oggetti**. I blocchi appunti di Azure Synapse Studio sono in grado di riconoscere i file IPYNB standard di Jupyter Notebook.

![sinapsi-creare-importare-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Sviluppare notebook

I blocchi appunti sono costituiti da celle, ovvero singoli blocchi di codice o testo che possono essere eseguiti in modo indipendente o in gruppo.

### <a name="add-a-cell"></a>Aggiungere una cella

Esistono diversi modi per aggiungere una nuova cella al blocco appunti.

1. Espandere il pulsante in alto a sinistra **e cella** e selezionare Aggiungi cella di **codice** o Aggiungi cella di **testo**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Posizionare il puntatore del mouse sullo spazio tra due celle e selezionare **Aggiungi codice** o **Aggiungi testo**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Utilizzare [i tasti di scelta rapida in modalità di comando](#shortcut-keys-under-command-mode). Premere **A** per inserire una cella sopra la cella corrente. Premere **B** per inserire una cella sotto la cella corrente.

### <a name="set-a-primary-language"></a>Impostare una lingua principale

I notebook di Azure Synapse Studio supportano quattro lingue di spark:Azure Synapse Studio notebooks support four spark languages:

* pyspark (python)
* scintilla (Scala)
* sparkSQL
* Spark.NET (C )Spark.NET (C

È possibile impostare la lingua principale per le nuove celle aggiunte dall'elenco a discesa nella barra dei comandi superiore.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Utilizzare più lingue

È possibile utilizzare più lingue in un blocco appunti specificando il comando magico della lingua corretto all'inizio di una cella. Nella tabella seguente sono elencati i comandi magici per passare da un linguaggio a celle all'altro.

|Comando Magico |Linguaggio | Descrizione |  
|---|------|-----|
|%%pyspark| Python | Eseguire una query Python sul contesto Spark.Execute a **Python** query against Spark Context.  |
|%%spark| Scala | Eseguire una query Scala sul contesto Spark.Execute a **Scala** query against Spark Context.  |  
|%%sql| SparkSQL | Eseguire una query SparkSQL sul contesto Spark.Execute a **SparkSQL** query against Spark Context.  |
|%%csharp | Spark.NET C # | Eseguire una Spark.NET query **in C'** sul contesto Spark. |

L'immagine seguente è un esempio di come è possibile scrivere una query PySpark utilizzando il comando magico **%%pyspark** o una query SparkSQL con il comando magico **%%sql** in un blocco appunti **Spark(Scala).** Si noti che la lingua principale per il blocco appunti è impostata su Scala.

   ![sinapsi-scintilla-magia](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Usare le tabelle temporanee per fare riferimento ai dati tra lingueUse temp tables to reference data across languages

Non è possibile fare riferimento direttamente a dati o variabili in lingue diverse in un blocco appunti di Synapse Studio. In Spark è possibile fare riferimento a una tabella temporanea tra più lingue. Ecco un esempio di come `Scala` leggere `PySpark` un `SparkSQL` frame di dati in e l'utilizzo di una tabella temporanea Spark come soluzione alternativa.

1. Nella cella 1 leggere un frame di dati dal connettore del pool SQL usando Scala e creare una tabella temporanea.

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

3. Nella cella 3, usare i dati in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense in stile IDE

Gli appunti di Azure Synapse Studio sono integrati con l'editor Monaco per portare IntelliSense in stile IDE nell'editor di celle. L'evidenziazione della sintassi, il creatore di errori e il completamento automatico del codice consentono di scrivere codice e identificare i problemi più rapidamente.

Le funzionalità di IntelliSense sono a diversi livelli di maturità per lingue diverse. Usa la tabella seguente per vedere cosa è supportato.

|Languages| Evidenziazione della sintassi | Indicatore di errore di sintassi  | Completamento del codice di sintassi | Completamento codice variabile| Completamento del codice della funzione di sistema| Completamento del codice della funzione utente| Rientro automatico | Piegatura del codice|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Scintilla (Scala)|Sì|Sì|Sì|Sì|-|-|-|Sì|
|SparkSQL|Sì|Sì|-|-|-|-|-|-|
|Spark.NET (C )Spark.NET (C|Sì|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formattare la cella di testo con i pulsanti della barra degli strumenti

È possibile utilizzare i pulsanti di formato nella barra degli strumenti delle celle di testo per eseguire azioni di markdown comuni. Include testo in grassetto, corsivo di testo, inserimento di frammenti di codice, inserimento di elenchi non ordinati, inserimento di elenchi ordinati e inserimento di immagine da URL.

  ![sinapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Annullare le operazioni sulle celle
Fare clic sul pulsante **di annullamento** o premere **CTRL** per revocare l'operazione di cella più recente. Ora puoi annullare fino alle ultime 20 azioni delle celle storiche. 

   ![sinapsi-undo-cellule](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Spostare una cella

Selezionare i puntini di sospensione (...) per accedere al menu delle azioni celle aggiuntive all'estrema destra. Quindi selezionare **Sposta cella su** o Sposta cella verso il **basso** per spostare la cella corrente. 

È inoltre possibile utilizzare [i tasti di scelta rapida in modalità di comando](#shortcut-keys-under-command-mode). Premete **Ctrl (Windows)** o Alt (Windows) o Comando (Mac OS) per spostarsi verso l'alto nella cella corrente. Premete **Ctrl (Windows)** o Alt (Windows) o Comando (Mac OS) per spostare la cella corrente verso il basso.

   ![spostare una cella](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Eliminare una cella

Per eliminare una cella, selezionare i puntini di sospensione (...) per accedere al menu delle azioni aggiuntive della cella all'estrema destra, quindi selezionare **Elimina cella**. 

È inoltre possibile utilizzare [i tasti di scelta rapida in modalità di comando](#shortcut-keys-under-command-mode). Premere **D,D** per eliminare la cella corrente.
  
   ![eliminare una cella](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Comprimere un input di cella
Fare clic sul pulsante freccia nella parte inferiore della cella corrente per comprimerla. Per espanderlo, fare clic sul pulsante freccia mentre la cella è compressa.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Comprimere l'output di una cella

Fare clic sul pulsante di output di **compressione** nella parte superiore sinistra dell'output della cella corrente per comprimerlo. Per espanderlo, fare clic su **Mostra output cella** mentre l'output della cella è compresso.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Eseguire blocchi appunti

È possibile eseguire le celle di codice nel blocco appunti singolarmente o tutte contemporaneamente. Lo stato e lo stato di ogni cella sono rappresentati nel blocco appunti.

### <a name="run-a-cell"></a>Eseguire una cella

Esistono diversi modi per eseguire il codice in una cella.

1. Passare il mouse sulla cella che si desidera eseguire e selezionare il pulsante **Esegui cella** o premere **CTRL .**

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Per accedere al menu delle azioni celle aggiuntive all'estrema destra, selezionare i puntini di sospensione (**...**). Selezionare **quindi Esegui cella**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Utilizzare [i tasti di scelta rapida in modalità di comando](#shortcut-keys-under-command-mode). Premete **Maiusc e Invio** per eseguire la cella corrente e selezionate la cella sottostante. Premete **Alt-Invio** per eseguire la cella corrente e inserire una nuova cella sotto.


### <a name="run-all-cells"></a>Eseguire tutte le celle
Fare clic sul pulsante **Esegui tutto** per eseguire tutte le celle nel blocco appunti corrente in sequenza.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Esegui tutte le celle sopra o sotto

Per accedere al menu delle azioni celle aggiuntive all'estrema destra, selezionare i puntini di sospensione (**...**). Quindi, selezionare **Esegui celle sopra** per eseguire tutte le celle sopra la corrente in sequenza. Selezionare **Esegui celle sotto** per eseguire tutte le celle al di sotto della corrente in sequenza.

   ![run-cells-sopra o-sotto](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicatore di stato della cella

Uno stato di esecuzione della cella passo-passo viene visualizzato sotto la cella per aiutarti a vedere lo stato di avanzamento corrente. Una volta completata l'esecuzione della cella, viene visualizzato un riepilogo dell'esecuzione con la durata totale e l'ora di fine per riferimento futuro.

![stato della cella](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicatore di avanzamento dello spark

Il blocco appunti di Azure Synapse Studio è basato esclusivamente su Spark.Azure Synapse Studio notebook is purely Spark based. Le celle di codice vengono eseguite nel pool Spark in remoto. Viene fornito un indicatore di stato del processo Spark con una barra di avanzamento in tempo reale che consente di comprendere lo stato di esecuzione del processo.


![indicatore di avanzamento scintilla](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configurazione sessione Spark

È possibile specificare la durata del timeout, il numero e la dimensione degli esecutori da assegnare alla sessione Spark corrente in **Configura sessione**. Riavviare la sessione Spark per rendere effettive le modifiche alla configurazione. Tutte le variabili del blocco appunti memorizzate nella cache vengono cancellate.

![session-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Portare i dati in un blocco appunti

È possibile caricare i dati da Archiviazione BLOB di Azure, Archivio dati di Azure generazione 2 e pool SQL, come illustrato negli esempi di codice seguenti.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leggere un file CSV da Azure Data Lake Store Gen2 come frame di dati SparkRead a CSV from Azure Data Lake Store Gen2 as a Spark DataFrame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leggere un file CSV da Archiviazione BLOB di Azure come frame di dati SparkRead a CSV from Azure Blob Storage as a Spark DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Leggere i dati dall'account di archiviazione primarioRead data from the primary storage account

È possibile accedere direttamente ai dati nell'account di archiviazione primario. Non c'è bisogno di fornire le chiavi segrete. In Esplora dati fare clic con il pulsante destro del mouse su un file e scegliere **Nuovo blocco appunti** per visualizzare automaticamente un nuovo blocco appunti con l'estrattore dati.

![da dati a cella](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizzare i dati in un blocco appunti

### <a name="display"></a>Display()

Viene fornita una visualizzazione dei risultati tabulari con l'opzione per creare un grafico a barre, un grafico a linee, un grafico a torta, un grafico a dispersione e un grafico ad area. È possibile visualizzare i dati senza dover scrivere codice. I grafici possono essere personalizzati in **Opzioni grafico**. 

L'output dei comandi magici **%%sql** viene visualizzato nella vista tabella di cui è stato eseguito il rendering per impostazione predefinita. È possibile chiamare **display(`<DataFrame name>`)** su funzioni Spark DataFrames o Resilient Distributed Datasets (RDD) per produrre la vista tabella di cui è stato eseguito il rendering.

   ![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Potete eseguire il rendering di librerie HTML o interattive, come **bokeh**, utilizzando **displayHTML()**.

L'immagine seguente è un esempio di tracciatura dei glifi su una mappa utilizzando **bokeh**.

   ![bokeh-esempio](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Eseguire il codice di esempio seguente per disegnare l'immagine precedente.

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

## <a name="save-notebooks"></a>Salvare i blocchi appunti

È possibile salvare un singolo blocco appunti o tutti i blocchi appunti nell'area di lavoro.

1. Per salvare le modifiche apportate a un singolo blocco appunti, selezionare il pulsante **Pubblica** sulla barra dei comandi del blocco appunti.

   ![pubblicazione-blocco note](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Per salvare tutti i blocchi appunti nell'area di lavoro, selezionare il pulsante **Pubblica tutto** sulla barra dei comandi dell'area di lavoro. 

   ![pubblicare-tutti](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nelle proprietà del blocco appunti è possibile configurare se includere l'output della cella durante il salvataggio.

   ![notebook-proprietà](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandi magici
È possibile usare i comandi magici di Jupyter nei blocchi appunti di Azure Synapse Studio.You can use your familiar Jupyter magic commands in Azure Synapse Studio notebooks. Controlla l'elenco qui sotto come gli attuali comandi magici disponibili. Diteci i vostri casi d'uso su GitHub in modo che possiamo continuare a costruire più comandi magici per soddisfare le vostre esigenze.

Magia di linea disponibile: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magia delle celle disponibili: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Combinazione di tasti

Analogamente aJupyter Notebooks, i blocchi appunti di Azure Synapse Studio dispongono di un'interfaccia utente modale. La tastiera esegue operazioni diverse a seconda della modalità in cui si trova la cella del notebook. I blocchi appunti di Synapse Studio supportano le due modalità seguenti per una determinata cella di codice: modalità di comando e modalità di modifica.

1. Una cella è in modalità di comando quando non è presente alcun cursore di testo che richiede di digitare. Quando una cella è in modalità Comando, è possibile modificare il blocco appunti nel suo complesso, ma non digitare in singole celle. Accedere alla modalità di comando premendo `ESC` o utilizzando il mouse per fare clic all'esterno dell'area dell'editor di una cella.

   ![modalità comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. La modalità di modifica è indicata da un cursore di testo che richiede di digitare nell'area dell'editor. Quando una cella è in modalità di modifica, non è possibile digitare nella cella. Accedere alla modalità di modifica premendo `Enter` o utilizzando il mouse per fare clic sull'area dell'editor di una cella.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Tasti di scelta rapida in modalità di comando

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei blocchi appunti di Azure Synapse.Using the following keystroke shortcuts, you can more easily navigate and run code in Azure Synapse notebooks.

| Azione |Tasti di scelta rapida per I notebook di Synapse Studio  |
|--|--|
|Eseguire la cella corrente e selezionare sotto | MAIUSC+INVIO |
|Eseguire la cella corrente e inserire sotto | ALT+INVIO |
|Seleziona cella sopra| Su |
|Seleziona cella sotto| Giù |
|Inserisci cella sopra| Una |
|Inserisci cella sotto| b |
|Estendere le celle selezionate sopra| Maiusc+su |
|Estendere le celle selezionate sotto| Maiusc+giù.|
|Sposta la cella verso l'alto| Ctrl , ALT , Alt . |
|Spostare la cella verso il basso| CTRL-ALT-ALT-TASTIERA |
|Eliminare le celle selezionate| D, D |
|Passare alla modalità di modifica| Immettere |

### <a name="shortcut-keys-under-edit-mode"></a>Tasti di scelta rapida in modalità di modifica

Usando i tasti di scelta rapida seguenti, è possibile spostarsi ed eseguire più facilmente il codice nei blocchi appunti di Azure Synapse in modalità di modifica.

| Azione |Scelte rapide per i blocchi appunti di Synapse Studio  |
|--|--|
|Spostare il cursore verso l'alto | Su |
|Spostare il cursore verso il basso|Giù|
|Annullamento|CTRL+Z|
|Ripristinare|CTRL+Y|
|Inserimento/Rimozione di commenti|CTRL e /|
|Elimina parola prima|CTRL e BACKSPACE|
|Elimina parola dopo|CTRL e CANC|
|Vai all'inizio della cella|CTRL + Home|
|Vai alla fine della cella |CTRL + fine|
|Vai di una parola a sinistra|CTRL e SINISTRA|
|Vai una parola a destra|CTRL e DESTRA|
|Selezionare tutto|CTRL+A|
|Indent| Premere CTRL e ]|
|Rientro|CTRL e [|
|Passare alla modalità comando| ESC |

## <a name="next-steps"></a>Passaggi successivi

- [.NET per la documentazione di Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
