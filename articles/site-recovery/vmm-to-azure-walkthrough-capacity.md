---
title: "Pianificare la capacità e la scalabilità per la replica di VM Hyper-V (con VMM) in Azure con Azure Site Recovery | Microsoft Docs"
description: "Usare le istruzioni disponibili in questo articolo per pianificare la capacità e la scalabilità durante la replica di VM Hyper-V in cloud VMM in Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---

# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>Passaggio 3: Pianificare la capacità e la scalabilità per la replica di Hyper-V in Azure (con VMM)

Dopo avere verificato i [prerequisiti per la distribuzione](vmm-to-azure-walkthrough-prerequisites.md), usare le istruzioni disponibili in questo articolo per configurare la pianificazione per la capacità e la scalabilità durante la replica di VM Hyper-V locali in cloud System Center Virtual Machine Manager (VMM) in Azure con [Azure Site Recovery](site-recovery-overview.md).

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="how-do-i-start-capacity-planning"></a>Come si inizia per pianificare la capacità?


Raccogliere informazioni sull'ambiente di replica e quindi pianificare la capacità usando i dati, insieme alle considerazioni illustrate in questo articolo.


## <a name="gather-information"></a>Raccogliere informazioni

1. Raccogliere informazioni sull'ambiente di replica, incluse le macchine virtuali, i dischi per ogni macchina virtuale e le risorse di archiviazione per ogni disco.
2. Identificare la frequenza di modifica giornaliera (varianza) per i dati replicati. Scaricare lo [strumento di pianificazione della capacità di Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) per ottenere la frequenza di modifica. È consigliabile eseguire lo strumento nel corso di una settimana per acquisire le medie.
 

## <a name="figure-out-capacity"></a>Calcolare la capacità

In base alle informazioni raccolte, eseguire lo [strumento Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) per analizzare l'ambiente di origine e i carichi di lavoro e stimare le esigenze di larghezza di banda e le risorse server per la posizione di origine, oltre che le risorse (macchine virtuali, archiviazione e così via) necessarie nella posizione di destinazione. È possibile eseguire lo strumento nelle due modalità descritte di seguito:

- Pianificazione rapida: eseguire lo strumento in questa modalità per ottenere le proiezioni di rete e server in base al numero medio di VM, dischi, archiviazione e frequenza di modifica.
- Pianificazione dettagliata: eseguire lo strumento in questa modalità e specificare i dettagli di ogni carico di lavoro a livello di VM. Analizzare la compatibilità della macchina virtuale e ottenere le proiezioni di rete e server.

Eseguire ora lo strumento:

1. Scaricare lo [strumento](http://aka.ms/asr-capacity-planner-excel)
2. Per eseguire la pianificazione rapida, seguire [queste istruzioni](site-recovery-capacity-planner.md#run-the-quick-planner) e selezionare lo scenario **Da Hyper-V ad Azure**.
3. Per eseguire la pianificazione dettagliata, seguire [queste istruzioni](site-recovery-capacity-planner.md#run-the-detailed-planner) e selezionare lo scenario **Da Hyper-V ad Azure**.

## <a name="control-network-bandwidth"></a>Controllare la larghezza di banda della rete

Dopo aver calcolato la larghezza di banda necessaria, sono disponibili un paio di opzioni per controllare la quantità di larghezza di banda usata per la replica:

* **Limitare la larghezza di banda**: il traffico Hyper-V che viene replicato in Azure passa per un host Hyper-V specifico. È possibile limitare la larghezza di banda nel server host.
* **Influire sulla larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite un paio di chiavi del Registro di sistema.

### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda
1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nel server host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'uso della larghezza di banda Internet per le operazioni di backup** e impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Limitare la larghezza di banda](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

### <a name="influence-network-bandwidth"></a>Influire sulla larghezza di banda di rete
1. Nel Registro di sistema passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM**oppure creare la chiave, se non esiste.
   * Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 4: Pianificare la rete](vmm-to-azure-walkthrough-network.md).

