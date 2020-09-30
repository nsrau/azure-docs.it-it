---
title: Azure HDInsight per Visual Studio Code
description: Informazioni su come usare Spark & hive Tools (Azure HDInsight) per Visual Studio Code. Usare gli strumenti per creare e inviare query e script.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2020
ms.custom: devx-track-python
ms.openlocfilehash: bb2fff699b31d8b3b311180c4b85e2bfd1da892c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530154"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Usare gli strumenti di Spark & hive per Visual Studio Code

Informazioni su come usare Apache Spark & strumenti hive per Visual Studio Code. Usare gli strumenti per creare e inviare Apache Hive processi batch, query hive interattive e script PySpark per Apache Spark. Prima di tutto viene descritto come installare gli strumenti hive di Spark & in Visual Studio Code. Verrà quindi illustrato come inviare processi a Spark & gli strumenti hive.  

Spark & gli strumenti hive possono essere installati su piattaforme supportate da Visual Studio Code. Si notino i prerequisiti seguenti per le diverse piattaforme.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questo articolo, è necessario quanto segue:

- Disporre di un cluster HDInsight di Azure. Per creare un cluster, vedere [Introduzione a HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). In alternativa, usare un cluster Spark e hive che supporta un endpoint Apache Livio.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è necessario solo per Linux e macOS.
- [Ambiente interattivo PySpark per Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Una directory locale. Questo articolo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installare Spark & Hive Tools

Dopo aver soddisfatto i prerequisiti, è possibile installare Spark & hive Tools for Visual Studio Code attenendosi alla procedura seguente:

1. Aprire Visual Studio Code.

2. Dalla barra dei menu passare a **Visualizza**  >  **estensioni**.

3. Nella casella di ricerca immettere **Spark & Hive**.

4. Selezionare **Spark & hive Tools** nei risultati della ricerca e quindi selezionare **Install (installa**):

   ![Spark & hive per l'installazione di Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selezionare **ricarica** quando necessario.

## <a name="open-a-work-folder"></a>Aprire una cartella di lavoro

Per aprire una cartella di lavoro e creare un file in Visual Studio Code, attenersi alla seguente procedura:

1. Dalla barra dei menu passare a **file**  >  **Apri cartella...**  >  **C:\HD\HDexample**, quindi selezionare il pulsante **Seleziona cartella** . La cartella verrà visualizzata nella visualizzazione **Explorer** a sinistra.

2. Nella visualizzazione **Esplora risorse** selezionare la cartella **HDexample** , quindi selezionare l'icona **nuovo file** accanto alla cartella di lavoro:

   ![icona del nuovo file di Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Assegnare un nome al nuovo file usando l' `.hql` estensione di file (query hive) o `.py` (script Spark). Questo esempio usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

Per un utente del cloud nazionale, attenersi alla procedura seguente per impostare prima l'ambiente di Azure e quindi usare il comando **Azure: Sign in** per accedere ad Azure:

1. Passare a **File**  >  **Preferenze**file  >  **Impostazioni**.
2. Eseguire una ricerca nella stringa seguente: **Azure: cloud**.
3. Selezionare il cloud nazionale nell'elenco:

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Connettersi a un account Azure

Prima di poter inviare script ai cluster da Visual Studio Code, l'utente può accedere alla sottoscrizione di Azure o [collegare un cluster HDInsight](#link-a-cluster). Usare il nome utente/password di Ambari o le credenziali aggiunti a un dominio per il cluster ESP per la connessione al cluster HDInsight. Per connettersi ad Azure, seguire questa procedura:

1. Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi**e immettere **Azure: accedi**:

    ![Strumenti di Spark & hive per Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni di accesso per accedere ad Azure. Dopo la connessione, il nome dell'account Azure viene visualizzato sulla barra di stato nella parte inferiore della finestra di Visual Studio Code.  

## <a name="link-a-cluster"></a>Collegare un cluster

### <a name="link-azure-hdinsight"></a>Collegamento: Azure HDInsight

È possibile collegare un cluster normale usando un nome utente gestito da [Apache Ambari](https://ambari.apache.org/)oppure è possibile collegare un cluster Hadoop di Enterprise Security Pack protetto usando un nome utente di dominio (ad esempio, `user1@contoso.com` ).

1. Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi...** e immettere **Spark/hive: collegare un cluster**.

   ![Comando del cluster del collegamento del riquadro comandi](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selezionare il tipo di cluster collegato **Azure HDInsight**.

3. Immettere l'URL del cluster HDInsight.

4. Immettere il nome utente di Ambari; il valore predefinito è **admin**.

5. Immettere la password di Ambari.

6. Selezionare il tipo di cluster.

7. Impostare il nome visualizzato del cluster (facoltativo).

8. Esaminare la visualizzazione **OUTPUT** per verificare.

   > [!NOTE]  
   > Il nome utente e la password collegati vengono usati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.  

### <a name="link-generic-livy-endpoint"></a>Collegamento: endpoint generico Livio

1. Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi...** e immettere **Spark/hive: collegare un cluster**.

2. Selezionare il tipo di cluster collegato**Generic Livy Endpoint** (Endpoint Livy generico).

3. Immettere l'endpoint generico Livio. Ad esempio: http \: //10.172.41.42:18080.

4. Selezionare il tipo di autorizzazione **Basic** (Di base) o **None** (Nessuna).  Se si seleziona di **base**:  
    &emsp;a. Immettere il nome utente di Ambari; il valore predefinito è **admin**.  
    &emsp;b. Immettere la password di Ambari.

5. Esaminare la visualizzazione **OUTPUT** per verificare.

## <a name="list-clusters"></a>Elencare i cluster

1. Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi...** e immettere **Spark/hive: list cluster**.

2. Selezionare la sottoscrizione desiderata.

3. Esaminare la visualizzazione **OUTPUT**. Questa visualizzazione Mostra il cluster (o i cluster) collegato e tutti i cluster nella sottoscrizione di Azure:

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Impostare il cluster predefinito

1. Riaprire la cartella **HDexample** descritta in [precedenza](#open-a-work-folder), se chiusa.  

2. Selezionare il file **HelloWorld. HQL** creato in [precedenza](#open-a-work-folder). Viene aperto nell'editor di script.

3. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **Spark/hive: imposta cluster predefinito**.  

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il **.VSCode\settings.jsnel file di** configurazione:

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Inviare query hive interattive e script batch hive

Con Spark & gli strumenti hive per Visual Studio Code è possibile inviare query hive interattive e script batch hive ai cluster.

1. Riaprire la cartella **HDexample** descritta in [precedenza](#open-a-work-folder), se chiusa.  

2. Selezionare il file **HelloWorld. HQL** creato in [precedenza](#open-a-work-folder). Viene aperto nell'editor di script.

3. Copiare e incollare il codice seguente nel file hive e quindi salvarlo:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Hive: Interactive** per inviare la query oppure utilizzare il tasto di scelta rapida CTRL + ALT + i.  Selezionare **Hive: batch** per inviare lo script oppure usare il tasto di scelta rapida CTRL + ALT + H.  

6. Se non è stato specificato un cluster predefinito, selezionare un cluster. Gli strumenti consentono anche di inviare un blocco di codice anziché l'intero file di script tramite il menu di scelta rapida. Dopo alcuni istanti, i risultati della query vengono visualizzati in una nuova scheda:

   ![Risultato della query Interactive Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Pannello **dei risultati** : è possibile salvare l'intero risultato come file CSV, JSON o di Excel in un percorso locale o semplicemente selezionare più righe.

    - Pannello **messaggi** : quando si seleziona un numero di **riga** , viene eseguito il salto alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

Gli utenti possono eseguire PySpark Interactive nei modi seguenti:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Uso del comando interattivo PySpark nel file PY
Utilizzando il comando interattivo PySpark per inviare le query, attenersi alla procedura seguente:

1. Riaprire la cartella **HDexample** descritta in [precedenza](#open-a-work-folder), se chiusa.  

2. Creare un nuovo file **HelloWorld.py** , seguendo i passaggi [precedenti](#open-a-work-folder) .

3. Copiare e incollare il codice seguente nel file di script:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. Il prompt per installare il kernel PySpark viene visualizzato nell'angolo inferiore destro della finestra. È possibile fare clic sul pulsante **Installa** per procedere con l'installazione di PySpark. in alternativa, fare clic sul pulsante **Ignora** per ignorare questo passaggio.

   ![Screenshot mostra un'opzione che consente di ignorare l'installazione di PySpark.](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Se è necessario installarlo in un secondo momento, è possibile **File**passare a  >  **Preference**  >  **Impostazioni**preferenza file, quindi deselezionare **HDInsight: Enable ignora Pyspark Installation** nelle impostazioni. 
    
    ![Screenshot mostra l'opzione per abilitare l'installazione di Skip Pyspark.](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Se l'installazione ha avuto esito positivo nel passaggio 4, la finestra di messaggio "PySpark installato correttamente" viene visualizzata nell'angolo inferiore destro della finestra. Fare clic sul pulsante **ricarica** per ricaricare la finestra.
    ![installazione di pyspark completata](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

8. Selezionare tutto il codice, fare clic con il pulsante destro del mouse sull'editor di script e selezionare **Spark: PySpark Interactive** per inviare la query. In alternativa, usare il tasto di scelta rapida CTRL + ALT + I.

    ![Menu di scelta rapida PySpark Interactive (PySpark interattivo)](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. Selezionare il cluster, se non è stato specificato un cluster predefinito. Dopo alcuni istanti, i risultati di **Python Interactive** vengono visualizzati in una nuova scheda. Fare clic su PySpark per passare dal kernel a **PySpark**e che il codice venga eseguito correttamente. Gli strumenti consentono anche di inviare un blocco di codice anziché l'intero file di script tramite il menu di scelta rapida:

   ![Finestra di Python interattivo per PySpark interattivo](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. Immettere **%% info**e quindi premere MAIUSC + INVIO per visualizzare le informazioni sul processo (facoltativo):

    ![informazioni sul processo di visualizzazione interattiva di pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Lo strumento supporta anche la query **SQL Spark** :

   ![risultato della visualizzazione interattiva di pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Eseguire query interattive in un file PY usando un commento #%%

1. Aggiungere **#%%** prima del codice py per ottenere l'esperienza del notebook.

    ![Aggiungi #%%](./media/hdinsight-for-vscode/run-cell.png)

2. Fare clic su **Run Cell**. Dopo alcuni istanti, i risultati di Python interattivo verranno visualizzati in una nuova scheda.

   ![Risultati della cella di esecuzione](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > Quando si verificano problemi nel kernel o nelle impostazioni, usare **Python: selezionare l'interprete per avviare il comando server Jupyter** e **riavviare il kernel IPython**, quindi ricaricare il VSCode, che può essere risolto.

## <a name="leverage-ipynb-support-from-python-extension"></a>Usare il supporto di IPYNB dall'estensione Python

1. È possibile creare un comando Jupyter Notebook by dal riquadro comandi o creando un nuovo file con estensione ipynb nell'area di lavoro. Per altre informazioni, vedere [uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Fare clic su PySpark per passare dal kernel al **PySpark**e quindi fare clic su **Esegui cella**, dopo un periodo di tempo, il risultato verrà visualizzato.

   ![Esegui risultati ipynb](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
>MS-Python >= la versione 2020.5.78807 non è supportata in questa estensione è un [problema noto](#known-issues).

## <a name="submit-pyspark-batch-job"></a>Inviare il processo batch PySpark

1. Riaprire la cartella **HDexample** illustrata in [precedenza](#open-a-work-folder), se chiusa.  

2. Creare un nuovo file **batchfile.py** seguendo i passaggi [precedenti](#open-a-work-folder) .

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

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **Spark: PySpark batch**oppure usare il tasto di scelta rapida CTRL + ALT + H.

6. Selezionare un cluster a cui inviare il processo PySpark:

   ![Inviare l'output del risultato del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Dopo aver inviato un processo Python, i log di invio vengono visualizzati nella finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati anche l'URL dell'interfaccia utente di Spark e l'URL dell'interfaccia utente di Yarn. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Eseguire l'integrazione con broker di identità di HDInsight

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Connettersi al cluster ESP HDInsight con ID Broker (HIB)

È possibile seguire la procedura normale per accedere alla sottoscrizione di Azure per connettersi al cluster ESP HDInsight con ID Broker (HIB). Dopo l'accesso, l'elenco cluster verrà visualizzato in Azure Explorer. Per altre istruzioni, vedere [Connettersi al cluster HDInsight](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Eseguire un processo hive/PySpark in un cluster ESP HDInsight con ID Broker (HIB)

Per eseguire un processo hive, è possibile seguire la procedura normale per inviare il processo al cluster ESP HDInsight con ID Broker (HIB). Per altre istruzioni, vedere [inviare query hive interattive e script batch hive](#submit-interactive-hive-queries-and-hive-batch-scripts) .

Per eseguire un processo PySpark interattivo, è possibile seguire la procedura normale per inviare il processo al cluster HDInsight ESP con ID Broker (HIB). Per altre istruzioni, vedere [inviare query PySpark interattive](#submit-interactive-pyspark-queries) .

Per eseguire un processo batch PySpark, è possibile seguire la procedura normale per inviare il processo al cluster HDInsight ESP con ID Broker (HIB). Per altre istruzioni, vedere [inviare un processo batch PySpark](#submit-pyspark-batch-job) .


## <a name="apache-livy-configuration"></a>Configurazione di Apache Livy

La configurazione di [Apache Tito](https://livy.incubator.apache.org/) è supportata. È possibile configurarlo nel **.VSCode\settings.js** file nella cartella dell'area di lavoro. Attualmente, la configurazione di Livy supporta solo script Python. Per ulteriori informazioni, vedere il [file Leggimi di Livio](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Come attivare la configurazione di Livio**

Metodo 1  
1. Dalla barra dei menu passare a preferenze **file**  >  **Preferences**  >  **Impostazioni**.
2. Nella casella **Cerca impostazioni** immettere **HDInsight Job Submission: Livio conf**.  
3. Selezionare **Edit in settings.json** (Modifica in settings.json) per il risultato della ricerca pertinente.

Il metodo 2 Invia un file e si noti che la `.vscode` cartella viene aggiunta automaticamente alla cartella di lavoro. È possibile visualizzare la configurazione di Livio selezionando **.vscode\settings.json**.

+ Impostazioni del progetto:

    ![Configurazione di HDInsight Apache Livio](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Per le impostazioni **driverMemory** e **executorMemory** , impostare il valore e l'unità. Ad esempio: 1g o 1024.

+ Configurazioni di Livio supportate:

    **Post/Batches** Corpo della richiesta

    | name | description | tipo |
    | --- | --- | --- |
    | file | File contenente l'applicazione da eseguire | Percorso (obbligatorio) |
    | proxyUser | Utente da rappresentare quando si esegue il processo | string |
    | className | Classe principale Java/Spark dell'applicazione | string |
    | args | Argomenti della riga di comando per l'applicazione | Elenco di stringhe |
    | jars | Jar da usare in questa sessione | Elenco di stringhe | 
    | pyFiles | File Python da usare in questa sessione | Elenco di stringhe |
    | files | File da usare in questa sessione | Elenco di stringhe |
    | driverMemory | Quantità di memoria da usare per il processo del driver | string |
    | driverCores | Numero di core da usare per il processo del driver | Int |
    | executorMemory | Quantità di memoria da usare per un processo executor | string |
    | executorCores | Numero di core da usare per ogni executor | Int |
    | numExecutors | Numero di executor da avviare per questa sessione | Int |
    | archives | Archivi da usare in questa sessione | Elenco di stringhe |
    | coda | Nome della coda YARN da inviare a| string |
    | name | Nome della sessione | string |
    | conf | Proprietà di configurazione Spark | Mappa di chiave=valore |

    Corpo della risposta l'oggetto batch creato.

    | name | description | tipo |
    | --- | ---| --- |
    | ID | ID sessione | Int |
    | appId | ID applicazione della sessione | string |
    | appInfo | Informazioni dettagliate sull'applicazione | Mappa di chiave=valore |
    | log | Righe di log | Elenco di stringhe |
    | state |Stato batch | string |

    > [!NOTE]
    > Quando si invia lo script, la configurazione di Livio assegnata viene visualizzata nel riquadro di output.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Eseguire l'integrazione con Azure HDInsight da Explorer

È possibile visualizzare in anteprima la tabella hive nei cluster direttamente tramite **Azure HDInsight** Explorer:

1. [Connettersi](#connect-to-an-azure-account) all'account Azure, se non è ancora stato fatto.

2. Selezionare l'icona di **Azure** dalla colonna più a sinistra.

3. Nel riquadro sinistro espandere **Azure: HDINSIGHT**. Sono elencate le sottoscrizioni e i cluster disponibili.

4. Espandere il cluster per visualizzare il database dei metadati hive e lo schema della tabella.

5. Fare clic con il pulsante destro del mouse sulla tabella hive. Ad esempio: **hivesampletable**. Selezionare **Anteprima**.

   ![Spark & hive per la tabella hive di Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Verrà visualizzata la finestra **Anteprima risultati** :

   ![Spark & hive per Visual Studio Code finestra risultati anteprima](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Pannello Risultati

   È possibile salvare l'intero risultato come file CSV, JSON o di Excel in un percorso locale oppure selezionare più righe.

- Pannello messaggi
   1. Quando il numero di righe nella tabella è maggiore di 100, viene visualizzato il messaggio seguente: "vengono visualizzate le prime 100 righe per la tabella hive".
   2. Quando il numero di righe nella tabella è minore o uguale a 100, viene visualizzato il messaggio seguente: "60 righe visualizzate per la tabella hive".
   3. Quando non è presente alcun contenuto nella tabella, viene visualizzato il messaggio seguente: " `0 rows are displayed for Hive table.` "

        >[!NOTE]
        >
        >In Linux installare XRITAGLIA per abilitare i dati di copia della tabella.
        >
        >![Spark & hive per Visual Studio Code in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Funzionalità aggiuntive

Spark & hive per Visual Studio Code supporta anche le funzionalità seguenti:

- **Completamento automatico di IntelliSense**. Suggerimenti popup per parole chiave, metodi, variabili e altri elementi di programmazione. Icone diverse rappresentano vari tipi di oggetti:

    ![Spark & strumenti hive per Visual Studio Code oggetti IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Indicatore di errore di IntelliSense**. Il servizio di linguaggio sottolineasce gli errori di modifica nello script hive.     
- **Evidenziazioni della sintassi**. Il servizio di linguaggio usa colori diversi per distinguere variabili, parole chiave, tipo di dati, funzioni e altri elementi di programmazione:

    ![Evidenziazioni della sintassi in Spark & Hive Tools per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Ruolo di sola lettura

Gli utenti a cui è assegnato il ruolo di sola lettura per il cluster non possono inviare processi al cluster HDInsight né visualizzare il database hive. Contattare l'amministratore del cluster per aggiornare il ruolo all' [**operatore cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) nel [portale di Azure](https://ms.portal.azure.com/). Se si dispone di credenziali Ambari valide, è possibile collegare manualmente il cluster usando le indicazioni seguenti.

### <a name="browse-the-hdinsight-cluster"></a>Esplorare il cluster HDInsight  

Quando si seleziona Azure HDInsight Explorer per espandere un cluster HDInsight, viene richiesto di collegare il cluster se si ha il ruolo di sola lettura per il cluster. Usare il metodo seguente per eseguire il collegamento al cluster usando le credenziali di Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Inviare il processo al cluster HDInsight

Quando si invia un processo a un cluster HDInsight, viene richiesto di collegare il cluster se si è in un ruolo di sola lettura per il cluster. Usare la procedura seguente per eseguire il collegamento al cluster usando le credenziali di Ambari.

### <a name="link-to-the-cluster"></a>Collegamento al cluster

1. Immettere un nome utente Ambari valido.
2. Immettere una password valida.

   ![Strumenti hive di Spark & per Visual Studio Code nomeutente](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & gli strumenti hive per Visual Studio Code password](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >È possibile usare `Spark / Hive: List Cluster` per controllare il cluster collegato:
  >
  >![Strumenti di Spark & hive per Visual Studio Code Reader collegati](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Sfogliare un account Data Lake Storage Gen2

Selezionare Azure HDInsight Explorer per espandere un account Data Lake Storage Gen2. Viene richiesto di immettere la chiave di accesso alle archiviazione se l'account Azure non ha accesso all'archiviazione Gen2. Dopo che la chiave di accesso è stata convalidata, l'account Data Lake Storage Gen2 viene espanso automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Inviare processi a un cluster HDInsight con Data Lake Storage Gen2

Inviare un processo a un cluster HDInsight usando Data Lake Storage Gen2. Viene richiesto di immettere la chiave di accesso alle archiviazione se l'account Azure non ha accesso in scrittura all'archiviazione Gen2. Dopo che la chiave di accesso è stata convalidata, il processo verrà inviato correttamente.

![Spark & strumenti hive per Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> È possibile ottenere la chiave di accesso per l'account di archiviazione dal portale di Azure. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Scollegare il cluster

1. Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi**, quindi immettere **Spark/hive: scollegare un cluster**.  

2. Selezionare un cluster da scollegare.  

3. Vedere la visualizzazione di **output** per la verifica.  

## <a name="sign-out"></a>Disconnetti  

Dalla barra dei menu passare a **Visualizza**  >  **riquadro comandi**e quindi immettere **Azure: disconnettersi**.

## <a name="known-issues"></a>Problemi noti
### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>MS-Python >= la versione 2020.5.78807 non è supportata in questa estensione 

"Impossibile connettersi al notebook di Jupyter". è un problema noto per Python Version >= 2020.5.78807. È consigliabile che gli utenti usino la versione **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** di MS-Python per evitare questo problema.

![Problemi noti di](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>Passaggi successivi

Per un video che illustra l'uso di Spark & hive per Visual Studio Code, vedere [spark & hive per Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
