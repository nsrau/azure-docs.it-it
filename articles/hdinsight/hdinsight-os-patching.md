---
title: Configurare il sistema operativo dell'applicazione di patch pianificazione per i cluster HDInsight basati su Linux - Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657045"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurare il sistema operativo dell'applicazione di patch pianificazione per i cluster HDInsight basati su Linux 

> [!IMPORTANT]
> Immagini di Ubuntu diventano disponibili per la nuova creazione del cluster HDInsight di Azure all'interno di tre mesi di essere pubblicata. A partire da gennaio 2019, i cluster in esecuzione non vengono aggiornati automaticamente. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

In alcuni casi, è necessario riavviare le macchine virtuali (VM) in un cluster HDInsight per installare le patch di sicurezza importante.

Usando le azioni di script descritte in questo articolo, è possibile modificare il sistema operativo l'applicazione delle patch pianificazione come indicato di seguito:

1. Installare tutti gli aggiornamenti o solo di kernel + gli aggiornamenti della sicurezza o gli aggiornamenti del kernel.
2. Eseguire un'operazione di riavvio immediato o pianificare un riavvio della macchina virtuale.

> [!NOTE]  
> Le azioni di script descritte in questo articolo funzioneranno solo con cluster HDInsight basati su Linux creati dopo il 1 ° agosto 2016. Le patch sono efficaci solo dopo il riavvio delle macchine virtuali.
> Le azioni di script non verranno applicato automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuro. Eseguire gli script ogni volta che è necessario applicare nuovi aggiornamenti da installare gli aggiornamenti e quindi riavviare la macchina virtuale.

## <a name="add-information-to-the-script"></a>Aggiungere informazioni allo script

Tramite uno script richiede le informazioni seguenti:

- Percorso dello script install-aggiornamenti-pianificazione-riavvii: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight Usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali nel cluster. Questo script fornisce opzioni per installare gli aggiornamenti e riavviare la macchina virtuale.
  
- Il riavvio di pianificazione percorso dello script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight Usa l'URI per trovare ed eseguire lo script in tutte le macchine virtuali nel cluster. Questo script riavvia la macchina virtuale.
  
- I tipi di nodo del cluster che lo script venga applicato a sono nodi HEAD, nodo di lavoro e zookeeper. Applicare lo script per tutti i tipi di nodo del cluster. Se lo script non viene applicato a un tipo di nodo, le macchine virtuali per quel tipo di nodo non verranno aggiornate o riavviate.

- Lo script install-aggiornamenti-pianificazione-riavvii accetta due parametri numerici:

    | Parametro | Definizione |
    | --- | --- |
    | Installare solo gli aggiornamenti del kernel / installare tutte le risorse kernel/installare gli aggiornamenti e sicurezza Aggiorna solo|0, 1 o 2. Il valore 0 consente di installare solo gli aggiornamenti del kernel. Il valore 1 consente di installare tutti gli aggiornamenti e kernel solo 2 installazioni + gli aggiornamenti della sicurezza. Se non viene specificato alcun parametro, il valore predefinito è 0. |
    | Nessun riavvio immediato di riavviare o abilitare pianificazione riavvio o abilitare |0, 1 o 2. Il valore 0 disabilita il riavvio. Pianificazione riavvio consente a un valore pari a 1 e 2 consente il riavvio immediato. Se non viene specificato alcun parametro, il valore predefinito è 0. L'utente deve modificare il parametro di input 1 e 2 di parametro di input. |
   
 - Lo script di pianificazione riavvii accetta un parametro numerico:

    | Parametro | Definizione |
    | --- | --- |
    | Abilitare il riavvio immediato di pianificazione riavviare o abilitare |1 o 2. Il valore 1 abilita il riavvio di pianificazione (operazione pianificato nell'12-24 ore). Un valore pari a 2 consente il riavvio immediato (entro 5 minuti). Se non è specificato alcun parametro, il valore predefinito è 1. |  

> [!NOTE]
> È necessario contrassegnare uno script come persistente dopo averlo applicato a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita. Se si applica lo script come parte del processo di creazione del cluster, ha mantenuto automaticamente.


## <a name="next-steps"></a>Passaggi successivi

Per passaggi specifici sull'uso di azioni script, vedere le sezioni seguenti in [cluster HDInsight basati su personalizzare Linux tramite azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
