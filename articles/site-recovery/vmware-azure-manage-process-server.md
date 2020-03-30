---
title: Gestire un server di elaborazione per macchine virtuali VMware/ripristino di emergenza del server fisico in Azure Site Recovery
description: Questo articolo descrive come gestire un server di elaborazione per il ripristino di emergenza di macchine virtuali/server fisici VMware tramite Azure Site Recovery.This article describes manage a process server for disaster recovery of VMware VMs/physical servers using Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257251"
---
# <a name="manage-process-servers"></a>Gestire server di elaborazione

In questo articolo vengono descritte le attività comuni per la gestione del server di elaborazione di Site Recovery.

Il server di elaborazione viene usato per ricevere, ottimizzare e inviare i dati di replica ad Azure.The process server is used to receive, optimize, and send replication data to Azure. Esegue inoltre un'installazione push del servizio Mobility nelle macchine virtuali VMware e nei server fisici che si desidera replicare ed esegue l'individuazione automatica dei computer locali. Per la replica delle macchine virtuali VMware locali o dei server fisici in Azure, il server di elaborazione viene installato per impostazione predefinita nel computer del server di configurazione. 

- Per distribuzioni di grandi dimensioni potrebbe essere necessario disporre di server di elaborazione locali aggiuntivi per garantire la scalabilità della capacità.
- Per il failback da Azure in locale, è necessario configurare un server di elaborazione temporaneo in Azure.For failback from Azure to on-premises, you must set up a temporary process server in Azure. È possibile eliminare questa macchina virtuale al termine del failback. 

Ulteriori informazioni sul server di elaborazione.


## <a name="upgrade-a-process-server"></a>Aggiornare un server di elaborazione

Quando si distribuisce un server di elaborazione in locale o come macchina virtuale di Azure per il failback, viene installata la versione più recente del server di elaborazione. I team di Site Recovery rilasciano correzioni e miglioramenti a intervalli regolari. È consigliabile mantenere aggiornati i server di elaborazione. È possibile aggiornare un server di elaborazione come segue:You can upgrade a process server as follows:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Spostare le macchine virtuali per bilanciare il carico del server di elaborazioneMove VMs to balance the process server load

Bilanciare il carico spostando le macchine virtuali tra due server di elaborazione, come indicato di seguito:Balance the load by moving VMs between two process servers, as follows:

1. Nell'insieme di credenziali, in **Gestisci** fare clic su **Infrastruttura di Site Recovery**. In **Per VMware & Computer fisici**fare clic su Server di **configurazione**.
2. Fare clic sul server di configurazione con cui sono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione per il quale si desidera bilanciare il carico del traffico.

    ![Bilanciamento del carico](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Fare clic su **Bilanciamento del carico**, selezionare il server di elaborazione di destinazione in cui si desidera spostare i computer. Quindi fare clic su **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Fare clic su **Seleziona computer**e scegliere i computer che si desidera spostare dal server di elaborazione corrente a quello di destinazione. Per ogni macchina virtuale vengono visualizzate informazioni dettagliate sulla modifica dei dati media. Fare quindi clic su **OK**. 
3. Nell'insieme di credenziali monitorare l'avanzamento del processo in **Monitoraggio** > dei processi di**Site Recovery**.

Ci vorranno circa 15 minuti perché le modifiche si riflettano nel portale. Per un effetto più rapido, [aggiornare il server](vmware-azure-manage-configuration-server.md#refresh-configuration-server)di configurazione .

## <a name="switch-an-entire-workload-to-another-process-server"></a>Passare un intero carico di lavoro a un altro server di elaborazioneSwitch an entire workload to another process server

Spostare l'intero carico di lavoro gestito da un server di elaborazione in un server di elaborazione diverso, come indicato di seguito:Move the entire workload handled by a process server to a different process server, as follows:

1. Nell'insieme di credenziali, in **Gestisci** fare clic su **Infrastruttura di Site Recovery**. In **Per VMware & Computer fisici**fare clic su Server di **configurazione**.
2. Fare clic sul server di configurazione con cui sono registrati i server di elaborazione.
3. Fare clic sul server di elaborazione da cui si desidera cambiare il carico di lavoro.
4. Fare clic su **Switch**, selezionare il server di elaborazione di destinazione in cui si desidera spostare il carico di lavoro. Quindi fare clic su **OK**

    ![Opzione](media/vmware-azure-manage-process-server/Switch.PNG)

5. Nell'insieme di credenziali monitorare l'avanzamento del processo in **Monitoraggio** > dei processi di**Site Recovery**.

Ci vorranno circa 15 minuti perché le modifiche si riflettano nel portale. Per un effetto più rapido, [aggiornare il server](vmware-azure-manage-configuration-server.md#refresh-configuration-server)di configurazione .

## <a name="register-a-master-target-server"></a>Registrare un server di destinazione masterRegister a master target server

Il server di destinazione master risiede nel server di configurazione e nei server di elaborazione con scalabilità orizzontale. Deve essere registrato con il server di configurazione. Nel caso in cui si verifica un errore in questa registrazione, può influire sull'integrità degli elementi protetti. Per registrare il server di destinazione master con il server di configurazione, accedere al server di configurazione/server di elaborazione con scalabilità orizzontale specifico in cui è richiesta la registrazione. Passare alla cartella **%PROGRAMDATA%**, AsR , Agent ed eseguire quanto segue al prompt dei comandi dell'amministratore.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Registrare di nuovo un server di elaborazione

Registrare nuovamente un server di elaborazione in esecuzione in locale o in una macchina virtuale di Azure con il server di configurazione come segue:Reregister a process server running on-premises or on an Azure VM with the configuration server as follows:

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

Se un server di elaborazione locale usa un proxy per connettersi ad Azure, è possibile modificare le impostazioni proxy come segue:If an on-premises process server uses a proxy to connect to Azure, you can modify the proxy settings as follows:

1. Accedere al computer del server di elaborazione. 
2. Aprire una finestra di comando di PowerShell di amministratore ed eseguire il comando seguente:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Individuare la cartella **%PROGRAMDATA% , AsR , Agent**, ed eseguire il comando seguente:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Rimuovere un server di elaborazione

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Escludere le cartelle dal software antivirus

Se il software antivirus è in esecuzione su un server di elaborazione con scalabilità orizzontale (o server di destinazione master), escludere le seguenti cartelle dalle operazioni antivirus:


- C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Directory di installazione del server di elaborazione. Ad esempio: C: , Programmi (x86) , Microsoft Azure Site Recovery