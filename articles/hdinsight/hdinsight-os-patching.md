---
title: Configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux - Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203649"
---
# <a name="os-patching-for-hdinsight"></a>Applicazione di patch del sistema operativo per HDInsight 

> [!IMPORTANT]
> Le immagini di Ubuntu diventano disponibili per la nuova creazione del cluster HDInsight entro 3 mesi dalla pubblicazione. A partire da gennaio 2019, le patch **non** sono applicate automaticamente ai cluster in esecuzione. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Procedura per configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster HDInsight basati su Linux
Le macchine virtuali in un cluster HDInsight devono essere riavviate di tanto in tanto in modo che sia possibile installare le patch di sicurezza importanti. 

Tramite l'azione di script descritta in questo articolo, è possibile modificare la pianificazione dell'applicazione delle patch al sistema operativo come segue:
1. Installare gli aggiornamenti del sistema operativo completi o solo gli aggiornamenti della sicurezza
2. Riavviare la macchina virtuale

> [!NOTE]  
> Questa azione di script funziona solo con i cluster HDInsight basati su Linux creati dopo il 1° agosto 2016. I patch verranno applicati solo al riavvio delle macchine virtuali. Questo script non verrà applicata automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuro. Eseguire lo script che ogni nuovo aggiornamento desidera essere applicati per installare gli aggiornamenti e riavviare la macchina virtuale.

## <a name="how-to-use-the-script"></a>Come usare lo script 

L'uso di questo script richiede le informazioni seguenti:
1. Il percorso dello script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster.
  
2. I tipi di nodo di cluster che vengono applicati allo script: nodo head, nodo del ruolo di lavoro, zookeeper. Questo script deve essere applicato a tutti i tipi di nodo del cluster. Se non viene applicato a un tipo di nodo, quindi le macchine virtuali per quel tipo di nodo non verrà aggiornate.


3.  Parametro: Questo script accetta un parametro numerico:

    | Parametro | Definizione |
    | --- | --- |
    | Installare il sistema operativo completo. gli aggiornamenti/installare solo gli aggiornamenti della sicurezza |0 o 1. Il valore 0 consente di installare gli aggiornamenti della sicurezza solo mentre 1 consente di installare gli aggiornamenti del sistema operativo completi. Se non viene specificato alcun parametro, che il valore predefinito è 0. |

> [!NOTE]  
> Quando si applica a un cluster esistente, è necessario contrassegnare questo script come persistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita.  Se si applica lo script come parte del processo di creazione del cluster, viene mantenuto automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata sull'uso dell'azione di script, vedere le sezioni seguenti di [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
