<properties
	pageTitle="Distribuire le risorse di Azure tramite C# | Microsoft Azure"
	description="Informazioni su come usare C# e Azure Resource Manager per creare risorse di Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Distribuire le risorse di Azure tramite C## 

In questo articolo viene illustrato come creare risorse di Azure mediante C#.

È innanzitutto necessario assicurarsi di aver eseguito queste operazioni:

- Installare [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificare l'installazione di [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Ottenere un [token di autenticazione](../resource-group-authenticate-service-principal.md)

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: Creare un progetto di Visual Studio e installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. È necessario installare la libreria di Gestione risorse di Azure, la libreria di autenticazione Azure Active Directory e la libreria provider Computer Resource. Per ottenere queste librerie in Visual Studio, seguire questa procedura:

1. Fare clic su **File** > **Nuovo** > **Progetto**.

2. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

3. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.

4. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

5. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, fare clic su **Installa** per le librerie di calcolo .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

6. Digitare *Microsoft.Azure.Management.Network* nella casella di ricerca, fare clic su **Installa** per le librerie di rete .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

7. Digitare *Microsoft.Azure.Management.Storage* nella casella di ricerca, fare clic su **Installa** per le librerie .NET di archiviazione, quindi seguire le istruzioni per l'installazione del pacchetto.

8. Digitare *Microsoft.Azure.Management.ResourceManager* nella casella di ricerca e quindi fare clic su **Installa** per le librerie di gestione delle risorse.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## Passaggio 2: Creare le credenziali da usare per autenticare le richieste

L'applicazione di Azure Active Directory è stata creata e la libreria di autenticazione è stata installata. È ora necessario formattare le informazioni sull'applicazione in credenziali usate per autenticare le richieste in Azure Resource Manager. Procedere come segue:

1. Aprire il file Program.cs per il progetto creato e quindi aggiungere le istruzioni using seguenti all'inizio del file:

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

2. Aggiungere questo metodo alla classe Program per ottenere il token necessario per creare le credenziali:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

	Sostituire {client-id} con l'identificatore dell'applicazione Azure Active Directory, {client-secret} con la chiave di accesso dell'applicazione di Active Directory e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID tenant, eseguire Get-AzureRmSubscription. È possibile trovare la chiave di accesso mediante il portale di Azure.

3. Aggiungere questo codice al metodo Main nel file Program.cs per creare le credenziali:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salvare il file Program.cs.

## Passaggio 3: Aggiungere il codice per registrare i provider e creare le risorse

### Registrare i provider e creare un gruppo di risorse

Tutte le risorse devono essere contenute in un gruppo di risorse. Per poter aggiungere risorse a un gruppo, la sottoscrizione deve essere registrata con i provider di risorse.

1. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi da usare per le risorse, la località delle risorse, ad esempio "Stati Uniti centrali", le informazioni dell'account amministratore e l'identificatore della sottoscrizione:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Sostituire tutti i valori delle variabili con i nomi e l'identificatore che si vuole usare. Per trovare l'ID della sottoscrizione, è possibile eseguire il cmdlet Get-AzureRmSubscription.

2. Aggiungere questo metodo alla classe Program per creare il gruppo di risorse e registrare i provider:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### Creare un account di archiviazione

È necessario un [account di archiviazione](../storage/storage-create-storage-account.md) per archiviare il file del disco rigido virtuale creato per la macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare l'account di archiviazione:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
            { SubscriptionId = subscriptionId };
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

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### Creare l'indirizzo IP pubblico

È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare l'indirizzo IP pubblico della macchina virtuale:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
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

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### Creare la rete virtuale

Una macchina virtuale creata con il modello di distribuzione di Gestione risorse deve essere inclusa in una rete virtuale.

1. Aggiungere questo metodo alla classe Program per creare una subnet e una rete virtuale:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
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
        
2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### Creare l'interfaccia di rete

Una macchina virtuale richiede un'interfaccia di rete per comunicare nella rete virtuale appena creata.

1. Aggiungere questo metodo alla classe Program per creare un'interfaccia di rete:

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
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

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

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

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

### Creare un set di disponibilità

I set di disponibilità semplificano la gestione della manutenzione delle macchine virtuali usate dall'applicazione.

1. Aggiungere questo metodo alla classe Program per creare il set di disponibilità:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### Creare una macchina virtuale

Ora che sono state create tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare la macchina virtuale:

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
          var networkManagementClient = new NetworkManagementClient(credential);
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

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
          Console.WriteLine(vm.ProvisioningState);
        }

	>[AZURE.NOTE] Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorare e selezionare immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avsetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##Passaggio 4: Aggiungere il codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Se si desidera eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1.	Aggiungere questo metodo alla classe Program per eliminare il gruppo di risorse:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## Passaggio 5: Eseguire l'applicazione console

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2. Premere **INVIO** dopo la restituzione di tutti i codici di stato per creare ciascuna risorsa. Dopo la creazione della macchina virtuale, eseguire il passaggio seguente prima di premere INVIO per eliminare tutte le risorse.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere Invio per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di Azure per visualizzare lo stato delle risorse:

	![Sfogliare i log di controllo nel portale di Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## Passaggi successivi

- Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Distribuire una macchina virtuale di Azure con C# e un modello di Resource Manager](virtual-machines-windows-csharp-template.md).
- Per informazioni su come gestire la macchina virtuale appena creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-csharp-manage.md).

<!---HONumber=AcomDC_0629_2016-->