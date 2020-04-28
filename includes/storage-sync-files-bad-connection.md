---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75773031"
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
4. Usare il cmdlet test-StorageSyncNetworkConnectivity per verificare la connettività di rete agli endpoint del servizio. Per altre informazioni, vedere [testare la connettività di rete agli endpoint di servizio](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Per assistenza nella risoluzione dei problemi di connettività di rete, contattare l'amministratore di rete.
