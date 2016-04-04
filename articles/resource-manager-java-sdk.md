<properties
   pageTitle="Resource Manager SDK per Java | Microsoft Azure"
   description="Panoramica dell'autenticazione ed esempi di utilizzo di Resource Manager Java SDK"
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
   ms.date="03/16/2016"
   ms.author="navale;tomfitz;"/>
   
# Azure Resource Manager SDK per Java
   
Gli SDK di anteprima di Azure Resource Manager (ARM) sono disponibili per più linguaggi e piattaforme. Ogni implementazione linguaggio è disponibile con la gestione pacchetti dell'ecosistema e in GitHub.

Il codice in ogni SDK viene generato dalle [specifiche dell'API basata su REST di Azure](https://github.com/azure/azure-rest-api-specs). Queste specifiche sono open source e si basano sulla specifica Swagger v2. Il codice degli SDK viene generato con un progetto open source denominato [AutoRest](https://github.com/azure/autorest). AutoRest trasforma le specifiche delle API basate su REST in librerie client in più linguaggi. Se ci sono aspetti del codice generato negli SDK che si vuole migliorare, l'intero set di strumenti per creare gli SDK è aperto, completamente disponibile e basato su un formato di specifica API ampiamente diffuso.

Azure Resource Manager Java SDK è ospitato nel [repository Azure Java SDK](https://github.com/azure/azure-sdk-for-java) di GitHub. Si noti che al momento della stesura di questo articolo l'SDK è in anteprima. Sono disponibili i pacchetti seguenti:

* Compute Management: (azure-mgmt-compute)
* DNS Management: (azure-mgmt-dns)
* Network Management: (azure-mgmt-network)
* Resource Management: (azure-mgmt-resources)
* SQL Management: (azure-mgmt-sql)
* Storage Management: (azure-mgmt-storage)
* Traffic Manager Management: (azure-mgmt-traffic-manager)
* Utilities and Helpers: (azure-mgmt-utility)
* WebSites/WebApps Management: (azure-mgmt-websites)

Per un elenco aggiornato, vedere la [pagina Wiki delle funzionalità Java](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) di Azure SDK.

## Prerequisiti
1. Java v1.6+
2. [Maven](https://maven.apache.org/) per lo sviluppo nell'SDK

## Ottenere l'SDK
Per iniziare a usare Azure Java SDK, sono consigliati i file con estensione jar distribuiti di [Maven](https://maven.apache.org/). È possibile aggiungere queste dipendenze a diversi strumenti di gestione delle dipendenze Java (ad esempio, Maven, Gradle, Ivy). Per un elenco delle librerie disponibili in maven, fare clic su questo [collegamento](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22).

In alternativa, è possibile scaricare l'SDK direttamente da Git. Per ottenere il codice sorgente dell'SDK con Git:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

Gli esempi di questa panoramica useranno Maven come origine per i pacchetti SDK.

L'SDK include esempi per alcuni scenari, ad esempio autenticazione, provisioning di una VM e altri. Sono tutti disponibili nel repository di GitHub [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Classi helper

L'SDK include classi helper per diversi pacchetti principali. Le classi helper vengono implementate nel pacchetto auzre-mgmt-utility:

* AuthHelper: classe helper di autenticazione
* ComputeHelper: classe helper di calcolo
* NetworkHelper: classe helper di rete
* ResourceHelper: classe helper delle distribuzioni
* StorageHelper: classe helper di archiviazione

**Informazioni sulla dipendenza Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

Verrà usata la versione 0.9.1 del pacchetto dell'utilità.

## Autenticazione

L'autenticazione per ARM viene gestita da Azure Active Directory (AD). Per connettersi a un'API, prima di tutto è necessario eseguire l'autenticazione con Azure AD per ricevere un token di autenticazione che è possibile passare a ogni richiesta. Per ottenere questo token, prima è necessario creare una cosiddetta applicazione Azure AD e un'entità servizio che verrà usata per l'accesso. Per istruzioni dettagliate, vedere [Creare un'applicazione e un'entità servizio di Azure AD](./resource-group-create-service-principal-portal.md).

Dopo avere creato l'entità servizio, sono necessari:

* ID client (GUID)
* Segreto client (stringa)
* ID tenant (GUID) o nome di dominio (stringa)

Una volta disponibili questi valori, è possibile ottenere un token di Active Directory, valido per un'ora.

Java SDK include la classe helper AuthHelper che crea il token di accesso, una volta forniti l'ID client, il segreto e l'ID tenant. L'esempio seguente nella classe [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java) usa il metodo *getAccessTokenFromServicePrincipalCredentials* di AuthHelper per ottenere il token di accesso:

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Creare una macchina virtuale 
Il pacchetto dell'utilità include una classe helper [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) per creare una macchina virtuale. Alcuni esempi sull'uso delle macchine virtuali sono disponibili nel pacchetto azure-mgmt-samples in [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

Il seguente è un semplice flusso per la creazione di una macchina virtuale. In questo esempio la classe helper creerà l'archivio e la rete durante la creazione della VM:

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Distribuire un modello
La classe [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) è stata creata per facilitare il processo di distribuzione di un modello ARM con Java SDK.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```
## Elencare tutte le macchine virtuali
Non è necessario utilizzare le classi helper (anche se le operazioni potrebbero risultare più semplici), ma utilizzare direttamente le classi del servizio per ogni provider di risorse. In questo esempio saranno elencate alcune delle risorse nella sottoscrizione autenticata; per ogni gruppo di risorse, individuare le macchine virtuali e quindi gli indirizzi IP associati.

```java
// authenticate and get access token
Configuration config = createConfiguration();
ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

// list all resource groups     
ArrayList<ResourceGroupExtended> resourceGroups = resourceManagementClient.getResourceGroupsOperations().list(null).getResourceGroups();
for (ResourceGroupExtended resourcesGroup : resourceGroups) {
   String rgName = resourcesGroup.getName();
   System.out.println("Resource Group: " + rgName);
   
   // list all virtual machines
   ArrayList<VirtualMachine> vms = computeManagementClient.getVirtualMachinesOperations().list(rgName).getVirtualMachines();
   for (VirtualMachine vm : vms) {
      System.out.println("    VM: " + vm.getName());
      // list all nics
      ArrayList<NetworkInterfaceReference> nics = vm.getNetworkProfile().getNetworkInterfaces();
      for (NetworkInterfaceReference nicReference : nics) {
         String[] nicURI = nicReference.getReferenceUri().split("/");
         NetworkInterface nic = networkResourceProviderClient.getNetworkInterfacesOperations().get(rgName, nicURI[nicURI.length - 1]).getNetworkInterface();
         System.out.println("        NIC: " + nic.getName());
         System.out.println("        Is primary: " + nic.isPrimary());
         ArrayList<NetworkInterfaceIpConfiguration> ips = nic.getIpConfigurations();

         // find public ip address
         for (NetworkInterfaceIpConfiguration ipConfiguration : ips) {
               System.out.println("        Private IP address: " + ipConfiguration.getPrivateIpAddress());
               String[] pipID = ipConfiguration.getPublicIpAddress().getId().split("/");
               PublicIpAddress pip = networkResourceProviderClient.getPublicIpAddressesOperations().get(rgName, pipID[pipID.length - 1]).getPublicIpAddress();
               System.out.println("        Public IP address: " + pip.getIpAddress());
         }
      }
}  
```

Altri esempi sono disponibili nei pacchetti degli esempi in [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Altre informazioni
Documentazione su Azure SDK per Java: [documenti su Java](http://azure.github.io/azure-sdk-for-java/)

Se si rilevano bug nell'SDK, registrare il problema in [Issues](https://github.com/Azure/azure-sdk-for-java/issues) o vedere [StackOverflow per Azure Java SDK](http://stackoverflow.com/questions/tagged/azure-java-sdk).

<!---HONumber=AcomDC_0323_2016-->