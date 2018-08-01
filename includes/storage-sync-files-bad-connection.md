---
title: File di inclusione
description: File di inclusione
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 754562487f0fe9f825107445a3059cc15a1faa26
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39146534"
---
Questo errore può verificarsi quando il servizio Sincronizzazione file di Azure non è accessibile dal server. È possibile risolvere questo errore eseguendo questa procedura:

1. Verificare che il servizio Windows `FileSyncSvc.exe` non venga bloccato dal firewall.
2. Verificare che la porta 443 sia aperta per le connessioni in uscita verso il servizio Sincronizzazione file di Azure. Per eseguire questa operazione, usare il cmdlet `Test-NetConnection`. L'URL per il segnaposto `<azure-file-sync-endpoint>` riportato di seguito è disponibile nel documento [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```PowerShell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verificare che la configurazione del proxy sia impostata come previsto. Questa operazione può essere eseguita con il cmdlet `Get-StorageSyncProxyConfiguration`. Altre informazioni sulla configurazione del proxy per Sincronizzazione file di Azure sono disponibili nel documento [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```PowerShell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Per assistenza nella risoluzione dei problemi di connettività di rete, contattare l'amministratore di rete.