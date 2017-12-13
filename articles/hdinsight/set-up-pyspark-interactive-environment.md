---
title: Strumenti di Azure HDInsight - Configurare l'ambiente PySpark Interactive per Visual Studio Code | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
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
ms.openlocfilehash: 5a64023df813262c461b9d772b722ebd613369ed
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente mostra come installare pacchetti Python quando si esegue **HDInsight: PySpark Interactive**.


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

8. Riavviare Visual Studio Code e tornare all'editor di script che sta eseguendo **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Spark in Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
