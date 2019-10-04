---
title: Gestire l'agente di mobilità nei server per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Gestire l'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 0a8b3a8bcfc2aa8270d7be140a94e5b83973f3e5
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972120"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Gestire l'agente Mobility nei computer protetti

Quando si usa Azure Site Recovery per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure, si configura l'agente di mobilità nel server. L'agente di mobilità coordina le comunicazioni tra il computer protetto, il server di configurazione/server di elaborazione con scalabilità orizzontale e gestisce la replica dei dati. Questo articolo riepiloga le attività comuni per la gestione dell'agente di mobilità dopo la distribuzione.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aggiornare il servizio Mobility dalla portale di Azure

1. Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.
2. Nel portale aprire l'insieme di credenziali > **Elementi replicati**.
3. Se la versione del server di configurazione è la più recente, viene visualizzata una notifica con il messaggio "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Fare clic sulla notifica e in **Aggiornamento agente** selezionare i computer in cui si intende aggiornare il servizio Mobility. Fare quindi clic su **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Viene avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aggiornare il servizio Mobility tramite uno script di PowerShell in Windows Server

Usare lo script seguente per aggiornare il servizio Mobility in un server tramite il cmdlet di Power Shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aggiornamento dell'account usato per l'installazione push del servizio Mobility

Quando è stato distribuito Site Recovery, per consentire l'installazione push del servizio Mobility è stato specificato un account che il server di elaborazione di Site Recovery usa per accedere ai computer e installare il servizio quando è abilitata la replica per la macchina. Se si intende aggiornare le credenziali per questo account, seguire [queste istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Disinstalla servizio Mobility

### <a name="on-a-windows-machine"></a>In un computer Windows

Eseguire la disinstallazione dall'interfaccia utente o da un prompt dei comandi.

- **Dall'interfaccia utente**: nel Pannello di controllo della macchina selezionare **Programmi**. Selezionare **Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master** > **Disinstalla**.
- **Da un prompt dei comandi:** : aprire una finestra del prompt dei comandi come amministratore nel computer. Eseguire il comando seguente: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>In un computer Linux
1. In un computer Linux accedere come utente **ROOT**.
2. In un terminale passare a/usr/local/ASR
3. Eseguire il comando seguente:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Installare Site Recovery provider VSS nel computer di origine

Azure Site Recovery provider VSS è necessario nel computer di origine per generare i punti di coerenza dell'applicazione. Se l'installazione del provider non ha avuto esito positivo tramite l'installazione push, attenersi alle linee guida indicate di seguito per installarla manualmente.

1. Aprire la finestra cmd amministratore.
2. Passare al percorso di installazione del servizio Mobility. (Ad esempio, C:\Programmi (x86) \Microsoft Azure site Recovery\agent)
3. Eseguire lo script InMageVSSProvider_Uninstall. cmd. Il servizio verrà disinstallato se esiste già.
4. Eseguire lo script InMageVSSProvider_Install. cmd per installare manualmente il provider VSS.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il ripristino di emergenza per macchine virtuali VMware](vmware-azure-tutorial.md)
- [Configurare il ripristino di emergenza per server fisici](physical-azure-disaster-recovery.md)
