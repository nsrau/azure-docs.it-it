<properties
   pageTitle="Passaggio di credenziali ad Azure con DSC | Microsoft Azure"
   description="Panoramica del passaggio sicuro di credenziali alle macchine virtuali di Azure tramite PowerShell Desired State Configuration"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="zachal"/>  

# Passaggio di credenziali al gestore estensione DSC di Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo illustra l'estensione DSC (Desired State Configuration) per Azure. Una panoramica del gestore estensione DSC è disponibile in [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](virtual-machines-windows-extensions-dsc-overview.md).

Nell'ambito del processo di configurazione, potrebbe essere necessario configurare account utente, servizi di accesso o installare un programma in un contesto utente. Per eseguire queste operazioni, è necessario fornire le credenziali.

DSC consente di eseguire configurazioni con parametri in cui le credenziali vengono passate nella configurazione e archiviate in modo sicuro in file MOF. Per semplificare la gestione delle credenziali, il gestore estensione di Azure offre la gestione automatica dei certificati.

Considerare lo script di configurazione DSC seguente che crea un account utente locale con la password specificata:

*user\_configuration.ps1*

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

È importante includere *node localhost* come parte della configurazione. Se l'istruzione manca, la successiva non funzionerà dal momento che il gestore dell'estensione cerca specificamente l'istruzione node localhost. È importante anche includere il cast di tipo *[PsCredential]*, perché questo tipo specifico attiva l'estensione per crittografare la credenziale.

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

Durante l'esecuzione del codice viene chiesta una credenziale. Una volta fornita, viene archiviata nella memoria per breve tempo. Quando viene pubblicata con il cmdlet `Set-AzureVmDscExtension`, viene trasmessa tramite HTTPS alla VM, dove Azure la archivia crittografata su disco, usando il certificato della VM locale. Viene quindi brevemente decrittografata nella memoria e nuovamente crittografata per passarla a DSC.

Questo comportamento è diverso dall'[uso di configurazioni sicure senza il gestore dell'estensione](https://msdn.microsoft.com/powershell/dsc/securemof). L'ambiente di Azure offre un modo per trasmettere i dati di configurazione in maniera sicura tramite certificati. Quando si usa il gestore dell'estensione DSC, non è necessario fornire $CertificatePath o una voce $CertificateID/$Thumbprint in ConfigurationData.


## Passaggi successivi ##

Per altre informazioni sul gestore dell'estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](virtual-machines-windows-extensions-dsc-overview.md).

Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Per trovare altre funzionalità che è possibile gestire con PowerShell DSC, [cercare in PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) altre risorse DSC.

<!---HONumber=AcomDC_0824_2016-->