---
title: Gestire un server di elaborazione usato per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive la gestione di un server di elaborazione impostata per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925578"
---
# <a name="manage-process-servers"></a>Gestire server di elaborazione

Questo articolo vengono descritte attività comuni per la gestione dei server di elaborazione di Site Recovery.

Il server di elaborazione consente di ricevere, ottimizzare e inviare dati di replica in Azure. Ed esegue un'installazione push del servizio Mobility nelle VM VMware e server fisici da replicare, ed esegue l'individuazione automatica delle macchine virtuali locali. Per la replica di macchine virtuali VMware locali o server fisici in Azure, il server di elaborazione viene installato per impostazione predefinita nel server di configurazione. 

- Per distribuzioni di grandi dimensioni potrebbe essere necessario disporre di server di elaborazione locali aggiuntivi per garantire la scalabilità della capacità.
- Per il failback da Azure a locale, è necessario configurare un server di elaborazione temporaneo in Azure. È possibile eliminare questa macchina virtuale al termine del failback. 

Altre informazioni sui server di elaborazione.


## <a name="upgrade-a-process-server"></a>Aggiornare un server di elaborazione

Quando si distribuisce un server di elaborazione in locale, o come una macchina virtuale di Azure per il failback, è installata la versione più recente del server di elaborazione. I team di Site Recovery rilasciano correzioni e miglioramenti a intervalli regolari. È consigliabile mantenere aggiornati i server di elaborazione. È possibile aggiornare un server di elaborazione, come indicato di seguito:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Spostare le macchine virtuali per bilanciare il carico del server di processo

Bilanciare il carico spostando le VM tra due server di elaborazione, come indicato di seguito:

1. Nell'insieme di credenziali, sotto **Manage** fare clic su **infrastruttura di Site Recovery**. Sotto **le macchine per VMware e fisici**, fare clic su **server di configurazione**.
2. Fare clic sul server di configurazione con cui vengono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione per il quale si desidera bilanciare il carico.

    ![Bilanciamento del carico](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Fare clic su **bilanciare il carico**, selezionare il server di elaborazione di destinazione a cui si desidera spostare le macchine. Quindi fare clic su **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Fare clic su **selezionare le macchine virtuali**e scegliere le macchine di cui si desidera spostare da corrente per il server di elaborazione di destinazione. Per ogni macchina virtuale vengono visualizzate informazioni dettagliate sulla modifica dei dati media. Fare quindi clic su **OK**. 
3. Nell'insieme di credenziali, monitorare lo stato di avanzamento del processo sotto **Monitoring** > **processi di Site Recovery**.

Richiederà circa 15 minuti per le modifiche siano visibili nel portale. Per un effetto più veloce [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Passare un intero carico di lavoro a un altro server di elaborazione

Spostare l'intero carico di lavoro gestito da un server di elaborazione per un server di elaborazione diverso, come indicato di seguito:

1. Nell'insieme di credenziali, sotto **Manage** fare clic su **infrastruttura di Site Recovery**. Sotto **le macchine per VMware e fisici**, fare clic su **server di configurazione**.
2. Fare clic sul server di configurazione con cui vengono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione da cui si desidera passare il carico di lavoro.
4. Fare clic su **commutatore**, selezionare il server di elaborazione di destinazione a cui si desidera spostare il carico di lavoro. Quindi fare clic su **OK**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. Nell'insieme di credenziali, monitorare lo stato di avanzamento del processo sotto **Monitoring** > **processi di Site Recovery**.

Richiederà circa 15 minuti per le modifiche siano visibili nel portale. Per un effetto più veloce [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Registrare di nuovo un server di elaborazione

Registrare di nuovo un server di elaborazione in esecuzione in locale o in una VM di Azure con il server di configurazione come indicato di seguito:

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

Se un server di elaborazione locale Usa un proxy per connettersi ad Azure, è possibile modificare le impostazioni del proxy come indicato di seguito:

1. Accedere al computer server di elaborazione. 
2. Aprire una finestra di comando di PowerShell di amministratore ed eseguire il comando seguente:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Passare alla cartella **%PROGRAMDATA%\ASR\Agent**, ed eseguire questo comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Rimuovere un server di elaborazione

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Escludere cartelle dall'antivirus

Se un software antivirus è in esecuzione in un server di elaborazione scale-out (o server di destinazione master), escludere le cartelle seguenti dalle operazioni di anti-virus:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Directory di installazione server di processo. Ad esempio:  C:\Programmi (x86)\Microsoft Azure Site Recovery