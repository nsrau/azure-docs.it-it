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
ms.date: 1/17/2019
ms.openlocfilehash: 8c0b40f0e6204d6cff59d3de3f20604d6913b9cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111634"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente illustra come configurare l'ambiente interattivo PySpark in VS Code.

La procedura usa il comando **python/pip** per creare un ambiente virtuale nel percorso della home directory. Se si vuole usare un'altra versione, è necessario cambiare la versione predefinita del comando **python/pip** manualmente. Per altre informazioni, vedere [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installare [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Installare Python da [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installare pip da [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (se non viene installato dall'installazione di Python).
   + Verificare che Python e pip siano stati installati correttamente usando i comandi seguenti. Facoltativa
 
        ![Versione Python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > È consigliabile installare manualmente Python invece di usare la versione predefinita di MacOS.


2. Installare **virtualenv** eseguendo il comando seguente.
   
   ```
   pip install virtualenv
   ```

3. Solo per Linux, installare i pacchetti necessari eseguendo i comandi seguenti se viene visualizzato il messaggio di errore.
   
    ![Versione Python pip](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Riavviare VS Code e quindi tornare all'editor di script che esegue **HDInsight: PySpark Interactive**.

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
* [Usare Apache Zeppelin per eseguire query Apache Hive in HDInsight di Azure](hdinsight-connect-hive-zeppelin.md)
