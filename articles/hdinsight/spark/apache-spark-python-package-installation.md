---
title: Script action for Python packages with Jupyter on Azure HDInsight
description: Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129641"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script

> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)

HDInsight dispone di due installazioni Python incorporate nel cluster Spark, Anaconda Python 2.7 e Python 3.5. In alcuni casi, i clienti devono personalizzare l'ambiente Python, ad esempio l'installazione di pacchetti Python esterni o di un'altra versione di Python. In questo articolo viene illustrato come gestire in modo sicuro gli ambienti Python per un cluster Apache Spark in HDInsight.In this article, we show the best practice of safely managing Python environments for an [Apache Spark](./apache-spark-overview.md) cluster on HDInsight.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se non si dispone già di un cluster Spark in HDInsight, è possibile eseguire azioni di script durante la creazione del cluster.  Vedere la documentazione su [come usare azioni script personalizzate](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source ispirate ad Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere Sito Web delle domande frequenti sul supporto di Azure.For more [information,](https://azure.microsoft.com/support/faq/)see Azure Support FAQ website . Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

|Componente |Descrizione |
|---|---|
|Predefinito|Questi componenti sono preinstallati nei cluster HDInsight e forniscono funzionalità di base del cluster. Ad esempio, Apache Hadoop YARN Resource Manager, il linguaggio di query Apache Hive (HiveQL) e la libreria Mahout appartengono a questa categoria. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni del cluster Apache Hadoop incluse in HDInsight](../hdinsight-component-versioning.md).|
|Personalizzato|L'utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato dall'utente.|

> [!IMPORTANT]
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe essere in grado di risolvere il problema OPPURE richiedere di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto estremamente competente per la tecnologia specifica. Ad esempio, esistono molti siti della community che possono essere [https://stackoverflow.com](https://stackoverflow.com)utilizzati, ad esempio: forum MSDN per [HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Anche i progetti Apache [https://apache.org](https://apache.org)hanno siti di progetto su , per esempio: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Comprendere l'installazione predefinita di Python

HDInsight Spark cluster viene creato con l'installazione di Anaconda.HDInsight Spark cluster is created with Anaconda installation. Ci sono due installazioni Python nel cluster, Anaconda Python 2.7 e Python 3.5. La tabella seguente mostra le impostazioni predefinite di Python per Spark, Livy e Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Predefinito impostato su 2.7|N/D|
|Livy|Predefinito impostato su 2.7|N/D|
|Jupyter|Kernel Di PySpark|Kernel PySpark3|

## <a name="safely-install-external-python-packages"></a>Installare in modo sicuro i pacchetti Python esterni

Il cluster HDInsight dipende dall'ambiente Python incorporato, sia Python 2.7 che Python 3.5. L'installazione diretta di pacchetti personalizzati in tali ambienti predefiniti predefiniti può causare modifiche impreviste alla versione della libreria e interrompere ulteriormente il cluster. Per installare in modo sicuro pacchetti Python esterni personalizzati per le applicazioni Spark, attenersi alla seguente procedura.

1. Creare l'ambiente virtuale Python utilizzando conda. Un ambiente virtuale fornisce uno spazio isolato per i progetti senza romperla con gli altri. Quando si crea l'ambiente virtuale Python, è possibile specificare la versione python che si desidera utilizzare. Si noti che è comunque necessario creare un ambiente virtuale anche se si desidera utilizzare Python 2.7 e 3.5. In questo modo si garantisce che l'ambiente predefinito del cluster non venga interrotto. Eseguire azioni di script nel cluster per tutti i nodi con script seguente per creare un ambiente virtuale Python.Run script actions on your cluster for all nodes with below script to create a Python virtual environment.

    -   `--prefix`specifica un percorso in cui vive un ambiente virtuale conda. Esistono diverse configurazioni che devono essere modificate ulteriormente in base al percorso specificato qui. In questo esempio viene usato il py35new, poiché il cluster dispone già di un ambiente virtuale esistente denominato py35.
    -   `python=`specifica la versione Python per l'ambiente virtuale. In questo esempio viene usata la versione 3.5, la stessa versione del cluster costruito in uno. È anche possibile usare altre versioni di Python per creare l'ambiente virtuale.
    -   `anaconda`specifica il package_spec come anaconda per installare i pacchetti Anaconda nell'ambiente virtuale.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installare i pacchetti Python esterni nell'ambiente virtuale creato, se necessario. Eseguire azioni di script nel cluster per tutti i nodi con script seguente per installare pacchetti Python esterni. È necessario disporre del privilegio sudo per scrivere i file nella cartella dell'ambiente virtuale.

    Per un elenco completo dei pacchetti disponibili, è possibile eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Per esempio, è possibile installare pacchetti resi disponibili tramite [conda-forge](https://conda-forge.org/feedstocks/).

    Utilizzare il seguente comando se si desidera installare una libreria con la sua versione più recente:

    - Usa canale conda:

        -   `seaborn`è il nome del pacchetto che si desidera installare.
        -   `-n py35new`specificare il nome dell'ambiente virtuale che viene appena creato. Assicurarsi di modificare il nome in modo corrispondente in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Oppure utilizzare PyPi `seaborn` repository, modificare e `py35new` di conseguenza:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Utilizzare il seguente comando se si desidera installare una libreria con una versione specifica:

    - Usa canale conda:

        -   `numpy=1.16.1`è il nome e la versione del pacchetto che si desidera installare.
        -   `-n py35new`specificare il nome dell'ambiente virtuale che viene appena creato. Assicurarsi di modificare il nome in modo corrispondente in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Oppure utilizzare PyPi `numpy==1.16.1` repository, modificare e `py35new` di conseguenza:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    se non si conosce il nome dell'ambiente virtuale, è possibile SSH nel nodo head del cluster ed eseguilo `/usr/bin/anaconda/bin/conda info -e` per visualizzare tutti gli ambienti virtuali.

3. Modificare le configurazioni Spark e Livy e puntare all'ambiente virtuale creato.

    1. Aprire l'interfaccia utente di Ambari, passare alla pagina Spark2, alla scheda Configs.

        ![Modificare la configurazione di Spark e Livy tramite Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Espandere Avanzato livy2-env, aggiungere sotto le istruzioni in basso. Se l'ambiente virtuale è stato installato con un prefisso diverso, modificare il percorso in modo corrispondente.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Modificare Livy config tramite Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Espandere Avanzate spark2-env, sostituire l'istruzione di esportazione PYSPARK_PYTHON esistente nella parte inferiore. Se l'ambiente virtuale è stato installato con un prefisso diverso, modificare il percorso in modo corrispondente.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Modificare la configurazione di Spark tramite AmbariChange Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Salvare le modifiche e riavviare i servizi interessati. Queste modifiche richiedono il riavvio del servizio Spark2.These changes need a restart of Spark2 service. Ambari UI richiederà un promemoria di riavvio richiesto, fare clic su Riavvia per riavviare tutti i servizi interessati.

        ![Modificare la configurazione di Spark tramite AmbariChange Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Se si desidera utilizzare il nuovo ambiente virtuale creato su Jupyter. È necessario modificare le configurazioni Jupyter e riavviare Jupyter. Eseguire azioni di script su tutti i nodi di intestazione con istruzione below per indirizzare Jupyter al nuovo ambiente virtuale creato. Assicurarsi di modificare il percorso del prefisso specificato per l'ambiente virtuale. Dopo aver eseguito questa azione di script, riavviare il servizio Jupyter tramite Ambari UI per rendere disponibile questa modifica.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    È possibile confermare l'ambiente Python in Jupyter Notebook eseguendo il codice seguente:You could double confirm the Python environment in Jupyter Notebook by running below code:

    ![Controllare la versione Python in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema noto

C'è un bug noto per Anaconda versione 4.7.11, 4.7.12 e 4.8.0. Se le azioni di `"Collecting package metadata (repodata.json): ...working..."` script si `"Python script has been killed due to timeout after waiting 3600 secs"`bloccano e non riescono con . È possibile scaricare [questo script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) ed eseguirlo come azioni di script su tutti i nodi per risolvere il problema.

Per verificare la versione di Anaconda, è possibile `/usr/bin/anaconda/bin/conda --v`SSH nel nodo dell'intestazione del cluster ed eseguire .

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)
* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
