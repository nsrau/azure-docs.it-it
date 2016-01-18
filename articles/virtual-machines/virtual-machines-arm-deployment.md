<properties
	pageTitle="Distribuire le risorse usando le librerie .NET | Microsoft Azure"
	description="Informazioni su come utilizzare le librerie di calcolo, archiviazione e rete .NET per creare ed eliminare le risorse in Microsoft Azure utilizzando Gestione risorse."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Distribuire le risorse di Azure utilizzando librerie di calcolo, rete e archiviazione .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Questa esercitazione illustra come usare le librerie di calcolo, archiviazione e rete .NET per creare ed eliminare risorse in Microsoft Azure. Viene inoltre illustrato come autenticare le richieste di Gestione risorse di Azure con Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sono inoltre necessari:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Account di archiviazione di Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: Aggiungere un'applicazione ad Azure AD e impostare le autorizzazioni

Per usare Azure AD per autenticare le richieste a Gestione risorse di Azure, è necessario aggiungere un'applicazione alla directory predefinita. Per altre informazioni, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md).

1. Aprire un prompt di Azure PowerShell, eseguire questo comando e, quando richiesto, immettere le credenziali per la sottoscrizione:

			Login-AzureRmAccount

2. Sostituire {password} nel comando seguente con quella che si desidera usare, quindi eseguire il comando per creare l'applicazione:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Annotare l'identificatore dell’applicazione che viene restituito dopo che l'applicazione viene creata perché sarà necessario per il passaggio successivo. L'identificatore dell'applicazione è disponibile anche nel campo ID client dell'applicazione nella sezione Active Directory del portale di Azure.

3. Sostituire {application-id} con l'identificatore appena registrato, quindi creare l'entità servizio per l'applicazione:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Impostare le autorizzazioni per l'uso dell'applicazione:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Passaggio 2: Creare un progetto di Visual Studio e installare le librerie

I pacchetti NuGet sono il modo più semplice per installare le librerie necessarie per completare questa esercitazione. È necessario installare la libreria di Gestione risorse di Azure, la libreria di autenticazione Azure Active Directory e la libreria provider Computer Resource. Per ottenere queste librerie in Visual Studio, eseguire le operazioni seguenti:

1. Fare clic su **File** > **Nuovo** > **Progetto**.

2. In **Modelli** > **Visual C#** selezionare **Applicazione console**, immettere il nome e il percorso del progetto e fare clic su **OK**.

3. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.

4. Digitare *Active Directory* nella casella di ricerca, fare clic su **Installa** per il pacchetto di Active Directory Authentication Library, quindi seguire le istruzioni per l'installazione del pacchetto.

5. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Microsoft.Azure.Management.Compute* nella casella di ricerca, fare clic su **Installa** per le librerie di calcolo .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

6. Digitare *Microsoft.Azure.Management.Network* nella casella di ricerca, fare clic su **Installa** per le librerie di rete .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

7. Digitare *Microsoft.Azure.Management.Storage* nella casella di ricerca, fare clic su **Installa** per le librerie di rete .NET e quindi seguire le istruzioni per l'installazione del pacchetto.

8. Digitare *Microsoft.Azure.Management.Resources* nella casella di ricerca e quindi fare clic su **Installa** per le librerie di gestione delle risorse.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## Passaggio 3: Creare le credenziali usate per autenticare le richieste

Dopo la creazione dell'applicazione Azure Active Directory e l'installazione della libreria di autenticazione, formattare le informazioni sull'applicazione nelle credenziali usate per autenticare le richieste in Gestione risorse di Azure.

1. Aprire il file Program.cs per il progetto creato, quindi aggiungere le seguenti istruzioni using all'inizio del file:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
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
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Sostituire {application-id} con l'identificatore dell'applicazione registrato in precedenza, {password} con la password scelta per l'applicazione AD e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID del tenant, eseguire Get-AzureSubscription.

3. Aggiungere questo codice al metodo Main nel file Program.cs per creare le credenziali:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Salvare il file Program.cs.

## Passaggio 4: Aggiungere il codice per registrare i provider e creare le risorse

### Registrare i provider e creare un gruppo di risorse

Tutte le risorse devono essere contenute in un gruppo di risorse. Per poter aggiungere risorse a un gruppo, la sottoscrizione deve essere registrata con i provider di risorse.

1. Aggiungere le variabili al metodo Main della classe Program per specificare i nomi da usare per le risorse, la località delle risorse, ad esempio "Stati Uniti occidentali", le informazioni dell'account amministratore e l'identificatore della sottoscrizione:

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Sostituire tutti i segnaposto racchiusi tra parentesi graffe con i nomi da usare. Per trovare l'ID della sottoscrizione, è possibile eseguire Get-AzureSubscription.

2. Aggiungere questo metodo alla classe Program per creare il gruppo di risorse:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Aggiungere questo codice al metodo Main per chiamare il metodo appena aggiunto:

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Creare un account di archiviazione

È necessario un account di archiviazione per archiviare il file di disco rigido virtuale creato per la macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare l'account di archiviazione:

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Creare l'indirizzo IP pubblico

È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare l'indirizzo IP pubblico:

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Creare la rete virtuale

Una macchina virtuale creata con il modello di distribuzione di Gestione risorse deve essere inclusa in una rete virtuale.

1. Aggiungere questo metodo alla classe Program per creare una subnet e una rete virtuale:

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Creare un set di disponibilità

I set di disponibilità semplificano la gestione della manutenzione delle macchine virtuali usate dall'applicazione.

1. Aggiungere questo metodo alla classe Program per creare il set di disponibilità:

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Aggiungere questo codice al metodo Main della classe Program per chiamare il metodo appena aggiunto:

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Creare una macchina virtuale

Ora che sono state create tutte le risorse di supporto, è possibile creare una macchina virtuale.

1. Aggiungere questo metodo alla classe Program per creare la macchina virtuale:

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
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
						CreateOption = "FromImage",
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
	```

	>[AZURE.NOTE]Questa esercitazione illustra come creare una macchina virtuale in cui è in esecuzione una versione del sistema operativo Windows Server. Per altre informazioni sulla selezione di altre immagini, vedere [Esplorazione e selezione di immagini delle macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](resource-groups-vm-searching.md).

2. Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Passaggio 5: Aggiungere il codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Se si desidera eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1. Aggiungere il metodo seguente alla classe Program per eliminare il gruppo di risorse:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Passaggio 6: Eseguire l'applicazione console

1. Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2. Premere **INVIO** dopo la restituzione di tutti i codici di stato per creare ciascuna risorsa. Dopo la creazione della macchina virtuale, eseguire il passaggio seguente prima di premere INVIO per eliminare tutte le risorse.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere Invio per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di Azure per visualizzare lo stato delle risorse:

	![Creare un'applicazione di Active Directory](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->