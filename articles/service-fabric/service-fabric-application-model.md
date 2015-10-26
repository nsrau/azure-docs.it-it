<properties
   pageTitle="Modello applicativo di Service Fabric"
   description="Come modellare un'applicazione in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/22/2015"
   ms.author="alexwun"/>

# Modellare un'applicazione in Service Fabric

Questo articolo fornisce una panoramica del modello applicativo di Service Fabric e descrive come definire un'applicazione e un servizio attraverso file manifesto e come includere l'applicazione in un pacchetto in modo che sia pronta per la distribuzione.

## Informazioni sul modello applicativo

Un'applicazione è una raccolta di servizi costituenti che eseguono determinate funzioni. Un servizio esegue una funzione completa e autonoma (avvio ed esecuzione indipendenti da altri servizi) ed è costituito da codice, configurazione e dati. Per ogni servizio, il codice è costituito dai file binari eseguibili, la configurazione è costituita dalle impostazioni del servizio che possono essere caricate in fase di esecuzione e i dati sono costituiti da dati statici arbitrari che devono essere usati dal servizio. Per ogni componente di questo modello applicativo gerarchico è possibile eseguire il controllo delle versioni e l'aggiornamento in modo indipendente.

![][1]


Un tipo di applicazione è una categorizzazione di un'applicazione e consiste in un'aggregazione di tipi di servizi. Un tipo di servizio è una categorizzazione di un servizio e può disporre di impostazioni e configurazioni diverse, ma la funzionalità di base resta la stessa. Le istanze di un servizio sono le diverse varianti di configurazione dello stesso tipo di servizio.

Le classi (o "tipi") di applicazioni e servizi vengono descritte mediante file XML (manifesti delle applicazioni e manifesti dei servizi) che agiscono come modelli in base ai quali possono essere create istanze delle applicazioni. Il codice di istanze di applicazioni diverse verrà eseguito come processo separato anche se ospitato dallo stesso nodo di Service Fabric. Il ciclo di vita di ogni istanza dell'applicazione inoltre può essere gestito, ovvero aggiornato, in modo indipendente. Il diagramma seguente illustra come i tipi di applicazioni siano costituiti da tipi di servizi, che a loro volta sono costituiti da codice, configurazione e pacchetti.

![ApplicationTypes e ServiceTypes di Service Fabric][Image1]

Vengono usati due diversi file manifesto per descrivere le applicazioni e i servizi: il manifesto del servizio e il manifesto dell'applicazione, descritti nei dettagli nelle sezioni seguenti.

Nel cluster possono essere attive una o più istanze di un tipo di servizio. Le istanze o le repliche del servizio con stato ad esempio raggiungono un'elevata affidabilità replicando lo stato tra le repliche contenute in nodi diversi del cluster. In questo modo forniscono in pratica la ridondanza del servizio, che continua a essere disponibile anche in caso di errore di un nodo di un cluster. Un [servizio partizionato](service-fabric-concepts-partitioning.md) suddivide ulteriormente il proprio stato e i modelli di accesso a tale stato tra i nodi del cluster.

Il diagramma seguente illustra la relazione tra applicazioni e istanze di servizi, partizioni e repliche.

![Partizioni e repliche in un servizio][Image2]


## Descrivere un servizio

Il manifesto del servizio definisce in modo dichiarativo il tipo e la versione del servizio e specifica i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità e la metrica di bilanciamento del carico, nonché i file binari e di configurazione del servizio. In altri termini, descrive i pacchetti di codice, configurazione e dati che costituiscono un pacchetto servizio per supportare uno o più tipi di servizi. Questo è un semplice esempio di manifesto del servizio:

~~~
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
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Gli attributi **Version** sono stringhe non strutturate e non analizzate dal sistema. Vengono usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**ServiceTypes** dichiara i tipi di servizi supportati da **CodePackages** nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. Si noti che i tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Se pertanto sono presenti più pacchetti di codice, questi verranno tutti attivati ogni volta che il sistema ricerca uno dei tipi di servizi dichiarati.

**SetupEntryPoint** è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali di Service Fabric (in genere l'account *LocalSystem*) prima di qualsiasi altro punto di ingresso. Poiché l'eseguibile specificato da **EntryPoint** è in genere l'host servizio a esecuzione prolungata, disporre di un punto di ingresso di configurazione separato evita di dover eseguire l'host servizio con privilegi elevati per periodi di tempo estesi. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con **SetupEntryPoint**) se termina o si arresta in modo anomalo.

**DataPackage** dichiara una cartella denominata dall'attributo **Name** che contiene dati statici arbitrari che devono essere usati dal processo in fase di esecuzione.

**ConfigPackage** dichiara una cartella denominata dall'attributo **Name** che contiene un file *Settings.xml*. Questo file contiene sezioni di impostazioni di coppie chiave-valore che possono essere lette dal processo in fase di esecuzione. Se è cambiato solo l'attributo **version** **ConfigPackage**, durante l'aggiornamento il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Questo è un esempio di file *Settings.xml*:

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Foo" />
    <Parameter Name="MySettingB" Value="Bar" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE]Un manifesto del servizio può contenere più pacchetti di codice, configurazione e dati, per ognuno dei quali è possibile effettuare il controllo delle versioni in modo indipendente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## Descrivere un'applicazione


Il manifesto dell'applicazione descrive in modo dichiarativo il tipo e la versione dell'applicazione e specifica i metadati di composizione dei servizi, ad esempio i nomi stabili, lo schema di partizionamento, il numero di istanze/fattore di replica, i criteri di sicurezza/isolamento, i vincoli di posizionamento, gli override di configurazione e i tipi di servizi costituenti. Vengono descritti anche i domini di bilanciamento del carico in cui viene posizionata l'applicazione. Un manifesto dell'applicazione quindi descrive elementi a livello di applicazione e fa riferimento a uno o più manifesti dei servizi per comporre un tipo di applicazione. Questo è un semplice esempio di manifesto dell'applicazione:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Analogamente ai manifesti dei servizi, gli attributi **Version** sono stringhe non strutturate e non analizzate dal sistema. Anche questi attributi vengono usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**ServiceManifestImport** contiene riferimenti a manifesti di servizi che costituiscono questo tipo di applicazione. I manifesti di servizi importati determinano i tipi di servizi validi per questo tipo di applicazione.

**DefaultServices** dichiara le istanze dei servizi create automaticamente ogni volta che viene creata un'istanza di un'applicazione sulla base di questo tipo di applicazione. I servizi predefiniti vengono forniti per comodità e dopo la creazione si comportano come normali servizi sotto ogni aspetto. Vengono aggiornati insieme agli altri servizi nell'istanza dell'applicazione e possono anche essere rimossi.

> [AZURE.NOTE]Un manifesto dell'applicazione può contenere più importazioni di manifesti di servizi e servizi predefiniti. È possibile controllare le versioni di ogni manifesto del servizio in modo indipendente.

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## Inserire un'applicazione in un pacchetto

### Layout del pacchetto

Il manifesto dell'applicazione, i manifesti dei servizi e gli altri file del pacchetto necessari devono essere organizzati in un layout specifico per la distribuzione in un cluster di Service Fabric. I manifesti di esempio di questo articolo dovrebbero essere organizzati nella struttura di directory seguente:

~~~
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
~~~

Le cartelle sono denominate in modo da corrispondere agli attributi **Name** di ogni elemento corrispondente. Se ad esempio il manifesto del servizio contenesse due pacchetti di codice denominati **MyCodeA** e **MyCodeB**, dovrebbero esistere due cartelle con gli stessi nomi contenenti i file binari necessari per ogni pacchetto di codice.

### Creazione di un pacchetto mediante Visual Studio

Se si usa Visual Studio 2015 per creare un'applicazione, è possibile usare il comando Pacchetto per creare automaticamente un pacchetto corrispondente al layout descritto precedentemente.

Per creare un pacchetto, fare clic con il pulsante destro del mouse sul progetto dell'applicazione in Esplora soluzioni e scegliere il comando Pacchetto, come mostrato di seguito:

![][2]

Dopo aver completato la creazione del pacchetto, ne verrà indicata la posizione nella finestra Output. Si noti che il passaggio di creazione del pacchetto viene eseguito automaticamente con la distribuzione o il debug di un'applicazione in Visual Studio.

### Test del pacchetto

La struttura del pacchetto può essere verificata in locale mediante PowerShell usando il comando **Test-ServiceFabricApplicationPackage**, che ricercherà eventuali problemi di analisi dei manifesti e verificherà tutte le istanze. Si noti che questo comando verifica soltanto la correttezza strutturale delle directory e dei file contenuti nel pacchetto. Non verificherà invece il contenuto dei pacchetti di codice e dati oltre a controllare che siano presenti tutti i file necessari:

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Questo errore indica che il file *MySetup.bat* a cui viene fatto riferimento nel manifesto del servizio **SetupEntryPoint** manca nel pacchetto di codice. Dopo aver aggiunto il file mancante, la verifica dell'applicazione ha esito positivo:

~~~
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
~~~

Dopo aver inserito correttamente l'applicazione nel pacchetto e aver superato la verifica, tutto è pronto per la distribuzione.

## Passaggi successivi

[Distribuire e rimuovere applicazioni][10]

<!--Image references-->
[1]: ./media/service-fabric-application-model/application-model.jpg
[2]: ./media/service-fabric-application-model/vs-package-command.png
[Image1]: media/service-fabric-application-model/Service1.jpg
[Image2]: media/service-fabric-application-model/Service2.jpg

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md

<!---HONumber=Oct15_HO3-->