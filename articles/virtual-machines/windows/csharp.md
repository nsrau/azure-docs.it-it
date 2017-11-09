---
title: Creare e gestire una macchina virtuale di Azure tramite C# | Microsoft Docs
description: Usare C# e Azure Resource Manager per distribuire una macchina virtuale e tutte le relative risorse di supporto.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Creare e gestire macchine virtuali Windows in Azure tramite C# #

Una [macchina virtuale di Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) richiede diverse risorse di supporto di Azure. Questo articolo descrive come creare, gestire ed eliminare le risorse della macchina virtuale tramite C#. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Visual Studio
> * Installare il pacchetto
> * Creare le credenziali
> * Creare le risorse
> * Eseguire le attività di gestione
> * Eliminare le risorse
> * Eseguire l'applicazione

L'esecuzione di questi passaggi richiede circa 20 minuti.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Se non è già installato, installare [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selezionare **Sviluppo per desktop .NET** nella pagina Carichi di lavoro e quindi fare clic su **Installa**. Nel riepilogo si noti che **Strumenti di sviluppo per .NET Framework 4-4.6** viene selezionato automaticamente. Se Visual Studio è già stato installato, è possibile aggiungere il carico di lavoro .NET usando l'utilità di avvio di Visual Studio.
2. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
3. In **Modelli** > **Visual C#** selezionare **App console (.NET Framework)**, immettere *myDotnetProject* come nome del progetto, selezionare il percorso del progetto e quindi fare clic su **OK**.

## <a name="install-the-package"></a>Installare il pacchetto

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questi passaggi. Per ottenere le librerie necessarie in Visual Studio, eseguire questa procedura:

1. Fare clic su **Strumenti** > **Gestione pacchetti NuGet** e quindi su **Console di Gestione pacchetti**.
2. Nella console digitare il comando seguente:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Creare le credenziali

Prima di iniziare questo passaggio, assicurarsi di avere accesso a un'[entità servizio Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). È inoltre necessario registrare l'ID dell'applicazione, la chiave di autenticazione e l'ID del tenant che saranno necessari in un passaggio successivo.

### <a name="create-the-authorization-file"></a>Creare il file di autorizzazione

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su *myDotnetProject* > **Aggiungi** > **Nuovo elemento** e quindi selezionare **File di testo** in *Elementi di Visual C#*. Assegnare al file il nome *azureauth.properties* e quindi fare clic su **Aggiungi**.
2. Aggiungere le proprietà di autorizzazione seguenti:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Sostituire **&lt;subscription-id&gt;** con l'identificatore della sottoscrizione, **&lt;application-id&gt;** con l'identificatore dell'applicazione Active Directory, **&lt;authentication-key&gt;** con la chiave dell'applicazione e **&lt;tenant-id&gt;** con l'identificatore del tenant.

3. Salvare il file azureauth.properties. 
4. Impostare una variabile di ambiente Windows denominata AZURE_AUTH_LOCATION con il percorso completo verso il file di autorizzazione creato. Ad esempio, è possibile usare il comando PowerShell seguente:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Creare il client di gestione

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti alle istruzioni esistenti all'inizio del file:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Per creare i client di gestione, aggiungere questo codice al metodo Main:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Creare le risorse

### <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Tutte le risorse devono essere contenute in un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md).

Per specificare i valori per l'applicazione e creare il gruppo di risorse, aggiungere questo codice al metodo Main:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Creare il set di disponibilità

I [set di disponibilità](tutorial-availability-sets.md) semplificano la manutenzione delle macchine virtuali usate dall'applicazione.

Per creare il set di disponibilità, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico

Un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) è necessario per comunicare con la macchina virtuale.

Per creare l'indirizzo IP pubblico della macchina virtuale, aggiungere questo codice al metodo Main:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Una macchina virtuale deve essere inclusa in una subnet di una [rete virtuale](../../virtual-network/virtual-networks-overview.md).

Per creare una subnet e una rete virtuale, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale.

Per creare un'interfaccia di rete, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Dopo avere creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

Per creare la macchina virtuale, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Se si desidera usare un disco esistente invece di un'immagine del marketplace, usare questo codice:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Eseguire le attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È inoltre possibile creare codice per automatizzare le attività ripetitive o complesse.

Quando si deve eseguire un'operazione con la macchina virtuale, è necessario ottenere un'istanza della stessa:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

Per ottenere informazioni sulla macchina virtuale, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Arrestare la VM

È possibile arrestare una macchina virtuale e mantenere tutte le sue impostazioni, pur continuando a vedersela addebitata oppure è possibile arrestare una macchina virtuale e deallocarla. Quando una macchina virtuale viene deallocata, lo stesso viene fatto per tutte le risorse associate e la fatturazione termina.

Per arrestare la macchina virtuale senza deallocarla, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Per deallocare la macchina virtuale, sostituire la chiamata PowerOff con il codice seguente:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Avviare la VM

Per avviare la macchina virtuale, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ridimensionare la VM

Al momento di decidere le dimensioni della macchina virtuale, è necessario considerare molti aspetti della distribuzione. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](sizes.md).  

Per modificare le dimensioni della macchina virtuale, aggiungere questo codice al metodo Main:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Aggiungere un disco dati alla VM

Per aggiungere un disco dati alla macchina virtuale, aggiungere questo codice al metodo Main per aggiungere un disco dati di 2 GB, un LUN pari a 0 e un tipo di caching di ReadWrite:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Per eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

Per eliminare il gruppo di risorse, aggiungere questo codice al metodo Main:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Eseguire l'applicazione

L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. 

1. Per eseguire l'applicazione console, fare clic su **Avvia**.

2. Prima di premere **INVIO** per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel Portale di Azure. Fare clic sullo stato della distribuzione per visualizzare le informazioni corrispondenti.

## <a name="next-steps"></a>Passaggi successivi
* Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Distribuire una macchina virtuale di Azure con C# e un modello di Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Altre informazioni sull'uso delle [librerie di Azure per .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

