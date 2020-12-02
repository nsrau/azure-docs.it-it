---
title: Configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 15e2d7a144b54f443b8298b20dbfacf78a50f9e1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447543"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Configurare TLS 1,2 nei client Windows che accedono al dispositivo Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Se si usa un client Windows per accedere al dispositivo Azure Stack Edge Pro, è necessario configurare TLS 1,2 nel client. Questo articolo fornisce le risorse e le linee guida per configurare TLS 1,2 nel client Windows. 

Le linee guida fornite di seguito si basano sui test eseguiti in un client che esegue Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Configurare TLS 1,2 per la sessione corrente di PowerShell

Seguire questa procedura per configurare TLS 1,2 nel client.

1. Eseguire PowerShell come amministratore.
2. Per impostare TLS 1,2 per la sessione corrente di PowerShell, digitare:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Configurare TLS 1,2 nel client

Se si vuole impostare TLS 1,2 a livello di sistema per l'ambiente in uso, attenersi alle linee guida riportate in questi documenti:

- [Generale-come abilitare TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [Come abilitare TLS 1.2 nei client](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Come abilitare TLS 1.2 nei server del sito e nei sistemi del sito remoti](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocolli in TLS/SSL (SSP Schannel)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- Pacchetti di [crittografia](/windows-server/security/tls/tls-registry-settings#tls-12): [configurazione specifica dell'ordine del pacchetto di crittografia TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) assicurarsi di elencare i pacchetti di crittografia correnti e anteporre quelli mancanti nell'elenco seguente:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    È anche possibile aggiungere questi pacchetti di crittografia modificando direttamente le impostazioni del registro di sistema.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Come impostare le curve ellittiche

    Assicurarsi di elencare le curve ellittiche correnti e anteporre eventuali elementi mancanti nell'elenco seguente:

    - P-256 
    - P-384

    È anche possibile aggiungere queste curve ellittiche modificando direttamente le impostazioni del registro di sistema.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Impostare dimensioni minime scambio chiavi RSA su 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Passaggi successivi

[Connettersi ad Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)