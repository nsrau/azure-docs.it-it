---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b26d4af29a92fb0f14c52e76a7eae1d0073a3aa0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96005324"
---
Questo errore può verificarsi quando il servizio Sincronizzazione file di Azure non è accessibile dal server. È possibile risolvere questo errore eseguendo questa procedura:

1. Verificare che il servizio Windows `FileSyncSvc.exe` non venga bloccato dal firewall.
2. Verificare che la porta 443 sia aperta per le connessioni in uscita verso il servizio Sincronizzazione file di Azure. Per eseguire questa operazione, usare il cmdlet `Test-NetConnection`. L'URL per il segnaposto `<azure-file-sync-endpoint>` riportato di seguito è disponibile nel documento [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verificare che la configurazione del proxy sia impostata come previsto. Questa operazione può essere eseguita con il cmdlet `Get-StorageSyncProxyConfiguration`. Altre informazioni sulla configurazione del proxy per Sincronizzazione file di Azure sono disponibili nel documento [Impostazioni di proxy e firewall di Sincronizzazione file di Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Usare il cmdlet Test-StorageSyncNetworkConnectivity per verificare la connettività di rete agli endpoint del servizio. Per altre informazioni, vedere [Testare la connettività di rete agli endpoint di servizio](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. Per assistenza nella risoluzione dei problemi di connettività di rete, contattare l'amministratore di rete.