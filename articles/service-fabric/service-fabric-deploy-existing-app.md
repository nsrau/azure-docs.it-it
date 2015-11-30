<properties
   pageTitle="Distribuire un'applicazione esistente nell'infrastruttura di servizi di Azure | Microsoft Azure"
   description="Procedura dettagliata su come creare un pacchetto di un'applicazione esistente in modo che possa essere distribuito in un cluster di infrastruttura di servizi di Azure"
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
   ms.workload="NA"
   ms.date="11/09/2015"
   ms.author="bscholl"/>

# Distribuire un'applicazione esistente nell'infrastruttura di servizi

Nell'infrastruttura di servizi è possibile eseguire qualsiasi tipo di applicazione esistente, ad esempio Node.js, Java o applicazioni native. L'infrastruttura di servizi considera tali applicazioni come servizi senza stato e li inserisce nei nodi di un cluster in base alla disponibilità e ad altre metriche. Questo articolo descrive come creare il pacchetto di un'applicazione esistente e come distribuirla in un cluster dell'infrastruttura di servizi.

## Vantaggi dell'esecuzione di un'applicazione esistente nell'infrastruttura di servizi

L'esecuzione dell'applicazione nel cluster dell'infrastruttura di servizi presenta i vantaggi seguenti:

- Disponibilità elevata: le applicazioni in esecuzione nell'infrastruttura di servizi sono a disponibilità elevata per impostazione predefinita. L'infrastruttura di servizi garantisce che un'istanza di un'applicazione sia sempre in esecuzione
- Monitoraggio dello stato: il monitoraggio predefinito dello stato dell'infrastruttura di servizi rileva se l'applicazione è in esecuzione e fornisce informazioni di diagnostica in caso di errore   
- Gestione del ciclo di vita delle applicazioni: oltre a garantire aggiornamenti senza tempi di inattività, l'infrastruttura di servizi consente inoltre di eseguire il rollback alla versione precedente se si verifica un problema durante l'aggiornamento.    
- Densità: è possibile eseguire più applicazioni in un cluster, eliminando la necessità che ogni applicazione venga eseguita nel proprio hardware

Questo articolo illustra i passaggi di base per creare il pacchetto di un'applicazione esistente e distribuirla nell'infrastruttura di servizi.


## Breve panoramica dei file manifesto dell'applicazione e del servizio

Prima di esaminare i dettagli della distribuzione di un'applicazione esistente, è utile comprendere il modello di creazione del pacchetto e di distribuzione dell'infrastruttura di servizi. Il modello di creazione del pacchetto e di distribuzione dell'infrastruttura di servizi si basa principalmente su due file:


* **Manifesto dell'applicazione**

  Il manifesto dell'applicazione viene usato per descrivere l'applicazione ed elenca i servizi che la compongono, nonché altri parametri, ad esempio il numero di istanze, che consentono di definire la modalità di distribuzione dei servizi. Nell'infrastruttura di servizi un'applicazione è "l'unità aggiornabile". Un'applicazione può essere aggiornata come una singola unità in cui i potenziali errori (e i potenziali ripristini) vengono gestiti nella piattaforma in modo da garantire che il processo di aggiornamento abbia un esito completamente positivo o in caso contrario, che non lasci l'applicazione in uno stato sconosciuto/instabile.


* **Manifesto del servizio**

  Il manifesto del servizio descrive i componenti di un servizio. Include dati, come ad esempio nome e tipo di servizio (informazioni che l'infrastruttura di servizi usa per gestire il servizio), il relativo codice, componenti di dati e di configurazione più alcuni parametri aggiuntivi usati per configurare il servizio una volta distribuito. Non esamineremo tutti i diversi parametri disponibili nel manifesto del servizio, ma esamineremo la sotto-categoria necessaria ad eseguire un'applicazione esistente in Service Fabric

Per informazioni dettagliate sul formato di creazione del pacchetto dell'infrastruttura di servizi, vedere [questo articolo](service-fabric-develop-your-service-index.md).

## Struttura del file del pacchetto dell'applicazione
Per distribuire un'applicazione nell'infrastruttura di servizi, l'applicazione deve seguire una struttura di directory predefinita. Di seguito è riportato un esempio di tale struttura.

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

La radice contiene il file ApplicationManifest.xml che definisce l'applicazione. Una sottodirectory per ogni servizio incluso nell'applicazione viene usata per contenere tutti gli elementi necessari per il servizio: il ServiceManifest.xml e in genere 3 directory:

- *code*: contiene il codice del servizio
- *config*: contiene un file settings.xml (e altri file, se necessario) a cui il servizio può accedere in fase di esecuzione per recuperare le impostazioni di configurazione specifiche.
- *data*: un'altra directory per archiviare dati locali aggiuntivi che potrebbero essere richiesti dal servizio. Nota: i dati devono essere usati per archiviare solo i dati ephymeral, l'infrastruttura di servizi non copia/replica le modifiche alla directory dei dati se il servizio deve essere trasferito, ad esempio, durante il failover.

Nota: non occorre creare le directory `config` e `data` se non sono necessarie.

## Processo di creazione di un pacchetto di un'applicazione esistente

Il processo di creazione di un pacchetto di un'applicazione esistente si basa sul procedimento seguente:

- creare la struttura di directory del pacchetto
- aggiungere i file di configurazione e di codice dell'applicazione
- aggiornare il file manifesto del servizio
- aggiornare il manifesto dell'applicazione

>[AZURE.NOTE]Viene fornito uno strumento di creazione di pacchetti che consente di creare automaticamente ApplicationPackage. Lo strumento è attualmente in anteprima. Per altre informazioni, vedere [qui](http://aka.ms/servicefabricpacktool).

### Creare la struttura di directory del pacchetto
È possibile iniziare creando la struttura di directory come descritto in precedenza.

### Aggiungere i file di codice e di configurazione dell'applicazione
Dopo aver creato la struttura di directory, è possibile aggiungere i file di configurazione e di codice dell'applicazione nelle directory del codice e di configurazione. È inoltre possibile creare directory aggiuntive o sottodirectory nelle directory del codice e di configurazione. L'infrastruttura di servizi esegue xcopy del contenuto della directory radice dell'applicazione in modo che non esista alcuna struttura predefinita da usare oltre alla creazione delle due directory principali Codice e Impostazioni (ma se si desidera, è possibile scegliere nomi diversi, come illustrato in dettaglio nella sezione successiva).

>[AZURE.NOTE]Assicurarsi di includere tutti i file o le dipendenze necessarie all'applicazione. L'infrastruttura di servizi copia il contenuto del pacchetto dell'applicazione in tutti i nodi del cluster in cui i servizi dell'applicazione vengono distribuiti. Il pacchetto deve contenere tutto il codice necessario per eseguire l'applicazione. È consigliabile non presupporre che le dipendenze siano già installate.

### Modificare il file manifesto del servizio.
Il passaggio successivo consiste nel modificare il file manifesto del servizio per includere le informazioni seguenti:

- Il nome del tipo di servizio. Si tratta di un 'ID' usato dall'infrastruttura di servizi per identificare un servizio.
- Il comando da usare per avviare l'applicazione (ExeHost)
- Qualsiasi script da eseguire per installare/configurare l'applicazione (SetupEntryPoint).

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
- È necessario specificare `UseImplicitHost="true"`. Questo attributo indica all'infrastruttura di servizi che il servizio si basa su un'applicazione autonoma, in modo che debba soltanto avviarla come processo e monitorarne l'integrità.

### CodePackage
Il CodePackage specifica il percorso (e versione) del codice del servizio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L'elemento `Name` viene usato per specificare il nome della directory nel pacchetto dell'applicazione che contiene il codice del servizio. `CodePackage` include anche l'attributo `version` che può essere usato per specificare la versione del codice e potenzialmente per aggiornare il codice del servizio mediante l'infrastruttura ALM dell'infrastruttura di servizi.
### SetupEntryPoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L'elemento SetupEntryPoint consente di specificare un file eseguibile o un file batch da eseguire prima dell'avvio del codice del servizio. È un elemento facoltativo perciò non è necessario includerlo se non esiste alcuna inizializzazione/installazione richiesta. L'elemento SetupEntryPoint viene eseguito ogni volta che il servizio viene riavviato. Esiste solo un SetupEntrypoint, quindi gli script di installazione/configurazione devono essere raggruppati in un singoli file batch se l'installazione/configurazione dell'applicazione richiede più script. Analogamente all'elemento EntryPoint, l'elemento SetupEntryPoint può eseguire qualsiasi tipo di file, ovvero file eseguibili, file batch e cmdlet di PowerShell. Nell'esempio precedente l'elemento SetupEntryPoint è basato su un file batch launchConfig.cmd, che si trova nella sottodirectory `scripts` della directory Codice, presupponendo che l'elemento WorkingDirectory sia impostato su Codice.

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

L'elemento `Entrypoint` nel manifesto del servizio viene usato per specificare la modalità di avvio del servizio. L'elemento `ExeHost` specifica il file eseguibile e i relativi argomenti da usare per avviare il servizio.

- `Program`: specifica il nome del file eseguibile da eseguire per avviare il servizio.
- `Arguments`: specifica gli argomenti da passare al file eseguibile. Può essere un elenco di parametri con argomenti.
- `WorkingFolder`: specifica la directory di lavoro per il processo che sta per essere avviato. È possibile specificare due valori:
	- `CodeBase`: la directory di lavoro dovrà essere impostata sulla directory Codice nel pacchetto dell'applicazione (directory `Code` nella struttura riportata di seguito)
	- `CodePackage`: la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`MyServicePkg`)
- L'elemento `WorkingDirectory` è utile per impostare la directory di lavoro corretta in modo che i percorsi relativi possano essere usati dagli script di applicazione o da quelli di inizializzazione.

### Endpoint

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
L'elemento `Endpoint` specifica gli endpoint sui quali l'applicazione può restare in ascolto. In questo esempio l'applicazione Node.js resta in ascolto sulla porta 3000.

## File manifesto dell'applicazione

Dopo aver configurato il file `servicemanifest.xml`, sarà necessario apportare alcune modifiche al file `ApplicationManifest.xml` per assicurarsi che vengano usati il tipo e il nome corretti per il servizio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

Nell'elemento `ServiceManifestImport` è possibile specificare uno o più servizi da includere nell'app. Per fare riferimento ai servizi viene usato l'elemento `ServiceManifestName` che specifica il nome della directory che include il file `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configurazione della registrazione
Per un'applicazione esistente è molto utile poter visualizzare i registri di console per scoprire se gli script di configurazione e applicazione mostrano eventuali errori. Il reindirizzamento della console può essere configurato nel file `ServiceManifest.xml` tramite l'elemento `ConsoleRedirection`

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

* L'elemento `ConsoleRedirection` può essere usato per reindirizzare l'output della console, di tipo stdout e stderr, a una directory di lavoro, in modo da verificare che non siano presenti errori durante l'installazione o l'esecuzione dell'applicazione nel cluster dell'infrastruttura di servizi.

	* L'elemento `FileRetentionCount` determina il numero di file salvati nella directory di lavoro. Un valore, ad esempio, 5 indica che i file di log per le 5 esecuzioni precedenti vengono archiviati nella directory di lavoro.
	* L'elemento `FileMaxSizeInKb` specifica le dimensioni massime dei file di log.

I file di log vengono salvati in una delle directory di lavoro del servizio, per determinare dove si trovano i file, è necessario utilizzare Esplora risorse di Service Fabric per determinare in quale nodo è in esecuzione il servizio e quale directory di lavoro è attualmente utilizzata. Più avanti in questo articolo verrà illustrato come eseguire questa operazione.

### Distribuzione
L'ultimo passaggio consiste nel distribuire l'applicazione. Lo script di PowerShell seguente illustra come distribuire l'applicazione nel cluster di sviluppo locale e avviare un nuovo servizio dell'infrastruttura di servizi.

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un servizio dell'infrastruttura di servizi può essere distribuito in varie 'configurazioni', ad esempio può essere distribuito come istanza singola o come istanze multiple o può essere distribuito in modo tale che esista un'istanza del servizio in ogni nodo del cluster dell'infrastruttura di servizi.

Il parametro `InstanceCount` del cmdlet `New-ServiceFabricService` consente di specificare il numero di istanze del servizio da avviare nel cluster di infrastruttura di servizi. È possibile impostare il valore `InstanceCount` in base al tipo di applicazione da distribuire. I due scenari più comuni sono: * `InstanCount = "1"`: in questo caso solo un'istanza del servizio verrà distribuita nel cluster. L'utilità di pianificazione dell'infrastruttura di servizi determina il nodo in cui il servizio dovrà essere distribuito.

* `InstanceCount ="-1"`: in questo caso solo un'istanza del servizio verrà distribuita in ogni nodo del cluster dell'infrastruttura di servizi. Il risultato finale sarà quello di avere una (e solo una) istanza del servizio per ogni nodo del cluster. Si tratta di una configurazione utile per applicazioni front-end (ad esempio: un endpoint REST) poiché le applicazioni client dovranno semplicemente “connettersi” a qualsiasi nodo del cluster per poter utilizzare l'endpoint. Questa configurazione può essere inoltre usata quando, ad esempio, tutti i nodi del cluster dell'infrastruttura di servizi sono connessi a un servizio di bilanciamento del carico in modo che il traffico del client possa essere distribuito nel servizio in esecuzione su tutti i nodi del cluster.

### Verificare l'applicazione in esecuzione

In Esplora infrastruttura di servizi identificare il nodo in cui è in esecuzione il servizio. In questo esempio è in esecuzione in Node1:

![app in esecuzione](./media/service-fabric-deploy-existing-app/runningapplication.png)

Se si passa al nodo e si accede all'applicazione, verranno visualizzate le informazioni essenziali sul nodo, incluso il percorso sul disco.

![percorso sul disco](./media/service-fabric-deploy-existing-app/locationondisk.png)

Se si passa alla directory tramite Esplora server è possibile trovare la directory di lavoro e la cartella dei log del servizio, come illustrato di seguito.

![percorso sul disco](./media/service-fabric-deploy-existing-app/loglocation.png)


## Passaggi successivi
In questo articolo si è appreso come creare il pacchetto di un'applicazione esistente e come distribuirla all'infrastruttura di servizi. Come passaggio successivo è possibile consultare altri articoli con contenuti aggiuntivi su questo argomento.

- Esempio per la creazione del pacchetto e la distribuzione di un'applicazione esistente in [Github](https://github.com/bmscholl/servicefabric-samples/tree/comingsoon/samples/RealWorld/Hosting/SimpleApplication), inclusa la versione preliminare dello strumento per la creazione dei pacchetti
- Esempio per la creazione dei pacchetti di più applicazioni su [Github](https://github.com/bmscholl/servicefabric-samples/tree/comingsoon/samples/RealWorld/Hosting/SimpleApplication)
- Come iniziare a [creare la prima applicazione dell'infrastruttura di servizi usando Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=Nov15_HO4-->