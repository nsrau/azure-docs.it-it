---
title: Distribuire una macchina virtuale di Azure tramite C# | Microsoft Docs
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
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: f04783896eb36e9a8c3e5cc8422a1fb054b36dd2
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017


---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Creare e gestire macchine virtuali Windows in Azure tramite C# #

Una [macchina virtuale di Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) richiede diverse risorse di supporto di Azure. Questo articolo descrive come creare, gestire ed eliminare le risorse della macchina virtuale tramite C#. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Visual Studio
> * Installare le librerie
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

## <a name="install-libraries"></a>Installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questi passaggi. Per ottenere le librerie necessarie in Visual Studio, eseguire questa procedura:

1. Fare clic con il pulsante destro del mouse su *myDotnetProject* in Esplora soluzioni, fare clic su **Gestisci pacchetti NuGet per la soluzione** e quindi su **Sfoglia**.
2. Digitare *Microsoft.IdentityModel.Clients.ActiveDirectory* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
3. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
4. Digitare *Microsoft.Azure.Management.Network* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
5. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## <a name="create-credentials"></a>Creare le credenziali

Prima di iniziare questo passaggio, assicurarsi di avere accesso a un'[entità servizio Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). È inoltre necessario registrare l'ID dell'applicazione, la chiave di autenticazione e l'ID del tenant che saranno necessari in un passaggio successivo.

1. Aprire il file Program.cs che è stato creato e quindi aggiungere le istruzioni using seguenti alle istruzioni esistenti all'inizio del file:
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Successivamente, nel metodo Main del file Program.cs aggiungere le variabili per specificare i valori comuni usati nel codice:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciption-id";
    var location = "westus";
    var vmName = "myVM";
    ```

    Sostituire **subscription-id** con l'identificatore della sottoscrizione.

3. Per creare le credenziali di Active Directory necessarie per inoltrare le richieste, aggiungere questo metodo alla classe Program:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("application-id", "authentication-key");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Sostituire **application-id**, **authentication-key** e **tenant-id** con i valori raccolti durante la creazione dell'entità servizio di Azure Active Directory.

4. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main nel file Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

## <a name="create-resources"></a>Creare le risorse

### <a name="initialize-management-clients"></a>Inizializzare i client di gestione

I client di gestione sono necessari per creare e gestire le risorse tramite .NET SDK in Azure. Per creare i client di gestione, aggiungere questo codice al metodo Main nel file Program.cs:

```
var resourceManagementClient = new ResourceManagementClient(credential)
    { SubscriptionId = subscriptionId };
var networkManagementClient = new NetworkManagementClient(credential)
    { SubscriptionId = subscriptionId };
var computeManagementClient = new ComputeManagementClient(credential)
    { SubscriptionId = subscriptionId };
```

### <a name="create-the-vm-and-supporting-resources"></a>Creare la macchina virtuale e le risorse di supporto

Tutte le risorse devono essere contenute in un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md).

1. Aggiungere questo metodo alla classe Program per creare il gruppo di risorse:
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName,
      string location)
    {   
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      resourceManagementClient,
      groupName,
      location);
    Console.WriteLine("Resource group creation: " + 
      rgResult.Result.Properties.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

I [set di disponibilità](tutorial-availability-sets.md) semplificano la manutenzione delle macchine virtuali usate dall'applicazione.

1. Per creare il set di disponibilità, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        "myAVSet",
        new AvailabilitySet()
        { 
          Sku = new Microsoft.Azure.Management.Compute.Models.Sku("Aligned"),
          PlatformFaultDomainCount = 3,
          Location = location 
        });
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Availability set created. Press enter to continue...");
    Console.ReadLine();
    ```

Un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) è necessario per comunicare con la macchina virtuale.

1. Per creare l'indirizzo IP pubblico della macchina virtuale, aggiungere questo metodo alla classe Program:
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync( 
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        "myIpAddress",
        new PublicIPAddress
        {
          Location = location,
          PublicIPAllocationMethod = "Dynamic"
        });
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("IP address creation: " + 
      ipResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Una macchina virtuale deve essere inclusa in una subnet di una [rete virtuale](../../virtual-network/virtual-networks-overview.md).

1. Per creare una subnet e una rete virtuale, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = new Subnet
      {
        Name = "mySubnet",
        AddressPrefix = "10.0.0.0/24"
      };
      var address = new AddressSpace 
      {
        AddressPrefixes = new List<string> { "10.0.0.0/16" }
      };
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        "myVNet",
        new VirtualNetwork
        {
          Location = location,
          AddressSpace = address,
          Subnets = new List<Subnet> { subnet }
        });
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual network creation: " + 
      vnResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale.

1. Per creare un'interfaccia di rete, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        "myVNet",
        "mySubnet");
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        "myIPaddress");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        "myNic",
        new NetworkInterface
        {
          Location = location,
          IpConfigurations = new List<NetworkInterfaceIPConfiguration>
          {
            new NetworkInterfaceIPConfiguration
            {
              Name = "myNic",
              PublicIPAddress = publicIP,
              Subnet = subnet
            }
          }
        });
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Network interface creation: " + 
      ncResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

Dopo avere creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Per creare la macchina virtuale, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      NetworkManagementClient networkManagementClient,
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
 
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        "myNic");
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        "myAVSet");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        "myVM",
        new VirtualMachine
        {
          Location = location,
          AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
          {
            Id = avSet.Id
          },
          HardwareProfile = new HardwareProfile
          {
            VmSize = "Standard_DS1"
          },
          OsProfile = new OSProfile
          {
            AdminUsername = "azureuser",
            AdminPassword = "Azure12345678",
            ComputerName = "myVM",
            WindowsConfiguration = new WindowsConfiguration
            {
              ProvisionVMAgent = true
            }
          },
          NetworkProfile = new NetworkProfile
          {
            NetworkInterfaces = new List<NetworkInterfaceReference>
            {
              new NetworkInterfaceReference { Id = nic.Id }
            }
          },
          StorageProfile = new StorageProfile
          {
            ImageReference = new ImageReference
            {
              Publisher = "MicrosoftWindowsServer",
              Offer = "WindowsServer",
              Sku = "2012-R2-Datacenter",
              Version = "latest"
            }
          }
        });
    }
    ```

    > [!NOTE]
    > Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      networkManagementClient,
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual machine creation: " + 
      vmResult.Result.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

## <a name="perform-management-tasks"></a>Eseguire le attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È inoltre possibile creare codice per automatizzare le attività ripetitive o complesse.

### <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

1. Per ottenere informazioni sulla macchina virtuale, aggiungere questo metodo alla classe Program:

    ```
    public static async void GetVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Getting information about the virtual machine...");

      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName,
        vmName,
        InstanceViewTypes.InstanceView);

      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }

      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }

      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }

      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:

    ```
    GetVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="stop-the-vm"></a>Arrestare la VM

È possibile arrestare una macchina virtuale e mantenere tutte le sue impostazioni, pur continuando a vedersela addebitata oppure è possibile arrestare una macchina virtuale e deallocarla. Quando una macchina virtuale viene deallocata, lo stesso viene fatto per tutte le risorse associate e la fatturazione termina.

1. Per arrestare la macchina virtuale senza deallocarla, aggiungere questo metodo alla classe Program:

    ```
    public static async void StopVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Stopping the virtual machine...");
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Per deallocare la macchina virtuale, sostituire la chiamata PowerOff con il codice seguente:

    ```
    await computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    StopVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="start-the-vm"></a>Avviare la VM

1. Per avviare la macchina virtuale, aggiungere questo metodo alla classe Program:

    ```
    public static async void StartVirtualMachineAsync(
      string groupName, 
      string vmName, 
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Starting the virtual machine...");
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    StartVirtualMachineAsync(
      groupName,
      vmName,
      ComputeManagementClient computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="resize-the-vm"></a>Ridimensionare la VM

Al momento di decidere le dimensioni della macchina virtuale, è necessario considerare molti aspetti della distribuzione. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](sizes.md).  

1. Per modificare le dimensioni della macchina virtuale, aggiungere questo metodo alla classe Program:

    ```
    public static async void UpdateVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Updating the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_DS3";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    UpdateVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Aggiungere un disco dati alla VM

1. Per aggiungere un disco dati alla macchina virtuale, aggiungere questo metodo alla classe Program:

    ```
    public static async void AddDataDiskAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
        {
          Lun = 0,
          Name = "myDataDisk1",
          CreateOption = DiskCreateOptionTypes.Empty,
          DiskSizeGB = 2,
          Caching = CachingTypes.ReadWrite
        });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. Per chiamare il metodo appena aggiunto, aggiungere questo codice al metodo Main:

    ```
    AddDataDiskAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

## <a name="delete-resources"></a>Eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Per eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1. Per eliminare il gruppo di risorse, aggiungere questo metodo alla classe Program:
   
    ```
    public static async void DeleteResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName)
    {
      Console.WriteLine("Deleting resource group...");
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    DeleteResourceGroupAsync(
      resourceManagementClient,
      groupName);
    Console.ReadLine();
    ```

3. Salvare il progetto.

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2. Premere **INVIO** dopo che è stato restituito lo stato di ogni risorsa. Nelle informazioni sullo stato dovrebbe essere visualizzato lo stato di provisioning **Completato**. Dopo che la macchina virtuale è stata creata, si avrà l'opportunità di eliminare tutte le risorse create. Prima di premere **INVIO** per iniziare a eliminare le risorse, è consigliabile dedicare qualche minuto alla verifica della loro creazione nel portale di Azure. Se il portale di Azure è aperto, potrebbe essere necessario aggiornare il pannello per visualizzare le nuove risorse.  

    L'esecuzione completa dell'applicazione console dall'inizio alla fine richiederà circa cinque minuti. L'eliminazione di tutte le risorse e del gruppo di risorse potrebbe richiedere alcuni minuti dopo l'esecuzione completa dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Distribuire una macchina virtuale di Azure con C# e un modello di Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Altre informazioni sull'uso delle [librerie di Azure per .NET](https://docs.microsoft.com/dotnet/azure/index?view=azure-dotnet).


