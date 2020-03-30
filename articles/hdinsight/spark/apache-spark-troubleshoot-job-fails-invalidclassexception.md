---
title: InvalidClassException error from Apache Spark - Azure HDInsight
description: Apache Spark job fails with InvalidClassException, class version mismatch, in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894358"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark job fails with InvalidClassException, class version mismatch, in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Si tenta di creare un processo Apache Spark in un cluster Spark 2.x. Non riesce con un errore simile al seguente:

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

Questo errore può essere causato dall'aggiunta di un jar aggiuntivo alla `spark.yarn.jars` configurazione, `commons-lang3` in particolare un jar ombreggiato che include una versione diversa del pacchetto e introduce una mancata corrispondenza di classe. Per impostazione predefinita, Spark 2.1/2/3 `commons-lang3`utilizza la versione 3.5 di .

> [!TIP]
> Ombreggiare una libreria è mettere il suo contenuto nel tuo barattolo, cambiandone il pacchetto. Questo differisce dal confezionamento della libreria, che sta mettendo la libreria nel proprio barattolo senza riconfezionamento.

## <a name="resolution"></a>Risoluzione

Rimuovere il file jar o ricompilare il file jar personalizzato (AzureLogAppender) e usare [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) per riposizionare le classi.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
