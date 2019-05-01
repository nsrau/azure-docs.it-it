---
title: Configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux - Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a887d6c69b9fa80f3144434e72a097e80d123a1b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722289"
---
# <a name="os-patching-for-hdinsight"></a>Applicazione di patch del sistema operativo per HDInsight 

> [!IMPORTANT]
> Le immagini di Ubuntu diventano disponibili per la nuova creazione del cluster HDInsight entro 3 mesi dalla pubblicazione. A partire da gennaio 2019, le patch **non** sono applicate automaticamente ai cluster in esecuzione. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Procedura per configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster HDInsight basati su Linux
Le macchine virtuali in un cluster HDInsight devono essere riavviate di tanto in tanto in modo che sia possibile installare le patch di sicurezza importanti. 

Tramite l'azione di script descritta in questo articolo, è possibile modificare la pianificazione dell'applicazione delle patch al sistema operativo come segue:
1. Abilitare o disabilitare il riavvio automatico
2. Impostare la frequenza di riavvio (giorni tra i riavvii)
3. Impostare il giorno della settimana in cui eseguire un riavvio

> [!NOTE]  
> Questa azione di script funziona solo con i cluster HDInsight basati su Linux creati dopo il 1° agosto 2016. I patch verranno applicati solo al riavvio delle macchine virtuali. 

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
> Quando si applica a un cluster esistente, è necessario contrassegnare questo script come persistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita.  Se si applica lo script come parte del processo di creazione del cluster, viene mantenuto automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata sull'uso dell'azione di script, vedere le sezioni seguenti di [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
