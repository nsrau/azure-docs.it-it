---
title: Migrate to Resource Manager with PowerShell
description: This article walks through the platform-supported migration of IaaS resources such as virtual machines (VMs), virtual networks, and storage accounts from classic to Azure Resource Manager by using Azure PowerShell commands
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f87e7795416431305141de24497e9760eb03641e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484367"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrate IaaS resources from classic to Azure Resource Manager by using PowerShell
Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager.

If you want, you can also migrate resources by using the [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Per informazioni di base su scenari di migrazione supportati, vedere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Per una guida approfondita e la procedura dettagliata di migrazione, vedere [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Review the most common migration errors](migration-classic-resource-manager-errors.md).

<br>
Here's a flowchart to identify the order in which steps need to be executed during a migration process.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Passaggio 1: Pianificare la migrazione
Here are a few best practices that we recommend as you evaluate whether to migrate IaaS resources from classic to Resource Manager:

* Leggere con attenzione le [funzionalità e configurazioni supportate e non supportate](migration-classic-resource-manager-overview.md). If you have virtual machines that use unsupported configurations or features, wait for the configuration or feature support to be announced. In alternativa, in base alle esigenze è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione.
* Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

> [!IMPORTANT]
> Application gateways aren't currently supported for migration from classic to Resource Manager. To migrate a virtual network with an application gateway, remove the gateway before you run a Prepare operation to move the network. Dopo aver completato la migrazione, riconnettere il gateway in Azure Resource Manager.
>
> Azure ExpressRoute gateways that connect to ExpressRoute circuits in another subscription can't be migrated automatically. In such cases, remove the ExpressRoute gateway, migrate the virtual network, and re-create the gateway. For more information, see [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Step 2: Install the latest version of PowerShell
Per l'installazione di Azure PowerShell sono previste due opzioni principali: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](https://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua. Questo articolo si basa su Azure PowerShell versione 2.1.0.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Step 3: Ensure that you're an administrator for the subscription
To perform this migration, you must be added as a coadministrator for the subscription in the [Azure portal](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. On the **Hub** menu, select **Subscription**. Se questa voce non viene visualizzata, selezionare **Tutti i servizi**.
3. Find the appropriate subscription entry, and then look at the **MY ROLE** field. For a coadministrator, the value should be _Account admin_.

If you're not able to add a coadministrator, contact a service administrator or coadministrator for the subscription to get yourself added.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Step 4: Set your subscription, and sign up for migration
Avviare prima un prompt di PowerShell. For migration, set up your environment for both classic and Resource Manager.

Accedere con l'account per il modello di Resource Manager.

```powershell
    Connect-AzAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato il nome **My Azure Subscription** per la sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registration is a one-time step, but you must do it once before you attempt migration. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:
>
> *BadRequest: Subscription is not registered for migration* (Richiesta non valida: la sottoscrizione non è registrata per la migrazione)

Registrarsi con il provider di risorse di migrazione usando il comando seguente:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Wait five minutes for the registration to finish. Check the status of the approval by using the following command:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Assicurarsi che RegistrationState sia `Registered` prima di procedere.

Now, sign in to your account for the classic deployment model.

```powershell
    Add-AzureAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato **My Azure Subscription** come sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>Step 5: Have enough Resource Manager VM vCPUs
Make sure that you have enough Azure Resource Manager virtual machine vCPUs in the Azure region of your current deployment or virtual network. È possibile usare il comando PowerShell seguente per controllare il numero corrente di CPU virtuali in Azure Resource Manager. Per altre informazioni sulle quote di CPU virtuali, vedere [Limiti e Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

In questo esempio viene verificata la disponibilità nell'area **Stati Uniti occidentali**. Sostituire il nome dell'area di esempio con il nome della propria area.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Passaggio 6: eseguire i comandi per la migrazione delle risorse IaaS
* [Migrate VMs in a cloud service (not in a virtual network)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Eseguire la migrazione di macchine virtuali in una rete virtuale](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrate a storage account](#step-62-migrate-a-storage-account)

> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passaggio 6.1: Opzione 1 - Eseguire la migrazione delle macchine virtuali in un servizio cloud (non in una rete virtuale)
Get the list of cloud services by using the following command. Then pick the cloud service that you want to migrate. Se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web o di lavoro, il comando restituisce un messaggio di errore.

```powershell
    Get-AzureService | ft Servicename
```

Ottenere il nome della distribuzione per il servizio cloud. In questo esempio il nome del servizio è **My Service**. Sostituire il nome del servizio di esempio con il nome del proprio servizio.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

* **Option 1: Migrate the VMs to a platform-created virtual network.**

    First, validate that you can migrate the cloud service by using the following commands:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. If validation is successful, you can move on to the Prepare step.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Option 2: Migrate to an existing virtual network in the Resource Manager deployment model.**

    This example sets the resource group name to **myResourceGroup**, the virtual network name to **myVirtualNetwork**, and the subnet name to **mySubNet**. Sostituire i nomi dell'esempio con i nomi delle proprie risorse.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    First, validate that you can migrate the virtual network by using the following command:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. If validation is successful, you can proceed with the following Prepare step:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Dopo aver completato la procedura di preparazione tramite una delle opzioni precedenti, eseguire una query dello stato di migrazione delle VM, Ensure that they're in the `Prepared` state.

In questo esempio viene impostato il nome **myVM** per la VM. Sostituire il nome di esempio con il nome della propria VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Controllare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. If you're not ready for migration and you want to go back to the old state, use the following command:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passaggio 6.1: Opzione 2 - Eseguire la migrazione delle macchine virtuali in un una rete virtuale

Per eseguire la migrazione delle macchine virtuali in una rete virtuale, migrare la rete virtuale. Le macchine virtuali migreranno automaticamente con la rete virtuale. Selezionare la rete virtuale per cui si vuole eseguire la migrazione.
> [!NOTE]
> [Migrate a single virtual machine](migrate-single-classic-to-resource-manager.md) created using the classic deployment model by creating a new Resource Manager virtual machine with Managed Disks by using the VHD (OS and data) files of the virtual machine.
<br>

> [!NOTE]
> The virtual network name might be different from what is shown in the new portal. The new Azure portal displays the name as `[vnet-name]`, but the actual virtual network name is of type `Group [resource-group-name] [vnet-name]`. Before you start the migration, look up the actual virtual network name by using the command `Get-AzureVnetSite | Select -Property Name` or view it in the old Azure portal. 

In questo esempio viene impostato il nome **myVnet** per la rete virtuale. Sostituire il nome della rete virtuale di esempio con il nome della propria rete virtuale.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se la rete virtuale contiene ruoli Web o di lavoro, o VM con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.

First, validate that you can migrate the virtual network by using the following command:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. If validation is successful, you can proceed with the following Prepare step:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Controllare la configurazione per le macchine virtuali preparate usando Azure PowerShell o il portale di Azure. If you're not ready for migration and you want to go back to the old state, use the following command:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Step 6.2: Migrate a storage account
After you're done migrating the virtual machines, perform the following prerequisite checks before you migrate the storage accounts.

> [!NOTE]
> If your storage account has no associated disks or VM data, you can skip directly to the "Validate storage accounts and start migration" section.

* Prerequisite checks if you migrated any VMs or your storage account has disk resources:
    * Migrate virtual machines whose disks are stored in the storage account.

        The following command returns RoleName and DiskName properties of all the VM disks in the storage account. RoleName è il nome della macchina virtuale a cui il disco è collegato. If this command returns disks, then ensure that virtual machines to which these disks are attached are migrated before you migrate the storage account.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Delete unattached VM disks stored in the storage account.

        Find unattached VM disks in the storage account by using the following command:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        If the previous command returns disks, delete these disks by using the following command:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Delete VM images stored in the storage account.

        The following command returns all the VM images with OS disks stored in the storage account.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Il comando seguente restituisce tutte le immagini di macchina virtuale con dischi dati archiviati nell'account di archiviazione.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Delete all the VM images returned by the previous commands by using this command:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Validate storage accounts and start migration.

    Convalidare ogni account di archiviazione per la migrazione con il comando che segue. In questo esempio il nome dell'account di archiviazione è **myStorageAccount**. Sostituire il nome di esempio con il nome del proprio account di archiviazione.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    The next step is to prepare the storage account for migration.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Controllare la configurazione per l'account di archiviazione preparato tramite Azure PowerShell o il portale di Azure. If you're not ready for migration and you want to go back to the old state, use the following command:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Overview of platform-supported migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Panoramica sulla migrazione supportata dalla piattaforma per risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
