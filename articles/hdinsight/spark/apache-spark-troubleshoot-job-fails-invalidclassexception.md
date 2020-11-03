---
title: Errore InvalidClassException da Apache Spark-Azure HDInsight
description: Apache Spark processo ha esito negativo con InvalidClassException, versione della classe non corrispondente, in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 6220c328d05e7cd68460b7bfd0708a9d393a290f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287887"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark processo ha esito negativo con InvalidClassException, versione della classe non corrispondente, in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Si tenta di creare un processo di Apache Spark in un cluster Spark 2. x. L'operazione ha esito negativo e restituisce un errore simile al seguente:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Causa

Questo errore può essere causato dall'aggiunta di un file jar aggiuntivo alla `spark.yarn.jars` configurazione, in particolare un file jar ombreggiato che include una versione diversa del `commons-lang3` pacchetto e introduce una mancata corrispondenza tra le classi. Per impostazione predefinita, Spark 2.1/2/3 usa la versione 3,5 di `commons-lang3` .

> [!TIP]
> Per ombreggiare una libreria è necessario inserirne il contenuto nel file jar, modificando il pacchetto. Questo comportamento è diverso rispetto al packaging della libreria, che consente di inserire la libreria nel proprio file jar senza riassemblarli.

## <a name="resolution"></a>Soluzione

Rimuovere il file jar o ricompilare il file jar personalizzato (AzureLogAppender) e usare [Maven-Shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) per rilocare le classi.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]