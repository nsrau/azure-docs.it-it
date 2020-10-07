---
title: 'Esercitazione: Spark & Hive Tools per VSCode (applicazione Spark)'
description: 'Esercitazione: Usare Spark & Hive Tools per VSCode per sviluppare applicazioni Spark, scritte in Python, e inviare a un pool di Apache Spark (anteprima).'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: 6778d78ff5e342d97c1c9bc477c1a88eca42a10a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91338113"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Esercitazione: Creare un'applicazione Apache Spark con VS Code usando un'area di lavoro di Synapse

Informazioni su come usare Spark & Hive Tools per Visual Studio Code. Usare gli strumenti per creare e inviare processi batch Apache Hive, query Hive interattive e script PySpark per Apache Spark. Verrà prima di tutto descritto come installare Spark & Hive Tools in Visual Studio Code. Quindi verrà illustrato come inviare processi a tali strumenti.

È possibile installare Spark & Hive Tools nelle piattaforme supportate da Visual Studio Code. Si notino i prerequisiti seguenti per le diverse piattaforme.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questo articolo, è necessario quanto segue:

- Un pool di Apache Spark. Per creare un pool di Apache Spark, vedere [Creare un pool di Apache Spark con il portale di Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.
- [Un ambiente interattivo PySpark per Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Una directory locale. Questo articolo usa **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Installare Spark & Hive Tools

Dopo aver soddisfatto i prerequisiti, è possibile installare Spark & Hive Tools per Visual Studio Code seguendo questa procedura:

1. Aprire Visual Studio Code.

2. Dalla barra dei menu passare a **Visualizza** > **Estensioni**.

3. Nella casella di ricerca immettere **Spark & Hive**.

4. Selezionare **Spark & Hive Tools** nei risultati della ricerca e quindi selezionare **Installa**:

     ![Installazione di Spark & Hive per Visual Studio Code in Python](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Selezionare **Ricarica** quando è necessario.

## <a name="open-a-work-folder"></a>Aprire una cartella di lavoro

Per aprire una cartella di lavoro e creare un file in Visual Studio Code, seguire questa procedura:

1. Sulla barra dei menu scegliere **File** > **Apri cartella** > **C:\HD\Synaseexample** e quindi selezionare il pulsante **Seleziona cartella**. La cartella verrà visualizzata nella visualizzazione **Explorer** a sinistra.

2. Nella visualizzazione **Explorer** selezionare la cartella **Synaseexample** e quindi selezionare l'icona **Nuovo file** accanto alla cartella di lavoro:

     ![Icona Nuovo file in Visual Studio Code](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Assegnare un nome al nuovo file con l'estensione `.py` (script di Spark). Questo esempio usa **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Connettersi ai pool di Spark

Accedere alla sottoscrizione di Azure per connettersi ai pool di Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

Seguire questa procedura per connettersi ad Azure:

1. Sulla barra dei menu scegliere **Visualizza** > **Riquadro comandi** e immettere **Azure: Accedi**:

     ![Accesso a Spark & Hive Tools per Visual Studio Code](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni per accedere ad Azure. Dopo essersi connessi, il nome dell'account Azure viene visualizzato sulla barra di stato nell'angolo in basso a sinistra della finestra di Visual Studio Code.

## <a name="set-the-default-spark-pool"></a>Impostare il pool di Spark predefinito

1. Riaprire la cartella **Synaseexample** descritta [in precedenza](#open-a-work-folder), se è stata chiusa.  

2. Selezionare il file **HelloWorld.py** creato [in precedenza](#open-a-work-folder). Viene aperto nell'editor di script.

3. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Synapse: Set default Spark pool** (Imposta il pool di Spark predefinito).  

4. [Connettersi](#connect-to-your-spark-pools) all'account Azure, se ancora non è stato fatto.

5. Selezionare un pool di Spark come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il file di configurazione **.VSCode\settings.json**:

     ![Impostare la configurazione del cluster predefinito](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Inviare query Synapse PySpark interattive al pool di Spark

Gli utenti possono eseguire query Synapse PySpark interattive sul pool di Spark nei modi seguenti:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Uso del comando interattivo di Synapse PySpark nel file PY
Per usare il comando interattivo di PySpark per inviare le query, seguire questa procedura:

1. Riaprire la cartella **Synaseexample** descritta [in precedenza](#open-a-work-folder), se è stata chiusa.  

2. Creare un nuovo file **HelloWorld.py** seguendo la procedura illustrata [in precedenza](#open-a-work-folder).

3. Copiare e incollare il codice seguente nel file di script:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. Nell'angolo in basso a destra della finestra viene visualizzata la richiesta di installare i kernel PySpark/SynapsePyspark. È possibile fare clic sul pulsante **Installa** per procedere alle installazioni di PySpark/SynapsePyspark oppure sul pulsante **Ignora** per ignorare questo passaggio.

     ![Installare il kernel pyspark](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Se è necessario installarlo in un secondo momento, è possibile passare a **File** > **Preferenza** > **Impostazioni**, quindi deselezionare **HDInsight: Enable Skip Pyspark Installation** (Consenti di ignorare l'installazione di Pyspark). 
    
     ![Consentire di ignorare l'installazione di pyspark](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Se l'installazione riesce nel passaggio 4, nell'angolo in basso a destra della finestra viene visualizzata la finestra con il messaggio corrispondente. Fare clic sul pulsante **Ricarica** per ricaricare la finestra.

     ![Installazione di pyspark completata](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Sulla barra dei menu scegliere **Visualizza** > **Riquadro comandi** oppure premere i tasti di scelta rapida **MAIUSC+CTRL+P** e immettere **Python: Select Interpreter to start Jupyter Server** (Seleziona interprete per avviare il server Jupyter).

     ![Selezionare l'interprete per avviare il server Jupyter](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Selezionare l'opzione Python seguente.

     ![Scegliere l'opzione seguente](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Sulla barra dei menu scegliere **Visualizza** > **Riquadro comandi** oppure premere i tasti di scelta rapida **MAIUSC+CTRL+P** e immettere **Developer: Ricarica finestra**.

     ![Ricaricare la finestra](./media/vscode-tool-synapse/reload-window.png)

10. [Connettersi](#connect-to-your-spark-pools) all'account Azure, se ancora non è stato fatto.

11. Selezionare tutto il codice, fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Synapse: Pyspark Interactive** per inviare la query. 

     ![Menu di scelta rapida PySpark Interactive (PySpark interattivo)](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Selezionare il pool di Spark, se non è stato specificato quello predefinito. Dopo alcuni istanti, i risultati di **Python interattivo** verranno visualizzati in una nuova scheda. Fare clic su PySpark per impostare il kernel su **Synapse PySpark**, quindi inviare di nuovo il codice selezionato, che verrà eseguito correttamente. Gli strumenti consentono anche di inviare un blocco di codice invece dell'intero file di script usando il menu di scelta rapida:

     ![interattivo](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Eseguire query interattive in un file PY usando un commento #%%

1. Aggiungere **#%%** prima del codice per ottenere l'esperienza del notebook.

     ![Aggiungere #%%](./media/vscode-tool-synapse/run-cell.png)

2. Fare clic su **Esegui cella**. Dopo alcuni istanti, i risultati di Python Interactive verranno visualizzati in una nuova scheda. Fare clic su PySpark per impostare il kernel su **Synapse PySpark**, quindi fare di nuovo clic su **Esegui cella**. Il codice verrà eseguito correttamente. 

     ![Risultati dell'esecuzione della cella](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Sfruttare il supporto per IPYNB dell'estensione Python

1. È possibile creare un notebook di Jupyter in base a comando dal riquadro comandi oppure creando un nuovo file con estensione ipynb nell'area di lavoro. Per altre informazioni, vedere [Uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Fare clic sul pulsante **Esegui cella**, seguire le istruzioni per **impostare il pool di Spark predefinito** (è consigliabile impostare il cluster/pool predefinito ogni volta prima di aprire un notebook) quindi scegliere **Ricarica** per ricaricare la finestra.

     ![Impostare il pool di Spark predefinito e ricaricare la finestra](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Fare clic su PySpark per impostare il kernel su **Synapse Pyspark**, quindi fare clic su **Esegui cella**. Dopo alcuni istanti verrà visualizzato il risultato.

     ![Risultati dell'esecuzione di ipynb](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. La versione di Ms-python maggiore o uguale a 2020.5.78807 non è supportata in questa estensione e si tratta di un [problema noto](#known-issues).
>  
>2. Per usare il kernel Synapse Pyspark è consigliabile disabilitare le impostazioni automatiche nel portale di Azure. In caso contrario, potrebbe essere necessario molto tempo per riattivare il cluster e impostare il kernel Synapse per il primo utilizzo. 
>
>    ![Impostazioni automatiche](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Inviare il processo batch PySpark al pool di Spark

1. Riaprire la cartella **Synaseexample** descritta [in precedenza](#open-a-work-folder), se è stata chiusa.  

2. Creare un nuovo file **BatchFile.py** seguendo la procedura descritta [in precedenza](#open-a-work-folder).

3. Copiare e incollare il codice seguente nel file di script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Connettersi](#connect-to-your-spark-pools) all'account Azure, se ancora non è stato fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Synapse: PySpark Batch**.

6. Selezionare un pool di Spark in cui inviare il processo PySpark:

     ![Output del risultato dell'invio del processo Python](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Dopo aver inviato un processo batch al pool di Spark, nella finestra **OUTPUT** in Visual Studio Code vengono visualizzati i log dell'invio. Vengono inoltre visualizzati gli URL dell'**interfaccia utente di Spark** e dell'**interfaccia utente dell'applicazione del processo Spark**. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.

## <a name="access-and-manage-synapse-workspace"></a>Accedere e gestire l'area di lavoro Synapse

In Azure Explorer è possibile eseguire varie operazioni all'interno di Spark & Hive Tools per VSCode. In Azure Explorer.

![Immagine di Azure](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Avviare l'area di lavoro

1. In Azure Explorer passare al nodo **SYNAPSE**, espanderlo e visualizzare l'elenco di sottoscrizioni Synapse.

     ![Explorer per Synapse](./media/vscode-tool-synapse/synapse-explorer.png)

2. Fare clic sull'area di lavoro della sottoscrizione di Synapse, espanderla e visualizzare l'elenco di aree di lavoro.

3. Fare clic con il pulsante destro del mouse su un'area di lavoro e scegliere **View Apache Spark applications** (Visualizza applicazioni Apache Spark) per aprire la pagina delle applicazioni Apache Spark nel sito Web Synapse Studio.

     ![Fare clic con il pulsante destro del mouse su un'area di lavoro](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Applicazione Synapse Studio](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Espandere un'area di lavoro. Vengono visualizzate le opzioni **Default Storage** (Account di archiviazione predefinito) e **Pool di Spark**.

5. Fare clic con il pulsante destro del mouse su **Default Storage**. Vengono visualizzate le opzioni **Copia percorso completo** e **Apri in Synapse Studio**. 

     ![Fare clic con il pulsante destro del mouse su archiviazione predefinita](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Fare clic su **Copia percorso completo**. Verrà copiato l'URL dell'account ADLS Gen2 primario, che è possibile incollare dove è necessario.

     - Fare clic su **Apri in Synapse Studio**. L'account di archiviazione primario verrà aperto in Synapse Studio.

     ![Account di archiviazione predefinito in Synapse Studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Espandere il **Default Storage**. Viene visualizzato l'account di archiviazione primario.

7. Espandere **Pool Spark**. Vengono visualizzati tutti i pool di Spark dell'area di lavoro.

     ![Espandere il pool di archiviazione](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Problemi noti

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>La versione di ms-python maggiore o uguale a 2020.5.78807 non è supportata in questa estensione 

L'errore "Non è stato possibile connettersi a Jupyter Notebook" è un problema noto per Python versione maggiore o uguale a 2020.5.78807. Per evitare questo problema, è consigliabile usare la versione **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** di ms-pyton.

![Problemi noti di](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](../overview-what-is.md)
- [Creare un nuovo pool di Apache Spark per un'area di lavoro di Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
