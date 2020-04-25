---
title: Ambiente interattivo PySpark con gli strumenti di Azure HDInsight
description: Informazioni su come usare gli strumenti di Azure HDInsight per Visual Studio Code per creare e inviare query e script.
keywords: VSCode,strumenti di Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: d9a3356ea18ccf4660d05b3fade9d9e6d6cbb5ee
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131339"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurare l'ambiente PySpark Interactive per Visual Studio Code

La procedura seguente illustra come configurare l'ambiente interattivo PySpark in VS Code.

La procedura usa il comando **python/pip** per creare un ambiente virtuale nel percorso della home directory. Se si vuole usare un'altra versione, è necessario cambiare la versione predefinita del comando **python/pip** manualmente. Per altre informazioni, vedere [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installare [Python](https://www.python.org/downloads/) e [PIP](https://pip.pypa.io/en/stable/installing/).

   * Installare Python da [https://www.python.org/downloads/](https://www.python.org/downloads/).
   * Installare PIP da [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (se non è installato dall'installazione di Python).
   * Verificare che Python e PIP siano installati correttamente usando i comandi seguenti. Facoltativa

        ![Controllare la versione PIP di Python comando](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Si consiglia di installare manualmente Python invece di usare la versione predefinita macOS.

2. Installare **virtualenv** eseguendo il comando seguente.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Altri pacchetti

Se viene riportato un messaggio di errore, installare i pacchetti necessari eseguendo i comandi seguenti:

   ![Installare il pacchetto libkrb5 per Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

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
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
