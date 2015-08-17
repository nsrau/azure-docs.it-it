<properties
   pageTitle="Distribuzione di un'applicazione di Service Fabric"
   description="Come distribuire e rimuovere applicazioni in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/03/2015"
   ms.author="alexwun"/>

# Distribuire un'applicazione

Dopo aver creato il [pacchetto di un tipo di applicazione][10], è possibile distribuirlo in un cluster di Service Fabric. La distribuzione prevede i tre passaggi seguenti:

1. Caricamento del pacchetto applicazione
2. Registrazione del tipo di applicazione
3. Creazione dell'istanza dell'applicazione

>[AZURE.NOTE]Se si usa Visual Studio per eseguire la distribuzione e il debug delle applicazioni nel cluster di sviluppo locale, tutti i passaggi descritti di seguito vengono gestiti automaticamente richiamando gli script di PowerShell presenti nella cartella Scripts del progetto dell'applicazione. Questo articolo illustra le operazioni eseguite da tali script per consentirne l'esecuzione anche all'esterno di Visual Studio.

## Caricare il pacchetto applicazione

Quando si carica il pacchetto applicazione, lo si inserisce in un percorso accessibile ai componenti interni di Service Fabric e il caricamento può essere eseguito tramite PowerShell. Prima di eseguire qualsiasi comando di PowerShell incluso in questo articolo, connettersi sempre al cluster di Service Fabric mediante **Connect-ServiceFabricCluster**.

Si supponga di disporre di una cartella *MyApplicationType* contenente il manifesto dell'applicazione, i manifesti dei servizi e i pacchetti di codice, configurazione e dati necessari. Il comando**Copy-ServiceFabricApplicationPackage** consentirà di caricare il pacchetto. Ad esempio:

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## Registrare il pacchetto applicazione

Quando si registra il pacchetto applicazione, il tipo e la versione dell'applicazione dichiarati nel manifesto di quest'ultima diventano disponibili per l'uso Il sistema leggerà il pacchetto caricato nel passaggio precedente, lo verificherà (mediante un'operazione equivalente all'esecuzione di **Test-ServiceFabricApplicationPackage** in locale), ne elaborerà il contenuto e infine copierà il pacchetto elaborato in un percorso di sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Il comando **Register-ServiceFabricApplicationType** restituirà il controllo solo dopo che il pacchetto applicazione è stato copiato correttamente dal sistema. La durata dell'operazione dipende dal contenuto del pacchetto. Se necessario, è possibile usare il parametro **-TimeoutSec** per specificare un intervallo di tempo più esteso per il timeout. Il timeout predefinito è di 60 secondi.

Il comando **Get-ServiceFabricApplicationType** elencherà tutte le versioni del tipo di applicazione registrate correttamente.

## Creare l'applicazione

È possibile creare un'istanza dell'applicazione con qualsiasi versione del tipo di applicazione registrata correttamente mediante il comando **New-ServiceFabricApplication**. Il nome di ogni applicazione deve iniziare con lo schema *fabric:* ed essere univoco per ogni istanza dell'applicazione. Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, verranno creati anch'essi in questa fase.

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

Il comando **Get-ServiceFabricApplication** elenca tutte le istanze dell'applicazione create correttamente, indicando il relativo stato complessivo.

Il comando **Get-ServiceFabricService** elenca tutte le istanze del servizio create correttamente all'interno di una determinata istanza dell'applicazione. Verranno elencati anche gli eventuali servizi predefiniti.

Per qualsiasi versione di un tipo di applicazione registrato, è possibile creare più istanze dell'applicazione. Ogni istanza verrà eseguita in isolamento, con un proprio processo e una propria directory di lavoro.

## Rimuovere un'applicazione

Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente usando il comando **Remove-ServiceFabricApplication**. In questo modo verranno rimossi automaticamente anche tutti i servizi appartenenti all'applicazione, nonché il relativo stato di servizio. Tale operazione non può essere annullata e lo stato dell'applicazione non può essere recuperato.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando una determinata versione di un tipo di applicazione non è più necessaria, è consigliabile annullarne la registrazione usando il comando **Unregister-ServiceFabricApplicationType**. L'annullamento della registrazione dei tipi inutilizzati determinerà il rilascio dello spazio di archiviazione usato dal contenuto del pacchetto applicazione di quel tipo in Image Store. È possibile annullare la registrazione di un tipo di applicazione solo se non sono state create istanze di applicazioni basate su di esso o non vi sono aggiornamenti di applicazioni in sospeso che vi fanno riferimento.

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

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## Risoluzione dei problemi

### Copy-ServiceFabricApplicationPackage chiede un parametro ImageStoreConnectionString

Nell'ambiente Service Fabric SDK dovrebbero già essere configurate le impostazioni predefinite corrette. Ma se necessario, il parametro ImageStoreConnectionString per tutti i comandi deve corrispondere al valore usato dal cluster di Service Fabric. Tale valore è incluso nel manifesto del cluster recuperato eseguendo il comando **Get-ServiceFabricClusterManifest**:

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

[Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md)

[Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

[Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric](service-fabric-diagnose-monitor-your-service-index.md)

[Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
 

<!---HONumber=August15_HO6-->