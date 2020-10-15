---
title: Come creare definizioni dei criteri di configurazione Guest da Criteri di gruppo baseline per Windows
description: Informazioni su come convertire Criteri di gruppo dalla linea di base di sicurezza di Windows Server 2019 in una definizione dei criteri.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: dce22885981ab01fe37fac8588899d12a5afb87d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893374"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Come creare definizioni dei criteri di configurazione Guest da Criteri di gruppo baseline per Windows

Prima di creare definizioni dei criteri personalizzati, è consigliabile leggere le informazioni generali concettuali in [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md). Per informazioni sulla creazione di definizioni dei criteri di configurazione Guest personalizzati per Linux, vedere [How to create Guest Configuration Policies for Linux](./guest-configuration-create-linux.md). Per informazioni sulla creazione di definizioni dei criteri di configurazione Guest personalizzati per Windows, vedere [How to create Guest Configuration Policies for Windows](./guest-configuration-create.md).

Quando si esegue il controllo di Windows, Configurazione guest usa un modulo risorse [DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview) per creare il file di configurazione. La configurazione DSC definisce la condizione in cui deve trovarsi il computer. Se la valutazione della configurazione è **non conforme**, viene attivato l'effetto del criterio *auditIfNotExists* .
La [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md) controlla solo le impostazioni all'interno dei computer.

> [!IMPORTANT]
> Le definizioni dei criteri personalizzati con la configurazione Guest sono una funzionalità in anteprima.
>
> L'estensione Configurazione guest è necessaria per eseguire controlli nelle macchine virtuali di Azure. Per distribuire l'estensione su larga scala in tutti i computer Windows, assegnare le definizioni dei criteri seguenti:
> - [Distribuisci i prerequisiti per abilitare i criteri di Configurazione guest nelle macchine virtuali Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

La community DSC ha pubblicato il [modulo BaselineManagement](https://github.com/microsoft/BaselineManagement) per convertire i modelli esportati criteri di gruppo in formato DSC. Insieme al cmdlet GuestConfiguration, il modulo BaselineManagement crea il pacchetto di configurazione Guest di criteri di Azure per Windows dal contenuto Criteri di gruppo. Per informazioni dettagliate sull'uso del modulo BaselineManagement, vedere l'articolo [Guida introduttiva: convertire criteri di gruppo in DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

Questa guida illustra il processo di creazione di un pacchetto di configurazione Guest di criteri di Azure da un oggetto Criteri di gruppo (GPO). Mentre la procedura dettagliata illustra la conversione della linea di base di sicurezza di Windows Server 2019, lo stesso processo può essere applicato ad altri oggetti Criteri di gruppo.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Scaricare la linea di base di sicurezza di Windows Server 2019 e installare i moduli di PowerShell correlati

Per installare **DSC**, **GuestConfiguration**, la **gestione di base**e i moduli di Azure correlati in PowerShell:

1. Al prompt di PowerShell, eseguire il comando seguente:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Creare una directory per e scaricare la linea di base di sicurezza di Windows Server 2019 da Windows Security Compliance Toolkit.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Sbloccare ed espandere il server scaricato 2019 Baseline.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Convalidare il contenuto della linea di base del server 2019 usando **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Conversione da Criteri di gruppo a configurazione Guest di criteri di Azure

Si convertirà quindi la baseline del server scaricato 2019 in un pacchetto di configurazione Guest usando i moduli di configurazione Guest e di gestione della linea di base.

1. Convertire il Criteri di gruppo nella configurazione dello stato desiderato usando il modulo di gestione di base.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Rinominare, riformattare ed eseguire gli script convertiti prima di creare un pacchetto di contenuto dei criteri.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Creare un pacchetto di contenuto di configurazione Guest di criteri di Azure.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Crea configurazione Guest di criteri di Azure

Il passaggio successivo consiste nel pubblicare il file nell'archivio BLOB di Azure. 

1. Lo script seguente contiene una funzione che è possibile usare per automatizzare questa attività. Si noti che i comandi usati nella `publish` funzione richiedono il `Az.Storage` modulo.

   ```azurepowershell-interactive
    function Publish-Configuration {
        param(
        [Parameter(Mandatory=$true)]
        $resourceGroup,
        [Parameter(Mandatory=$true)]
        $storageAccountName,
        [Parameter(Mandatory=$true)]
        $storageContainerName,
        [Parameter(Mandatory=$true)]
        $filePath,
        [Parameter(Mandatory=$true)]
        $blobName
        )

        # Get Storage Context
        $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
            -Name $storageAccountName | `
            ForEach-Object { $_.Context }

        # Upload file
        $Blob = Set-AzStorageBlobContent -Context $Context `
            -Container $storageContainerName `
            -File $filePath `
            -Blob $blobName `
            -Force

        # Get url with SAS token
        $StartTime = (Get-Date)
        $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
        $SAS = New-AzStorageBlobSASToken -Context $Context `
            -Container $storageContainerName `
            -Blob $blobName `
            -StartTime $StartTime `
            -ExpiryTime $ExpiryTime `
            -Permission rl `
            -FullUri

        # Output
        return $SAS
    }
   ```

1. Creare parametri per definire il gruppo di risorse, l'account di archiviazione e il contenitore univoci. 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. Usare la funzione Publish con i parametri assegnati per pubblicare il pacchetto di configurazione Guest nell'archivio BLOB pubblico.


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. Una volta creato e caricato un pacchetto di criteri personalizzati di Configurazione guest, creare la definizione dei criteri di Configurazione guest. Usare il `New-GuestConfigurationPolicy` cmdlet per creare la configurazione Guest.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Pubblicare le definizioni dei criteri usando il `Publish-GuestConfigurationPolicy` cmdlet. Il cmdlet ha solo il parametro **Path** che punta al percorso dei file JSON creati da `New-GuestConfigurationPolicy`. Per eseguire il comando Publish, è necessario l'accesso per creare le definizioni dei criteri in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica di Criteri di Azure](../overview.md#getting-started). Il ruolo predefinito migliore è **Collaboratore ai criteri delle risorse**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Assegnare la definizione dei criteri di configurazione Guest

Una volta creati i criteri in Azure, l'ultimo passaggio consiste nell'assegnazione dell'iniziativa. Vedere come assegnare l'iniziativa con il [portale](../assign-policy-portal.md), l'[interfaccia della riga di comando di Azure](../assign-policy-azurecli.md) e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Le definizioni dei criteri di configurazione Guest devono **sempre** essere assegnate usando l'iniziativa che combina i criteri _AuditIfNotExists_ e _DeployIfNotExists_ . Se vengono assegnati solo i criteri _AuditIfNotExists_, i prerequisiti non vengono distribuiti e i criteri indicano sempre che "0" server sono conformi.

Per assegnare una definizione dei criteri con l'effetto _DeployIfNotExists_, è necessario un livello di accesso aggiuntivo. Per concedere privilegi minimi, è possibile creare una definizione del ruolo personalizzata che estende il ruolo **Collaboratore ai criteri delle risorse**. Nell'esempio seguente viene creato un ruolo denominato **Resource Policy Contributor DINE** con l'autorizzazione aggiuntiva _Microsoft.Authorization/roleAssignments/write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sul controllo delle macchine virtuali con [Configurazione guest](../concepts/guest-configuration.md).
- Vedere come [creare criteri a livello di codice](./programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](./get-compliance-data.md).
