---
title: Installare Jupyter localmente e connetterlo a Spark in Azure HDInsight
description: Informazioni su come installare il notebook di Jupyter in locale nel computer e su come connetterlo a un cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 96b2e7deff464f00ced4457a514ac833a90bd42d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873892"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installare un notebook di Jupyter in locale e connetterlo ad Apache Spark in HDInsight

Questo articolo illustra come installare Jupyter notebook con i kernel PySpark (per Python) e Apache Spark (per scala) personalizzati con Magic Spark. Si connette quindi il notebook a un cluster HDInsight.

Sono necessari quattro passaggi principali per l'installazione di Jupyter e la connessione a Apache Spark in HDInsight.

* Configurare il cluster Spark.
* Installare Jupyter Notebook.
* Installare i kernel PySpark e Spark con il magic Spark.
* Configurare il magic Spark per l'accesso al cluster Spark in HDInsight.

Per altre informazioni sui kernel personalizzati e su Spark Magic, vedere [kernel disponibili per i notebook di Jupyter con Apache Spark cluster Linux in HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md). Il notebook locale si connette al cluster HDInsight.

* Familiarità nell'uso di Jupyter Notebook con Spark in HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Installare Jupyter Notebook nel computer

Installare Python prima di installare Jupyter notebook. La [distribuzione anaconda](https://www.anaconda.com/download/) installerà sia, Python che Jupyter notebook.

Scaricare il [programma di installazione di Anaconda](https://www.anaconda.com/download/) per la piattaforma in uso ed eseguirlo. Quando si esegue l'installazione guidata, assicurarsi di selezionare l'opzione per l'aggiunta di Anaconda alla variabile PATH.  Vedere anche [installazione di Jupyter con Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installare Spark Magic

1. Immettere uno dei comandi seguenti per installare Spark Magic. Vedere anche la [documentazione di sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Versione del cluster | Comando di installazione |
    |---|---|
    |v 3.6 e v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Assicurarsi `ipywidgets` che sia installato correttamente eseguendo il comando seguente:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installare i kernel PySpark e Spark

1. Identificare `sparkmagic` la posizione in cui è installato immettendo il comando seguente:

    ```cmd
    pip show sparkmagic
    ```

    Modificare quindi la directory di lavoro nel **percorso** identificato con il comando precedente.

1. Dalla nuova directory di lavoro, immettere uno o più dei comandi seguenti per installare i kernel desiderati:

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. facoltativo. Immettere il comando seguente per abilitare l'estensione del server:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurare il magic Spark per la connessione al cluster HDInsight Spark

In questa sezione viene configurata la magia di Spark installata in precedenza per connettersi a un cluster Apache Spark.

1. Avviare la shell di Python con il comando seguente:

    ```cmd
    python
    ```

2. Le informazioni di configurazione di Jupyter sono in genere archiviate nella home directory dell'utente. Immettere il comando seguente per identificare la home directory e creare una cartella denominata ** \. sparkmagic**.  Il percorso completo sarà output.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. All'interno della cartella `.sparkmagic` , creare un file denominato **config.js** e aggiungere il frammento di codice JSON seguente al suo interno.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Apportare le modifiche seguenti al file:

    |Valore del modello | Nuovo valore |
    |---|---|
    |NOME utente|Accesso al cluster. il valore predefinito è `admin` .|
    |CLUSTERDNSNAME|Nome cluster|
    |{BASE64ENCODEDPASSWORD}|Password con codifica Base64 per la password effettiva.  È possibile generare una password base64 all'indirizzo [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Usare se si usa `sparkmagic 0.12.7` (cluster v 3.5 e v 3.6).  Se si usa `sparkmagic 0.2.3` (cluster v 3.4), sostituire con `"should_heartbeat": true` .|

    È possibile visualizzare un file di esempio completo in [config.jsdi esempio in](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Gli heartbeat vengono inviati per assicurare che le sessioni non vengano perse. Quando un computer va in sospensione o viene arrestato, l'heartbeat non verrà inviato e la sessione verrà quindi eliminata. Per disabilitare questo comportamento per i cluster v3.4, è possibile impostare la configurazione di Livy `livy.server.interactive.heartbeat.timeout` su `0` dall'interfaccia utente di Ambari. Per i cluster v3.5, se non si imposta la configurazione 3.5 precedente, la sessione non verrà eliminata.

5. Avviare Jupyter. Usare il comando seguente dal prompt dei comandi.

    ```cmd
    jupyter notebook
    ```

6. Verificare che sia possibile usare il Magic Spark disponibile con i kernel. Completare questi passaggi.

    a. Creare un nuovo notebook. Nell'angolo destro selezionare **nuovo**. Verranno visualizzati il kernel predefinito **Python 2** o **Python 3** e i kernel installati. I valori effettivi possono variare a seconda delle scelte di installazione.  Selezionare **PySpark**.

    ![Kernel disponibili in Jupyter notebook](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernel nel notebook di Jupyter")

    > [!IMPORTANT]  
    > Dopo aver selezionato **nuovo** verificare la Shell per individuare eventuali errori.  Se viene visualizzato l'errore `TypeError: __init__() got an unexpected keyword argument 'io_loop'` è possibile che si verifichi un problema noto con determinate versioni di tornado.  In tal caso, arrestare il kernel e quindi eseguire il downgrade dell'installazione di Tornado con il comando seguente: `pip install tornado==4.5.3` .

    b. Eseguire il frammento di codice seguente.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se è stato possibile recuperare l'output, viene verificata la connessione al cluster HDInsight.

    Se si vuole aggiornare la configurazione del notebook per connettersi a un cluster diverso, aggiornare il config.jscon il nuovo set di valori, come illustrato nel passaggio 3, sopra.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Perché installare Jupyter nel computer locale

Motivi per installare Jupyter nel computer e quindi connetterlo a un cluster Apache Spark in HDInsight:

* Offre la possibilità di creare i notebook in locale, testare l'applicazione in un cluster in esecuzione e quindi caricare i notebook nel cluster. Per caricare i notebook nel cluster, è possibile caricarli usando il notebook di Jupyter che esegue o il cluster o salvarli `/HdiNotebooks` nella cartella dell'account di archiviazione associato al cluster. Per altre informazioni sul modo in cui i notebook vengono archiviati nel cluster, vedere la sezione [Dove sono archiviati i notebook?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Con i notebook disponibili in locale è possibile connettersi a cluster Spark diversi in base alle esigenze dell'applicazione.
* È possibile usare GitHub per implementare un sistema di controllo del codice sorgente e usare il controllo della versione per il notebook. È anche possibile avere a disposizione un ambiente di collaborazione in cui più utenti possono lavorare allo stesso notebook.
* È possibile lavorare con i notebook in locale anche senza avere un cluster. È necessario avere un cluster solo per eseguire i test dei notebook, non per gestire manualmente i notebook o un ambiente di sviluppo.
* Potrebbe essere più semplice configurare un ambiente di sviluppo locale diverso da quello per configurare l'installazione di Jupyter nel cluster.  È possibile sfruttare tutti i software installati localmente senza configurare uno o più cluster remoti.

> [!WARNING]  
> Con Jupyter installato nel computer locale più utenti possono eseguire contemporaneamente lo stesso notebook nello stesso cluster Spark. In questo caso, vengono create più sessioni di Livy. Se si verifica un problema e si vuole eseguire il debug, tenere traccia della sessione di Livy che appartiene l'utente sarà un'attività complessa.  

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Kernel per il notebook di Jupyter in Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con notebook di Jupyter in Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
