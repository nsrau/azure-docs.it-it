---
title: Configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux - Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503233"
---
# <a name="os-patching-for-hdinsight"></a>Applicazione di patch del sistema operativo per HDInsight 

> [!IMPORTANT]
> Immagini di Ubuntu diventano disponibili per la nuova creazione del cluster HDInsight all'interno di tre mesi di essere pubblicata. A partire da gennaio 2019, le patch **non** sono applicate automaticamente ai cluster in esecuzione. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Procedura per configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster HDInsight basati su Linux
Le macchine virtuali in un cluster HDInsight devono essere riavviate di tanto in tanto in modo che sia possibile installare le patch di sicurezza importanti. 

Tramite le azioni di script descritte in questo articolo, è possibile modificare il sistema operativo l'applicazione delle patch pianificazione come indicato di seguito:
1. Installare tutti gli aggiornamenti o installazione kernel + security solo aggiornamenti o installa solo gli aggiornamenti del kernel.
2. Riavvio immediato o pianificazione di un riavvio della macchina virtuale.

> [!NOTE]  
> Queste azioni di script funziona solo con i cluster HDInsight basati su Linux creati dopo il 1 ° agosto 2016. I patch verranno applicati solo al riavvio delle macchine virtuali. Questi script non verranno applicata automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuro. Eseguire gli script che ogni nuovo aggiornamento desidera essere applicati per installare gli aggiornamenti e riavviare la macchina virtuale.

## <a name="how-to-use-the-script"></a>Come usare lo script 

Con questo script richiede le informazioni seguenti:
1. Percorso dello script install-aggiornamenti-pianificazione-riavvii: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster. Questo script fornisce opzioni per installare gli aggiornamenti e riavviare la macchina virtuale.
  
2. Il riavvio di pianificazione percorso dello script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster. Questo script riavvia la macchina virtuale.
  
3. I tipi di nodo di cluster che vengono applicati allo script: nodo head, nodo del ruolo di lavoro, zookeeper. Questo script deve essere applicato a tutti i tipi di nodo del cluster. Se non viene applicato a un tipo di nodo, quindi le macchine virtuali per quel tipo di nodo non verrà aggiornate o riavviate.

4. Parametro: Lo script install-aggiornamenti-pianificazione-riavvii accetta due parametri numerici:

    | Parametro | Definizione |
    | --- | --- |
    | Installare solo gli aggiornamenti del kernel / installare tutte le risorse kernel/installare gli aggiornamenti e sicurezza Aggiorna solo |0 o 1 o 2. Il valore 0 consente di installare, solo gli aggiornamenti del kernel durante 1 installazioni di tutti gli aggiornamenti e kernel 2 installazioni + security Aggiorna solo. Se non viene specificato alcun parametro, il valore predefinito è 0. |
    | Nessun riavvio immediato di riavviare o abilitare pianificazione riavvio o abilitare |0 o 1 o 2. Il valore 0 disabilita il riavvio, mentre 1 abilita il riavvio di pianificazione e 2 abilita il riavvio immediato. Se non viene specificato alcun parametro, il valore predefinito è 0. L'utente deve immettere il parametro 1 alla 2 parametro di input. |
   
 5. Parametro: Lo script di pianificazione riavvii accetta un parametro numerico:

    | Parametro | Definizione |
    | --- | --- |
    | Abilitare il riavvio immediato di pianificazione riavviare o abilitare |1 o 2. Il valore 1 abilita il riavvio di pianificazione (riavvio viene pianificato in 12-24 ore successive) anche se il riavvio immediato 2 Abilita (in 5 minuti). Se non viene specificato alcun parametro, il valore predefinito è 1. |  

> [!NOTE] 
> È necessario contrassegnare lo script come persistente quando si applica a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita.  Se si applica lo script come parte del processo di creazione del cluster, viene mantenuto automaticamente.


## <a name="next-steps"></a>Passaggi successivi

Per passaggi specifici sull'uso dell'azione di script, vedere le sezioni seguenti in [cluster HDInsight basati su personalizzare Linux tramite azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
