---
title: Strumenti di Azure HDInsight - Configurare l'ambiente PySpark Interactive per Visual Studio Code | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 839f379228322eb2da0ff61609634bf1f86e4bb3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente mostra come installare pacchetti python quando si esegue **HDInsight: PySpark Interactive**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>Configurare l'ambiente PySpark Interactive in MacOS e Linux
È necessario usare i comandi **pip3** per i passaggi seguenti, se è installato **python 3.x**.
1. Verificare che **Python** e **pip** siano installati.
 
    ![versione python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installare Jupyter
    ```
    sudo pip install jupyter
    ```
    +  È possibile che venga visualizzato il seguente messaggio di errore in Linux e MacOS:

        ![errore1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + Installare libkrb5-dev (solo per Linux); è possibile che venga visualizzato il messaggio di errore seguente:

        ![errore2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Installare sparkmagic
   ```
   sudo pip install sparkmagic
   ```

4. Assicurarsi che ipywidgets sia installato correttamente eseguendo:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installare i kernel del wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installare i kernel del wrapper. Eseguire **pip show sparkmagic**; viene indicato il percorso in cui è installato sparkmagic. 

    ![percorso sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Passare al percorso ed eseguire:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![installazione di kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Verificare lo stato dell'installazione: 

    ```
    jupyter-kernelspec list
    ```
    ![elenco di kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Per i kernel disponibili: **python2** e **pysparkkernel** corrispondono a **python 2.x**, **python3** e **pyspark3kernel** corrispondono a **python 3.x**. 

8. Riavviare VScode e tornare all'editor di script eseguendo **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VSCode: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./hdinsight-connect-hive-zeppelin.md).
