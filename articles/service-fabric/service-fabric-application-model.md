---
title: Modello di applicazione di Service Fabric | Documentazione Microsoft
description: Come modellare e descrivere le applicazioni e servizi in infrastruttura di servizi.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/05/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 62374d57829067b27bb5876e6bbd9f869cff9187
ms.openlocfilehash: 4991992f15b941ab9250705e20ff5f37defc30d0


---
# <a name="model-an-application-in-service-fabric"></a>Modellare un'applicazione in Service Fabric
In questo articolo viene fornita una panoramica del modello di applicazione Azure Service Fabric. Inoltre descrive come definire un'applicazione e un servizio attraverso file manifesto e come includere l'applicazione in un pacchetto in modo che sia pronta per la distribuzione.

## <a name="understand-the-application-model"></a>Informazioni sul modello applicativo
Un'applicazione è una raccolta di servizi costituenti che eseguono determinate funzioni. Un servizio esegue una funzione completa e autonoma (avvio ed esecuzione indipendenti da altri servizi) ed è costituito da codice, configurazione e dati. Per ogni servizio, il codice è costituito dai file binari eseguibili, la configurazione è costituita dalle impostazioni del servizio che possono essere caricate in fase di esecuzione e i dati sono costituiti da dati statici arbitrari che devono essere usati dal servizio. Per ogni componente di questo modello applicativo gerarchico è possibile eseguire il controllo delle versioni e l'aggiornamento in modo indipendente.

![Modello di applicazione di Service Fabric][appmodel-diagram]

Un tipo di applicazione è una categorizzazione di un'applicazione e consiste in un'aggregazione di tipi di servizi. Un tipo di servizio è una categorizzazione di un servizio. La categorizzazione di un servizio può disporre di impostazioni e configurazioni diverse, ma la funzionalità di base resta la stessa. Le istanze di un servizio sono le diverse varianti di configurazione dello stesso tipo di servizio.  

Le classi (o "tipi") di applicazioni e servizi vengono descritte mediante file XML (manifesti delle applicazioni e manifesti dei servizi), che agiscono come modelli in base ai quali possono essere create istanze delle applicazioni dall'archivio immagini del cluster. La definizione dello schema per i file ServiceManifest.xml e ApplicationManifest.xml viene installata con l'SDK e gli strumenti di Service Fabric in *C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

Il codice di istanze di applicazioni diverse verrà eseguito come processo separato anche se ospitato dallo stesso nodo di Service Fabric. Il ciclo di vita di ogni istanza dell'applicazione, inoltre, può essere gestito, ovvero aggiornato, in modo indipendente. Il diagramma seguente illustra come i tipi di applicazioni siano costituiti da tipi di servizi, che a loro volta sono costituiti da codice, configurazione e pacchetti. Per semplificare il diagramma, vengono visualizzati solo i pacchetti codice/configurazione/dati relativi a `ServiceType4` , anche se ogni tipo di servizio può includere alcuni o tutti questi tipi di pacchetti.

![Tipi di applicazioni di Service Fabric e tipi di servizio][cluster-imagestore-apptypes]

Vengono usati due diversi file manifesto per descrivere le applicazioni e i servizi: il manifesto del servizio e il manifesto dell'applicazione. Questi prerequisiti sono analizzati in dettaglio nelle sezioni che seguono.

Nel cluster possono essere attive una o più istanze di un tipo di servizio. Le istanze o le repliche del servizio con stato ad esempio raggiungono un'elevata affidabilità replicando lo stato tra le repliche contenute in nodi diversi del cluster. Essenzialmente, questa replica fornisce ridondanza affinché il servizio sia disponibile anche se un nodo in un cluster ha un malfunzionamento. Un [servizio partizionato](service-fabric-concepts-partitioning.md) suddivide ulteriormente il proprio stato e i modelli di accesso a tale stato tra i nodi del cluster.

Il diagramma seguente illustra la relazione tra applicazioni e istanze di servizi, partizioni e repliche.

![Partizioni e repliche in un servizio][cluster-application-instances]

> [!TIP]
> È possibile visualizzare il layout delle applicazioni in un cluster usando lo strumento Service Fabric Explorer disponibile all'indirizzo http://&lt;yourclusteraddress&gt;:19080/Explorer. Per altre informazioni, vedere [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 

## <a name="describe-a-service"></a>Descrivere un servizio
Il manifesto del servizio definisce in modo dichiarativo il tipo di servizio e la versione. Specifica i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità, le metriche del bilanciamento del carico, i file binari del servizio e i file di configurazione.  In altri termini, descrive i pacchetti di codice, configurazione e dati che costituiscono un pacchetto servizio per supportare uno o più tipi di servizi. Questo è un semplice esempio di manifesto del servizio:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**Version** sono stringhe non strutturate e non analizzate dal sistema. Vengono usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**ServiceTypes** dichiara i tipi di servizi supportati da **CodePackages** nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Si noti che i tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Se pertanto sono presenti più pacchetti di codice, questi verranno tutti attivati ogni volta che il sistema ricerca uno dei tipi di servizi dichiarati.

**SetupEntryPoint** è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali di Service Fabric (in genere l'account *LocalSystem* ) prima di qualsiasi altro punto di ingresso. L'eseguibile specificato da **EntryPoint** è in genere l'host del servizio a esecuzione prolungata. La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con **SetupEntryPoint**) se termina o si arresta in modo anomalo.

**EnvironmentVariables** offre un elenco di variabili di ambiente impostate per questo pacchetto di codice. Tali variabili possono essere sostituite in `ApplicationManifest.xml` per specificare valori diversi per diverse istanze del servizio. 

**DataPackage** dichiara una cartella, denominata dall'attributo **Name**, che contiene i dati statici arbitrari che devono essere usati dal processo in fase di esecuzione.

**ConfigPackage** dichiara una cartella, denominata dall'attributo **Name**, che contiene un file *Settings.xml*. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se durante un aggiornamento è cambiato solo l'attributo **version** **ConfigPackage**, il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Questo è un esempio di file *Settings.xml*:

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> Un manifesto del servizio può contenere più pacchetti di codice, configurazione e dati. Ognuna di queste può essere creata in modo indipendente.
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Descrivere un'applicazione
Il manifesto dell'applicazione descrive in modo dichiarativo il tipo di applicazione e la versione. Specifica i metadati di composizione dei servizi, ad esempio i nomi stabili, lo schema di partizionamento, il numero di istanze/fattore di replica, i criteri di sicurezza/isolamento, i vincoli di posizionamento, gli override di configurazione e i tipi di servizi costituenti. Vengono descritti anche i domini di bilanciamento del carico in cui viene posizionata l'applicazione.

Un manifesto dell'applicazione quindi descrive elementi a livello di applicazione e fa riferimento a uno o più manifesti dei servizi per comporre un tipo di applicazione. Questo è un semplice esempio di manifesto dell'applicazione:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

Analogamente ai manifesti dei servizi, gli attributi **Version** sono stringhe non strutturate e non analizzate dal sistema. Anche questi attributi vengono usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**ServiceManifestImport** contiene riferimenti a manifesti di servizi che costituiscono questo tipo di applicazione. I manifesti di servizi importati determinano i tipi di servizi validi per questo tipo di applicazione. In ServiceManifestImport è possibile sostituire i valori di configurazione nel file Settings.xml e le variabili di ambiente nel file servicemanifest.xml. 


**DefaultServices** dichiara le istanze dei servizi create automaticamente ogni volta che viene creata un'istanza di un'applicazione sulla base di questo tipo di applicazione. I servizi predefiniti vengono forniti per comodità e dopo la creazione si comportano come normali servizi sotto ogni aspetto. Vengono aggiornati insieme agli altri servizi nell'istanza dell'applicazione e possono anche essere rimossi.

> [!NOTE]
> Un manifesto dell'applicazione può contenere più importazioni di manifesti di servizi e servizi predefiniti. È possibile controllare le versioni di ogni manifesto del servizio in modo indipendente.
> 
> 

Per informazioni su come mantenere applicazioni diverse e parametri di servizio per ambienti singoli, vedere [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Inserire un'applicazione in un pacchetto
### <a name="package-layout"></a>Layout del pacchetto
Il manifesto dell'applicazione, i manifesti dei servizi e gli altri file del pacchetto necessari devono essere organizzati in un layout specifico per la distribuzione in un cluster di Service Fabric. I manifesti di esempio di questo articolo dovrebbero essere organizzati nella struttura di directory seguente:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Le cartelle sono denominate in modo da corrispondere agli attributi **Name** di ogni elemento corrispondente. Se ad esempio il manifesto del servizio contenesse due pacchetti di codice denominati **MyCodeA** e **MyCodeB**, dovrebbero esistere due cartelle con gli stessi nomi contenenti i file binari necessari per ogni pacchetto di codice.

### <a name="use-setupentrypoint"></a>Usare SetupEntryPoint
Gli scenari tipici per l'utilizzo di **SetupEntryPoint** sono quando è necessario eseguire un file eseguibile prima dell'avvio del servizio o quando è necessario eseguire un'operazione con privilegi elevati. Ad esempio:

* Impostazione e inizializzazione di variabili di ambiente necessari per il file eseguibile del servizio. Questo non è limitato solo agli eseguibili scritti tramite i modelli di programmazione di Service Fabric. Ad esempio, npm.exe richiede alcune variabili di ambiente configurate per la distribuzione di un'applicazione node.js.
* Impostazione del controllo di accesso mediante l'installazione di certificati di sicurezza.

Per altre informazioni su come configurare **SetupEntryPoint**, vedere [Configurare i criteri per il punto di ingresso dell'installazione del servizio](service-fabric-application-runas-security.md)  

### <a name="configure"></a>Configurare 
### <a name="build-a-package-by-using-visual-studio"></a>Creare un pacchetto mediante Visual Studio
Se si usa Visual Studio 2015 per creare un'applicazione, è possibile usare il comando Pacchetto per creare automaticamente un pacchetto corrispondente al layout descritto precedentemente.

Per creare un pacchetto, fare clic con il pulsante destro sul progetto dell'applicazione in Esplora soluzioni e scegliere il comando Pacchetto, come mostrato di seguito:

![Inserire un'applicazione in un pacchetto con Visual Studio][vs-package-command]

Dopo aver completato la creazione del pacchetto, ne verrà indicata la posizione nella finestra **Output** . Si noti che il passaggio di creazione del pacchetto viene eseguito automaticamente con la distribuzione o il debug di un'applicazione in Visual Studio.

### <a name="build-a-package-by-command-line"></a>Creare un pacchetto dalla riga di comando
È anche possibile creare un pacchetto dell'applicazione a livello di codice usando `msbuild.exe`. Si tratta dell'operazione eseguita da Visual Studio, pertanto l'output è lo stesso.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

### <a name="test-the-package"></a>Testare il pacchetto
È possibile verificare la struttura del pacchetto in modalità locale tramite PowerShell usando il comando **Test-ServiceFabricApplicationPackage** . Questo comando consente di verificare i problemi di analisi dei manifesti e tutti i riferimenti. Si noti che questo comando si limita a verificare la correttezza strutturale delle directory e i file nel pacchetto. Non verificherà il codice o i contenuti del pacchetto di dati oltre a controllare che tutti i file necessari siano presenti.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Questo errore indica che il file *MySetup.bat* a cui viene fatto riferimento nel manifesto del servizio **SetupEntryPoint** manca nel pacchetto di codice. Dopo aver aggiunto il file mancante, la verifica dell'applicazione ha esito positivo:

```
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

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Dopo aver inserito correttamente l'applicazione nel pacchetto e aver superato la verifica, tutto è pronto per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire e rimuovere applicazioni con PowerShell][10]: descrive come usare PowerShell per gestire le istanze dell'applicazione

[Gestire i parametri dell'applicazione per più ambienti][11]: descrive come configurare parametri e variabili di ambiente per istanze di applicazione diverse.

[Configurare i criteri di sicurezza per l'applicazione][12]: descrive come eseguire i servizi nell'ambito dei criteri di sicurezza per limitare l'accesso.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md



<!--HONumber=Jan17_HO3-->


