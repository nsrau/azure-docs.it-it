---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure dopo la migrazione ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come preparare le macchine per configurare il ripristino di emergenza tra le aree di Azure dopo la migrazione ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4b764c8e7c3d97ff521add05033265f705c4136f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399531"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure dopo la migrazione ad Azure 


Seguire questo articolo, se è già [eseguire la migrazione di macchine virtuali locali alle macchine virtuali di Azure](tutorial-migrate-on-premises-to-azure.md) usando la [Site Recovery](site-recovery-overview.md) servizio e volerla ora ottenere le macchine virtuali impostato per il ripristino di emergenza in un'area di Azure secondaria. L'articolo descrive come verificare che sia installato l'agente di macchine Virtuali di Azure su macchine virtuali migrate e come rimuovere il servizio Mobility di Site Recovery non è più necessario dopo la migrazione.



## <a name="verify-migration"></a>Verificare la migrazione

Prima di configurare il ripristino di emergenza assicurarsi che la migrazione sia stata completata come previsto. Per completare una migrazione, dopo il failover è necessario selezionare l'opzione **Completa la migrazione** per ogni computer di cui eseguire la migrazione. 

## <a name="verify-the-azure-vm-agent"></a>Verificare l'agente di macchine Virtuali di Azure

Ogni macchina virtuale di Azure deve avere il [agente di macchine Virtuali di Azure](../virtual-machines/extensions/agent-windows.md) installato. Per replicare macchine virtuali di Azure, Site Recovery installa un'estensione dell'agente.

- Se il computer è in esecuzione versione 9.7.0.0 o versioni successive del servizio Mobility di Site Recovery, l'agente di macchine Virtuali di Azure viene installato automaticamente dal servizio Mobility nelle macchine virtuali di Windows. Nelle versioni precedenti del servizio Mobility, è necessario installare automaticamente l'agente.
- Per le macchine virtuali Linux, è necessario installare manualmente l'agente di macchine Virtuali di Azure. È sufficiente installare l'agente di macchine Virtuali di Azure se il servizio Mobility installato nella macchina migrata è versione 9.6 o versioni precedenti.


### <a name="install-the-agent-on-windows-vms"></a>Installare l'agente nelle macchine virtuali Windows

Se si esegue una versione del servizio di mobilità di Site Recovery precedenti alla 9.7.0.0 o alcuni altri è necessario installare manualmente l'agente, eseguire le operazioni seguenti:  

1. Assicurarsi di che avere autorizzazioni di amministratore nella macchina virtuale.
2. Scaricare il [programma di installazione di agente di macchine Virtuali](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Eseguire il file di programma di installazione.

#### <a name="validate-the-installation"></a>Convalidare l'installazione
Per verificare che l'agente è installato:

1. Nella cartella C:\WindowsAzure\Packages della macchina virtuale di Azure VM dovrebbe essere presente il file WaAppAgent.exe.
2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**.
3. Verificare che il valore visualizzato nel campo **Versione prodotto** sia uguale o superiore a 2.6.1198.718.

[Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sull'installazione dell'agente per Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installare l'agente nelle macchine virtuali Linux

Installare il [VM Linux di Azure](../virtual-machines/extensions/agent-linux.md) agente manualmente come indicato di seguito:

1. Assicurarsi di avere le autorizzazioni di amministratore nel computer.
2. È consigliabile installare l'agente VM Linux usando un RPM o un pacchetto DEB dal repository di pacchetti della distribuzione. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository.
    - È consigliabile aggiornare l'agente solo tramite un repository di distribuzione.
    - Non è consigliabile installare l'agente VM Linux direttamente da GitHub e aggiornarlo.
    -  Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. 

#### <a name="validate-the-installation"></a>Convalidare l'installazione 

1. Eseguire questo comando: **ps -e** per assicurarsi che l'agente di Azure sia in esecuzione nella VM Linux.
2. Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:
    - Per Ubuntu: **walinuxagent avvio del servizio**
    - Per altre distribuzioni: **avvio waagent del servizio**


## <a name="uninstall-the-mobility-service"></a>Disinstallare il servizio Mobility

1. Disinstallare manualmente il servizio Mobility dalla macchina virtuale di Azure, usando uno dei metodi seguenti. 
    - Per Windows, nel Pannello di controllo > **Installazione applicazioni** disinstallare **Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master**. Al prompt dei comandi con privilegi elevati, eseguire:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Per Linux accedere come utente ROOT. In un terminale passare a **/user/local/ASR** ed eseguire il comando seguente:
        ```
        ./uninstall.sh -Y
        ```
2. Riavviare la macchina virtuale prima di configurare la replica.

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi verifica](site-recovery-extension-troubleshoot.md) per l'estensione Site Recovery nell'agente di macchine Virtuali di Azure.
[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
