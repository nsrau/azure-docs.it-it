---
title: Eseguire il mapping di archiviazione in Azure Site Recovery per la replica di macchine virtuali Hyper-V tra centri dati logici locali | Microsoft Docs
description: Preparare il mapping di archiviazione per la replica di macchine virtuali Hyper-V tra due data center locali con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 20e14e9c-87c4-4ad3-aa95-441697b1fc43
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1f21c41374e2c09a6b9706df2e084123de035f1a


---
# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Preparare il mapping di archiviazione per la replica di macchine virtuali Hyper-V tra due data center locali con Azure Site Recovery
Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. Questo articolo descrive il mapping di archiviazione, un processo che consente un utilizzo ottimale dello spazio di archiviazione quando si usa Site Recovery per replicare macchine virtuali Hyper-V tra due data center VMM locali.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica
Il mapping di archiviazione è utile solo quando si esegue la replica di macchine virtuali Hyper-V presenti in cloud VMM da un data center principale in un data center secondario usando la replica Hyper-V o SAN, come indicato di seguito:

* **Replica da sito locale a sito locale con la replica Hyper-V**: viene eseguito il mapping delle classificazioni di archiviazione su un server VMM di origine e uno di destinazione per eseguire le operazioni seguenti:
  
  * **Identificare la risorsa di archiviazione di destinazione per le macchine virtuali di replica**: le macchine virtuali verranno replicate in una destinazione di archiviazione (condivisione SMB o volumi condivisi del cluster) desiderata.
  * **Posizionamento delle macchine virtuali di replica**: il mapping di archiviazione viene usato per posizionare in modo ottimale le macchine virtuali di replica nei server host Hyper-V. Le macchine virtuali di replica saranno posizionate negli host che possono accedere alla classificazione di archiviazione mappata.
  * **Nessun mapping di archiviazione**: se non si configura il mapping di archiviazione, le macchine virtuali vengono replicate nella posizione di archiviazione predefinita specificata nel server host Hyper-V associato alla macchina virtuale di replica.
* **Replica da sito locale a sito locale con la replica SAN**: viene eseguito il mapping dei pool di array di archiviazione su un server VMM di origine e uno di destinazione.
  
  * **Specificare il pool**: specifica il pool di archiviazione secondario che dovrà ricevere i dati di replica dal pool principale.
  * **Identificare pool di archiviazione di destinazione**: garantisce che i LUN di un gruppo di replica di origine siano replicati nel pool di archiviazione di destinazione mappato precedentemente indicato.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurare classificazioni di archiviazione per la replica Hyper-V
Quando si usa la replica Hyper-V con Site Recovery, si esegue il mapping tra le classificazioni di archiviazione nei server VMM di origine e di destinazione o in un singolo server VMM, se i due siti sono gestiti dallo stesso server VMM. Si noti che:

* Quando il mapping è configurato correttamente e la replica è abilitata, il disco rigido virtuale di una macchina virtuale nella posizione primaria verrà replicato nella risorsa di archiviazione nella posizione di destinazione mappata.
* Le classificazioni di archiviazione devono essere disponibili per i gruppi host situati nei cloud di origine e di destinazione.
* Le classificazioni non devono necessariamente avere lo stesso tipo di archiviazione. È possibile ad esempio mappare una classificazione di origine contenente condivisioni SMB a una classificazione di destinazione contenente CSV
* Per ulteriori informazioni, vedere [Come creare classificazioni di archiviazione in VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Esempio
Se le classificazioni sono configurate correttamente in VMM quando si selezionano il server VMM di origine e destinazione durante il mapping di archiviazione, verranno visualizzate le classificazioni di origine e destinazione. Di seguito è riportato un esempio di condivisioni di file di archiviazione e le classificazioni per un'organizzazione con due posizioni a New York e Chicago.

| **Posizione** | **Server VMM** | **Condivisione file (origine)** | **Classificazione (origine)** | **Mappata a** | **Condivisione file (destinazione)** |
| --- | --- | --- | --- | --- | --- |
| New York |VMM_Source |SourceShare1 |GOLD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZE |BRONZE_TARGET |TargetShare3 | | |
| Chicago |VMM_Target | |GOLD_TARGET |Non mappata | |
|  |SILVER_TARGET |Non mappata | | | |
|  |BRONZE_TARGET |Non mappata | | | |

È possibile configurare questi elementi nella scheda **Archiviazione server** della pagina **Risorse** del portale di Site Recovery.

![Configurare il mapping di archiviazione](./media/site-recovery-storage-mapping/storage-mapping1.png)

Con questo esempio:

* Una macchina virtuale di replica che viene creata per una macchina virtuale in un archivio GOLD (SourceShare1) viene replicata in un archivio GOLD_TARGET (TargetShare1).
* Una macchina virtuale di replica che viene creata per una macchina virtuale in un archivio SILVER (SourceShare2) viene replicata in un archivio SILVER_TARGET (TargetShare2).

Le condivisioni di file effettive e le classificazioni assegnate in VMM vengono visualizzate nella schermata successiva.

![Classificazioni di archiviazione in VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Posizioni di archiviazione multiple
Se la classificazione di destinazione viene assegnata a più condivisioni SMB o volumi condivisi del cluster, la posizione di archiviazione ottimale verrà selezionata automaticamente quando la macchina virtuale è protetta. Se con la classificazione specificata non è disponibile una risorsa di archiviazione di destinazione adeguata, la posizione di archiviazione predefinita specificata nell'host Hyper-V viene utilizzata per inserire i dischi rigidi virtuali di replica.

Nella tabella seguente viene illustrato come la classificazione di archiviazione e i volumi condivisi del cluster sono configurati nell’esempio.

| **Posizione** | **Classificazione** | **Risorsa di archiviazione associata** |
| --- | --- | --- |
| New York |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| Chicago |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

In questa tabella viene riepilogato il comportamento quando si abilita la protezione per le macchine virtuali (VM1 - VM5) in questo ambiente di esempio.

| **Macchina virtuale** | **Risorsa di archiviazione di origine** | **Classificazione di origine** | **Risorsa di archiviazione di destinazione mappata** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Entrambi GOLD_TARGET</p> |
| VM2 |\\FileServer\SourceShare1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Entrambi GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Entrambi SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |N/D |Nessun mapping, per cui viene utilizzata la posizione di archiviazione predefinita dell'host Hyper-V |

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito familiarità con il mapping di archiviazione, è ora possibile [prepararsi alla distribuzione di Azure Site Recovery](site-recovery-best-practices.md).




<!--HONumber=Nov16_HO3-->


