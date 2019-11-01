---
title: Gestire un server di elaborazione usato per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come gestire un server di elaborazione configurato per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 509b9aed9f5daebb70a18336837da2152667a458
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242848"
---
# <a name="manage-process-servers"></a>Gestire server di elaborazione

Questo articolo descrive le attività comuni per la gestione del server di elaborazione Site Recovery.

Il server di elaborazione viene usato per ricevere, ottimizzare e inviare i dati di replica ad Azure. Esegue inoltre un'installazione push del servizio Mobility nelle VM VMware e nei server fisici da replicare ed esegue l'individuazione automatica dei computer locali. Per la replica di macchine virtuali VMware locali o server fisici in Azure, il server di elaborazione viene installato per impostazione predefinita nel computer del server di configurazione. 

- Per distribuzioni di grandi dimensioni potrebbe essere necessario disporre di server di elaborazione locali aggiuntivi per garantire la scalabilità della capacità.
- Per il failback da Azure a locale, è necessario configurare un server di elaborazione temporaneo in Azure. È possibile eliminare questa macchina virtuale al termine del failback. 

Altre informazioni sul server di elaborazione.


## <a name="upgrade-a-process-server"></a>Aggiornare un server di elaborazione

Quando si distribuisce un server di elaborazione locale o come macchina virtuale di Azure per il failback, viene installata la versione più recente del server di elaborazione. I team di Site Recovery rilasciano correzioni e miglioramenti a intervalli regolari. È consigliabile mantenere aggiornati i server di elaborazione. È possibile aggiornare un server di elaborazione come indicato di seguito:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Spostare le macchine virtuali per bilanciare il carico del server di elaborazione

Bilanciare il carico spostando le macchine virtuali tra due server di elaborazione, come indicato di seguito:

1. Nell'insieme di credenziali, in **Gestisci** fare clic su **infrastruttura Site Recovery**. In **per VMware & computer fisici**fare clic su **server di configurazione**.
2. Fare clic sul server di configurazione con cui vengono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione per il quale si desidera bilanciare il carico del traffico.

    ![Bilanciamento del carico](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Fare clic su **bilanciamento del carico**, selezionare il server di elaborazione di destinazione in cui si desidera spostare i computer. Quindi fare clic su **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Fare clic su **Seleziona computer**e scegliere i computer che si desidera spostare dall'attuale al server di elaborazione di destinazione. Per ogni macchina virtuale vengono visualizzate informazioni dettagliate sulla modifica dei dati media. Fare quindi clic su **OK**. 
3. Nell'insieme di credenziali monitorare lo stato di avanzamento del processo in **monitoraggio** > **Site Recovery processi**.

Saranno necessari circa 15 minuti affinché le modifiche vengano riflesse nel portale. Per un effetto più rapido, [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Passare un intero carico di lavoro a un altro server di elaborazione

Spostare l'intero carico di lavoro gestito da un server di elaborazione in un server di elaborazione diverso, come indicato di seguito:

1. Nell'insieme di credenziali, in **Gestisci** fare clic su **infrastruttura Site Recovery**. In **per VMware & computer fisici**fare clic su **server di configurazione**.
2. Fare clic sul server di configurazione con cui vengono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione da cui si desidera passare il carico di lavoro.
4. Fare clic su **Switch**, selezionare il server di elaborazione di destinazione in cui si desidera spostare il carico di lavoro. Quindi fare clic su **OK**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. Nell'insieme di credenziali monitorare lo stato di avanzamento del processo in **monitoraggio** > **Site Recovery processi**.

Saranno necessari circa 15 minuti affinché le modifiche vengano riflesse nel portale. Per un effetto più rapido, [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registrare un server di destinazione master

Il server di destinazione master risiede nel server di configurazione e nei server di elaborazione con scalabilità orizzontale. Deve essere registrato con il server di configurazione. Se si verifica un errore in questa registrazione, può influisca sull'integrità degli elementi protetti. Per registrare il server di destinazione master con il server di configurazione, accedere al server di configurazione specifico o al server di elaborazione con scalabilità orizzontale in cui è richiesta la registrazione. Passare alla cartella **%ProgramData%\ASR\Agent**ed eseguire il comando seguente nel prompt dei comandi dell'amministratore.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Registrare di nuovo un server di elaborazione

Registrare nuovamente un server di elaborazione in esecuzione in locale o in una VM di Azure con il server di configurazione come segue:

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

Se un server di elaborazione locale usa un proxy per connettersi ad Azure, è possibile modificare le impostazioni del proxy come indicato di seguito:

1. Accedere al computer del server di elaborazione. 
2. Aprire una finestra di comando di PowerShell di amministratore ed eseguire il comando seguente:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Passare alla cartella **%ProgramData%\ASR\Agent**ed eseguire il comando seguente:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Rimuovere un server di elaborazione

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Escludi cartelle da software antivirus

Se il software antivirus viene eseguito in un server di elaborazione con scalabilità orizzontale (o server di destinazione master), escludere le cartelle seguenti dalle operazioni antivirus:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Directory di installazione del server di elaborazione. Ad esempio: C:\Programmi (x86) \Microsoft Azure Site Recovery