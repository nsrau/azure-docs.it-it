---
title: Gestire il server di elaborazione per il ripristino di emergenza da macchine virtuali VMware e server fisici ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive la gestione di un server di elaborazione per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: ba80c8ce57495eaa46e915cb0c472eb4aabcee57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863628"
---
# <a name="manage-process-servers"></a>Gestire server di elaborazione

Per impostazione predefinita il server di elaborazione usato quando si esegue la replica di macchine virtuali VMware o server fisici in Azure è installato nel computer server di configurazione locale. Esistono un paio di casi in cui è necessario configurare un server di elaborazione distinto:

- Per distribuzioni di grandi dimensioni potrebbe essere necessario disporre di server di elaborazione locali aggiuntivi per garantire la scalabilità della capacità.
- Per il failback è necessario un server di elaborazione temporaneo configurato in Azure. È possibile eliminare questa macchina virtuale al termine del failback. 

Questo articolo riepiloga le attività di gestione comuni per i server di elaborazione aggiuntivi.

## <a name="upgrade-a-process-server"></a>Aggiornare un server di elaborazione

Aggiornare un server di elaborazione in esecuzione in locale o in Azure (per motivi di failback), come indicato di seguito:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   In genere, quando si usa l'immagine della raccolta di Azure per creare un server di elaborazione in Azure per il failback, viene eseguita la versione più recente disponibile di tale server. I team di Site Recovery rilasciano correzioni e miglioramenti a intervalli regolari. È consigliabile mantenere aggiornati i server di elaborazione.

## <a name="balance-the-load-on-process-server"></a>Bilanciare il carico nel server di elaborazione

Per bilanciare il carico tra due server di elaborazione:

1. Passare a **Insieme di credenziali di Servizi di ripristino** > **Gestisci** > **Infrastruttura di Site Recovery**  >  **Per VMware e computer fisici** > **Server di configurazione**.
2. Fare clic sul server di configurazione in cui sono registrati i server di elaborazione.
3. Nella pagina è disponibile l'elenco dei server di elaborazione registrati nei server di configurazione.
4. Fare clic sul server di elaborazione di cui si vuole modificare il carico di lavoro.

    ![Bilanciamento del carico](media/vmware-azure-manage-process-server/LoadBalance.png)

5. È possibile usare l'opzione **Bilanciamento del carico** o **Switch**, come spiegato di seguito, a seconda dei requisiti.

### <a name="load-balance"></a>Bilanciamento del carico

Tramite questa opzione è possibile selezionare una o più macchine virtuali e trasferirle a un altro server di elaborazione.

1. Fare clic su **Bilanciamento del carico** e selezionare il server di elaborazione di destinazione dall'elenco a discesa. Fare clic su **OK**.

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Fare clic su **Seleziona macchine virtuali** e scegliere le macchine virtuali da spostare dal server di elaborazione corrente a quello di destinazione. Per ogni macchina virtuale vengono visualizzate informazioni dettagliate sulla modifica dei dati media.
3. Fare clic su **OK**. Monitorare lo stato del processo in **Insieme di credenziali di Servizi di ripristino** > **Monitoraggio** > **Processi di Site Recovery**.
4. Dopo il completamento di questa operazione, sono necessari 15 minuti perché le modifiche siano visualizzate. In alternativa, per renderle immediatamente effettive, [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

### <a name="switch"></a>Switch

Tramite questa opzione, l'intero carico di lavoro protetto in un server di elaborazione viene spostato in un altro server di elaborazione.

1. Fare clic su **Switch**, selezionare il server di elaborazione di destinazione e fare clic su **OK**.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. Monitorare lo stato del processo in **Insieme di credenziali di Servizi di ripristino** > **Monitoraggio** > **Processi di Site Recovery**.
3. Dopo il completamento di questa operazione, sono necessari 15 minuti perché le modifiche siano visualizzate. In alternativa, per renderle immediatamente effettive, [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="reregister-a-process-server"></a>Registrare di nuovo un server di elaborazione

Se è necessario registrare di nuovo un server di elaborazione in esecuzione in locale o in Azure con il server di configurazione, eseguire le operazioni seguenti:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Dopo avere salvato le impostazioni, eseguire le operazioni seguenti:

1. Nel server di elaborazione aprire un prompt dei comandi come amministratore.
2. Passare alla cartella **%PROGRAMDATA%\ASR\Agent** ed eseguire il comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificare le impostazioni proxy per un server di elaborazione locale

Se il server di elaborazione usa un proxy per la connessione a Site Recovery in Azure, usare questa procedura se è necessario modificare le impostazioni proxy esistenti.

1. Accedere al computer del server di elaborazione. 
2. Aprire una finestra di comando di PowerShell di amministratore ed eseguire il comando seguente:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Passare alla cartella **%PROGRAMDATA%\ASR\Agent** ed eseguire il comando seguente:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```


## <a name="remove-a-process-server"></a>Rimuovere un server di elaborazione

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Gestire il software antivirus nei server di elaborazione

Se il software antivirus è attivo in un server di elaborazione autonomo o in un server di destinazione master, escludere le cartelle seguenti dalle operazioni antivirus:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Directory di installazione del server di elaborazione, ad esempio: C:\Programmi (x86)\Microsoft Azure Site Recovery

