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
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Distribuire le risorse di Azure utilizzando librerie di calcolo, rete e archiviazione .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


In questa esercitazione viene illustrato come utilizzare alcuni dei client disponibili nelle librerie di calcolo, archiviazione e rete .NET per creare ed eliminare le risorse in Microsoft Azure. Viene inoltre illustrato come autenticare le richieste di Gestione risorse di Azure con Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Per completare questa esercitazione sono inoltre necessari:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Account di archiviazione di Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/it-IT/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/it-IT/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

L'esecuzione di questi passaggi richiede circa 30 minuti.

## Passaggio 1: Aggiungere un'applicazione ad Azure AD e impostare le autorizzazioni

Per usare Azure AD per autenticare le richieste a Gestione risorse di Azure, è necessario aggiungere un'applicazione alla directory predefinita. Per aggiungere un'applicazione, eseguire le operazioni seguenti:

1. Aprire un prompt di Azure PowerShell, e poi eseguire questo comando e, quando richiesto, immettere le credenziali per la sottoscrizione:

	    Login-AzureRmAccount

2. Sostituire {password} nel comando seguente con quella che si desidera usare, quindi eseguire il comando per creare l'applicazione:

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Annotare l'identificatore dell’applicazione che viene restituito dopo che l'applicazione viene creata perché sarà necessario per il passaggio successivo. L'identificatore dell'applicazione è disponibile anche nel campo ID client dell'applicazione nella sezione Active Directory del portale di Azure.

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

5. Nella parte superiore della pagina selezionare **Includi versione preliminare**. Digitare *Calcolo di Azure* nella casella di ricerca, fare clic su **Installa** per le librerie di calcolo .NET, quindi seguire le istruzioni per l'installazione del pacchetto.

6. Digitare *Rete di Azure* nella casella di ricerca, fare clic su **Installa** per le librerie di rete .NET, quindi seguire le istruzioni per l'installazione del pacchetto.

7. Digitare *Archiviazione di Azure* nella casella di ricerca, fare clic su **Installa** per le librerie di rete .NET, quindi seguire le istruzioni per l'installazione del pacchetto.

8. Digitare *Gestione risorse di Azure* nella casella di ricerca, fare clic su **Installa** per le librerie di gestione delle risorse.

È ora possibile iniziare a usare le librerie per creare l'applicazione.

## Passaggio 3: Creare le credenziali usate per autenticare le richieste

Dopo la creazione dell'applicazione Azure Active Directory e l'installazione della libreria di autenticazione, formattare le informazioni sull'applicazione nelle credenziali usate per autenticare le richieste in Gestione risorse di Azure. Eseguire le operazioni seguenti:

1.	Aprire il file Program.cs per il progetto creato, quindi aggiungere le seguenti istruzioni using all'inizio del file:

    using Microsoft.Azure; using Microsoft.IdentityModel.Clients.ActiveDirectory; using Microsoft.Azure.Management.Resources; using Microsoft.Azure.Management.Resources.Models; using Microsoft.Azure.Management.Storage; using Microsoft.Azure.Management.Storage.Models; using Microsoft.Azure.Management.Network; using Microsoft.Azure.Management.Network.Models; using Microsoft.Azure.Management.Compute; using Microsoft.Azure.Management.Compute.Models;


2. Aggiungere il metodo seguente alla classe Program per ottenere il token necessario per creare le credenziali:

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);

          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Sostituire {application-id} con l'identificatore dell'applicazione registrato in precedenza, {password} con la password scelta per l'applicazione AD e {tenant-id} con l'identificatore del tenant per la sottoscrizione. Per trovare l'ID del tenant, eseguire Get-AzureSubscription.

3.	Aggiungere il codice seguente al metodo Main nel file Program.cs per creare le credenziali:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Sostituire {subscription-id} con l'identificatore della sottoscrizione.

4.	Salvare il file Program.cs.

## Passaggio 4: Aggiungere il codice per registrare i provider e creare le risorse

### Registrare i provider e creare un gruppo di risorse

Le risorse vengono sempre distribuite per un gruppo di risorse. È possibile usare le classi [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) per creare il gruppo di risorse in cui vengono distribuite le risorse.

1.	Aggiungere il metodo seguente alla classe Program per creare il gruppo di risorse:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");

          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateResourceGroup(credential);
		Console.ReadLine();

###Creare un account di archiviazione

È necessario un account di archiviazione per archiviare il file di disco rigido virtuale creato per la macchina virtuale.

1.	Aggiungere il metodo seguente alla classe Program per creare l'account di archiviazione:

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");

          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateStorageAccount(credential);
		Console.ReadLine();

###Creare la configurazione di rete

Una macchina virtuale è più produttiva quando viene aggiunta a una rete virtuale.

1.	Aggiungere il seguente metodo alla classe Program per creare una subnet, un indirizzo IP pubblico e una rete virtuale:

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateNetwork(credential);
		Console.ReadLine();

###Creare una macchina virtuale

Ora che sono state create tutte le risorse di supporto, è possibile creare una macchina virtuale.

1.	Aggiungere il seguente metodo alla classe Program per creare la macchina virtuale:

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);

            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]I nomi dei file VHD dell'immagine cambiano regolarmente nella raccolta immagini. È quindi necessario ottenere un nome di immagine corrente per distribuire la macchina virtuale. Per eseguire questa operazione, vedere [Gestire le immagini mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), quindi sostituire {source-image-name} con il nome del file VHD da usare. Ad esempio, "a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201411.01-en.us-127GB.vhd".

	Sostituire {subscription-id} con l'identificatore della sottoscrizione.

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		CreateVirtualMachine(credential);
    Console.ReadLine();

##Passaggio 5: Aggiungere il codice per eliminare le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Se si desidera eliminare le macchine virtuali e tutte le risorse di supporto, è sufficiente eliminare il gruppo di risorse.

1.	Aggiungere il metodo seguente alla classe Program per eliminare il gruppo di risorse:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Aggiungere il codice seguente al metodo Main per chiamare il metodo appena aggiunto:

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Passaggio 6: Eseguire l'applicazione console

1.	Per eseguire l'applicazione console, fare clic su **Avvia** in Visual Studio e accedere ad Azure AD usando lo stesso nome utente e la stessa password specificati per la sottoscrizione.

2.	Premere **INVIO** dopo la restituzione di tutti i codici di stato per creare ciascuna risorsa. Dopo la creazione della macchina virtuale, eseguire il passaggio seguente prima di premere INVIO per eliminare tutte le risorse.

	L'esecuzione completa dell'applicazione console dall'inizio alla fine dovrebbe richiedere circa 5 minuti. Prima di premere INVIO per avviare l'eliminazione delle risorse, è consigliabile dedicare alcuni minuti alla verifica della creazione delle risorse nel portale di Azure prima dell'eliminazione.

3. Passare a Log di controllo nel portale di Azure per visualizzare lo stato delle risorse:

	![Creare un'applicazione di Active Directory](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_1203_2015-->