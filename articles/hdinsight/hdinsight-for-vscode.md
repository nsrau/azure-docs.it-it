---
title: Azure HDInsight for Visual Studio Code
description: Informazioni su come usare Spark & Hive Tools (Azure HDInsight) per Visual Studio Code. Utilizzare gli strumenti per creare e inviare query e script.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: de433d85c2f04a7140fbcb918730218ac3a05e54
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878630"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Usare gli strumenti Spark & Hive per il codice di Visual StudioUse Spark & Hive Tools for Visual Studio Code

Informazioni su come usare Apache Spark & Hive Tools per Visual Studio Code. Usa gli strumenti per creare e inviare processi batch Apache Hive, query Hive interattive e script PySpark per Apache Spark. In primo luogo verrà descritto come installare Spark & Hive Tools in Visual Studio Code. Quindi verrà illustrato come inviare i processi a Spark & Hive Tools.  

Spark & Hive Tools possono essere installati su piattaforme supportate da Visual Studio Code. Tenere presente i prerequisiti seguenti per piattaforme diverse.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questo articolo, è necessario quanto segue:

- Disporre di un cluster HDInsight di Azure. Per creare un cluster, vedere [Introduzione a HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). In alternativa, usare un cluster Spark e Hive che supporta un endpoint Apache Livy.
- [Codice di Visual Studio](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono è richiesto solo per Linux e macOS.
- [Ambiente interattivo PySpark per Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Una directory locale. Questo articolo usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installare Spark & Hive Tools

Dopo aver soddisfatto i prerequisiti, è possibile installare Spark & Hive Tools per Visual Studio Code attenendosi alla seguente procedura:

1. Aprire Visual Studio Code.

2. Dalla barra dei menu, accedere a **Visualizza** > **estensioni**.

3. Nella casella di ricerca immettere **Spark & Hive**.

4. Selezionare **Strumenti Spark & Hive** dai risultati della ricerca e quindi selezionare **Installa**:

   ![Installazione di Spark & Hive per Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Selezionare **Ricarica** quando necessario.

## <a name="open-a-work-folder"></a>Aprire una cartella di lavoro

Per aprire una cartella di lavoro e creare un file in Visual Studio Code, attenersi alla seguente procedura:

1. Dalla barra dei menu, accedere a Cartella > **aperta** **file...**  >  **,** quindi selezionare il pulsante **Seleziona cartella.** La cartella verrà visualizzata nella visualizzazione **Explorer** a sinistra.

2. Nella vista **Esplora risorse,** selezionate la cartella **HDexample,** quindi selezionate l'icona **Nuovo file** accanto alla cartella di lavoro:

   ![icona del nuovo file del codice di Visual Studio](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Assegnare un nome al `.hql` nuovo file utilizzando `.py` l'estensione (query Hive) o (script Spark). Questo esempio usa **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Configurare l'ambiente di Azure

Per un utente cloud nazionale, seguire questi passaggi per impostare prima l'ambiente Azure e quindi usare il comando Azure: Accedi per accedere ad Azure:For a national cloud user, follow these steps to set the Azure environment first, and then use the **Azure: Sign In** command to sign in to Azure:

1. Passare a**Impostazioni****preferenze** >  **file** > .
2. Eseguire la ricerca nella stringa seguente: **Azure: Cloud**.
3. Selezionare il cloud nazionale dall'elenco:

   ![Impostare la configurazione della voce di accesso predefinita](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Connettersi a un account AzureConnect to an Azure account

Prima di poter inviare script ai cluster da Visual Studio Code, è necessario connettersi all'account di Azure o collegare un cluster. Utilizzare le credenziali di nome utente e password Apache Ambari o un account aggiunto al dominio. Seguire questi passaggi per connettersi ad Azure:Follow these steps to connect to Azure:

1. Dalla barra dei menu, passare a **Visualizza** > **tavolozza comandi...** e immettere **Azure: Accedi:**

    ![Strumenti Spark & Hive per l'accesso al codice di Visual StudioSpark & Hive Tools for Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Seguire le istruzioni di accesso per accedere ad Azure.Follow the sign-in instructions to sign in to Azure. Dopo aver connesso, il nome dell'account Azure viene visualizzato nella barra di stato nella parte inferiore della finestra Codice di Visual Studio.After you're connected, your Azure account name shows on the status bar at the bottom of the Visual Studio Code window.  

## <a name="link-a-cluster"></a>Collegare un cluster

### <a name="link-azure-hdinsight"></a>Collegamento: Azure HDInsight

È possibile collegare un cluster normale utilizzando un nome utente gestito da [Apache Ambari](https://ambari.apache.org/)oppure un cluster Hadoop protetto di Enterprise Security Pack utilizzando un nome utente di dominio (ad esempio: `user1@contoso.com`).

1. Dalla barra dei menu, accedere a **Visualizza** > **tavolozza dei comandi...** e **immettere Spark / Hive: Link a Cluster**.

   ![Comando del cluster di collegamento della tavolozza dei comandi](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Selezionare il tipo di cluster collegato **Azure HDInsight**.

3. Immettere l'URL del cluster HDInsight.

4. Inserisci il tuo nome utente Ambari; l'impostazione predefinita è **admin**.

5. Inserisci la password Ambari.

6. Selezionare il tipo di cluster.

7. Impostare il nome visualizzato del cluster (facoltativo).

8. Esaminare la visualizzazione **OUTPUT** per verificare.

   > [!NOTE]  
   > Il nome utente e la password collegati vengono usati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.  

### <a name="link-generic-livy-endpoint"></a>Collegamento: Endpoint di Livy generico

1. Dalla barra dei menu, accedere a **Visualizza** > **tavolozza dei comandi...** e **immettere Spark / Hive: Link a Cluster**.

2. Selezionare il tipo di cluster collegato**Generic Livy Endpoint** (Endpoint Livy generico).

3. Immettere l'endpoint Livy generico. Ad esempio:\:http //10.172.41.42:18080.

4. Selezionare il tipo di autorizzazione **Basic** (Di base) o **None** (Nessuna).  Se si seleziona **Di base**:  
    &emsp;a. Inserisci il tuo nome utente Ambari; l'impostazione predefinita è **admin**.  
    &emsp;b. Inserisci la password Ambari.

5. Esaminare la visualizzazione **OUTPUT** per verificare.

## <a name="list-clusters"></a>Elencare i cluster

1. Dalla barra dei menu, accedere a **Visualizza** > **tavolozza comandi...** e **immettere Spark / Hive: List Cluster**.

2. Selezionare la sottoscrizione desiderata.

3. Esaminare la visualizzazione **OUTPUT**. Questa visualizzazione mostra i cluster collegati (o cluster) e tutti i cluster nella sottoscrizione di Azure:This view shows your linked (or clusters) and all the clusters under your Azure subscription:

    ![Impostare una configurazione del cluster predefinito](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Impostare il cluster predefinito

1. Riaprire la cartella **HDexample** descritta [in precedenza,](#open-a-work-folder)se chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza.](#open-a-work-folder) Si apre nell'editor di script.

3. Fare clic con il pulsante destro del mouse sull'editor di script, quindi **selezionare Spark/Hive: Imposta cluster predefinito**.  

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Selezionare un cluster come predefinito per il file di script corrente. Gli strumenti aggiornano automaticamente il **file . File di configurazione VSCode-settings.json:**

   ![Impostare la configurazione del cluster predefinito](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Inviare query Hive interattive e script batch HiveSubmit interactive Hive queries and Hive batch scripts

Con Spark & Hive Tools per Visual Studio Code, è possibile inviare query Hive interattive e script batch Hive ai cluster.

1. Riaprire la cartella **HDexample** descritta [in precedenza,](#open-a-work-folder)se chiusa.  

2. Selezionare il file **HelloWorld.hql** creato [in precedenza.](#open-a-work-folder) Si apre nell'editor di script.

3. Copiare e incollare il codice seguente nel file Hive e quindi salvarlo:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Fare clic con il pulsante destro del mouse sull'editor di script e selezionare **Hive: Interattivo** per inviare la query oppure usare la scelta rapida da tastiera Ctrl .  Selezionare **Hive: Batch** per inviare lo script oppure usare la scelta rapida da tastiera Ctrl .  

6. Se non è stato specificato un cluster predefinito, selezionare un cluster. Gli strumenti consentono inoltre di inviare un blocco di codice anziché l'intero file di script utilizzando il menu di scelta rapida. Dopo alcuni istanti, i risultati della query vengono visualizzati in una nuova scheda:

   ![Risultato della query Apache Hive interattiva](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Pannello RISULTATI:** è possibile salvare l'intero risultato come file CSV, JSON o Excel in un percorso locale o semplicemente selezionare più righe.

    - **Pannello MESSAGGI:** quando si seleziona un numero **di riga,** si passa alla prima riga dello script in esecuzione.

## <a name="submit-interactive-pyspark-queries"></a>Inviare query PySpark interattive

Per inviare query PySpark interattive, attenersi alla seguente procedura:

1. Riaprire la cartella **HDexample** descritta [in precedenza,](#open-a-work-folder)se chiusa.  

2. Creare un nuovo file **HelloWorld.py** seguendo la procedura [precedente.](#open-a-work-folder)

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

4. [Connettersi](#connect-to-an-azure-account) all'account Azure o collegare un cluster se non è ancora stato fatto.

5. Selezionare tutto il codice, fare clic con il pulsante destro del mouse sull'editor di script e selezionare **Spark: PySpark Interactive** per inviare la query. In alternativa, è possibile utilizzare la scelta rapida da tastiera Ctrl .

   ![Menu di scelta rapida PySpark Interactive (PySpark interattivo)](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Selezionare il cluster, se non è stato specificato un cluster predefinito. Dopo alcuni istanti, i risultati di **Python Interactive** vengono visualizzati in una nuova scheda. Gli strumenti consentono inoltre di inviare un blocco di codice anziché l'intero file di script utilizzando il menu di scelta rapida:

   ![Finestra di Python interattivo per PySpark interattivo](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Immettete **%%info**, quindi premete Maiusc e Invio per visualizzare le informazioni sul lavoro (opzionale):

   ![informazioni sul lavoro di visualizzazione interattiva di pyspark](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Lo strumento supporta anche la query **Spark SQL:**

   ![Pyspark Risultato della visualizzazione interattiva](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Lo stato di invio viene visualizzato a sinistra della barra di stato inferiore quando si eseguono query. Non inviare altre query se lo stato è impostato su **PySpark Kernel (busy)** (Kernel PySpark - Occupato).  

   > [!NOTE]
   >
   > Quando Python **Extension Enabled** è deselezionato nelle impostazioni (è selezionato per impostazione predefinita), i risultati dell'interazione pyspark inviati utilizzeranno la vecchia finestra:
   >
   > ![Estensione Python per PySpark interattivo disabilitata](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Inviare il processo batch PySpark

1. Riaprire la cartella **HDexample** descritta [in precedenza,](#open-a-work-folder)se chiusa.  

2. Creare un nuovo file **BatchFile.py** seguendo la procedura [precedente.](#open-a-work-folder)

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

5. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare **Spark: PySpark Batch**oppure utilizzare la scelta rapida da tastiera Ctrl .

6. Selezionare un cluster per inviare il processo PySpark a:

   ![Invia output dei risultati del processo Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Dopo aver inviato un processo Python, i log di invio vengono visualizzati nella finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati anche l'URL dell'interfaccia utente Spark e l'URL dell'interfaccia utente Di filati. È possibile aprire l'URL in un Web browser per tenere traccia dello stato del processo.

## <a name="apache-livy-configuration"></a>Configurazione di Apache Livy

La configurazione di [Apache Livy](https://livy.incubator.apache.org/) è supportata. È possibile configurarlo nel **file . File VSCode-settings.json** nella cartella dell'area di lavoro. Attualmente, la configurazione di Livy supporta solo script Python. Per ulteriori informazioni, vedere [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Come attivare la configurazione di Livy**

Metodo 1  
1. Dalla barra dei menu, accedere a**Impostazioni****preferenze** >  **file** > .
2. Nella casella **Impostazioni di ricerca** immettere **HDInsight Job Submission: Livy Conf**.  
3. Selezionare **Edit in settings.json** (Modifica in settings.json) per il risultato della ricerca pertinente.

Metodo 2 Inviare un file `.vscode` e notare che la cartella viene aggiunta automaticamente alla cartella di lavoro. È possibile visualizzare la configurazione di Livy selezionando **.vscode.settings.json**.

+ Impostazioni del progetto:

    ![Configurazione di HDInsight Apache Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Per le impostazioni **driverMemory** ed **executorMemory,** impostare il valore e l'unità. Ad esempio: 1g o 1024m.

+ Configurazioni Livy supportate:

    **POST /batch** Corpo della richiesta

    | name | description | type |
    | --- | --- | --- |
    | file | File contenente l'applicazione da eseguire | Percorso (obbligatorio) |
    | proxyUser | Utente da rappresentare quando si esegue il processo | string |
    | className | Classe principale Java/Spark dell'applicazione | string |
    | args | Argomenti della riga di comando per l'applicazione | Elenco di stringhe |
    | jars | Jar da utilizzare in questa sessione | Elenco di stringhe | 
    | pyFiles | File Python da usare in questa sessione | Elenco di stringhe |
    | files | File da utilizzare in questa sessione | Elenco di stringhe |
    | driverMemory | Quantità di memoria da usare per il processo del driver | string |
    | driverCores | Numero di core da usare per il processo del driver | Int |
    | executorMemory | Quantità di memoria da usare per un processo executor | string |
    | executorCores | Numero di core da usare per ogni executor | Int |
    | numExecutors | Numero di executor da avviare per questa sessione | Int |
    | archives | Archivi da usare in questa sessione | Elenco di stringhe |
    | coda | Nome della coda YARN da inviare| string |
    | name | Nome di questa sessione | string |
    | conf | Proprietà di configurazione Spark | Mappa di chiave=valore |

    Corpo della risposta Oggetto Batch creato.

    | name | description | type |
    | --- | ---| --- |
    | ID | ID sessione | Int |
    | appId | ID applicazione di questa sessione | string |
    | appInfo | Informazioni dettagliate sull'applicazione | Mappa di chiave=valore |
    | log | Righe di registro | Elenco di stringhe |
    | state |Stato batch | string |

    > [!NOTE]
    > La configurazione Livy assegnata viene visualizzata nel riquadro di output quando si invia lo script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Eseguire l'integrazione con Azure HDInsight da Explorer

È possibile visualizzare in anteprima Hive Table nei cluster direttamente tramite Azure HDInsight Explorer:You can preview Hive Table in your clusters directly through the **Azure HDInsight** explorer:

1. [Connettersi](#connect-to-an-azure-account) all'account Azure, se non è ancora stato fatto.

2. Selezionare l'icona di **Azure** dalla colonna più a sinistra.

3. Nel riquadro sinistro, espandere **A'URE: HDINSIGHT**. Vengono elencate le sottoscrizioni e i cluster disponibili.

4. Espandere il cluster per visualizzare il database dei metadati Hive e lo schema della tabella.

5. Fare clic con il pulsante destro del mouse sulla tabella Hive. Ad esempio: **hivesampletable**. Selezionare **Anteprima**.

   ![Tabella hive di anteprima di Spark & Hive per Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Viene visualizzata la finestra **Anteprima risultati:**

   ![Finestra dei risultati dell'anteprima di Spark & Hive per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Pannello RISULTATI

   È possibile salvare l'intero risultato come file CSV, JSON o Excel in un percorso locale o semplicemente selezionare più righe.

- Pannello MESSAGGIO
   1. Quando il numero di righe nella tabella è maggiore di 100, viene visualizzato il messaggio seguente: "Le prime 100 righe vengono visualizzate per la tabella Hive".
   2. Quando il numero di righe nella tabella è minore o uguale a 100, viene visualizzato il messaggio seguente: "60 righe vengono visualizzate per la tabella Hive".
   3. Quando non c'è contenuto nella tabella, viene`0 rows are displayed for Hive table.`visualizzato il seguente messaggio: " "

        >[!NOTE]
        >
        >In Linux, installare xclip per abilitare i dati della tabella di copia.
        >
        >![Spark & Hive per il codice di Visual Studio in Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Funzionalità aggiuntive

Spark & Hive per Visual Studio Code supporta anche le funzionalità seguenti:Spark & Hive for Visual Studio Code also supports the following features:

- **Completamento automatico di IntelliSense**. Vengono visualizzati suggerimenti per parole chiave, metodi, variabili e altri elementi di programmazione. Icone diverse rappresentano vari tipi di oggetti:

    ![Spark & Hive Tools per gli oggetti IntelliSense del codice di Visual StudioSpark & Hive Tools for Visual Studio Code IntelliSense objects](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Indicatore di errore IntelliSense**. Il servizio di linguaggio sottolinea gli errori di modifica nello script Hive.The language service underlines editing errors in the Hive script.     
- **La sintassi evidenzia**. Il servizio di linguaggio usa colori diversi per differenziare variabili, parole chiave, tipi di dati, funzioni e altri elementi di programmazione:The language service uses different colors to differentiate variables, keywords, data type, functions, and other programming elements:

    ![Evidenziazioni della sintassi in Spark & Hive Tools per Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Ruolo di sola lettura

Gli utenti a cui è assegnato il ruolo di sola lettura per il cluster non possono inviare processi al cluster HDInsight né visualizzare il database Hive. Contattare l'amministratore del cluster per aggiornare il ruolo a [**Operatore cluster HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) nel portale di [Azure.](https://ms.portal.azure.com/) Se si dispone di credenziali Ambari valide, è possibile collegare manualmente il cluster utilizzando le indicazioni seguenti.

### <a name="browse-the-hdinsight-cluster"></a>Esplorare il cluster HDInsight  

Quando si seleziona Esplora hdInsight di Azure per espandere un cluster HDInsight, viene richiesto di collegare il cluster se si dispone del ruolo solo lettore per il cluster. Utilizzare il metodo seguente per creare un collegamento al cluster utilizzando le credenziali Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Inviare il processo al cluster HDInsightSubmit the job to the HDInsight cluster

Quando si invia un processo a un cluster HDInsight, viene richiesto di collegare il cluster se si è nel ruolo solo lettore per il cluster. Utilizzare la procedura seguente per creare un collegamento al cluster utilizzando le credenziali Ambari.

### <a name="link-to-the-cluster"></a>Collegamento al cluster

1. Inserisci un nome utente Ambari valido.
2. Immettere una password valida.

   ![Spark & Hive Tools for Visual Studio Code Username](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive Tools per la password del codice di Visual Studio](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >È possibile `Spark / Hive: List Cluster` utilizzare per controllare il cluster collegato:You can use to check the linked cluster:
  >
  >![Spark & Hive Tools per Visual Studio Code Reader collegati](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Esplorare un account Data Lake Storage Gen2

Selezionare Esplora hdInsight di Azure per espandere un account Data Lake Storage Gen2.Select the Azure HDInsight explorer to expand a Data Lake Storage Gen2 account. Viene richiesto di immettere la chiave di accesso all'archiviazione se l'account Azure non ha accesso all'archiviazione Gen2.You're prompted to enter the storage access key if your Azure account has no access to Gen2 storage. Dopo la convalida della chiave di accesso, l'account Data Lake Storage Gen2 viene espanso automaticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Inviare processi a un cluster HDInsight con Data Lake Storage Gen2

Inviare un processo a un cluster HDInsight usando Data Lake Storage Gen2.Submit a job to an HDInsight cluster using Data Lake Storage Gen2. Viene richiesto di immettere la chiave di accesso all'archiviazione se l'account Azure non dispone dell'accesso in scrittura all'archiviazione Gen2.You're prompted to enter the storage access key if your Azure account has no write access to Gen2 storage. Dopo la convalida della chiave di accesso, il processo verrà inviato correttamente.

![Strumenti Spark & Hive per Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> È possibile ottenere la chiave di accesso per l'account di archiviazione dal portale di Azure.You can get the access key for the storage account from the Azure portal. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Scollegare il cluster

1. Dalla barra dei menu, andare a **Visualizza** > **tavolozza dei**comandi , quindi **immettere Spark / Hive: Scollega un cluster**.  

2. Selezionare un cluster da scollegare.  

3. Vedere la visualizzazione **OUTPUT** per la verifica.  

## <a name="sign-out"></a>Disconnessione  

Dalla barra dei menu passare a **Visualizza** > **tavolozza comandi**e quindi immettere **Azure: Esci**.

## <a name="next-steps"></a>Passaggi successivi

Per un video che illustra l'utilizzo di Spark & Hive per Visual Studio Code, vedere [Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
