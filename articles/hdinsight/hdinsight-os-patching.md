---
title: Configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux - Azure | Microsoft Docs
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>Applicazione di patch del sistema operativo per HDInsight 
Come servizio gestito di Hadoop, HDInsight si occupa dell'applicazione di patch al sistema operativo delle macchine virtuali sottostanti usate dal cluster HDInsight. A partire dal 1° agosto 2016, sono stati modificati i criteri di applicazione delle patch del sistema operativo guest per i cluster HDInsight basati su Linux (versione 3.4 o successiva). L'obiettivo del nuovo criterio è di ridurre in modo consistente il numero di riavvii causati dall'applicazione delle patch. Il nuovo criterio continuerà ad applicare le patch alle macchine virtuali sui cluster Linux ogni lunedì o giovedì a partire dalle ore 00:00 UTC con un flusso di intervalli sui nodi in ogni cluster. Tuttavia, le macchine virtuali verranno riavviate solo una volta ogni 30 giorni a causa dell'applicazione delle patch al sistema operativo guest. In aggiunta, il primo riavvio di un cluster appena creato non verrà eseguito prima di 30 giorni dalla data di creazione del cluster. I patch verranno applicati al riavvio delle macchine virtuali.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Procedura per configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster HDInsight basati su Linux
Le macchine virtuali in un cluster HDInsight devono essere riavviate di tanto in tanto in modo che sia possibile installare le patch di sicurezza importanti. A partire dal 1° agosto 2016, i nuovi cluster HDInsight basati su Linux (versione 3.4 o successive) verranno riavviati in base alla pianificazione seguente:

1. Una macchina virtuale nel cluster può solo riavviare il computer per le patch al massimo una volta in un periodo di 30 giorni.
2. Il riavvio avviene a partire da mezzanotte (UTC).
3. Il processo di riavvio è scaglionato tra le macchine virtuali del cluster, pertanto il cluster è ancora disponibile durante il processo di riavvio.
4. Il primo riavvio di un cluster appena creato non verrà eseguito prima di 30 giorni dalla data di creazione del cluster.

Tramite l'azione di script descritta in questo articolo, è possibile modificare la pianificazione dell'applicazione delle patch al sistema operativo come segue:
1. Abilitare o disabilitare il riavvio automatico
2. Impostare la frequenza di riavvio (giorni tra i riavvii)
3. Impostare il giorno della settimana in cui eseguire un riavvio

> [!NOTE]
> Questa azione di script funziona solo con i cluster HDInsight basati su Linux creati dopo il 1° agosto 2016. I patch verranno applicati solo al riavvio delle macchine virtuali. 
>

## <a name="how-to-use-the-script"></a>Come usare lo script 

L'uso di questo script richiede le informazioni seguenti:
1. Il percorso dello script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster.
  
2. I tipi di nodo di cluster che vengono applicati allo script: nodo head, nodo del ruolo di lavoro, zookeeper. Questo script deve essere applicato a tutti i tipi di nodo del cluster. Se non viene applicato a un tipo di nodo, le macchine virtuali per quel tipo di nodo continueranno a usare la pianificazione per l'applicazione di patch precedente.


3.  Parametro: Questo script accetta tre parametri numerici:

    | Parametro | Definizione |
    | --- | --- |
    | Abilitare/disabilitare il riavvio automatico |0 o 1. Il valore 0 disabilita il riavvio automatico, mentre 1 consente il riavvio automatico. |
    | Frequenza |da 7 a 90 (incluso). Il numero di giorni di attesa prima di riavviare le macchine virtuali per le patch che richiedono un riavvio. |
    | Giorno della settimana |da 1 a 7 (incluso). Il valore 1 indica che il riavvio deve essere eseguito il lunedì e 7 indica la domenica. Ad esempio usando i parametri di 1 60 2 si produce un riavvio automatico ogni 60 giorni (al massimo) il martedì. |
    | Persistenza |Quando si applica un'azione di script a un cluster esistente, è possibile contrassegnare lo script come persistente. Gli script persistenti vengono applicati quando vengono aggiunti nuovi nodi del ruolo di lavoro al cluster tramite operazioni di ridimensionamento. |

> [!NOTE]
> Quando si applica a un cluster esistente, è necessario contrassegnare questo script come persistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno il la pianificazione dell'applicazione di patch predefinita.
Se si applica lo script come parte del processo di creazione del cluster, viene mantenuto automaticamente.
>

## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata sull'uso dell'azione di script, vedere le sezioni seguenti di [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
