<properties
   pageTitle="Distribuire un eseguibile esistente in Service Fabric di Azure | Microsoft Azure"
   description="Procedura dettagliata su come creare il pacchetto di un'applicazione esistente come eseguibile guest, in modo da consentirne la distribuzione in un cluster di Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/20/2016"
   ms.author="bscholl;mikhegn"/>

# Distribuire un eseguibile guest in Service Fabric

In Azure Service Fabric è possibile eseguire qualsiasi tipo di applicazione, ad esempio Node.js, Java o applicazioni native. La terminologia di Service Fabric fa riferimento a questi tipi di applicazioni come eseguibili guest. Gli eseguibili guest vengono considerati da Service Fabric come servizi senza stato. Di conseguenza verranno inseriti nei nodi di un cluster in base a disponibilità e altre metriche. Questo articolo descrive come creare un pacchetto e distribuire un eseguibile guest in un cluster di Service Fabric, usando Visual Studio o un'utilità della riga di comando.

## Vantaggi dell'esecuzione di un'eseguibile guest in Service Fabric

L'esecuzione di un eseguibile guest in un cluster di Service Fabric presenta numerosi vantaggi:

- Disponibilità elevata. Le applicazioni in esecuzione in Service Fabric sono caratterizzate da disponibilità elevata per impostazione predefinita. Service Fabric garantisce che un'istanza di un'applicazione sia sempre in esecuzione.
- Monitoraggio dell’integrità. Il monitoraggio predefinito di Service Fabric rileva se un'applicazione è in esecuzione e fornisce informazioni di diagnostica in caso di errore.
- Gestione del ciclo di vita delle applicazioni. Oltre a garantire aggiornamenti senza tempi di inattività, Service Fabric consente inoltre di eseguire il rollback alla versione precedente se si verifica un problema durante l'aggiornamento.
- Densità. È possibile eseguire più applicazioni in un cluster, eliminando la necessità che ogni applicazione venga eseguita nel proprio hardware.

Questo articolo illustra i passaggi di base per creare il pacchetto di un eseguibile guest e distribuirlo in Service Fabric.

## Breve panoramica dei file manifesto dell'applicazione e del servizio

Come parte della distribuzione di un eseguibile guest, è utile comprendere il modello di creazione del pacchetto e di distribuzione di Service Fabric. Il modello di distribuzione del pacchetto di Service Fabric si basa principalmente su due file XML: l'applicazione e i manifesti del servizio. La definizione dello schema per i file ApplicationManifest.xml e ServiceManifest.xml viene installata con l'SDK e gli strumenti di Service Fabric in *C:\\Programmi\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

* **Manifesto dell'applicazione**

  Il manifesto dell'applicazione viene usato per descrivere l'applicazione ed elenca i servizi che la compongono, nonché altri parametri, ad esempio il numero di istanze, che consentono di definire la modalità di distribuzione dei servizi.

  Nell’ambito di Service Fabric un'applicazione è "l'unità aggiornabile". Un'applicazione può essere aggiornata come una singola unità in cui i potenziali errori (e i potenziali ripristini) vengono gestiti nella piattaforma in modo da garantire che il processo di aggiornamento venga completato o, in caso contrario, che non lasci l'applicazione in uno stato sconosciuto/instabile.

* **Manifesto del servizio**

  Il manifesto del servizio descrive i componenti di un servizio. Include dati, come ad esempio nome e tipo di servizio (informazioni che Service Fabric usa per gestire il servizio), il relativo codice, componenti di dati e di configurazione più alcuni parametri aggiuntivi usati per configurare il servizio una volta distribuito.

  Non verranno esaminati tutti i diversi parametri disponibili nel manifesto del servizio, ma verrà esaminato il subset necessario per l'esecuzione di un eseguibile guest in Service Fabric.

## Struttura del file del pacchetto dell'applicazione
Per distribuire un'applicazione nell'infrastruttura di servizi, l'applicazione deve seguire una struttura di directory predefinita. Di seguito è riportato un esempio di tale struttura.

```
|-- ApplicationPackage
    |-- code
        |-- existingapp.exe
    |-- config
        |-- Settings.xml
    |-- data
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

La radice contiene il file ApplicationManifest.xml che definisce l'applicazione. Una sottodirectory per ogni servizio incluso nell'applicazione viene usata per contenere tutti gli elementi necessari per il servizio: il file ServiceManifest.xml e in genere le directory seguenti:

- *Code*. Contiene il codice del servizio.
- *Config*. Contiene un file settings.xml (e altri file, se necessario) a cui il servizio può accedere in fase di esecuzione per recuperare specifiche impostazioni di configurazione.
- *Data*. Un'altra directory in cui archiviare dati locali aggiuntivi che potrebbero essere necessari al servizio. Nota: i dati devono essere usati per archiviare solo i dati temporanei, Service Fabric non copia/replica le modifiche alla directory dei dati se il servizio deve essere trasferito, ad esempio, durante il failover.

Nota: non occorre creare le directory `config` e `data` se non sono necessarie.

## Processo di creazione di un pacchetto di un'applicazione esistente

Quando si crea il pacchetto di un file eseguibile guest, è possibile scegliere di usare un modello di progetto di Visual Studio o di creare manualmente il pacchetto dell'applicazione. Se si usa Visual Studio, la struttura del pacchetto dell'applicazione e i file manifesto vengono creati automaticamente dalla Creazione guidata nuovo progetto. Per una guida dettagliata su come creare il pacchetto di un eseguibile guest con Visual Studio, vedere di seguito.

Il processo per la creazione manuale del pacchetto di un eseguibile guest si basa sulla procedura seguente:

1. Creare la struttura di directory del pacchetto.
2. Aggiungere i file di codice e di configurazione dell'applicazione.
3. Modificare il file manifesto del servizio.
4. Modificare il file manifesto dell’applicazione.

>[AZURE.NOTE] Viene fornito uno strumento di creazione di pacchetti che consente di creare automaticamente ApplicationPackage. Lo strumento è attualmente in anteprima. È possibile scaricarlo [qui](http://aka.ms/servicefabricpacktool).

### Creare la struttura di directory del pacchetto
È possibile iniziare creando la struttura di directory come descritto in precedenza.

### Aggiungere i file di codice e di configurazione dell'applicazione
Dopo aver creato la struttura di directory, è possibile aggiungere i file di configurazione e di codice dell'applicazione nelle directory del codice e di configurazione. È inoltre possibile creare directory aggiuntive o sottodirectory nelle directory del codice e di configurazione.

Service Fabric esegue xcopy del contenuto della directory radice dell'applicazione in modo che non esista alcuna struttura predefinita da usare oltre alla creazione delle due directory principali Codice e Impostazioni (ma se si desidera, è possibile scegliere nomi diversi, come illustrato in dettaglio nella sezione successiva).

>[AZURE.NOTE] Assicurarsi di includere tutti i file o le dipendenze necessarie all'applicazione. L'infrastruttura di servizi copia il contenuto del pacchetto dell'applicazione in tutti i nodi del cluster in cui i servizi dell'applicazione vengono distribuiti. Il pacchetto deve contenere tutto il codice necessario per eseguire l'applicazione. È consigliabile non presupporre che le dipendenze siano già installate.

### Modificare il file manifesto del servizio
Il passaggio successivo consiste nel modificare il file manifesto del servizio per includere le informazioni seguenti:

- Il nome del tipo di servizio. Si tratta di un ID usato da Service Fabric per identificare un servizio.
- Il comando da usare per avviare l'applicazione (ExeHost).
- Qualsiasi script da eseguire per installare/configurare l'applicazione (SetupEntrypoint).

Di seguito è riportato un esempio di un file `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Analizziamo ora le diverse parti del file che è necessario aggiornare:

### ServiceTypes:

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- È possibile scegliere qualsiasi nome per `ServiceTypeName`. Il valore viene usato nel file `ApplicationManifest.xml` per identificare il servizio.
- È necessario specificare `UseImplicitHost="true"`. Questo attributo indica a Service Fabric che il servizio si basa su un'applicazione autonoma, in modo che debba soltanto avviarla come processo e monitorarne l'integrità.

### CodePackage
L’elemento CodePackage specifica il percorso (e la versione) del codice del servizio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L'elemento `Name` consente di specificare il nome della directory nel pacchetto dell'applicazione che contiene il codice del servizio. `CodePackage` include anche l'attributo `version`, che consente di specificare la versione del codice e potenzialmente per aggiornare il codice del servizio mediante l'infrastruttura ALM di Service Fabric.
### SetupEntryPoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L'elemento SetupEntrypoint consente di specificare un file eseguibile o un file batch da eseguire prima dell'avvio del codice del servizio. È un elemento facoltativo perciò non è necessario includerlo se non esiste alcuna inizializzazione/installazione richiesta. L'elemento SetupEntryPoint viene eseguito ogni volta che il servizio viene riavviato.

Esiste solo un SetupEntrypoint, quindi gli script di installazione/configurazione devono essere raggruppati in un singoli file batch se l'installazione/configurazione dell'applicazione richiede più script. Analogamente all'elemento SetupEntryPoint, SetupEntrypoint può eseguire qualsiasi tipo di file, ad esempio file eseguibili, file batch e cmdlet di PowerShell. Nell'esempio precedente l'elemento SetupEntrypoint è basato su un file batch LaunchConfig.cmd, che si trova nella sottodirectory `scripts` della directory code, presupponendo che l'elemento WorkingFolder sia impostato su code.

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

L'elemento `Entrypoint` nel manifesto del servizio consente di specificare la modalità di avvio del servizio. L'elemento `ExeHost` specifica il file eseguibile e i relativi argomenti da usare per avviare il servizio.

- `Program` specifica il nome del file eseguibile da eseguire per avviare il servizio.
- `Arguments` specifica gli argomenti da passare al file eseguibile. Può essere un elenco di parametri con argomenti.
- `WorkingFolder` specifica la directory di lavoro per il processo che sta per essere avviato. È possibile specificare due valori:
	- `CodeBase` specifica che la directory di lavoro dovrà essere impostata sulla directory Code nel pacchetto dell'applicazione (directory `Code` nella struttura riportata di seguito).
	- `CodePackage` specifica che la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`MyServicePkg`).
- `WorkingFolder` è utile per impostare la directory di lavoro corretta, in modo che i percorsi relativi possano essere usati dagli script di applicazione o da quelli di inizializzazione.

### Endpoint

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
L'elemento `Endpoint` specifica gli endpoint sui quali l'applicazione può restare in ascolto. In questo esempio l'applicazione Node.js resta in ascolto sulla porta 3000.

## Modificare il file manifesto dell’applicazione

Dopo aver configurato il file `Servicemanifest.xml`, sarà necessario apportare alcune modifiche al file `ApplicationManifest.xml` per assicurarsi che vengano usati il tipo e il nome corretti per il servizio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

Nell'elemento `ServiceManifestImport` è possibile specificare uno o più servizi da includere nell'app. Per fare riferimento ai servizi viene usato l'elemento `ServiceManifestName`, che specifica il nome della directory in cui è presente il file `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configurare la registrazione
Per gli eseguibili guest è molto utile poter visualizzare i registri di console per scoprire se gli script di configurazione e applicazione mostrano eventuali errori. Il reindirizzamento della console può essere configurato nel file `ServiceManifest.xml` tramite l'elemento `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* L'elemento `ConsoleRedirection` può essere usato per reindirizzare l'output della console, di tipo stdout o stderr, a una directory di lavoro, in modo da verificare che non siano presenti errori durante l'installazione o l'esecuzione dell'applicazione nel cluster di Service Fabric.

	* L'elemento `FileRetentionCount` determina il numero di file salvati nella directory di lavoro. Un valore pari a 5, ad esempio, indica che i file di log per le 5 esecuzioni precedenti vengono archiviati nella directory di lavoro.
	* L'elemento `FileMaxSizeInKb` specifica le dimensioni massime dei file di log.

I file di log vengono salvati in una directory di lavoro del servizio, per determinare dove si trovano i file, è necessario utilizzare Service Fabric Explorer per determinare in quale nodo è in esecuzione il servizio e quale directory di lavoro è attualmente utilizzata. Più avanti in questo articolo verrà illustrato questo processo.

### Distribuzione
L'ultimo passaggio consiste nel distribuire l'applicazione. Lo script di PowerShell seguente illustra come distribuire l'applicazione nel cluster di sviluppo locale e avviare un nuovo servizio Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un servizio Service Fabric può essere distribuito in varie "configurazioni", ad esempio può essere distribuito come istanza singola o come istanze multiple o può essere distribuito in modo tale che esista un'istanza del servizio in ogni nodo del cluster Service Fabric.

Il parametro `InstanceCount` del cmdlet `New-ServiceFabricService` consente di specificare il numero di istanze del servizio da avviare nel cluster Service Fabric. È possibile impostare il valore `InstanceCount` in base al tipo di applicazione da distribuire. I due scenari più comuni sono:

* `InstanceCount = "1"`. In questo caso solo un'istanza del servizio verrà distribuita nel cluster. L'utilità di pianificazione di Service Fabric determina il nodo in cui il servizio dovrà essere distribuito.

* `InstanceCount ="-1"`. In questo caso solo un'istanza del servizio verrà distribuita in ogni nodo del cluster Service Fabric. Il risultato finale sarà quello di avere una (e solo una) istanza del servizio per ogni nodo del cluster.

Si tratta di una configurazione utile per applicazioni front-end (ad esempio: un endpoint REST) poiché le applicazioni client dovranno semplicemente "connettersi" a qualsiasi nodo del cluster per poter utilizzare l'endpoint. Questa configurazione può essere inoltre usata quando, ad esempio, tutti i nodi del cluster Service Fabric sono connessi a un servizio di bilanciamento del carico in modo che il traffico del client possa essere distribuito nel servizio in esecuzione su tutti i nodi del cluster.

### Verificare l'applicazione in esecuzione

In Esplora infrastruttura di servizi identificare il nodo in cui è in esecuzione il servizio. In questo esempio è in esecuzione in Node1:

![Nodo in cui è in esecuzione il servizio](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se si passa al nodo e si accede all'applicazione, verranno visualizzate le informazioni essenziali sul nodo, incluso il percorso sul disco.

![Percorso sul disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se si passa alla directory tramite Esplora server è possibile trovare la directory di lavoro e la cartella dei log del servizio, come illustrato di seguito.

![Percorso del log](./media/service-fabric-deploy-existing-app/loglocation.png)

## Uso di Visual Studio per creare il pacchetto di un'applicazione esistente

Visual Studio include un modello di servizio di Service Fabric che consente di distribuire un eseguibile guest in un cluster di Service Fabric. Per completare la pubblicazione, è necessario eseguire queste operazioni:

>[AZURE.NOTE] Questa funzionalità richiede l'[SDK versione 2.1.150](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/13/release-of-service-fabric-sdk-2-1-150-and-runtime-5-1-150/)

1. Scegliere File -> Nuovo progetto e creare una nuova applicazione di Service Fabric.
2. Scegliere Eseguibile guest come modello di servizio.
3. Fare clic su Sfoglia per selezionare la cartella con il file eseguibile e compilare il resto dei parametri per creare il nuovo servizio.
  - *Comportamento del pacchetto di codice*: si può impostare questa opzione per copiare tutto il contenuto della cartella nel progetto di Visual Studio. Una scelta utile se il file eseguibile non verrà modificato. Se si prevede che il file eseguibile venga modificato e si vuole avere la possibilità di selezionare nuove build in modo dinamico, si può scegliere invece di collegarsi alla cartella.
  - *Programma*: consente di specificare il nome del file eseguibile da eseguire per avviare il servizio.
  - *Argomenti*: consente di specificare gli argomenti da passare al file eseguibile. Può essere un elenco di parametri con argomenti.
  - *WorkingFolder* (Cartella di lavoro): scegliere la directory di lavoro per il processo da avviare. È possibile specificare due valori:
  	- *CodeBase*: specifica che la directory di lavoro dovrà essere impostata sulla directory code nel pacchetto dell'applicazione, ovvero la directory `Code` nella struttura riportata di seguito.
    - *CodePackage* (Pacchetto di codice): specifica che la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`MyServicePkg`).
4. Assegnare un nome del servizio e fare clic su OK.
5. Se il servizio richiede un endpoint per la comunicazione, è possibile aggiungere il protocollo, la porta e il tipo al file ServiceManifest.xml, ad esempio: ```<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />```.
6. È ora possibile provare il pacchetto e l'azione di pubblicazione con il cluster locale tramite il debug della soluzione in Visual Studio. Quando si è pronti, è possibile pubblicare l'applicazione in un cluster remoto o archiviare la soluzione nel controllo del codice sorgente.

>[AZURE.NOTE] Quando si crea il progetto di applicazione in Visual Studio, si possono usare cartelle collegate. In questo modo si stabilisce il collegamento al percorso di origine dall'interno del progetto, rendendo possibile l'aggiornamento dell'eseguibile guest nella destinazione originale e ottenendo che gli aggiornamenti diventino parte del pacchetto dell'applicazione in fase di compilazione.

## Passaggi successivi
In questo articolo si è appreso come creare il pacchetto di un eseguibile guest e come distribuirlo in Service Fabric. Come passaggio successivo è possibile consultare altri articoli con contenuti aggiuntivi su questo argomento.

- [Esempio per la creazione del pacchetto e la distribuzione di un eseguibile guest in GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluso un collegamento alla versione preliminare dello strumento per la creazione dei pacchetti
- [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)
- [Creare la prima applicazione Service Fabric in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0713_2016-->