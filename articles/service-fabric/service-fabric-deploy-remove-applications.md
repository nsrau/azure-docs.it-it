---
title: Distribuzione dell'applicazione Azure Service Fabric | Microsoft Docs
description: Come distribuire e rimuovere applicazioni in Service Fabric con PowerShell.
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
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: 5a1279ba9626ece30491c8fc899054873f6359e2
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuire e rimuovere applicazioni con PowerShell
> [!div class="op_single_selector"]
> * [Gestione risorse](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfaccia della riga di comando di Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Dopo aver creato il [pacchetto di un tipo di applicazione][10], è possibile distribuirlo in un cluster di Azure Service Fabric. La distribuzione prevede i tre passaggi seguenti:

1. Caricamento del pacchetto dell'applicazione nell'archivio di immagini
2. Registrare il tipo di applicazione
3. Creare l'istanza dell'applicazione

Dopo che un'applicazione è stata distribuita e un'istanza è in esecuzione nel cluster, è possibile eliminare l'istanza dell'applicazione e il tipo di applicazione corrispondente. Per rimuovere completamente un'applicazione dal cluster, sono necessari i passaggi seguenti:

1. Rimuovere (o eliminare) l'istanza dell'applicazione in esecuzione
2. Annullare la registrazione del tipo di applicazione se non è più necessario
3. Rimuovere il pacchetto applicazione da Image Store.

Se si usa Visual Studio per eseguire la distribuzione e il debug delle applicazioni nel cluster di sviluppo locale, tutti i passaggi precedenti vengono gestiti automaticamente tramite uno script di PowerShell.  Questo script è disponibile nella cartella *Scripts* del progetto dell'applicazione. Questo articolo illustra le operazioni eseguite da tali script per consentirne l'esecuzione anche all'esterno di Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Connettersi al cluster
Prima di eseguire qualsiasi comando PowerShell incluso in questo articolo, iniziare sempre usando [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) per connettersi al cluster di Service Fabric. Per connettersi al cluster di sviluppo locale eseguire le operazioni seguenti:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Per alcuni esempi di connessione a un cluster remoto o a un cluster protetto mediante Azure Active Directory, certificati X509 o Windows Active Directory, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Caricare il pacchetto applicazione
Quando si carica il pacchetto dell’applicazione, la si inserisce in un percorso accessibile ai componenti interni di Service Fabric.
Se si desidera verificare il pacchetto dell'applicazione in locale, usare il cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps).

Il comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) consente di caricare il pacchetto dell'applicazione nell'archivio di immagini del cluster.

Si supponga di compilare e assemblare un'applicazione denominata *MyApplication* in Visual Studio 2015. Per impostazione predefinita, il nome del tipo di applicazione elencato nel file ApplicationManifest.xml è "MyApplicationType".  Il pacchetto dell'applicazione, che contiene il manifesto dell'applicazione necessario, i manifesti dei servizi e i pacchetti di codice, configurazione e dati, si trova in *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Il comando seguente elenca il contenuto del pacchetto dell'applicazione:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
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

Se il pacchetto dell'applicazione è di grandi dimensioni e/o dispone di molti file, è possibile [comprimerlo](service-fabric-package-apps.md#compress-a-package). La compressione riduce le dimensioni e il numero di file.
L'effetto collaterale è che la registrazione e l'annullamento della registrazione del tipo di applicazione sono più veloci. Il tempo di caricamento potrebbe attualmente risultare più lento, specialmente se si include il tempo per comprimere il pacchetto. 

Per comprimere un pacchetto, usare il medesimo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). La compressione può avere luogo separatamente dal caricamento, tramite il flag `SkipCopy`, oppure contemporaneamente. L'applicazione della compressione a un pacchetto compresso è no-op.
Per decomprimere un pacchetto compresso, usare il medesimo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) con lo switch `UncompressPackage`.

Il cmdlet seguente comprime il pacchetto senza copiarlo nell'archivio immagini. Ora il pacchetto include i file compressi per i pacchetti `Code` e `Config`. I manifesti dell'applicazione e del servizio non sono compressi in quanto necessari per numerose operazioni interne, come la condivisione dei pacchetti e l'estrazione del nome e della versione del tipo di applicazione per determinate convalide. La compressione dei manifesti renderebbe inefficienti tali operazioni.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Per i pacchetti di applicazione di grandi dimensioni, la compressione richiede tempo. Per ottenere risultati ottimali, usare un'unità SSD rapida. Anche i tempi di compressione e la dimensione del pacchetto compresso variano in base al contenuto del pacchetto.
Ad esempio, ecco le statistiche della compressione per alcuni pacchetti, che mostrano le dimensioni del pacchetto iniziale e di quello compresso, insieme al tempo di compressione.

|Dimensioni iniziali (MB)|Numero di file|Tempo di compressione|Dimensioni pacchetto compresso (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Una volta compresso, un pacchetto può essere caricato in uno o più cluster di Service Fabric in base alle necessità. Il meccanismo di distribuzione è lo stesso per i pacchetti compressi e non. Se il pacchetto è compresso, viene archiviato come tale nell'archivio immagini del cluster e viene decompresso nel nodo prima dell'esecuzione dell'applicazione.


L'esempio seguente carica il pacchetto nell'archivio di immagini, in una cartella denominata "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se non si specifica il parametro *-ApplicationPackagePathInImageStore*, il pacchetto dell'applicazione viene copiato nella cartella "Debug" dell'archivio immagini.

>[!NOTE]
>Se la sessione di PowerShell è connessa a un cluster di Service Fabric, **Copy-ServiceFabricApplicationPackage** rileverà automaticamente la stringa di connessione dell'archivio immagini appropriata. Per le versioni di Service Fabric precedenti alla versione 5.6, l'argomento **-ImageStoreConnectionString** deve essere specificato in modo esplicito.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Il cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , che fa parte del modulo PowerShell Service Fabric SDK, viene usato per ottenere la stringa di connessione dell'archivio immagini.  Per importare il modulo SDK, eseguire:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Per informazioni agguntive sull'archivio di immagini e ImageStoreConnectionString, vedere [Understand the image store connection string](service-fabric-image-store-connection-string.md) (Comprendere la stringa di connessione dell'archivio di immagini).
>
>
>

Il tempo necessario per caricare un pacchetto varia in base a diversi fattori. Tra questi, ad esempio, il numero di file nel pacchetto, le dimensioni del pacchetto stesso e dei file singoli. Anche la velocità di rete tra il computer di origine e il cluster di Service Fabric influisce sul tempo di caricamento. Il timeout predefinito per [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) è di 30 minuti.
In base ai fattori descritti, potrebbe essere necessario aumentare il timeout. Se si sta eseguendo la compressione del pacchetto nella chiamata di copia, è necessario considerare anche il tempo di compressione.



## <a name="register-the-application-package"></a>Registrare il pacchetto applicazione
Quando si registra il pacchetto dell'applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso. Il sistema leggerà il pacchetto caricato al passaggio precedente, lo verificherà, ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno.  

Eseguire il cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) per registrare il tipo di applicazione nel cluster e renderlo disponibile per la distribuzione:

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" è la cartella nell'archivio immagini in cui si trova il pacchetto dell'applicazione. Il tipo di applicazione con il nome "MyApplicationType" e la versione "1.0.0" (entrambi si trovano nel manifesto dell'applicazione) è registrato nel cluster.

Il comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) restituirà il controllo solo dopo che il sistema avrà registrato correttamente il pacchetto dell'applicazione. La durata della registrazione dipende dalla dimensione e dal contenuto del pacchetto. Se necessario, è possibile usare il parametro **-TimeoutSec** per specificare un intervallo di tempo più esteso per il timeout. Il timeout predefinito è di 60 secondi.

Se si dispone di un pacchetto dell'applicazione di grandi dimensioni o se si verificano timeout, usare il parametro **-Async**. Il comando restituisce un valore quando il cluster accetta il comando di registrazione e l'elaborazione continua come da richiesta.
Il comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) elenca tutte le versioni del tipo di applicazione registrate correttamente e il loro stato di registrazione. È possibile usare questo comando per determinare quando viene eseguita la registrazione.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Rimuovere il pacchetto di un'applicazione dall'archivio di immagini
Al termine della registrazione dell'applicazione, è consigliabile rimuovere il pacchetto dell'applicazione.  L'eliminazione dei pacchetti di applicazioni dall'archivio immagini consente di liberare risorse di sistema.  Conservando pacchetti inutilizzati, viene occupato spazio di archiviazione su disco e si verificano problemi di prestazioni delle applicazioni.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Creazione dell'applicazione
È possibile creare un'istanza di un'applicazione da qualsiasi versione del tipo di applicazione registrata mediante il cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps). Il nome di ogni applicazione deve iniziare con lo schema *"fabric:"* e deve essere univoco per ogni istanza dell'applicazione. Vengono creati anche i servizi predefiniti specificati nel manifesto dell'applicazione del tipo di applicazione di destinazione.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Per qualsiasi versione di un tipo di applicazione registrato, è possibile creare più istanze dell'applicazione. Ogni istanza viene eseguita in isolamento, con un proprio processo e una propria directory di lavoro.

Per vedere quali app e servizi sono in esecuzione nel cluster, eseguire i cmdlet [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps):

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
Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente per nome usando il cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps). [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) rimuove automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. 

> [!WARNING]
> Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Annullare la registrazione di un tipo di applicazione
Quando una determinata versione di un tipo di applicazione non è più necessaria, è consigliabile annullare la registrazione del tipo di applicazione usando il cmdlet [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps). L'annullamento della registrazione dei tipi di applicazione inutilizzati rilascia lo spazio di archiviazione usato dall'archivio di immagini rimuovendo i file binari dell'applicazione. L'annullamento della registrazione di un tipo di applicazione non rimuove il pacchetto dell'applicazione. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

Eseguire [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) per vedere i tipi di applicazione attualmente registrati nel cluster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Eseguire [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) per annullare la registrazione di un tipo di applicazione specifico:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

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
Problema: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) raggiunge il timeout per un pacchetto dell'applicazione di grandi dimensioni (nell'ordine di GB).
Soluzione:
- Specificare un timeout maggiore per il comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps), con il parametro `TimeoutSec`. Il timeout è di 30 minuti per impostazione predefinita.
- Controllare la connessione di rete tra il computer di origine e il cluster. Se la connessione è lenta, provare a usare una macchina con una connessione di rete più veloce.
Se il computer client si trova in un'area diversa dal cluster, si consiglia di usare un computer cliente in un'area più vicina o nella stessa area del cluster.
- Controllare se si stiano raggiungendo le limitazioni esterne. Ad esempio, quando l'archivio immagini è configurato per usare l'archiviazione di Azure, il caricamento potrebbe essere limitato.

Problema: Il pacchetto è stato caricato completamente ma si è verificato un timeout di [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Soluzione:
- [Comprimere il pacchetto](service-fabric-package-apps.md#compress-a-package) prima di copiarlo nell'archivio immagini.
La compressione riduce le dimensioni e il numero di file, cosa che a sua volta riduce il traffico e le operazioni di Service Fabric. L'operazione di caricamento potrebbe risultare più lenta (specialmente se si include il tempo di compressione), ma registrazione e relativo annullamento del tipo dell'applicazione saranno più veloci.
- Specificare un timeout maggiore per il comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps), con il parametro `TimeoutSec`.
- Specificare lo switch `Async` per [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Il comando viene completato quando il cluster accetta il comando e la registrazione del tipo di applicazione continua in modo asincrono. Per questo motivo, in questo caso non è necessario specificare un timeout maggiore. Il comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) elenca tutte le versioni del tipo di applicazione registrate correttamente e il loro stato di registrazione. È possibile usare questo comando per determinare quando viene eseguita la registrazione.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Distribuire un pacchetto di applicazione con numerosi file
Problema: Si verifica un timeout di [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) per un pacchetto di applicazione con molti file (nell'ordine di migliaia).
Soluzione:
- [Comprimere il pacchetto](service-fabric-package-apps.md#compress-a-package) prima di copiarlo nell'archivio immagini. La compressione riduce il numero dei file.
- Specificare un timeout maggiore per il comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps), con il parametro `TimeoutSec`.
- Specificare lo switch `Async` per [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Il comando viene completato quando il cluster accetta il comando e la registrazione del tipo di applicazione continua in modo asincrono.
Per questo motivo, in questo caso non è necessario specificare un timeout maggiore. Il comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) elenca tutte le versioni del tipo di applicazione registrate correttamente e il loro stato di registrazione. È possibile usare questo comando per determinare quando viene eseguita la registrazione.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Passaggi successivi
[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

[Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

[Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
