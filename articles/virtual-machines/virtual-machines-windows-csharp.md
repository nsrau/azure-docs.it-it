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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>Distribuire una macchina virtuale di Azure tramite C## #

In questo articolo viene illustrato come usare c# per creare una macchina virtuale di Azure e le relative risorse di supporto.

L'esecuzione di questi passaggi richiede circa 20 minuti.

## <a name="step-1-create-a-visual-studio-project"></a>Passaggio 1: creare un progetto di Visual Studio

In questo passaggio, ci si assicura che Visual Studio sia installato e si crea un'applicazione console da usare per creare le risorse.

1. Se non è già installato, installare [Visual Studio](https://www.visualstudio.com/).
2. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
3. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

## <a name="step-2-install-libraries"></a>Passaggio 2: installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questi passaggi. Per ottenere le librerie necessarie in Visual Studio, eseguire questa procedura:


1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Gestisci pacchetti NuGet** e fare clic su **Sfoglia**.
2. Digitare *Microsoft.IdentityModel.Clients.ActiveDirectory* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
3. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
4. Digitare *Microsoft.Azure.Management.Network* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
5. Digitare *Microsoft.Azure.Management.Storage* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.
6. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca, quindi fare clic su **Installa** e seguire le istruzioni per installare il pacchetto.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>Passaggio 3: creare le credenziali usate per autenticare le richieste

Prima di iniziare questo passaggio, assicurarsi di avere accesso a un'[entità servizio Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Dall'entità servizio si acquisisce un token per autenticare le richieste ad Azure Resource Manager.

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Storage;
    using Microsoft.Azure.Management.Storage.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Sostituire questi valori:
    
    - *{client-id}* con l'identificatore dell'applicazione Azure Active Directory. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD. Per individuare l'applicazione AD nel Portale di Azure, fare clic su **Azure Active Directory** nel menu di risorse e quindi fare clic su **Registrazioni per l'app**.
    - *{client-secret}* con la chiave di accesso dell'applicazione AD. È possibile trovare questo identificatore nel pannello Proprietà dell'applicazione AD.
    - *{tenant-id}* con l'identificatore del tenant della sottoscrizione. È possibile trovare l'identificatore del tenant nel pannello Proprietà di Azure Active Directory nel Portale di Azure. È indicato come *ID directory*.

3. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main nel file Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Salvare il file Program.cs.

## <a name="step-3-create-the-resources"></a>Passaggio 3: creare le risorse

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrare i provider e creare un gruppo di risorse

Tutte le risorse devono essere contenute in un gruppo di risorse. Per poter aggiungere risorse a un gruppo, la sottoscrizione deve essere registrata con i provider di risorse.

1. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi da usare per le risorse:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    Sostituire questi valori:

    - Tutti i nomi di risorsa che iniziano con *my* con nomi appropriati per l'ambiente.
    - *subscriptionId* con l'ID sottoscrizione. È possibile reperire l'ID sottoscrizione nel pannello Sottoscrizioni nel Portale di Azure.
    - *location* con l'[area di Azure](https://azure.microsoft.com/regions/) in cui si vogliono creare le risorse.
    - *adminName* con il nome dell'account amministratore nella macchina virtuale.
    - *adminPassword* con la password dell'account amministratore.

2. Per creare il gruppo di risorse e registrare i provider, aggiungere questo metodo alla classe Program:
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      Console.WriteLine("Registering the providers...");
      var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
      Console.WriteLine(rpResult.RegistrationState);
   
      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si usa un disco non gestito, è necessario un [account di archiviazione](../storage/storage-create-storage-account.md) per archiviare il file del disco rigido virtuale creato per la macchina virtuale.

1. Per creare l'account di archiviazione, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

      return await storageManagementClient.StorageAccounts.CreateAsync(
        groupName,
        storageName,
        new StorageAccountCreateParameters()
          {
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
              { Name = SkuName.StandardLRS},
            Kind = Kind.Storage,
            Location = location
          }
      );
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale.

1. Per creare l'indirizzo IP pubblico della macchina virtuale, aggiungere questo metodo alla classe Program:
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        ipName,
        new PublicIPAddress
          {
            Location = location,
            PublicIPAllocationMethod = "Dynamic"
          }
      );
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Una macchina virtuale creata con il modello di distribuzione di Gestione risorse deve essere inclusa in una rete virtuale.

1. Per creare una subnet e una rete virtuale, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        vnetName,
        new VirtualNetwork
          {
            Location = location,
            AddressSpace = address,
            Subnets = new List<Subnet> { subnet }
          }
      );
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale.

1. Per creare un'interfaccia di rete, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string subnetName,
      string vnetName,
      string ipName,
      string nicName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        nicName,
        new NetworkInterface
          {
            Location = location,
            IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                  {
                    Name = nicName,
                    PublicIPAddress = pubipResponse,
                    Subnet = subnetResponse
                  }
              }
          }
      );
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      subnetName,
      vnetName,
      ipName,
      nicName);
    Console.WriteLine(ncResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-an-availability-set"></a>Creare un set di disponibilità

I set di disponibilità semplificano la gestione della manutenzione delle macchine virtuali usate dall'applicazione.

1. Per creare il set di disponibilità, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Dopo avere creato tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Per creare la macchina virtuale, aggiungere questo metodo alla classe Program:

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName,
      string subscriptionId,
      string location,
      string nicName,
      string avsetName,
      string storageName,
      string adminName,
      string adminPassword,
      string vmName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
      Console.WriteLine("Creating the virtual machine...");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        vmName,
        new VirtualMachine
          {
            Location = location,
            AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
            HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
            OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
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
                  },
                OsDisk = new OSDisk
                  {
                    Name = "mytestod1",
                    CreateOption = DiskCreateOptionTypes.FromImage,
                    Vhd = new VirtualHardDisk
                      {
                        Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                      }
                  }
              }
          }
      );
    }
    ```

    > [!NOTE]
    > Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      nicName,
      avSetName,
      storageName,
      adminName,
      adminPassword,
      vmName);
    Console.WriteLine(vmResult.Result.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-4-delete-the-resources"></a>Passaggio 4: Eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Per eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1. Per eliminare il gruppo di risorse, aggiungere questo metodo alla classe Program:
   
    ```
    public static async void DeleteResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId)
    {
      Console.WriteLine("Deleting resource group...");
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Per chiamare il metodo aggiunto prima, aggiungere questo codice al metodo Main:
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>Passaggio 5: Eseguire l'applicazione console

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2. Premere **INVIO** dopo la visualizzazione dello stato *Riuscito*. 
   
3. Dopo aver creato la macchina virtuale e prima di premere **Invio** per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica delle risorse stesse nel Portale di Azure.

## <a name="next-steps"></a>Passaggi successivi
* Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Distribuire una macchina virtuale di Azure con C# e un modello di Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e C#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


