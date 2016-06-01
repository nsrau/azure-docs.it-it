<properties
   pageTitle="Resource Manager SDK per .Net | Microsoft Azure"
   description="Panoramica dell'autenticazione ed esempi di utilizzo di Resource Manager .Net SDK"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK per .Net  
Gli SDK di anteprima di Azure Resource Manager (ARM) sono disponibili per più linguaggi e piattaforme. Ogni implementazione linguaggio è disponibile con la gestione pacchetti dell'ecosistema e in GitHub.

Il codice in ogni SDK viene generato dalle [specifiche dell'API basata su REST di Azure](https://github.com/azure/azure-rest-api-specs). Queste specifiche sono open source e si basano sulla specifica Swagger v2. Il codice degli SDK viene generato con un progetto open source denominato [AutoRest](https://github.com/azure/autorest). AutoRest trasforma le specifiche delle API basate su REST in librerie client in più linguaggi. Se ci sono aspetti del codice generato negli SDK che si vuole migliorare, l'intero set di strumenti per creare gli SDK è aperto, completamente disponibile e basato su un formato di specifica API ampiamente diffuso.

Azure SDK per .NET viene fornito come set di pacchetti NuGet che consente di chiamare la maggior parte delle API esposte da Azure Resource Manager. Se l'SDK non espone la funzionalità necessaria, è possibile combinare facilmente l'SDK con le normali chiamate all'API REST ARM in background.

Questa documentazione, anche se non ha lo scopo di descrivere tutti gli aspetti di Azure SDK per .NET, delle API ARM di Azure o di Visual Studio, consente tuttavia di iniziare a lavorare rapidamente.

Un progetto di esempio completo, da cui sono tratti tutti i frammenti di codice usati in questo articolo, è scaricabile da [qui](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Autenticazione
L'autenticazione per ARM viene gestita da Azure Active Directory (AD). Per connettersi a un'API, prima di tutto è necessario eseguire l'autenticazione con Azure AD per ricevere un token di autenticazione che è possibile passare a ogni richiesta. Per ottenere questo token, prima è necessario creare una cosiddetta applicazione Azure AD e un'entità servizio che verrà usata per l'accesso. Per istruzioni dettagliate vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](resource-group-create-service-principal-portal.md).

Dopo avere creato l'entità servizio, sono necessari:
* ID client (GUID)
* Segreto client (stringa)
* ID tenant (GUID) o nome di dominio (stringa)

### Ricezione di AccessToken dal codice
Il token di autenticazione può essere acquisito facilmente con le righe di codice seguenti, passando solo l'ID tenant di Azure AD, l'ID client applicazione di Azure AD e il segreto client applicazione di Azure AD. Salvare il token per diverse richieste perché, per impostazione predefinita, è valido per 1 ora.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Query di sottoscrizioni di Azure associate all'applicazione autenticata
Uno dei primi passaggi da eseguire consiste nell'effettuare una query delle sottoscrizioni di Azure associate all'applicazione appena autenticata. L'ID per la sottoscrizione di destinazione sarà obbligatorio per passare ogni chiamata API eseguita d'ora in poi.

Il codice di esempio seguente esegue una query delle API di Azure usando direttamente l'API REST, e non una delle funzionalità di Azure SDK per .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Si noti che si ottiene una risposta JSON da Azure, da cui si estraggono quindi gli ID sottoscrizione per restituire un elenco di ID. Tutte le chiamate successive alle API ARM di Azure in questa documentazione usano un singolo ID sottoscrizione di Azure, quindi, se l'applicazione è associata a più sottoscrizioni, è sufficiente selezionare quella corretta e proseguire passandola come parametro.

Da adesso, per ogni chiamata eseguita alle API di Azure, si userà Azure SDK per .NET, quindi il codice sarà leggermente diverso.

### Wrapping del token come oggetto TokenCredentials
Per tutte le chiamate API seguenti sarà necessario il token ricevuto da Azure AD nel formato di un oggetto "TokenCredentials". Per creare un oggetto di questo tipo, è sufficiente passare il token non elaborato come parametro al costruttore della classe.

```csharp
var credentials = new TokenCredentials(token);
```

## Creazione di un gruppo di risorse
Ora verrà creato un gruppo di risorse, perché questo è l'elemento più importante in Azure. Le risorse generali e i gruppi di risorse vengono gestiti da *ResourceManagementClient* e, come per i client di gestione più specializzati seguenti che verranno usati, è necessario fornire le credenziali, oltre a un ID sottoscrizione, per identificare la sottoscrizione da usare.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Creazione di risorse manuale o mediante modelli
È possibile interagire con le API di Azure Resource Manager in molti modi diversi, ma i principali sono due:

* Manualmente, chiamando provider di risorse specifici
* Usando un modello di Azure Resource Manager (chiamato anche modello ARM)

L'uso dei modelli ARM presenta i vantaggi seguenti:

* È possibile specificare in modo dichiarativo l'aspetto voluto del risultato finale, invece di specificare come ottenerlo
* Non è necessario gestire manualmente l'esecuzione parallela delle distribuzioni, perché sarà ARM a farlo automaticamente
* Non è necessario conoscere C# o qualsiasi altro linguaggio per distribuire un modello ARM anche se è possibile usare qualunque linguaggio per avviare una distribuzione basata su modelli
* Il linguaggio specifico del dominio, DSL, usato nei modelli viene compilato con JSON e può essere facilmente compreso da chiunque abbia usato JSON

Nonostante tutti i vantaggi dei modelli, prima di tutto verrà illustrato come chiamare le API manualmente.

### Creazione dettagliata di una macchina virtuale
Sono quindi stati creati la sottoscrizione e il gruppo di risorse. Se si vuole distribuire una macchina virtuale, è opportuno sapere da quali parti è effettivamente costituita. Tali parti sono le seguenti:

* 1 o più account di archiviazione, per archiviare i dischi persistenti
* 1 o più indirizzi IP pubblici, per essere accessibili da Internet (incluso un nome DNS)
* 1 o più reti virtuali, per la comunicazione interna tra le risorse
* 1 o più schede di interfaccia di rete, per consentire alla VM di comunicare
* 1 o più macchine virtuali (VM), per eseguire il software

Un altro aspetto interessante è che alcune di queste risorse possono essere create in parallelo, mentre altre non possono. ad esempio:

* Le schede di interfaccia di rete dipendono dall'IP pubblico e dalla rete virtuale
* Le VM dipendono dalle schede di interfaccia di rete e dagli account di archiviazione

È necessario verificare di non creare un'istanza di nessuna risorsa prima che siano state create le dipendenze necessarie. L'[esempio](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) completo fornito con questa documentazione illustra come creare in modo efficiente le risorse in parallelo mentre si continua a tenere traccia di ciò che è stato creato.

#### Creazione dell'account di archiviazione
È necessario un account di archiviazione per archiviare i dischi VHD virtuali per la macchina virtuale. Se esiste già un account di archiviazione, è possibile usarlo per più VM, ma si ricordi di distribuire il carico tra più account di archiviazione per non raggiungere i limiti. Si ricordi che il tipo e la posizione dell'account di archiviazione possono limitare la scelta delle dimensioni delle VM perché non tutte le dimensioni delle VM sono disponibili in tutte le aree e/o per tutti i tipi di account di archiviazione.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Creazione dell'indirizzo IP pubblico
L'indirizzo IP pubblico rende le risorse di Azure accessibili da Internet. Insieme all'indirizzo IP verrà assegnato anche un nome di dominio completo (FQDN) che è possibile usare per eseguire l'accesso più facilmente.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Creazione della rete virtuale
Ogni VM creata con le API ARM devono fare parte di una rete virtuale, anche se nella rete è presente una sola VM. La rete virtuale deve contenere almeno una subnet, ma è possibile averne diverse in cui dividere e proteggere le risorse.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Creazione della scheda di interfaccia di rete
La scheda di interfaccia di rete connette la VM alla rete virtuale in cui si trova. Una VM può avere diverse schede di interfaccia di rete ed essere quindi associata a più reti virtuali. In questo esempio si presuppone che si associno le VM a una sola rete virtuale.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Creazione della macchina virtuale
Ora verrà creata la macchina virtuale vera e propria. La VM dipende direttamente o indirettamente da tutte le risorse create sopra, quindi è necessario attendere che tutte le risorse siano pronte prima di provare a effettuare il provisioning di una VM. Il provisioning di una VM è l'operazione che tra tutte richiede più tempo, quindi è probabile che l'applicazione rimanga in attesa per un certo periodo.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Uso di una distribuzione basata su modelli
Per istruzioni dettagliate per la distribuzione di un modello leggere ed eseguire l'esercitazione [Distribuire le risorse di Azure usando le librerie .NET e un modello](./virtual-machines/virtual-machines-windows-csharp-template.md).

In breve, distribuire un modello è molto più facile che effettuare il provisioning manuale delle risorse e il codice seguente illustra come farlo puntando agli URI in cui si trovano il modello e un file dei parametri.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```


 
   

<!---HONumber=AcomDC_0518_2016-->