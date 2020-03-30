---
title: Configurare la pianificazione delle patch del sistema operativo per i cluster di Azure HDInsightConfigure OS patching schedule for Azure HDInsight clusters
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206861"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurare la pianificazione dell'applicazione di patch al sistema operativo per i cluster HDInsight basati su LinuxConfigure the OS patching schedule for Linux-based HDInsight clusters

> [!IMPORTANT]
> Le immagini Di Ubuntu diventano disponibili per la creazione di nuovi cluster di Azure HDInsight entro tre mesi dalla pubblicazione. A partire da gennaio 2019, l'esecuzione di cluster non vengono patchate automaticamente. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

HDInsight fornisce supporto per eseguire attività comuni nel cluster, ad esempio l'installazione di patch del sistema operativo, aggiornamenti della sicurezza e il riavvio dei nodi. Queste attività vengono eseguite utilizzando i due script seguenti che possono essere eseguiti come [azioni script](hdinsight-hadoop-customize-cluster-linux.md)e configurati con parametri:

- `schedule-reboots.sh`- Eseguire un riavvio immediato o pianificare un riavvio nei nodi del cluster.- Do a immediate restart, or schedule a restart on the cluster nodes.
- `install-updates-schedule-reboots.sh`- Installare tutti gli aggiornamenti, solo kernel e aggiornamenti di sicurezza o solo gli aggiornamenti del kernel.

> [!NOTE]  
> Le azioni script non applicheranno automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuri. Eseguire gli script ogni volta che è necessario applicare nuovi aggiornamenti per installare gli aggiornamenti e quindi riavviare la macchina virtuale.

## <a name="preparation"></a>Preparazione

Applicare una patch a un ambiente rappresentativo non di produzione prima della distribuzione nell'ambiente di produzione. Sviluppare un piano per testare adeguatamente il sistema prima dell'applicazione effettiva delle patch.

Di volta in volta, da una sessione ssh con il cluster, è possibile che venga visualizzato un messaggio che indica che è disponibile un aggiornamento. Il messaggio può essere simile al:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

L'applicazione di patch è facoltativa e a vostra discrezione.

## <a name="restart-nodes"></a>Riavviare i nodi
  
La pianificazione dello [script-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), imposta il tipo di riavvio che verrà eseguito sui computer del cluster. Quando si invia l'azione script, impostarla in modo che venga applicata a tutti e tre i tipi di nodo: nodo head, nodo di lavoro e zookeeper. Se lo script non viene applicato a un tipo di nodo, le macchine virtuali per tale tipo di nodo non verranno aggiornate o riavviate.

Accetta `schedule-reboots script` un parametro numerico:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di riavvio da eseguire | 1 o 2 | Il valore 1 abilita il riavvio della pianificazione (pianificato in 12-24 ore). Il valore 2 consente il riavvio immediato (in 5 minuti). Se non viene specificato alcun parametro, il valore predefinito è 1.If no parameter is given, the default is 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installare gli aggiornamenti e riavviare i nodi

Lo script [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fornisce opzioni per installare diversi tipi di aggiornamenti e riavviare la macchina virtuale.

Lo `install-updates-schedule-reboots` script accetta due parametri numerici, come descritto nella tabella seguente:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di aggiornamenti da installare | 0, 1 o 2 | Il valore 0 installa solo gli aggiornamenti del kernel. Un valore pari a 1 installa tutti gli aggiornamenti e 2 installa solo gli aggiornamenti della sicurezza del kernel. Se non viene fornito alcun parametro, il valore predefinito è 0.If no parameter is provided, the default is 0. |
| Tipo di riavvio da eseguire | 0, 1 o 2 | Il valore 0 disabilita il riavvio. Il valore 1 abilita il riavvio pianificato e 2 consente il riavvio immediato. Se non viene fornito alcun parametro, il valore predefinito è 0.If no parameter is provided, the default is 0. L'utente deve modificare il parametro di input 1 nel parametro di input 2. |

> [!NOTE]
> È necessario contrassegnare uno script come persistente dopo averlo applicato a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita. Se si applica lo script come parte del processo di creazione del cluster, questo viene mantenuto automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Per passaggi specifici sull'uso delle azioni script, vedere le sezioni seguenti in [Personalizzare i cluster HDInsight basati su Linux tramite l'azione script:For](hdinsight-hadoop-customize-cluster-linux.md)specific steps on using script actions, see the following sections in Customize Linux-based HDInsight clusters using script action :

- [Usare un'azione script durante la creazione del clusterUse a script action during cluster creation](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
