<properties
   pageTitle="Distribuzione di un'applicazione di Service Fabric | Microsoft Azure"
   description="Come distribuire e rimuovere applicazioni in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/10/2016"
   ms.author="ryanwi"/>

# Distribuire e rimuovere applicazioni con PowerShell

Dopo aver creato un [pacchetto di un tipo di applicazione][10], è possibile distribuirlo in un cluster di Azure Service Fabric. La distribuzione prevede i tre passaggi seguenti:

1. Caricare il pacchetto applicazione
2. Registrare il tipo di applicazione
3. Creare l'istanza dell'applicazione

>[AZURE.NOTE] Se si usa Visual Studio per eseguire la distribuzione e il debug delle applicazioni nel cluster di sviluppo locale, tutti i passaggi seguenti vengono gestiti automaticamente tramite uno script di PowerShell presente nella cartella Scripts del progetto dell'applicazione. Questo articolo illustra le operazioni eseguite da tali script per consentirne l'esecuzione anche all'esterno di Visual Studio.

## Caricare il pacchetto applicazione

Quando si carica il pacchetto dell’applicazione, la si inserisce in un percorso accessibile ai componenti interni di Service Fabric. È possibile utilizzare PowerShell per eseguire il caricamento. Prima di eseguire qualsiasi comando di PowerShell incluso in questo articolo, iniziare sempre utilizzando [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) per connettersi al cluster di Service Fabric.

Si supponga di disporre di una cartella denominata *MyApplicationType* contenente il manifesto dell'applicazione necessaria, i manifesti del servizio e i pacchetti di codice/configurazione/dati. Il comando [Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) consente di caricare il pacchetto nell'archivio immagini del cluster. Il cmdlet **Get-ImageStoreConnectionStringFromClusterManifest**, che fa parte del modulo PowerShell Service Fabric SDK, viene usato per ottenere la stringa di connessione dell'archivio immagini. Per importare il modulo SDK, eseguire:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

È possibile copiare un pacchetto dell'applicazione da *C:\\users\\ryanwi\\Documents\\Visual Studio 2015\\Projects\\MyApplication\\myapplication\\pkg\\debug* a *c:\\temp\\MyApplicationType*. Rinominare la directory "debug" in "MyApplicationType". Nell'esempio seguente viene caricato il pacchetto:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
    |   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───it-IT
    |         Microsoft.ServiceFabric.Internal.Strings.resources.dll
    |         System.Fabric.Strings.resources.dll
    |
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## Registrare il pacchetto applicazione

Quando si registra il pacchetto dell’applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso Il sistema leggerà il pacchetto caricato nel passaggio precedente, lo verificherà (mediante un'operazione equivalente all'esecuzione di [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) in locale), ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Il comando [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) restituirà il controllo solo dopo che il sistema ha copiato correttamente il pacchetto dell’applicazione. La durata dell'operazione dipende dal contenuto del pacchetto. Se necessario, è possibile usare il parametro **-TimeoutSec** per specificare un intervallo di tempo più esteso per il timeout. Il timeout predefinito è di 60 secondi.

Il comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) elenca tutte le versioni del tipo di applicazione registrate correttamente.

## Creazione dell'applicazione

È possibile creare un'istanza di un'applicazione utilizzando qualsiasi versione del tipo di applicazione registrata correttamente mediante il comando [New-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx). Il nome di ogni applicazione deve iniziare con lo schema *fabric:* ed essere univoco per ogni istanza dell'applicazione. I servizi predefiniti specificati nel manifesto dell'applicazione del tipo di applicazione di destinazione vengono creati in questa fase.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

Il comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) elenca tutte le istanze dell'applicazione create correttamente, indicando il relativo stato complessivo.

Il comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) elenca tutte le istanze del servizio create correttamente all'interno di una determinata istanza dell'applicazione. Vengono elencati anche gli eventuali servizi predefiniti.

Per qualsiasi versione di un tipo di applicazione registrato, è possibile creare più istanze dell'applicazione. Ogni istanza viene eseguita in isolamento, con un proprio processo e una propria directory di lavoro.

## Rimuovere un'applicazione

Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente usando il comando [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx). Questo comando rimuove automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando una determinata versione di un tipo di applicazione non è più necessaria, è consigliabile annullarne la registrazione usando il comando [Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx). L'annullamento della registrazione dei tipi inutilizzati determina il rilascio dello spazio di archiviazione usato dal contenuto del pacchetto dell'applicazione di quel tipo nell'archivio immagini. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## Risoluzione dei problemi

### Copy-ServiceFabricApplicationPackage chiede un parametro ImageStoreConnectionString

Nell'ambiente Service Fabric SDK dovrebbero già essere configurate le impostazioni predefinite corrette. Tuttavia, se necessario, ImageStoreConnectionString per tutti i comandi deve corrispondere al valore che viene usato dal cluster Service Fabric. È possibile trovarlo nel manifesto del cluster recuperato tramite il comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx):

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## Passaggi successivi

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

[Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

[Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_0817_2016-->