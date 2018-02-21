---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure dopo la migrazione ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come preparare le macchine per configurare il ripristino di emergenza tra le aree di Azure dopo la migrazione ad Azure usando Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: c06af21cd6e273b98c004e8bd0e6eac61ba7d644
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure dopo la migrazione ad Azure 

>[!NOTE]
> Il ripristino di emergenza per le macchine virtuali di Azure con Azure Site Recovery è attualmente disponibile in anteprima.

Leggere questo articolo dopo aver [eseguito la migrazione di computer locali a macchine virtuali di Azure](tutorial-migrate-on-premises-to-azure.md) con il servizio [Site Recovery](site-recovery-overview.md). Questo articolo spiega come preparare le macchine virtuali di Azure per la configurazione del ripristino di emergenza in un'area secondaria di Azure con Site Recovery.



## <a name="before-you-start"></a>Prima di iniziare

Prima di configurare il ripristino di emergenza assicurarsi che la migrazione sia stata completata come previsto. Per completare una migrazione, dopo il failover è necessario selezionare l'opzione **Completa la migrazione** per ogni computer di cui eseguire la migrazione. 



## <a name="install-the-azure-vm-agent"></a>Installare l'agente di macchine virtuali di Azure

L'[agente di macchine virtuali](../virtual-machines/windows/agent-user-guide.md) di Azure deve essere installato nella macchina virtuale per consentire a Site Recovery di eseguire la replica.


1. Per installare l'agente di macchine virtuali in macchine virtuali che eseguono Windows, scaricare ed eseguire il [programma di installazione dell'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione, sono necessari i privilegi di amministratore.
2. Per installare l'agente di macchine virtuali in macchine virtuali che eseguono Linux, installare l'ultima versione dell'[agente Linux](../virtual-machines/linux/agent-user-guide.md). Per completare l'installazione è necessario disporre dei privilegi di amministratore. È consigliabile eseguire l'installazione dal repository di distribuzione. Non è consigliabile installare l'agente di macchine virtuali Linux direttamente da GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Convalidare l'installazione in macchine virtuali Windows

1. Nella cartella C:\WindowsAzure\Packages della macchina virtuale di Azure VM dovrebbe essere presente il file WaAppAgent.exe.
2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**.
3. Verificare che il valore visualizzato nel campo **Versione prodotto** sia uguale o superiore a 2.6.1198.718.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migrazione da server fisici o macchine virtuali VMware

Se si esegue la migrazione da server fisici o macchine virtuali VMware locali ad Azure, tenere presente che:

- È necessario installare l'agente di macchine virtuali di Azure solo se nella macchina di cui è stata eseguita la migrazione è installata la versione 9.6 o precedente del servizio Mobility.
- Nelle macchine virtuali Windows che eseguono la versione 9.7.0.0 o versioni successive del servizio Mobility il programma di installazione del servizio installa l'ultima versione disponibile dell'agente di macchine virtuali di Azure. Quando si esegue la migrazione queste macchine virtuali soddisfano già i prerequisiti di installazione dell'agente per qualsiasi estensione della macchina virtuale, inclusa Site Recovery.
- È necessario disinstallare manualmente il servizio Mobility dalla macchina virtuale di Azure usando uno dei metodi seguenti. Riavviare la macchina virtuale prima di configurare la replica.
    - Per Windows, nel Pannello di controllo > **Installazione applicazioni** disinstallare **Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master**. Al prompt dei comandi con privilegi elevati, eseguire:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Per Linux accedere come utente ROOT. In un terminale passare a **/user/local/ASR** ed eseguire il comando seguente:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Passaggi successivi

[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
