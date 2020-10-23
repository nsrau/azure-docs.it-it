---
title: Azione script per pacchetti Python con Jupyter in Azure HDInsight
description: Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: b29e4411a104bbcd1d6d5b3320df47a742e2ca84
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461244"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script

HDInsight include due installazioni di Python predefinite nel cluster Spark, Anaconda Python 2.7 e Python 3.5. È possibile che i clienti debbano personalizzare l'ambiente Python come l'installazione di pacchetti Python esterni. In questo articolo viene illustrata la procedura consigliata per gestire in modo sicuro gli ambienti Python per cluster Apache Spark in HDInsight.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se non si dispone di un cluster Spark in HDInsight, è possibile eseguire azioni script durante la creazione del cluster. Vedere la documentazione su [come usare azioni script personalizzate](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source progettate su Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere il [sito Web delle domande frequenti del supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

|Componente |Descrizione |
|---|---|
|Predefinito|Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Ad esempio, a questa categoria appartengono Apache Hadoop YARN Resource Manager, il linguaggio di query Apache Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni del cluster Apache Hadoop incluse in HDInsight](../hdinsight-component-versioning.md).|
|Personalizzato|In qualità di utente del cluster è possibile installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato autonomamente.|

> [!IMPORTANT]
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. Il supporto tecnico Microsoft potrebbe essere in grado di risolvere il problema OPPURE richiedere di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto estremamente competente per la tecnologia specifica. Per esempio, è possibile ricorrere a molti siti di community, come: [Pagina delle domande di Domande e risposte Microsoft per HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html), `https://stackoverflow.com`. Anche per i progetti Apache sono disponibili siti specifici in `https://apache.org`.

## <a name="understand-default-python-installation"></a>Informazioni sull'installazione predefinita di Python

Il cluster Spark HDInsight viene creato con l'installazione di Anaconda. Nel cluster sono presenti due installazioni di Python, Anaconda Python 2.7 e Python 3.5. La tabella seguente illustra le impostazioni predefinite di Python per Spark, Livy e Jupyter.

|Impostazione |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Versione di Spark|Impostazione predefinita: 2.7|È possibile modificare la configurazione in 3,5|
|Versione di Livio|Impostazione predefinita: 2.7|È possibile modificare la configurazione in 3,5|
|Jupyter|Kernel PySpark|Kernel PySpark3|

## <a name="safely-install-external-python-packages"></a>Installare pacchetti Python esterni in modo sicuro

Il cluster HDInsight dipende dall'ambiente Python incorporato, sia Python 2.7 che Python 3.5. L'installazione diretta di pacchetti personalizzati in questi ambienti predefiniti incorporati può causare modifiche impreviste della versione della libreria. e compromettere ulteriormente il cluster. Per installare in modo sicuro pacchetti Python esterni personalizzati per le applicazioni Spark, attenersi alla procedura seguente.

1. Creare un ambiente virtuale Python tramite Conda. Un ambiente virtuale fornisce uno spazio isolato per i progetti senza compromettere altri elementi. Quando si crea un ambiente virtuale Python, è possibile specificare la versione di Python da usare. Sarà comunque necessario creare un ambiente virtuale anche se si vuole usare Python 2.7 e 3.5. Questo requisito consiste nel verificare che l'ambiente predefinito del cluster non venga compromesso. Per creare un ambiente virtuale Python, eseguire azioni script nel cluster per tutti i nodi con lo script seguente.

    -   `--prefix` specifica un percorso in cui risiede un ambiente virtuale Conda. Sono disponibili diverse configurazioni che devono essere modificate ulteriormente in base al percorso qui specificato. In questo esempio viene usato py35new, perché il cluster dispone già di un ambiente virtuale esistente denominato PY35.
    -   `python=` specifica la versione di Python per l'ambiente virtuale. In questo esempio viene usata la versione 3.5, ovvero la stessa versione del cluster compilato. Per creare un ambiente virtuale è anche possibile usare altre versioni di Python.
    -   `anaconda` specifica package_spec come Anaconda per installare pacchetti Anaconda nell'ambiente virtuale.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Se necessario, installare pacchetti Python esterni nell'ambiente virtuale creato. Per installare pacchetti Python esterni, eseguire azioni script nel cluster per tutti i nodi con lo script seguente. Per scrivere i file nella cartella dell'ambiente virtuale è necessario disporre del privilegio sudo.

    Per un elenco completo dei pacchetti disponibili, eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Per esempio, è possibile installare pacchetti resi disponibili tramite [conda-forge](https://conda-forge.org/feedstocks/).

    Per installare una libreria con la versione più recente, usare il comando seguente:

    - Usare il canale Conda:

        -   `seaborn` indica il nome del pacchetto da installare.
        -   `-n py35new` permette di specificare il nome dell'ambiente virtuale appena creato. Assicurarsi di modificare il nome in modo corrispondente, in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - In alternativa, usare il repository PyPi e modificare `seaborn` e `py35new` in modo corrispondente:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Per installare una libreria con una versione specifica, usare il comando seguente:

    - Usare il canale Conda:

        -   `numpy=1.16.1` indica il nome e la versione del pacchetto da installare.
        -   `-n py35new` permette di specificare il nome dell'ambiente virtuale appena creato. Assicurarsi di modificare il nome in modo corrispondente, in base alla creazione dell'ambiente virtuale.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - In alternativa, usare il repository PyPi e modificare `numpy==1.16.1` e `py35new` in modo corrispondente:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Se non si conosce il nome dell'ambiente virtuale, è possibile connettersi tramite SSH al nodo head del cluster ed eseguire `/usr/bin/anaconda/bin/conda info -e` per mostrare tutti gli ambienti virtuali.

3. Modificare le configurazioni di Spark e Livy e puntare all'ambiente virtuale creato.

    1. Aprire l'interfaccia utente di Ambari e passare alla pagina Spark2, scheda Configurazione.

        ![Modificare la configurazione di Spark e Livy tramite Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Espandere Advanced livy2-env, quindi aggiungere le istruzioni seguenti in basso. Se l'ambiente virtuale è stato installato con un prefisso diverso, modificare il percorso di conseguenza.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Modificare la configurazione di Livy tramite Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Espandere Advanced spark2-env e sostituire l'istruzione di esportazione PYSPARK_PYTHON esistente in basso. Se l'ambiente virtuale è stato installato con un prefisso diverso, modificare il percorso di conseguenza.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Modificare la configurazione di Spark tramite Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Salvare le modifiche e riavviare i servizi interessati. Queste modifiche richiedono il riavvio del servizio Spark2. L'interfaccia utente di Ambari mostrerà un promemoria per indicare la necessità di un riavvio; fare quindi clic su Riavvia per riavviare tutti i servizi interessati.

        ![Riavviare i servizi](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Per usare il nuovo ambiente virtuale creato in Jupyter. Modificare le configurazioni di Jupyter e riavviare Jupyter. Eseguire azioni script su tutti i nodi di intestazione con l'istruzione seguente per puntare Jupyter al nuovo ambiente virtuale creato. Assicurarsi di modificare il percorso con il prefisso specificato per l'ambiente virtuale. Dopo l'esecuzione di questa azione script, riavviare il servizio Jupyter tramite l'interfaccia utente di Ambari per rendere disponibile questa modifica.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Per confermare l'ambiente Python in Jupyter Notebook, eseguire il codice seguente:

    ![Controllare la versione di Python in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema noto

Esiste un bug noto per la versione di Anaconda `4.7.11`, `4.7.12` e `4.8.0`. Se si nota che le azioni script smettono di rispondere `"Collecting package metadata (repodata.json): ...working..."` e non riescono con `"Python script has been killed due to timeout after waiting 3600 secs"` . È possibile scaricare [questo script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) ed eseguirlo come azione script su tutti i nodi per risolvere il problema.

Per controllare la versione di Anaconda, è possibile usare SSH per il nodo dell'intestazione del cluster ed eseguire `/usr/bin/anaconda/bin/conda --v`.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Pacchetti esterni con Jupyter Notebook in Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
