---
title: Strumenti di Azure HDInsight - Configurare l'ambiente PySpark Interactive per Visual Studio Code
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 82aebd780b7b821675a6ee891cc81e2b1c6fdafb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437215"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente illustra come installare pacchetti Python quando si esegue **HDInsight: PySpark Interactive**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Configurare l'ambiente PySpark Interactive in MacOS e Linux
Se si usa **python 3.x**, è necessario usare il comando **pip3** per la procedura seguente:

1. Verificare che **Python** e **pip** siano installati.
 
    ![Versione Python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installare Jupyter.
    ```
    sudo pip install jupyter
    ```
   È possibile che venga visualizzato il messaggio di errore seguente in Linux e MacOS:

   ![Errore 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Installare **libkrb5-dev** (solo per Linux). È possibile che venga visualizzato il messaggio di errore seguente:

   ![Errore 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Installare **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Assicurarsi che **ipywidgets** sia installato correttamente eseguendo il comando seguente:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installare i kernel del wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installare i kernel del wrapper. Eseguire **pip show sparkmagic**. L'output mostra il percorso dell'installazione di **sparkmagic**. 

    ![percorso sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Passare al percorso e quindi eseguire:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![installazione di kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Verificare lo stato dell'installazione.

    ```
    jupyter-kernelspec list
    ```
    ![elenco di kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Per i kernel disponibili: 
    - **python2** e **pysparkkernel** corrispondono a **python 2.x**. 
    - **python3** e **pyspark3kernel** corrispondono a **python 3.x**. 

8. Riavviare VS Code e quindi tornare all'editor di script che esegue **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter in un cluster Apache Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
