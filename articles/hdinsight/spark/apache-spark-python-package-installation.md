---
title: Azione script per pacchetti Python con Jupyter in Azure HDInsight
description: Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80129641"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script

> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)

HDInsight include due installazioni di Python predefinite nel cluster Spark, Anaconda Python 2,7 e Python 3,5. In alcuni casi, i clienti devono personalizzare l'ambiente Python, ad esempio l'installazione di pacchetti Python esterni o un'altra versione di Python. Questo articolo illustra la procedura consigliata per gestire in modo sicuro gli ambienti Python per un cluster [Apache Spark](./apache-spark-overview.md) in HDInsight.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se non si dispone già di un cluster Spark in HDInsight, è possibile eseguire azioni script durante la creazione del cluster.  Vedere la documentazione su [come usare azioni script personalizzate](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source ispirate ad Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per ulteriori informazioni, vedere il [sito Web domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

|Componente |Descrizione |
|---|---|
|Predefinito|Questi componenti sono preinstallati nei cluster HDInsight e forniscono funzionalità di base del cluster. Ad esempio, Apache Hadoop YARN Gestione risorse, il linguaggio di query Apache Hive (HiveQL) e la libreria Mahout appartengono a questa categoria. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni del cluster Apache Hadoop incluse in HDInsight](../hdinsight-component-versioning.md).|
|Personalizzato|L'utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato dall'utente.|

> [!IMPORTANT]
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe essere in grado di risolvere il problema OPPURE richiedere di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto estremamente competente per la tecnologia specifica. Ad esempio, è possibile usare molti siti della community, come il [Forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com). Anche i progetti Apache hanno siti di [https://apache.org](https://apache.org)progetto in, ad esempio: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Informazioni sull'installazione predefinita di Python

Il cluster HDInsight Spark viene creato con l'installazione di Anaconda. Nel cluster sono presenti due installazioni di Python, Anaconda Python 2,7 e Python 3,5. La tabella seguente illustra le impostazioni predefinite di Python per Spark, Titon e Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Impostazione predefinita su 2,7|N/D|
|Livy|Impostazione predefinita su 2,7|N/D|
|Jupyter|Kernel PySpark|Kernel PySpark3|

## <a name="safely-install-external-python-packages"></a>Installare in modo sicuro pacchetti Python esterni

Il cluster HDInsight dipende dall'ambiente Python incorporato, sia Python 2,7 che Python 3,5. L'installazione diretta di pacchetti personalizzati in questi ambienti predefiniti predefiniti può causare modifiche impreviste della versione della libreria e suddividere ulteriormente il cluster. Per installare in modo sicuro pacchetti Python esterni personalizzati per le applicazioni Spark, seguire questa procedura.

1. Creare un ambiente virtuale Python usando conda. Un ambiente virtuale fornisce uno spazio isolato per i progetti senza compromettere altri elementi. Quando si crea l'ambiente virtuale Python, è possibile specificare la versione di Python che si vuole usare. Si noti che è comunque necessario creare un ambiente virtuale anche se si vuole usare Python 2,7 e 3,5. Per assicurarsi che l'ambiente predefinito del cluster non venga interrotto. Eseguire azioni script nel cluster per tutti i nodi con lo script seguente per creare un ambiente virtuale Python.

    -   `--prefix`Specifica un percorso in cui risiede un ambiente virtuale conda. Sono disponibili diverse configurazioni che devono essere modificate ulteriormente in base al percorso specificato. In questo esempio viene usato py35new, perché il cluster ha un ambiente virtuale esistente denominato PY35 già.
    -   `python=`Specifica la versione di Python per l'ambiente virtuale. In questo esempio viene usata la versione 3,5, ovvero la stessa versione del cluster compilato in una. È anche possibile usare altre versioni di Python per creare l'ambiente virtuale.
    -   `anaconda`Specifica il package_spec come Anaconda per installare i pacchetti Anaconda nell'ambiente virtuale.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Se necessario, installare pacchetti Python esterni nell'ambiente virtuale creato. Eseguire azioni script nel cluster per tutti i nodi con lo script seguente per installare pacchetti Python esterni. È necessario avere il privilegio sudo qui per scrivere i file nella cartella dell'ambiente virtuale.

    Per un elenco completo dei pacchetti disponibili, è possibile eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Per esempio, è possibile installare pacchetti resi disponibili tramite [conda-forge](https://conda-forge.org/feedstocks/).

    Usare il comando seguente se si vuole installare una libreria con la versione più recente:

    - Usa canale conda:

        -   `seaborn`nome del pacchetto che si desidera installare.
        -   `-n py35new`specificare il nome dell'ambiente virtuale che viene appena creato. Assicurarsi di modificare il nome corrispondente in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - In alternativa, usare il repository `seaborn` pypi `py35new` , modificare e corrispondenti:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Usare il comando seguente se si vuole installare una libreria con una versione specifica:

    - Usa canale conda:

        -   `numpy=1.16.1`nome e versione del pacchetto che si desidera installare.
        -   `-n py35new`specificare il nome dell'ambiente virtuale che viene appena creato. Assicurarsi di modificare il nome corrispondente in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - In alternativa, usare il repository `numpy==1.16.1` pypi `py35new` , modificare e corrispondenti:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Se non si conosce il nome dell'ambiente virtuale, è possibile connettersi tramite SSH al nodo head del cluster ed `/usr/bin/anaconda/bin/conda info -e` eseguire per mostrare tutti gli ambienti virtuali.

3. Modificare le configurazioni di Spark e Livio e puntare all'ambiente virtuale creato.

    1. Aprire l'interfaccia utente di Ambariri, passare alla pagina Spark2, scheda config.

        ![Modificare la configurazione di Spark e Livio tramite Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Espandere Advanced livy2-ENV, quindi aggiungere le istruzioni seguenti in basso. Se è stato installato l'ambiente virtuale con un prefisso diverso, modificare il percorso corrispondente.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Modificare la configurazione di Livio tramite Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Espandere Advanced spark2-ENV e sostituire l'istruzione Export PYSPARK_PYTHON esistente in basso. Se è stato installato l'ambiente virtuale con un prefisso diverso, modificare il percorso corrispondente.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Modificare la configurazione di Spark tramite Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Salvare le modifiche e riavviare i servizi interessati. Queste modifiche richiedono il riavvio del servizio Spark2. L'interfaccia utente di Ambari richiederà un promemoria di riavvio necessario, quindi fare clic su Riavvia per riavviare tutti i servizi interessati.

        ![Modificare la configurazione di Spark tramite Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Se si vuole usare il nuovo ambiente virtuale creato in Jupyter. È necessario modificare le configurazioni di Jupyter e riavviare Jupyter. Eseguire azioni script su tutti i nodi di intestazione con l'istruzione seguente per puntare Jupyter al nuovo ambiente virtuale creato. Assicurarsi di modificare il percorso del prefisso specificato per l'ambiente virtuale. Dopo l'esecuzione di questa azione script, riavviare il servizio Jupyter tramite l'interfaccia utente di Ambari per rendere disponibile questa modifica.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    È possibile fare doppio per confermare l'ambiente Python in Jupyter Notebook eseguendo il codice seguente:

    ![Controllare la versione di Python in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema noto

Esiste un bug noto per la versione Anaconda 4.7.11, 4.7.12 e 4.8.0. Se le azioni script si bloccano `"Collecting package metadata (repodata.json): ...working..."` e si verificano `"Python script has been killed due to timeout after waiting 3600 secs"`errori in. È possibile scaricare [questo script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) ed eseguirlo come azioni script su tutti i nodi per risolvere il problema.

Per controllare la versione di Anaconda, è possibile connettersi tramite SSH al nodo dell'intestazione `/usr/bin/anaconda/bin/conda --v`del cluster ed eseguire.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)
* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
