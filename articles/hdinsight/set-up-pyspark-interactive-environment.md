---
title: Strumenti di Azure HDInsight-ambiente interattivo PySpark per Visual Studio Code
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f62f7994818e487202e35e4931472355fb6ac3d2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130182"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente illustra come configurare l'ambiente interattivo PySpark in VS Code.

La procedura usa il comando **python/pip** per creare un ambiente virtuale nel percorso della home directory. Se si vuole usare un'altra versione, è necessario cambiare la versione predefinita del comando **python/pip** manualmente. Per altre informazioni, vedere [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installare [Python](https://www.python.org/downloads/) e [PIP](https://pip.pypa.io/en/stable/installing/).

   + Installare Python da [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installare PIP da [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (se non è installato dall'installazione di Python).
   + Verificare che Python e PIP siano installati correttamente usando i comandi seguenti. Facoltativa

        ![Controllare la versione PIP di Python comando](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Si consiglia di installare manualmente Python invece di usare la versione predefinita macOS.

2. Installare **virtualenv** eseguendo il comando seguente.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Altri pacchetti

Se viene visualizzato un messaggio di errore, installare i pacchetti necessari eseguendo i comandi seguenti:

   ![Installare il pacchetto libkrb5 per Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Riavviare VS Code e quindi tornare all'editor di script che esegue **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo
* HDInsight per VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Apache Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Apache Spark)](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter in un cluster Apache Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
