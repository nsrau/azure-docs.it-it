---
title: Azione script per pacchetti Python con Jupyter in Azure HDInsight
description: Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215608"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script

> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)

HDInsight include due installazioni di Python predefinite nel cluster Spark, Anaconda Python 2,7 e Python 3,5. In alcuni casi, i clienti devono personalizzare l'ambiente Python, ad esempio l'installazione di pacchetti Python esterni o un'altra versione di Python. Questo articolo illustra la procedura consigliata per gestire in modo sicuro gli ambienti Python per un cluster [Apache Spark](https://spark.apache.org/) in HDInsight.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere [Creazione dei cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se non dispone di un cluster Spark in HDInsight Linux, è possibile eseguire le azioni script durante la creazione del cluster. Vedere la documentazione su [come usare azioni script personalizzate](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source ispirate ad Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per ulteriori informazioni, vedere il [sito Web domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

* **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Ad esempio, Apache Hadoop YARN Gestione risorse, il linguaggio di query Apache Hive (HiveQL) e la libreria Mahout appartengono a questa categoria. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni del cluster Apache Hadoop incluse in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [!IMPORTANT]
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe essere in grado di risolvere il problema OPPURE richiedere di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto estremamente competente per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) o [https://stackoverflow.com](https://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [https://apache.org](https://apache.org), ad esempio [Hadoop](https://hadoop.apache.org/).

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

    -   `--prefix` specifica un percorso in cui risiede un ambiente virtuale conda. Sono disponibili diverse configurazioni che devono essere modificate ulteriormente in base al percorso specificato. In questo esempio viene usato py35new, perché il cluster ha un ambiente virtuale esistente denominato PY35 già.
    -   `python=` specifica la versione di Python per l'ambiente virtuale. In questo esempio viene usata la versione 3,5, ovvero la stessa versione del cluster compilato in una. È anche possibile usare altre versioni di Python per creare l'ambiente virtuale.
    -   `anaconda` specifica il package_spec come Anaconda per installare i pacchetti Anaconda nell'ambiente virtuale.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Se necessario, installare pacchetti Python esterni nell'ambiente virtuale creato. Eseguire azioni script nel cluster per tutti i nodi con lo script seguente per installare pacchetti Python esterni. È necessario avere il privilegio sudo qui per scrivere i file nella cartella dell'ambiente virtuale.

    Per un elenco completo dei pacchetti disponibili, è possibile eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Per esempio, è possibile installare pacchetti resi disponibili tramite [conda-forge](https://conda-forge.org/feedstocks/).

    -   `seaborn` è il nome del pacchetto che si desidera installare.
    -   `-n py35new` specificare il nome dell'ambiente virtuale che viene appena creato. Assicurarsi di modificare il nome corrispondente in base alla creazione dell'ambiente virtuale.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Se non si conosce il nome dell'ambiente virtuale, è possibile connettersi tramite SSH al nodo intestazione del cluster ed eseguire `/usr/bin/anaconda/bin/conda info -e` per mostrare tutti gli ambienti virtuali.

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
 
4.  Se si vuole usare il nuovo ambiente virtuale creato in Jupyter. È necessario modificare le configurazioni di Jupyter e riavviare Jupyter. Eseguire azioni script su tutti i nodi di intestazione con l'istruzione seguente per puntare Jupyter al nuovo ambiente virtuale creato. Assicurarsi di modificare il percorso del prefisso specificato per l'ambiente virtuale. Dopo l'esecuzione di questa azione script, riavviare il servizio Jupyter tramite l'interfaccia utente di Ambari per rendere disponibile questa modifica.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    È possibile fare doppio per confermare l'ambiente Python in Jupyter Notebook eseguendo il codice seguente:

    ![Controllare la versione di Python in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema noto

Esiste un bug noto per la versione Anaconda 4.7.11 e 4.7.12. Se le azioni script si bloccano a `"Collecting package metadata (repodata.json): ...working..."` e si verificano errori con `"Python script has been killed due to timeout after waiting 3600 secs"`. È possibile scaricare [questo script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) ed eseguirlo come azioni script su tutti i nodi per risolvere il problema.

Per controllare la versione di Anaconda, è possibile usare SSH per il nodo dell'intestazione del cluster ed eseguire `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark with Machine Learning: Use Spark in HDInsight for analyzing building temperature using HVAC data](apache-spark-ipython-notebook-machine-learning.md) (Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio usando dati HVAC)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug dei processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
