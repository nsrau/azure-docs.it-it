---
title: Distribuzione dell'applicazione Azure Service Fabric | Microsoft Docs
description: Usare le API del client Fabric per distribuire e rimuovere le applicazioni in Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/07/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ebe8b9f0cace419125bde84a9ff2a912af061156
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Distribuire e rimuovere applicazioni con il client Fabric
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [API client Fabric](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Dopo aver creato il [pacchetto di un tipo di applicazione][10], è possibile distribuirlo in un cluster di Azure Service Fabric. La distribuzione prevede i tre passaggi seguenti:

1. Caricamento del pacchetto dell'applicazione nell'archivio di immagini
2. Registrare il tipo di applicazione
3. Creare l'istanza dell'applicazione

Dopo che un'applicazione è stata distribuita e un'istanza è in esecuzione nel cluster, è possibile eliminare l'istanza dell'applicazione e il tipo di applicazione corrispondente. Per rimuovere completamente un'applicazione dal cluster, sono necessari i passaggi seguenti:

1. Rimuovere (o eliminare) l'istanza dell'applicazione in esecuzione
2. Annullare la registrazione del tipo di applicazione se non è più necessario
3. Rimuovere il pacchetto applicazione da Image Store.

Se si usa [Visual Studio per eseguire la distribuzione e il debug delle applicazioni](service-fabric-publish-app-remote-cluster.md) nel cluster di sviluppo locale, tutti i passaggi precedenti vengono gestiti automaticamente tramite uno script di PowerShell.  Questo script è disponibile nella cartella *Scripts* del progetto dell'applicazione. Questo articolo illustra le operazioni eseguite da tali script per consentirne l'esecuzione anche all'esterno di Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Connettersi al cluster
Connettersi al cluster tramite la creazione di un'istanza [client Fabric](/dotnet/api/system.fabric.fabricclient) prima di eseguire uno degli esempi di codice forniti in questo articolo. Per esempi di connessione a un cluster di sviluppo locale, un cluster remoto o un cluster protetto usando Azure Active Directory, certificati X509 o Windows Active Directory, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Per connettersi al cluster di sviluppo locale eseguire le operazioni seguenti:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Caricare il pacchetto applicazione
Si supponga di compilare e assemblare un'applicazione denominata *MyApplication* in Visual Studio. Per impostazione predefinita, il nome del tipo di applicazione elencato nel file ApplicationManifest.xml è "MyApplicationType".  Il pacchetto dell'applicazione, che contiene il manifesto dell'applicazione necessario, i manifesti dei servizi e i pacchetti di codice, configurazione e dati, si trova in *C:\Users\&lt;username&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug*.

Quando si carica il pacchetto dell'applicazione, lo si inserisce in un percorso accessibile ai componenti interni di Service Fabric. Service Fabric verifica il pacchetto dell'applicazione durante la registrazione. Tuttavia, se si desidera verificare il pacchetto dell'applicazione in locale, ad esempio prima di caricarlo, usare il cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps).

L'API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) carica il pacchetto dell'applicazione nell'archivio immagini del cluster. 

Se il pacchetto dell'applicazione è di grandi dimensioni e/o è costituito da numerosi file, è possibile [comprimerlo](service-fabric-package-apps.md#compress-a-package) e copiarlo nell'archivio immagini con PowerShell. La compressione riduce le dimensioni e il numero di file.

Per informazioni agguntive sull'archivio di immagini e ImageStoreConnectionString, vedere [Understand the image store connection string](service-fabric-image-store-connection-string.md) (Comprendere la stringa di connessione dell'archivio di immagini).

## <a name="register-the-application-package"></a>Registrare il pacchetto applicazione
Quando si registra il pacchetto dell'applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso. Il sistema leggerà il pacchetto caricato al passaggio precedente, lo verificherà, ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno.  

L'API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registra il tipo di applicazione nel cluster e la rende disponibile per la distribuzione.

L'API [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) fornisce informazioni su tutti i tipi di applicazioni registrati correttamente. È possibile usare questa API per determinare quando viene eseguita la registrazione.

## <a name="create-an-application-instance"></a>Creare un'istanza dell'applicazione
È possibile creare un'istanza di un'applicazione da qualsiasi tipo di applicazione registrato correttamente usando l'API [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync). Il nome di ogni applicazione deve iniziare con lo schema *"fabric:"* e deve essere univoco per ogni istanza dell'applicazione (all'interno di un cluster). Vengono creati anche i servizi predefiniti specificati nel manifesto dell'applicazione del tipo di applicazione di destinazione.

Per qualsiasi versione di un tipo di applicazione registrato, è possibile creare più istanze dell'applicazione. Ogni istanza dell'applicazione viene eseguita in isolamento, con una propria directory di lavoro e un proprio set di processi.

Per vedere quali applicazioni e servizi denominati sono in esecuzione nel cluster, eseguire le API [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) e [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync).

## <a name="create-a-service-instance"></a>Creare un'istanza del servizio
È possibile creare un'istanza di un servizio da un tipo di servizio usando l'API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync).  Se il servizio è dichiarato come servizio predefinito nel manifesto dell'applicazione, viene creata un'istanza del servizio durante la creazione dell'istanza dell'applicazione.  La chiamata all'API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) per un servizio di cui è già stata creata un'istanza restituirà un'eccezione di tipo FabricException contenente un codice di errore con un valore di FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Rimuovere un'istanza del servizio
Quando un'istanza del servizio non è più necessaria, è possibile rimuoverla dall'istanza dell'applicazione in esecuzione tramite la chiamata all'API [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync).  

> [!WARNING]
> Tale operazione non può essere annullata e lo stato del servizio non può essere recuperato.

## <a name="remove-an-application-instance"></a>Rimuovere un'istanza dell'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente in base al nome tramite la chiamata all'API [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync). Il metodo [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) rimuove automaticamente anche tutti i servizi che appartengono all'applicazione, rimuovendo così in modo permanente lo stato di tutti i servizi.

> [!WARNING]
> Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

## <a name="unregister-an-application-type"></a>Annullare la registrazione di un tipo di applicazione
Quando una determinata versione di un tipo di applicazione non è più necessaria, è consigliabile annullare la registrazione di quella specifica versione del tipo di applicazione usando l'API [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync). L'annullamento della registrazione delle versioni inutilizzate dei tipi di applicazioni rilascia lo spazio di archiviazione usato dall'archivio immagini. È possibile annullare la registrazione di una versione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su quella versione del tipo di applicazione e non vi sono aggiornamenti di applicazioni in sospeso che fanno riferimento a quella versione del tipo di applicazione.

## <a name="remove-an-application-package-from-the-image-store"></a>Rimuovere il pacchetto di un'applicazione dall'archivio di immagini
Quando il pacchetto di un'applicazione non è più necessario, è possibile eliminarlo dall'archivio immagini tramite l'API [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage), liberando così risorse di sistema.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage chiede un parametro ImageStoreConnectionString
Nell'ambiente Service Fabric SDK dovrebbero già essere configurate le impostazioni predefinite corrette. Tuttavia, se necessario, ImageStoreConnectionString per tutti i comandi deve corrispondere al valore che viene usato dal cluster Service Fabric. È possibile trovare ImageStoreConnectionString nel manifesto del cluster, recuperato tramite i comandi [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Il cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , che fa parte del modulo PowerShell Service Fabric SDK, viene usato per ottenere la stringa di connessione dell'archivio immagini.  Per importare il modulo SDK, eseguire:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString è disponibile nel manifesto del cluster:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Per informazioni agguntive sull'archivio di immagini e ImageStoreConnectionString, vedere [Understand the image store connection string](service-fabric-image-store-connection-string.md) (Comprendere la stringa di connessione dell'archivio di immagini).

### <a name="deploy-large-application-package"></a>Distribuire un pacchetto dell'applicazione di grandi dimensioni
Problema: l'API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) raggiunge il timeout per un pacchetto dell'applicazione di grandi dimensioni (nell'ordine di GB).
Soluzione:
- Specificare un timeout maggiore per il metodo [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) con il parametro `timeout`. Il timeout è di 30 minuti per impostazione predefinita.
- Controllare la connessione di rete tra il computer di origine e il cluster. Se la connessione è lenta, provare a usare una macchina con una connessione di rete più veloce.
Se il computer client si trova in un'area diversa dal cluster, si consiglia di usare un computer cliente in un'area più vicina o nella stessa area del cluster.
- Controllare se si stiano raggiungendo le limitazioni esterne. Ad esempio, quando l'archivio immagini è configurato per usare l'archiviazione di Azure, il caricamento potrebbe essere limitato.

Problema: il pacchetto è stato caricato completamente, ma si è verificato il timeout dell'API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync).
Soluzione:
- [Comprimere il pacchetto](service-fabric-package-apps.md#compress-a-package) prima di copiarlo nell'archivio immagini.
La compressione riduce le dimensioni e il numero di file, cosa che a sua volta riduce il traffico e le operazioni di Service Fabric. L'operazione di caricamento potrebbe risultare più lenta (specialmente se si include il tempo di compressione), ma registrazione e relativo annullamento del tipo dell'applicazione saranno più veloci.
- Specificare un timeout maggiore per l'API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) con il parametro `timeout`.

### <a name="deploy-application-package-with-many-files"></a>Distribuire un pacchetto di applicazione con numerosi file
Problema: si verifica un timeout di [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) per un pacchetto di applicazione con molti file (nell'ordine di migliaia).
Soluzione:
- [Comprimere il pacchetto](service-fabric-package-apps.md#compress-a-package) prima di copiarlo nell'archivio immagini. La compressione riduce il numero dei file.
- Specificare un timeout maggiore per il metodo [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) con il parametro `timeout`.

## <a name="code-example"></a>Esempio di codice
L'esempio seguente copia un pacchetto dell'applicazione nell'archivio immagini, effettua il provisioning del tipo di applicazione, crea un'istanza dell'applicazione, crea un'istanza del servizio, rimuove l'istanza dell'applicazione, annulla il provisioning del tipo di applicazione ed elimina il pacchetto dell'applicazione dall'archivio immagini.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Passaggi successivi
[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

[Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

[Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

