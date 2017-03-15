---
title: Distribuzione di un&quot;applicazione di Service Fabric | Documentazione Microsoft
description: Come distribuire e rimuovere applicazioni in Service Fabric
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
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuire e rimuovere applicazioni con PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Dopo aver creato il [pacchetto di un tipo di applicazione][10], è possibile distribuirlo in un cluster di Azure Service Fabric. La distribuzione prevede i tre passaggi seguenti:

1. Caricamento del pacchetto dell'applicazione nell'archivio di immagini
2. Registrare il tipo di applicazione
3. Creare l'istanza dell'applicazione

Dopo che un'app è stata distribuita e un'istanza è in esecuzione nel cluster, è possibile eliminare l'istanza dell'app e il suo tipo di applicazione. Per rimuovere completamente un'app dal cluster sono necessari i passaggi seguenti:

1. Rimuovere (o eliminare) l'istanza dell'applicazione in esecuzione
2. Annullare la registrazione del tipo di applicazione se non è più necessario
3. Rimuovere il pacchetto applicazione da Image Store.

Se si usa [Visual Studio per eseguire la distribuzione e il debug delle applicazioni](service-fabric-publish-app-remote-cluster.md) nel cluster di sviluppo locale, tutti i passaggi precedenti vengono gestiti automaticamente tramite uno script di PowerShell.  Questo script è disponibile nella cartella *Scripts* del progetto dell'applicazione. Questo articolo illustra le operazioni eseguite da tali script per consentirne l'esecuzione anche all'esterno di Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Connettersi al cluster
Prima di eseguire qualsiasi comando PowerShell incluso in questo articolo, iniziare sempre usando [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) per connettersi al cluster di Service Fabric. Per connettersi al cluster di sviluppo locale eseguire le operazioni seguenti:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Per alcuni esempi di connessione a un cluster remoto o a un cluster protetto mediante Azure Active Directory, certificati X509 o Windows Active Directory, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Caricare il pacchetto applicazione
Quando si carica il pacchetto dell’applicazione, la si inserisce in un percorso accessibile ai componenti interni di Service Fabric. Se si desidera verificare il pacchetto dell'applicazione in locale, usare il cmdlet [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  Il comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) consente di caricare il pacchetto dell'applicazione nell'archivio di immagini del cluster. Il cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , che fa parte del modulo PowerShell Service Fabric SDK, viene usato per ottenere la stringa di connessione dell'archivio immagini.  Per importare il modulo SDK, eseguire:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Si supponga di compilare e assemblare un'app denominata *MyApplication* in Visual Studio. Per impostazione predefinita, il nome del tipo di applicazione elencato nel file ApplicationManifest.xml è "MyApplicationType".  Il pacchetto dell'applicazione, che contiene il manifesto dell'applicazione necessario, i manifesti dei servizi e i pacchetti di codice, configurazione e dati, si trova in *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Il comando seguente elenca il contenuto del pacchetto dell'applicazione:

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

L'esempio seguente carica il pacchetto nell'archivio di immagini, in una cartella denominata "MyApplicationV1":

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

Se non si specifica il parametro *-ApplicationPackagePathInImageStore*, il pacchetto dell'app viene copiato nella cartella "Debug" dell'archivio di immagini.

Per informazioni agguntive sull'archivio di immagini e ImageStoreConnectionString, vedere [Understand the image store connection string](service-fabric-image-store-connection-string.md) (Comprendere la stringa di connessione dell'archivio di immagini).

## <a name="register-the-application-package"></a>Registrare il pacchetto applicazione
Quando si registra il pacchetto dell'applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso. Il sistema leggerà il pacchetto caricato al passaggio precedente, lo verificherà, ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno.  

Eseguire il cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) per registrare il tipo di applicazione nel cluster e renderlo disponibile per la distribuzione:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" è la cartella nell'archivio di immagini in cui si trova il pacchetto dell'applicazione. Il tipo di applicazione con il nome "MyApplicationType" e la versione "1.0.0" (entrambi si trovano nel manifesto dell'applicazione) è registrato nel cluster.

Il comando [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) restituirà il controllo solo dopo che il sistema avrà registrato correttamente il pacchetto dell'applicazione. La durata della registrazione dipende dalla dimensione e dal contenuto del pacchetto. Se necessario, è possibile usare il parametro **-TimeoutSec** per specificare un intervallo di tempo più esteso per il timeout. Il timeout predefinito è di 60 secondi.  Se si tratta di un pacchetto app di grande dimensione e si verificano timeout, usare il parametro **-Async**.

Il comando [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) elenca tutte le versioni del tipo di applicazione registrate correttamente e il loro stato di registrazione.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>Creazione dell'applicazione
È possibile creare un'istanza di un'applicazione da qualsiasi versione del tipo di applicazione registrata mediante il cmdlet [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). Il nome di ogni applicazione deve iniziare con lo schema *fabric:* ed essere univoco per ogni istanza dell'applicazione. Vengono creati anche i servizi predefiniti specificati nel manifesto dell'applicazione del tipo di applicazione di destinazione.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Per qualsiasi versione di un tipo di applicazione registrato, è possibile creare più istanze dell'applicazione. Ogni istanza viene eseguita in isolamento, con un proprio processo e una propria directory di lavoro.

Per vedere quali app e servizi sono in esecuzione nel cluster, eseguire i cmdlet [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice):

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente per nome usando il cmdlet [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) rimuove automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Annullare la registrazione di un tipo di applicazione
Quando una determinata versione di un tipo di applicazione non è più necessaria, è consigliabile annullare la registrazione del tipo di applicazione usando il cmdlet [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). L'annullamento della registrazione dei tipi di applicazione inutilizzati rilascia lo spazio di archiviazione utilizzato dell'archivio di immagini. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

Eseguire [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) per vedere i tipi di applicazione attualmente registrati nel cluster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Eseguire [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) per annullare la registrazione di un tipo di applicazione specifico:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>Rimuovere il pacchetto di un'applicazione dall'archivio di immagini
Quando il pacchetto di un'applicazione non è più necessario, è possibile eliminarlo dall'archivio di immagini per liberare risorse di sistema.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage chiede un parametro ImageStoreConnectionString
Nell'ambiente Service Fabric SDK dovrebbero già essere configurate le impostazioni predefinite corrette. Tuttavia, se necessario, ImageStoreConnectionString per tutti i comandi deve corrispondere al valore che viene usato dal cluster Service Fabric. È possibile trovare ImageStoreConnectionString nel manifesto del cluster recuperato tramite il comando [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest):

```powershell
PS C:\> Get-ServiceFabricClusterManifest
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

## <a name="next-steps"></a>Passaggi successivi
[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

[Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

[Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

