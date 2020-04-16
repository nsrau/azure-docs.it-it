---
title: PySpark interactive environment with Azure HDInsight Tools
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393667"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente illustra come configurare l'ambiente interattivo PySpark in VS Code.

La procedura usa il comando **python/pip** per creare un ambiente virtuale nel percorso della home directory. Se si vuole usare un'altra versione, è necessario cambiare la versione predefinita del comando **python/pip** manualmente. Per altre informazioni, vedere [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installare [Python](https://www.python.org/downloads/) e [pip](https://pip.pypa.io/en/stable/installing/).

   + Installare Python [https://www.python.org/downloads/](https://www.python.org/downloads/)da .
   + Installare pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) da (se non è installato dall'installazione di Python).
   + Verificare che Python e pip siano installati correttamente utilizzando i comandi seguenti. Facoltativa

        ![Controllare Python pip versione comando](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Si consiglia di installare manualmente Python invece di utilizzare la versione predefinita di macOS.

2. Installare **virtualenv** eseguendo il comando seguente.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Altri pacchetti

Se si verifica un messaggio di errore, installare i pacchetti necessari eseguendo i comandi seguenti:

   ![Installare il pacchetto libkrb5 per python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Riavviare Visual Studio Code e tornare all'editor di script che sta eseguendo **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Passaggi successivi

### <a name="demo"></a>Demo

* HDInsight per VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md)
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter in un cluster Apache Spark per HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
