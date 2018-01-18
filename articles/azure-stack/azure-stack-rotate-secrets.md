---
title: Ruotare i segreti nello Stack di Azure | Documenti Microsoft
description: Informazioni su come eseguire la rotazione i segreti nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Ruotare i segreti nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

Aggiornare le password per i componenti dello Stack di Azure a un ritmo regolare.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Aggiornare le password per baseboard management controller (BMC)

I baseboard management controller (BMC) monitorare lo stato dei server fisico. Le istruzioni su come aggiornare la password del BMC specifiche variano in base al fornitore dell'hardware (OEM) original equipment manufacturer.

1. Aggiornare il BMC su server fisici di Stack Azure seguendo le istruzioni OEM. La password per ciascun BMC nell'ambiente in uso deve essere lo stesso.
2. Aprire un endpoint con privilegi in Azure Stack sessioni. Per istruzioni, vedere [utilizzando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).
3. Dopo il PowerShell Prompt dei comandi è stato modificato in **[nome indirizzo IP o VM ERCS]: PS >** o **[azs ercs01]: PS >**, a seconda dell'ambiente, eseguire `Set-BmcPassword` eseguendo `invoke-command`. Passare la variabile di sessione con privilegi endpoint come parametro. Ad esempio: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    È anche possibile utilizzare la versione di PowerShell statica con le password come righe di codice:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo Stack di Azure e la sicurezza, vedere [condizioni di sicurezza dell'infrastruttura di Azure Stack](azure-stack-security-foundations.md).
