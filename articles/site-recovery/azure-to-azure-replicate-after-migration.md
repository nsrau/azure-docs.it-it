---
title: Configurare il ripristino di emergenza dopo la migrazione ad Azure con Azure Site Recovery
description: Questo articolo descrive come preparare le macchine per configurare il ripristino di emergenza tra le aree di Azure dopo la migrazione ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cd15b562c3707a28b54bc59166d54871120909e2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084944"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure dopo la migrazione ad Azure 


Seguire questo articolo se è stata [eseguita la migrazione di computer locali alle VM di Azure](tutorial-migrate-on-premises-to-azure.md) usando il servizio [Site Recovery](site-recovery-overview.md) e si desidera ora configurare le macchine virtuali per il ripristino di emergenza in un'area di Azure secondaria. Questo articolo descrive come verificare che l'agente di macchine virtuali di Azure sia installato nelle VM migrate e come rimuovere il servizio di Site Recovery Mobility che non è più necessario dopo la migrazione.



## <a name="verify-migration"></a>Verifica migrazione

Prima di configurare il ripristino di emergenza assicurarsi che la migrazione sia stata completata come previsto. Per completare una migrazione, dopo il failover è necessario selezionare l'opzione **Completa la migrazione** per ogni computer di cui eseguire la migrazione. 

## <a name="verify-the-azure-vm-agent"></a>Verificare l'agente di macchine virtuali di Azure

Per ogni macchina virtuale di Azure deve essere installato l' [agente di macchine virtuali di Azure](../virtual-machines/extensions/agent-windows.md) . Per replicare le macchine virtuali di Azure, Site Recovery installa un'estensione nell'agente.

- Se il computer esegue la versione 9.7.0.0 o successiva del servizio Site Recovery Mobility, l'agente di macchine virtuali di Azure viene installato automaticamente dal servizio Mobility nelle macchine virtuali Windows. Nelle versioni precedenti del servizio Mobility è necessario installare l'agente manualmente.
- Per le macchine virtuali Linux, è necessario installare manualmente l'agente di macchine virtuali di Azure. È necessario installare l'agente di macchine virtuali di Azure solo se nella macchina di cui è stata eseguita la migrazione è installata la versione 9.6 o precedente del servizio Mobility.


### <a name="install-the-agent-on-windows-vms"></a>Installare l'agente nelle VM Windows

Se si esegue una versione di Site Recovery servizio Mobility precedente a 9.7.0.0 o se è necessario installare l'agente manualmente, eseguire le operazioni seguenti:  

1. Assicurarsi di disporre delle autorizzazioni di amministratore per la macchina virtuale.
2. Scaricare il [programma di installazione dell'agente di macchine virtuali](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Eseguire il file del programma di installazione.

#### <a name="validate-the-installation"></a>Convalidare l'installazione
Per verificare che l'agente sia installato:

1. Nella cartella C:\WindowsAzure\Packages della macchina virtuale di Azure VM dovrebbe essere presente il file WaAppAgent.exe.
2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**.
3. Verificare che il valore visualizzato nel campo **Versione prodotto** sia uguale o superiore a 2.6.1198.718.

[Altre](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) informazioni sull'installazione di Agent per Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installare l'agente nelle VM Linux

Installare manualmente l'agente di [macchine virtuali Linux di Azure](../virtual-machines/extensions/agent-linux.md) come indicato di seguito:

1. Assicurarsi di disporre delle autorizzazioni di amministratore per il computer.
2. È consigliabile installare l'agente di macchine virtuali Linux usando un RPM o un pacchetto DEB dal repository del pacchetto della distribuzione. Tutti i [provider di distribuzione supportati](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrano il pacchetto agente Linux di Azure nelle immagini e nei repository.
    - Si consiglia vivamente di aggiornare l'agente solo tramite un repository di distribuzione.
    - Non è consigliabile installare l'agente di macchine virtuali Linux direttamente da GitHub e aggiornarlo.
    -  Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. 

#### <a name="validate-the-installation"></a>Convalidare l'installazione 

1. Eseguire questo comando: **PS-e** per assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale Linux.
2. Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:
    - Per Ubuntu: **avvio del servizio walinuxagent**
    - Per altre distribuzioni: **Service waagent Start**


## <a name="uninstall-the-mobility-service"></a>Disinstallare il servizio Mobility

1. Disinstallare manualmente il servizio Mobility dalla macchina virtuale di Azure usando uno dei metodi seguenti. 
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

[Esaminare la risoluzione dei problemi](site-recovery-extension-troubleshoot.md) relativi all'estensione Site Recovery nell'agente di macchine virtuali di Azure.
[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
