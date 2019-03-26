---
title: Gestire l'agente di mobilità nei server per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Gestire l'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 441b58e60bf8dfd5f164ac24d746b9791158ade2
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420118"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Gestire l'agente di mobilità nei computer protetti

È configurare agente di mobilità nel server quando si usa Azure Site Recovery per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure. Agente di mobilità coordina le comunicazioni tra computer protetto, configurazione server di elaborazione di server di scalabilità orizzontale e gestisce la replica dei dati. Questo articolo riepiloga le attività comuni per la gestione dell'agente di mobilità dopo la distribuzione.

## <a name="update-mobility-service-from-azure-portal"></a>Aggiornamento del servizio mobility dal portale di Azure

1. Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.
2. Nel portale aprire l'insieme di credenziali > **Elementi replicati**.
3. Se la versione del server di configurazione è la più recente, viene visualizzata una notifica con il messaggio "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Fare clic sulla notifica e in **Aggiornamento agente** selezionare i computer in cui si intende aggiornare il servizio Mobility. Fare quindi clic su **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Viene avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aggiornamento del servizio Mobility tramite script di powershell nel server di Windows

Usare lo script per aggiornare il servizio mobility in un server tramite il cmdlet power shell seguente

```azurepowershell
Update-AzureRmRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aggiornare l'account usato per l'installazione push del servizio Mobility

Quando è stato distribuito Site Recovery, per consentire l'installazione push del servizio Mobility è stato specificato un account che il server di elaborazione di Site Recovery usa per accedere ai computer e installare il servizio quando è abilitata la replica per la macchina. Se si intende aggiornare le credenziali per questo account, seguire [queste istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Disinstallare il servizio Mobility

### <a name="on-a-windows-machine"></a>In un computer Windows

Eseguire la disinstallazione dall'interfaccia utente o da un prompt dei comandi.

- **Dall'interfaccia utente**: nel Pannello di controllo della macchina selezionare **Programmi**. Selezionare **Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master** > **Disinstalla**.
- **Da un prompt dei comandi:**: aprire una finestra del prompt dei comandi come amministratore nel computer. Eseguire il comando seguente: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>In un computer Linux
1. In un computer Linux accedere come utente **ROOT**.
2. Nel terminale, passare a /user/local/ASR.
3. Eseguire il comando seguente:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
