---
title: Supporto software open source in Azure HDInsight
description: Microsoft Azure offre un livello di supporto generale per le tecnologie open source.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 65f21450f194175af37c6c198468978392cee462
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195163"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Supporto software open source in Azure HDInsight

Il servizio Microsoft Azure HDInsight usa un ambiente di tecnologie open source costituito da Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere la sezione **Ambito del supporto** in [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

## <a name="components"></a>Componenti

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

### <a name="built-in-components"></a>Componenti predefiniti

Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. A questa categoria appartengono i componenti seguenti:

* [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gestione risorse.
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

## <a name="component-usage"></a>Utilizzo componenti

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. La tabella seguente descrive i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

|Uso |Descrizione |
|---|---|
|Invio del processo|È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.|
|Personalizzazione cluster|Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.|
|Esempi|Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.|

## <a name="next-steps"></a>Passaggi successivi

* [Personalizzare i cluster HDInsight di Azure usando azioni script](./hdinsight-hadoop-customize-cluster-linux.md)
* [Sviluppare script di azioni script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azione script](./spark/apache-spark-python-package-installation.md)
