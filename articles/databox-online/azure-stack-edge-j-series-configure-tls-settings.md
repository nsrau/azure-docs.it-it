---
title: Configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Azure Stack Edge
description: Viene descritto come configurare TLS 1,2 nei client Windows che accedono al dispositivo GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: fdd88a902e812c89270a175aef4bf0de197c6e9b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146131"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>Configurare TLS 1,2 nei client Windows che accedono al dispositivo Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Se si usa un client Windows per accedere al dispositivo Azure Stack Edge, è necessario configurare TLS 1,2 nel client. Questo articolo fornisce le risorse e le linee guida per configurare TLS 1,2 nel client Windows. 

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

- [Generale-come abilitare TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [Come abilitare TLS 1.2 nei client](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Come abilitare TLS 1.2 nei server del sito e nei sistemi del sito remoti](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocolli in TLS/SSL (SSP Schannel)](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- Pacchetti di [crittografia](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12): [configurazione specifica dell'ordine del pacchetto di crittografia TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) assicurarsi di elencare i pacchetti di crittografia correnti e anteporre quelli mancanti nell'elenco seguente:

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
    
    - [Impostare dimensioni minime scambio chiavi RSA su 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Passaggi successivi

[Connetti a Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)