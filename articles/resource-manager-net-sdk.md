<properties
   pageTitle="Resource Manager SDK per .NET | Microsoft Azure"
   description="Panoramica dell'autenticazione ed esempi di utilizzo di Resource Manager SDK per .NET"
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
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK per .NET  
Gli SDK di Azure Resource Manager sono disponibili per più linguaggi e piattaforme. Ogni implementazione del linguaggio è disponibile tramite Gestione pacchetti del relativo ecosistema e in GitHub.

Il codice in ogni SDK viene generato dalle [specifiche dell'API basata su REST di Azure](https://github.com/azure/azure-rest-api-specs). Queste specifiche sono open source e si basano sulla specifica Swagger 2.0. Il codice degli SDK viene generato con un progetto open source denominato [AutoRest](https://github.com/azure/autorest). AutoRest trasforma le specifiche delle API basate su REST in librerie client in più linguaggi. Se si vogliono migliorare alcuni aspetti del codice generato negli SDK, l'intero set di strumenti per creare gli SDK è aperto, disponibile gratuitamente e basato su un formato di specifica API ampiamente diffuso.

[Azure SDK per .NET](https://azure.microsoft.com/downloads/) è un set di pacchetti NuGet che consente di chiamare la maggior parte delle API di Azure Resource Manager. Se l'SDK non fornisce la funzionalità necessaria, è possibile combinarlo facilmente con le normali chiamate all'API REST Resource Manager in background.

Questo articolo non ha lo scopo di descrivere tutti gli aspetti di Azure SDK per .NET, delle API di Azure Resource Manager o di Visual Studio, ma consente di iniziare a lavorare rapidamente.

Tutti i frammenti di codice seguenti provengono da un [progetto di esempio scaricabile](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Installare i pacchetti NuGet

Gli esempi in questo articolo richiedono due pacchetti NuGet, oltre ad Azure SDK per .NET. Per installarli:

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
2. Cercare **Microsoft.IdentityModel.Clients.ActiveDirectory** e installare la versione stabile più recente del pacchetto.
3. Cercare **Microsoft.Azure.Management.ResourceManager** e selezionare **Includi versione preliminare**. Installare l'ultima versione di anteprima, ad esempio 1.1.2-preview.

## Autentica
Azure Active Directory (AD) gestisce l'autenticazione per Resource Manager. Per connettersi a un'API, prima di tutto è necessario autenticarsi con Azure AD per ricevere un token di accesso che è possibile passare a ogni richiesta. Per ottenere questo token, è necessario creare un'applicazione Azure AD e un'entità servizio che verrà usata per l'accesso. Per istruzioni dettagliate, vedere uno di questi articoli:

- [Usare Azure PowerShell per creare un'applicazione Active Directory per l'accesso alle risorse](resource-group-authenticate-service-principal.md)
- [Usare l'interfaccia della riga di comando di Azure per creare un'applicazione Active Directory per l'accesso alle risorse](resource-group-authenticate-service-principal-cli.md)
- [Usare il portale per creare un'applicazione Active Directory che accede alle risorse](resource-group-create-service-principal-portal.md)

Dopo avere creato l'entità servizio, saranno disponibili:

- ID client o ID (GUID) dell'applicazione
- Segreto client o password (stringa)
- ID tenant (GUID) o nome di dominio (stringa)

### Ricevere il token di accesso dal codice
È possibile acquisire il token di autenticazione con le righe di codice seguenti, passando solo l'ID tenant di Azure AD, l'ID client dell'applicazione Azure AD e il segreto client dell'applicazione Azure AD. Salvare il token per diverse richieste perché, per impostazione predefinita, è valido per un'ora.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

Invece di usare l'ID tenant per l'accesso, è possibile usare il dominio di Active Directory, come illustrato nel codice seguente. L'uso di questo approccio richiede la modifica della firma del metodo, includendo il nome di dominio invece dell'ID tenant.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

È possibile ottenere il token di accesso per un'applicazione Azure AD che usa un certificato per l'autenticazione usando il codice seguente:

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Eseguire query su sottoscrizioni di Azure collegate all'applicazione autenticata
È consigliabile eseguire una query sulle sottoscrizioni di Azure associate all'applicazione appena autenticata. L'ID sottoscrizione relativo alla sottoscrizione di destinazione dovrà essere passato a ogni chiamata API eseguita da ora in poi.

L'esempio di codice seguente esegue una query direttamente sulle API di Azure tramite l'API REST, ovvero non usa alcuna delle funzionalità di Azure SDK per .NET.

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

Come si può notare, si ottiene una risposta in formato JSON da Azure. Da questa risposta si estraggono quindi gli ID sottoscrizione per restituire un elenco di ID. Tutte le chiamate successive alle API di Azure Resource Manager in questo articolo usano un singolo ID sottoscrizione di Azure. Di conseguenza, se l'applicazione è associata a più sottoscrizioni, basta scegliere quella giusta e in seguito passarla come parametro.

Da questo momento, ogni chiamata eseguita alle API di Azure userà Azure SDK per .NET. Il codice avrà quindi un aspetto leggermente diverso.

### Eseguire il wrapping del token come oggetto TokenCredentials
Per tutte le chiamate API successive sarà necessario il token ricevuto da Azure AD nel formato di oggetto TokenCredentials. È possibile creare questo tipo di oggetto passando il token non elaborato come parametro al costruttore della classe.

```csharp
var credentials = new TokenCredentials(token);
```

Se è disponibile una versione precedente del pacchetto NuGet di Resource Manager, denominato Microsoft.Azure.Management.Resources, è necessario usare il codice seguente:

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Creare un gruppo di risorse
Ora verrà creato un gruppo di risorse, perché questo è l'elemento più importante in Azure. *ResourceManagementClient* gestisce le risorse generali e i gruppi di risorse. Per i client di gestione più specializzati elencati di seguito, che saranno eventualmente usati, è necessario fornire le credenziali e un ID sottoscrizione per identificare la sottoscrizione che si vuole utilizzare.

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

## Creare risorse manualmente o tramite modelli
Esistono diverse modalità di interazione con le API di Azure Resource Manager, ma le principali sono due:

* Manualmente, chiamando provider di risorse specifici.
* Usando un modello di Azure Resource Manager.

L'uso di un modello di Resource Manager offre i vantaggi seguenti:

* È possibile specificare in modo dichiarativo l'aspetto che dovrà avere il risultato, invece di specificare come ottenerlo.
* Non è necessario gestire manualmente l'esecuzione parallela delle distribuzioni, perché lo farà automaticamente Resource Manager.
* Non è necessario conoscere C# o qualsiasi altro linguaggio per distribuire un modello di Resource Manager, anche se è possibile usare qualsiasi linguaggio per avviare una distribuzione basata su modelli.
* Il linguaggio specifico di dominio usato nei modelli è compilato con JSON. Dovrebbe essere abbastanza facile da comprendere per chiunque abbia già utilizzato JSON.

Nonostante tutti i vantaggi offerti dai modelli, prima di tutto verrà illustrato come chiamare le API manualmente.

### Creare una macchina virtuale, un elemento alla volta
Sono disponibili la sottoscrizione e il gruppo di risorse. Se si vuole distribuire una macchina virtuale, è necessario conoscere gli elementi che ne fanno parte:

* Uno o più account di archiviazione per archiviare i dischi persistenti.
* Uno o più indirizzi IP pubblici, che includono un nome DNS, per rendere le risorse in Azure accessibili da Internet.
* Una o più reti virtuali per la comunicazione interna tra le risorse.
* Una o più schede di interfaccia di rete per consentire alle VM di comunicare.
* Una o più macchine virtuali per eseguire il software.

Alcune di queste risorse, ma non tutte, possono essere create in parallelo. ad esempio:

* Le schede di interfaccia rete dipendono dagli indirizzi IP pubblici e dalle reti virtuali.
* Le macchine virtuali dipendono dalle schede di interfaccia di rete e dagli account di archiviazione.

Evitare di provare a creare un'istanza di qualsiasi risorsa prima di aver creato le dipendenze necessarie. L'[esempio](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) completo illustra come creare le risorse in parallelo in modo efficiente, tenendo comunque traccia di ciò che è stato creato.

#### Creare l'account di archiviazione
È necessario un account di archiviazione per archiviare i dischi rigidi virtuali per la macchina virtuale. Se esiste già un account di archiviazione, è possibile usarlo per diverse macchine virtuali. Occorrerà tuttavia distribuire il carico tra più account di archiviazione per evitare di raggiungere eventuali limiti. Tenere anche presente che il tipo e la posizione dell'account di archiviazione possono limitare la scelta delle dimensioni delle macchine virtuali, perché non tutte le dimensioni sono disponibili in tutte le aree o per tutti i tipi di account di archiviazione.

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

#### Creare l'indirizzo IP pubblico
L'indirizzo IP pubblico rende le risorse di Azure accessibili da Internet. Insieme all'indirizzo IP verrà assegnato anche un nome di dominio completo (FQDN) che può essere usato per eseguire l'accesso più facilmente.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### Creare la rete virtuale
Ogni macchina virtuale creata con le API di Resource Manager deve fare parte di una rete virtuale, anche se nella rete è presente una sola VM. La rete virtuale deve contenere almeno una subnet, ma è possibile usarne più d'una per dividere e proteggere le risorse.

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

#### Creare la scheda di interfaccia di rete
La scheda di interfaccia di rete connette la macchina virtuale alla rete virtuale in cui risiede. Una macchina virtuale può avere diverse schede di interfaccia di rete ed essere quindi associata a più reti virtuali. In questo esempio si presuppone che le macchine virtuali siano collegate a una sola rete virtuale.

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

#### Creare la macchina virtuale
Infine, si creerà la macchina virtuale. La VM dipende, direttamente o indirettamente, da tutte le risorse create in precedenza, quindi è necessario attendere che tutte le risorse siano pronte prima di provare a effettuare il provisioning di una macchina virtuale. Il provisioning di una VM richiede più tempo rispetto alla creazione delle altre risorse, quindi è probabile che l'applicazione rimanga in attesa per un certo intervallo.

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

### Distribuire un modello
Per istruzioni dettagliate sulla distribuzione di un modello, vedere [Distribuire una macchina virtuale di Azure con C# e un modello di Azure Resource Manager](./virtual-machines/virtual-machines-windows-csharp-template.md).

In breve, la distribuzione di un modello è molto più semplice del provisioning manuale delle risorse. Il codice seguente mostra come eseguire questa operazione puntando agli URI in cui sono disponibili il modello e un file dei parametri.

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

<!---HONumber=AcomDC_0629_2016-->