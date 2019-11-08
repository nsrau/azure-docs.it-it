---
title: Configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight di Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748487"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight basati su Linux

> [!IMPORTANT]
> Le immagini Ubuntu diventano disponibili per la creazione di nuovi cluster HDInsight di Azure entro tre mesi dalla pubblicazione. A partire dal 2019 gennaio, i cluster in esecuzione non vengono sottoposti a patch automatici. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

HDInsight fornisce supporto per l'esecuzione di attività comuni nel cluster, ad esempio l'installazione di patch del sistema operativo, gli aggiornamenti della sicurezza e il riavvio dei nodi. Queste attività vengono eseguite usando i due script seguenti che possono essere eseguiti come [azioni script](hdinsight-hadoop-customize-cluster-linux.md)e configurati con i parametri:

- `schedule-reboots.sh`: eseguire un riavvio immediato o pianificare un riavvio nei nodi del cluster.
- `install-updates-schedule-reboots.sh`: installare tutti gli aggiornamenti, solo quelli di kernel + Security o solo gli aggiornamenti del kernel.

> [!NOTE]  
> Le azioni script non applicano automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuri. Eseguire gli script ogni volta che è necessario applicare nuovi aggiornamenti per installare gli aggiornamenti, quindi riavviare la macchina virtuale.

## <a name="restart-nodes"></a>Riavviare i nodi
  
La pianificazione dello script [-](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)riavvia, imposta il tipo di riavvio che verrà eseguito nei computer del cluster. Quando si invia l'azione script, impostarla su applica a tutti e tre i tipi di nodo: nodo Head, nodo del ruolo di lavoro e Zookeeper. Se lo script non viene applicato a un tipo di nodo, le macchine virtuali per quel tipo di nodo non verranno aggiornate o riavviate.

Il `schedule-reboots script` accetta un parametro numerico:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di riavvio da eseguire | 1 o 2 | Il valore 1 consente il riavvio della pianificazione (pianificato in 12-24 ore). Il valore 2 Abilita il riavvio immediato (in 5 minuti). Se non viene specificato alcun parametro, il valore predefinito è 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installare gli aggiornamenti e riavviare i nodi

Lo script [Install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fornisce opzioni per l'installazione di diversi tipi di aggiornamenti e il riavvio della macchina virtuale.

Lo script `install-updates-schedule-reboots` accetta due parametri numerici, come descritto nella tabella seguente:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di aggiornamenti da installare | 0, 1 o 2 | Il valore 0 installa solo gli aggiornamenti del kernel. Il valore 1 installa tutti gli aggiornamenti e 2 installa solo gli aggiornamenti kernel + Security. Se non viene specificato alcun parametro, il valore predefinito è 0. |
| Tipo di riavvio da eseguire | 0, 1 o 2 | Il valore 0 Disabilita il riavvio. Il valore 1 consente il riavvio della pianificazione e 2 Abilita il riavvio immediato. Se non viene specificato alcun parametro, il valore predefinito è 0. L'utente deve modificare il parametro di input 1 nel parametro di input 2. |

> [!NOTE]
> È necessario contrassegnare uno script come reso permanente dopo che è stato applicato a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita. Se si applica lo script come parte del processo di creazione del cluster, questo viene reso automaticamente permanente.

## <a name="next-steps"></a>Passaggi successivi

Per i passaggi specifici sull'uso di azioni script, vedere le sezioni seguenti in [personalizzare cluster HDInsight basati su Linux tramite azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
