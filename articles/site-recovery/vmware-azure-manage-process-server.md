---
title: Gestire un server di elaborazione in Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive la gestione della configurazione di un server di elaborazione per la replica di macchine virtuali VMware e server fisici in Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
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
  In genere, quando si usa l'immagine della raccolta di Azure per creare un server di elaborazione in Azure per il failback, viene eseguita la versione più recente disponibile di tale server. I team di Site Recovery rilasciano correzioni e miglioramenti a intervalli regolari. È consigliabile mantenere aggiornati i server di elaborazione.



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
  ```
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


