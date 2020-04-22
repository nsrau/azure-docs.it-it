---
title: Supporto software open source in Azure HDInsight
description: Microsoft Azure offre un livello di supporto generale per le tecnologie open source.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772171"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Supporto software open source in Azure HDInsight

Il servizio Microsoft Azure HDInsight usa un ambiente di tecnologie open source formate intorno ad Apache Hadoop.The Microsoft Azure HDInsight service uses an environment of open-source technologies formed around Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere la sezione **Ambito del supporto** in [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

## <a name="components"></a>Componenti

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

### <a name="built-in-components"></a>Componenti integrati

Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. A questa categoria appartengono i componenti seguenti:

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gestione risorse.
* Il linguaggio di query Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Un elenco completo dei componenti del cluster è disponibile in [Componenti e versioni di Apache Hadoop disponibili in HDInsight.](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Componenti personalizzati

Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che il supporto tecnico Microsoft sia in grado di risolvere il problema oppure che richieda di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. Possono essere usati molti siti di community. Ad esempio, il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com).
>
> Anche per i progetti Apache sono disponibili siti specifici nel [sito Web di Apache](https://apache.org). Ad esempio, [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Utilizzo dei componenti

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. Nella tabella seguente vengono descritti i modi più comuni in cui i componenti personalizzati vengono usati nei cluster HDInsight:The following table describes the common ways that custom components are used on HDInsight clusters:

|Uso |Descrizione |
|---|---|
|Invio del processo|È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.|
|Personalizzazione cluster|Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.|
|Esempi|Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.|

## <a name="next-steps"></a>Passaggi successivi

* [Personalizzare i cluster di Azure HDInsight usando le azioni scriptCustomize Azure HDInsight clusters by using script actions](./hdinsight-hadoop-customize-cluster-linux.md)
* [Sviluppare script di azioni script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script](./spark/apache-spark-python-package-installation.md)
