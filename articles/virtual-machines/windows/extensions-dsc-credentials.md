---
title: Passaggio di credenziali ad Azure con DSC | Microsoft Docs
description: Panoramica del passaggio sicuro di credenziali alle macchine virtuali di Azure tramite PowerShell Desired State Configuration
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: acd768c0219ec23c0453a65c575faf5213d9c616
ms.contentlocale: it-it
ms.lasthandoff: 03/31/2017

---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Passaggio di credenziali al gestore estensione DSC di Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo illustra l'estensione DSC (Desired State Configuration) per Azure. Una panoramica del gestore estensione DSC è disponibile in [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="passing-in-credentials"></a>Passaggio di credenziali
Nell'ambito del processo di configurazione, potrebbe essere necessario configurare account utente, servizi di accesso o installare un programma in un contesto utente. Per eseguire queste operazioni, è necessario fornire le credenziali. 

DSC consente di eseguire configurazioni con parametri in cui le credenziali vengono passate nella configurazione e archiviate in modo sicuro in file MOF. Per semplificare la gestione delle credenziali, il gestore estensione di Azure offre la gestione automatica dei certificati. 

Considerare lo script di configurazione DSC seguente che crea un account utente locale con la password specificata:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

È importante includere *node localhost* come parte della configurazione. Se l'istruzione manca, la successiva procedura non funzionerà dal momento che il gestore dell'estensione cerca specificamente l'istruzione node localhost. È importante anche includere il cast di tipo *[PsCredential]*, perché questo tipo specifico attiva l'estensione per crittografare la credenziale. 

Pubblicare questo script nell'archivio BLOB:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Impostare l'estensione DSC di Azure e specificare la credenziale:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Protezione delle credenziali
Durante l'esecuzione del codice viene chiesta una credenziale. Una volta fornita, viene archiviata nella memoria per breve tempo. Quando viene pubblicata con il cmdlet `Set-AzureVmDscExtension` , viene trasmessa tramite HTTPS alla VM, dove Azure la archivia crittografata su disco, usando il certificato della VM locale. Viene quindi brevemente decrittografata nella memoria e nuovamente crittografata per passarla a DSC.

Questo comportamento è diverso dall' [uso di configurazioni sicure senza il gestore dell'estensione](https://msdn.microsoft.com/powershell/dsc/securemof). L'ambiente di Azure offre un modo per trasmettere i dati di configurazione in maniera sicura tramite certificati. Quando si usa il gestore dell'estensione DSC, non è necessario fornire $CertificatePath o una voce $CertificateID/$Thumbprint in ConfigurationData.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul gestore dell'estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Esaminare il [modello di Azure Resource Manager per l'estensione DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Per trovare altre funzionalità che è possibile gestire con PowerShell DSC, [cercare in PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) altre risorse DSC.


