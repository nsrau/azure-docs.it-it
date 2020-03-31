---
title: Gestire l'agente Mobility per i server VMware/physical con Azure Site Recovery
description: Gestire l'agente del servizio Mobility per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure usando il servizio Azure Site Recovery.Manage Mobility Service agent for disaster recovery of VMware VMs and physical servers to Azure using the Azure Site Recovery service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256965"
---
# <a name="manage-the-mobility-agent"></a>Gestire l'agente di mobilità 

You set up mobility agent on your server when you use Azure Site Recovery for disaster recovery of VMware VMs and physical servers to Azure. L'agente Mobility coordina le comunicazioni tra il computer protetto, il server di configurazione/server di elaborazione con scalabilità orizzontale e gestisce la replica dei dati. In questo articolo vengono riepilogate le attività comuni per la gestione dell'agente per dispositivi mobili dopo la distribuzione.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aggiornare il servizio per dispositivi mobili dal portale di AzureUpdate mobility service from Azure portal

1. Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.
2. Nel portale aprire l'insieme di credenziali > **Elementi replicati**.
3. Se la versione del server di configurazione è la più recente, viene visualizzata una notifica con il messaggio "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Fare clic sulla notifica e in **Aggiornamento agente** selezionare i computer in cui si intende aggiornare il servizio Mobility. Fare quindi clic su **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Viene avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aggiornare il servizio Mobility tramite script di PowerShell nel server WindowsUpdate Mobility service through powershell script on Windows server

Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.

Utilizzare lo script seguente per aggiornare il servizio mobility in un server tramite il cmdlet power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Aggiornare manualmente il servizio mobility in ogni server protettoUpdate mobility service manually on each protected server

1. Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.

2. [Individuare il programma di installazione dell'agente](vmware-physical-mobility-service-overview.md#locate-installer-files) in base al sistema operativo del server.

>[!IMPORTANT]
> Se si replica la macchina virtuale di Azure IaaS da un'area di Azure a un'altra, non usare questo metodo. Consulta [le nostre linee guida](azure-to-azure-autoupdate.md) per informazioni su tutte le opzioni disponibili.

3. Copiare il file di installazione nel computer protetto ed eseguirlo per aggiornare l'agente mobility.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Account di aggiornamento utilizzato per l'installazione push del servizio MobilityUpdate account used for push installation of Mobility service

Quando è stato distribuito Site Recovery, per consentire l'installazione push del servizio Mobility è stato specificato un account che il server di elaborazione di Site Recovery usa per accedere ai computer e installare il servizio quando è abilitata la replica per la macchina. Se si intende aggiornare le credenziali per questo account, seguire [queste istruzioni](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Disinstallare il servizio Mobility

### <a name="on-a-windows-machine"></a>In un computer Windows

Eseguire la disinstallazione dall'interfaccia utente o da un prompt dei comandi.

- **Dall'interfaccia utente**: nel Pannello di controllo della macchina selezionare **Programmi**. Selezionare**Disinstallazione**server di destinazione server di destinazione di **Microsoft Azure Site Recovery/Server** > di destinazione master .
- **Da un prompt dei comandi**: aprire una finestra dei comandi con privilegi di amministratore sul computer. Eseguire il comando seguente: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>In un computer Linux
1. In un computer Linux accedere come utente **ROOT**.
2. In un terminale, andare a /usr/locale/ASR.
3. Eseguire il comando seguente:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Installare il provider VSS di Site Recovery nel computer di origineInstall Site Recovery VSS provider on source machine

Il provider VSS di Azure Site Recovery è necessario nel computer di origine per generare punti di coerenza dell'applicazione. Se l'installazione del provider non è riuscita tramite l'installazione push, seguire le linee guida riportate di seguito per installarlo manualmente.

1. Aprire la finestra cmd admin.
2. Passare al percorso di installazione del servizio per dispositivi mobili. (Ad es. - File di programma (x86) Microsoft Azure Site Recovery-agent
3. Eseguire lo script InMageVSSProvider_Uninstall.cmd . Questo disinstallerà il servizio se esiste già.
4. Eseguire lo script InMageVSSProvider_Install.cmd per installare manualmente il provider VSS.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il ripristino di emergenza per macchine virtuali VMware](vmware-azure-tutorial.md)
- [Configurare il ripristino di emergenza per server fisici](physical-azure-disaster-recovery.md)
