---
title: Supporto del software open source in Azure HDInsight
description: Microsoft Azure offre un livello di supporto generale per le tecnologie open source.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f1e1518cce060374f14f1db7d5f4e594e3de8f85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086263"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Supporto del software open source in Azure HDInsight

Il servizio Microsoft Azure HDInsight usa un ecosistema di tecnologie open source sviluppate sulla base di Apache Hadoop. Microsoft Azure offre un livello di supporto generale per le tecnologie open source. Per altre informazioni, vedere la sezione **Ambito del supporto** in [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight offre un livello di supporto aggiuntivo per i componenti predefiniti.

## <a name="components"></a>Componenti

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

### <a name="built-in-components"></a>Componenti predefiniti

Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. A questa categoria appartengono i componenti seguenti:

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager.
* Il linguaggio di query Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Un elenco completo dei componenti del cluster è disponibile in [Componenti e versioni di Apache Hadoop disponibili in HDInsight.](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Componenti personalizzati

Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono completamente supportati. Il supporto tecnico Microsoft aiuta a isolare e risolvere i problemi legati a tali componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che il supporto tecnico Microsoft sia in grado di risolvere il problema oppure che richieda di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. Possono essere usati molti siti di community. Esempi di questi componenti sono la [pagina delle domande di Domande e risposte Microsoft per HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html) e [Stack Overflow](https://stackoverflow.com).
>
> Anche per i progetti Apache sono disponibili siti specifici nel [sito Web di Apache](https://apache.org). Ad esempio, [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Utilizzo dei componenti

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. La tabella seguente illustra gli utilizzi più comuni dei componenti personalizzati nei cluster HDInsight:

|Utilizzo |Descrizione |
|---|---|
|Invio del processo|È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.|
|Personalizzazione cluster|Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.|
|Esempi|Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.|

## <a name="next-steps"></a>Passaggi successivi

* [Personalizzare i cluster Azure HDInsight con azioni script](./hdinsight-hadoop-customize-cluster-linux.md)
* [Sviluppare script di azioni script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gestire in modo sicuro l'ambiente Python in Azure HDInsight con azioni script](./spark/apache-spark-python-package-installation.md)
