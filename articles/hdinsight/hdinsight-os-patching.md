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
ms.openlocfilehash: d0a490fd3b23c96923af10db3c1f9ee9ea0dfad5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044892"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight basati su Linux 

> [!IMPORTANT]
> Le immagini Ubuntu diventano disponibili per la creazione di nuovi cluster HDInsight di Azure entro tre mesi dalla pubblicazione. A partire dal 2019 gennaio, i cluster in esecuzione non vengono sottoposti a patch automatici. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

Occasionalmente, è necessario riavviare le macchine virtuali (VM) in un cluster HDInsight per installare importanti patch di sicurezza.

Utilizzando le azioni script descritte in questo articolo, è possibile modificare la pianificazione dell'applicazione di patch del sistema operativo come indicato di seguito:

1. Installare tutti gli aggiornamenti oppure installare solo gli aggiornamenti kernel + Security o kernel.
2. Eseguire un riavvio immediato o pianificare un riavvio della macchina virtuale.

> [!NOTE]  
> Le azioni script descritte in questo articolo funzioneranno solo con i cluster HDInsight basati su Linux creati dopo il 1 ° agosto 2016. Le patch hanno effetto solo dopo il riavvio delle macchine virtuali.
> Le azioni script non applicano automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuri. Eseguire gli script ogni volta che è necessario applicare nuovi aggiornamenti per installare gli aggiornamenti, quindi riavviare la macchina virtuale.

## <a name="add-information-to-the-script"></a>Aggiungere informazioni allo script

Per usare uno script sono necessarie le informazioni seguenti:

- Il percorso dello script Install-Updates-Schedule-reboots: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight usa questo URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster. Questo script fornisce le opzioni per installare gli aggiornamenti e riavviare la macchina virtuale.
  
- Il percorso dello script Schedule-reboots: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight usa questo URI per trovare ed eseguire lo script in tutte le macchine virtuali del cluster. Questo script riavvia la macchina virtuale.
  
- I tipi di nodo del cluster a cui viene applicato lo script sono nodo Head, workernode e Zookeeper. Applicare lo script a tutti i tipi di nodo nel cluster. Se lo script non viene applicato a un tipo di nodo, le macchine virtuali per quel tipo di nodo non verranno aggiornate o riavviate.

- Lo script Install-Updates-Schedule-reboots accetta due parametri numerici:

    | Parametro | Definizione |
    | --- | --- |
    | Installa solo gli aggiornamenti del kernel/installa tutti gli aggiornamenti/installa solo kernel + Security Updates|0, 1 o 2. Il valore 0 installa solo gli aggiornamenti del kernel. Il valore 1 installa tutti gli aggiornamenti e 2 installa solo gli aggiornamenti kernel + Security. Se non viene specificato alcun parametro, il valore predefinito è 0. |
    | Nessun riavvio/Abilita pianificazione riavvio/Abilita riavvio immediato |0, 1 o 2. Il valore 0 Disabilita il riavvio. Il valore 1 consente il riavvio della pianificazione e 2 Abilita il riavvio immediato. Se non viene specificato alcun parametro, il valore predefinito è 0. L'utente deve modificare il parametro di input 1 nel parametro di input 2. |
   
 - Lo script Schedule-reboots accetta un parametro numerico:

    | Parametro | Definizione |
    | --- | --- |
    | Abilita riavvio pianificazione/Abilita riavvio immediato |1 o 2. Il valore 1 consente il riavvio della pianificazione (pianificato in 12-24 ore). Il valore 2 Abilita il riavvio immediato (in 5 minuti). Se non viene specificato alcun parametro, il valore predefinito è 1. |  

> [!NOTE]
> È necessario contrassegnare uno script come reso permanente dopo che è stato applicato a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita. Se si applica lo script come parte del processo di creazione del cluster, questo viene reso automaticamente permanente.


## <a name="next-steps"></a>Passaggi successivi

Per i passaggi specifici sull'uso di azioni script, vedere le sezioni seguenti in [personalizzare cluster HDInsight basati su Linux tramite azione script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usare un'azione script durante la creazione di un cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
