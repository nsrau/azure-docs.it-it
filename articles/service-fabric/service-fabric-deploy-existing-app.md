---
title: Distribuire un eseguibile esistente in Azure Service Fabric | Documentazione Microsoft
description: Procedura dettagliata su come creare il pacchetto di un&quot;applicazione esistente come eseguibile guest, in modo da consentirne la distribuzione in un cluster di Service Fabric
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/17/2016
ms.author: mfussell;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e9c53dc601406961ee7aeca2e350ba14e691cb9b
ms.lasthandoff: 03/29/2017


---
# <a name="deploy-a-guest-executable-to-service-fabric"></a>Distribuire un eseguibile guest in Service Fabric
In Azure Service Fabric è possibile eseguire qualsiasi tipo di applicazione, ad esempio Node.js, Java o applicazioni native. Service Fabric fa riferimento a questi tipi di applicazioni come eseguibili guest.
Gli eseguibili guest vengono considerati da Service Fabric come servizi senza stato. Di conseguenza, vengono inseriti nei nodi di un cluster in base alla disponibilità e ad altre metriche. Questo articolo descrive come creare un pacchetto e distribuire un eseguibile guest in un cluster di Service Fabric, usando Visual Studio o un'utilità della riga di comando.

Questo articolo illustra i passaggi per creare il pacchetto di un eseguibile guest e distribuirlo in Service Fabric.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Vantaggi dell'esecuzione di un'eseguibile guest in Service Fabric
L'esecuzione di un eseguibile guest in un cluster di Service Fabric presenta numerosi vantaggi:

* Disponibilità elevata. Le applicazioni eseguite in Service Fabric sono rese altamente disponibili. Service Fabric garantisce che siano in esecuzione istanze dell'applicazione.
* Monitoraggio dell’integrità. Il monitoraggio dell'integrità di Service Fabric rileva se un'applicazione è in esecuzione e fornisce informazioni di diagnostica in caso di errore.   
* Gestione del ciclo di vita delle applicazioni. Oltre a garantire aggiornamenti senza tempi di inattività, Service Fabric consente il ripristino automatico della versione precedente se durante un aggiornamento viene segnalato un evento di integrità negativo.    
* Densità. È possibile eseguire più applicazioni in un cluster, eliminando la necessità che ogni applicazione venga eseguita nel proprio hardware.

## <a name="samples"></a>Esempi
* [Esempio per la creazione di un pacchetto e distribuzione di un file guest eseguibile](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)
* [Esempio di due eseguibili guest (C# e nodejs) che comunicano tramite il servizio Naming usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Panoramica dei file manifesto dell'applicazione e del servizio
Nell'ambito della distribuzione di un eseguibile guest è utile comprendere il modello di creazione di pacchetti e di distribuzione di Service Fabric, descritto nel [modello applicativo](service-fabric-application-model.md). Il modello di creazione di pacchetti di Service Fabric si basa su due file XML: il manifesto dell'applicazione e il manifesto del servizio. La definizione dello schema per i file ApplicationManifest.xml e ServiceManifest.xml viene installata con Service Fabric SDK in *C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto dell'applicazione**. Il manifesto dell'applicazione viene usato per descrivere l'applicazione. Elenca i servizi da cui è costituita e altri parametri usati per definire come dovranno essere distribuiti tali servizi, ad esempio il numero di istanze.
  
  In Service Fabric un'applicazione è un'unità di distribuzione e aggiornamento. Un'applicazione può essere aggiornata come una singola unità in cui vengono gestiti i potenziali errori e i potenziali ripristini dello stato precedente. Service Fabric garantisce che il processo di aggiornamento venga completato o, in caso di errore, che non lasci l'applicazione in uno stato sconosciuto o instabile.
* **Manifesto del servizio** . Include dati come il nome e il tipo di servizio, nonché il codice e la configurazione. Include dati come il nome e il tipo di servizio nonché il codice, la configurazione e i dati del servizio, più alcuni parametri aggiuntivi usati per configurare il servizio una volta distribuito.

## <a name="application-package-file-structure"></a>Struttura del file del pacchetto dell'applicazione
Per distribuire un'applicazione in Service Fabric, l'applicazione deve seguire una struttura di directory predefinita. Di seguito è riportato un esempio di tale struttura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot contiene il file ApplicationManifest.xml che definisce l'applicazione. Per contenere tutti gli elementi necessari per il servizio viene usata una sottodirectory per ogni servizio incluso nell'applicazione. Tali sottodirectory sono ServiceManifest.xml e, in genere, la seguente:

* *Code*. Contiene il codice del servizio.
* *Config*. Questa directory contiene un file Settings.xml (e altri file, se necessario) a cui il servizio può accedere in fase di esecuzione per recuperare specifiche impostazioni di configurazione.
* *Dati*. Un'altra directory in cui archiviare dati locali aggiuntivi che potrebbero essere necessari al servizio. I dati devono essere usati per archiviare solo dati temporanei. Service Fabric non copia o replica le modifiche alla directory dei dati se il servizio deve essere trasferito, ad esempio durante il failover.

> [!NOTE]
> Non è necessario creare le directory `config` e `data` se non sono necessarie.
> 
> 

## <a name="package-an-existing-executable"></a>Creare il pacchetto di un eseguibile esistente
Quando si crea il pacchetto di un eseguibile guest, è possibile scegliere di usare un modello di progetto di Visual Studio oppure di [creare manualmente il pacchetto dell'applicazione](#manually). Se si usa Visual Studio, la struttura del pacchetto dell'applicazione e i file manifesto vengono creati automaticamente dal nuovo modello di progetto.

> [!TIP]
> Il modo più semplice per creare il pacchetto di un eseguibile Windows esistente in un servizio consiste nell'usare Visual Studio.
> 
> 

## <a name="use-visual-studio-to-package-an-existing-executable"></a>Usare Visual Studio per creare il pacchetto di un eseguibile esistente
Visual Studio include un modello di servizio di Service Fabric che consente di distribuire un eseguibile guest in un cluster di Service Fabric.

1. Scegliere **File** > **Nuovo progetto** e creare un'applicazione di Service Fabric.
2. Scegliere **Eseguibile guest** come modello di servizio.
3. Fare clic su **Sfoglia** per selezionare la cartella contenente l'eseguibile e immettere i restanti parametri per creare il servizio.
   * *Comportamento del pacchetto di codice*. È possibile impostare questa opzione per copiare tutto il contenuto della cartella nel progetto di Visual Studio. Questa scelta si rivela utile se il file eseguibile non viene modificato. Se si prevede che il file eseguibile venga modificato e si vuole avere la possibilità di selezionare nuove build in modo dinamico, si può scegliere invece di collegarsi alla cartella. Si noti che quando si crea il progetto di applicazione in Visual Studio si possono usare cartelle collegate. In questo modo si stabilisce il collegamento al percorso di origine dall'interno del progetto, rendendo possibile l'aggiornamento dell'eseguibile guest nella destinazione di origine. Gli aggiornamenti diventano parte del pacchetto dell'applicazione in fase di compilazione.
   * *Programma*: specifica l'eseguibile da eseguire per avviare il servizio.
   * *Argomenti*: specifica gli argomenti da passare all'eseguibile. Può essere un elenco di parametri con argomenti.
   * *WorkingFolder*: specifica la directory di lavoro per il processo che verrà avviato. È possibile specificare tre valori:
     * `CodeBase` specifica che la directory di lavoro verrà impostata sulla directory del codice nel pacchetto dell'applicazione (directory `Code` nella struttura di file precedente).
     * `CodePackage` specifica che la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`GuestService1Pkg` nella struttura di file precedente).
     * `Work` specifica che i file vengono inseriti in una sottodirectory denominata work.
4. Assegnare un nome al servizio e fare clic su **OK**.
5. Se il servizio richiede un endpoint per la comunicazione, è ora possibile aggiungere il protocollo, la porta e il tipo al file ServiceManifest.xml. Ad esempio: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. È ora possibile usare l'azione di creazione del pacchetto e di pubblicazione sul cluster locale eseguendo il debug della soluzione in Visual Studio. Quando si è pronti, pubblicare l'applicazione in un cluster remoto o archiviare la soluzione nel controllo del codice sorgente.
7. Per informazioni su come visualizzare il servizio dell'eseguibile guest in esecuzione in Service Fabric Explorer, andare alla fine di questo articolo.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Creare manualmente il pacchetto e distribuire un eseguibile esistente
Il processo per la creazione manuale del pacchetto di un eseguibile guest si basa sulla procedura generale seguente:

1. Creare la struttura di directory del pacchetto.
2. Aggiungere i file di codice e di configurazione dell'applicazione.
3. Modificare il file manifesto del servizio.
4. Modificare il file manifesto dell’applicazione.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Creare la struttura di directory del pacchetto
È possibile iniziare creando la struttura di directory come descritto nella sezione precedente "Struttura del file del pacchetto dell'applicazione".

### <a name="add-the-applications-code-and-configuration-files"></a>Aggiungere i file di codice e di configurazione dell'applicazione
Dopo aver creato la struttura di directory, è possibile aggiungere i file di configurazione e di codice dell'applicazione nelle directory del codice e di configurazione. È inoltre possibile creare directory aggiuntive o sottodirectory nelle directory del codice e di configurazione.

Service Fabric esegue un'operazione `xcopy` del contenuto della radice della directory dell'applicazione in modo che non esista alcuna struttura predefinita da usare oltre alla creazione delle due directory principali relative a codice e impostazioni. (ma se si desidera, è possibile scegliere nomi diversi, come illustrato in dettaglio nella sezione successiva).

> [!NOTE]
> Assicurarsi di includere tutti i file e le dipendenze necessari all'applicazione. Service Fabric copia il contenuto del pacchetto dell'applicazione in tutti i nodi del cluster in cui vengono distribuiti i servizi dell'applicazione. Il pacchetto deve contenere tutto il codice necessario per eseguire l'applicazione. Non presupporre che le dipendenze siano già installate.
> 
> 

### <a name="edit-the-service-manifest-file"></a>Modificare il file manifesto del servizio
Il passaggio successivo consiste nel modificare il file manifesto del servizio per includere le informazioni seguenti:

* Il nome del tipo di servizio. Si tratta di un ID usato da Service Fabric per identificare un servizio.
* Il comando da usare per avviare l'applicazione (ExeHost).
* Qualsiasi script da eseguire per installare l'applicazione (SetupEntrypoint).

Di seguito è riportato un esempio di file `ServiceManifest.xml` :

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

Nelle sezioni seguenti vengono esaminate le diverse parti del file che è necessario aggiornare.

#### <a name="update-servicetypes"></a>Aggiornare ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* È possibile scegliere qualsiasi nome per `ServiceTypeName`. Il valore viene usato nel file `ApplicationManifest.xml` per identificare il servizio.
* Specificare `UseImplicitHost="true"`. Questo attributo indica a Service Fabric che il servizio si basa su un'applicazione autonoma, in modo che debba soltanto avviarla come processo e monitorarne l'integrità.

#### <a name="update-codepackage"></a>Aggiornare CodePackage
L’elemento CodePackage specifica il percorso (e la versione) del codice del servizio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L'elemento `Name` consente di specificare il nome della directory nel pacchetto dell'applicazione che contiene il codice del servizio. `CodePackage` include anche l'attributo `version`, Questo consente di specificare la versione del codice e potenzialmente di aggiornare il codice del servizio mediante l'infrastruttura Application Lifecycle Management di Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Facoltativo: aggiornare SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L'elemento SetupEntryPoint consente di specificare un file eseguibile o un file batch da eseguire prima dell'avvio del codice del servizio. È un passaggio facoltativo e non deve pertanto essere necessariamente incluso se non è richiesta alcuna inizializzazione. L'elemento SetupEntryPoint viene eseguito ogni volta che il servizio viene riavviato.

Esiste un solo SetupEntryPoint, di conseguenza gli script di installazione devono essere raggruppati in un singolo file batch se l'installazione dell'applicazione richiede più script. SetupEntryPoint può eseguire qualsiasi tipo di file: file eseguibili, file batch e cmdlet di PowerShell. Per altri dettagli, vedere l'articolo su come [configurare SetupEntryPoint](service-fabric-application-runas-security.md).

Nell'esempio precedente SetupEntryPoint esegue un file batch denominato `LaunchConfig.cmd` che si trova nella sottodirectory `scripts` della directory del codice, presupponendo che l'elemento WorkingFolder sia impostato su CodeBase.

#### <a name="update-entrypoint"></a>Aggiornare EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

L'elemento `EntryPoint` nel manifesto del servizio consente di specificare la modalità di avvio del servizio. L'elemento `ExeHost` specifica il file eseguibile e i relativi argomenti da usare per avviare il servizio.

* `Program` specifica il nome dell'eseguibile che deve avviare il servizio.
* `Arguments` specifica gli argomenti da passare al file eseguibile. Può essere un elenco di parametri con argomenti.
* `WorkingFolder` specifica la directory di lavoro per il processo che sta per essere avviato. È possibile specificare tre valori:
  * `CodeBase` specifica che la directory di lavoro verrà impostata sulla directory code nel pacchetto dell'applicazione (directory `Code` nella struttura di file precedente).
  * `CodePackage` specifica che la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`GuestService1Pkg` nella struttura di file precedente).
    * `Work` specifica che i file vengono inseriti in una sottodirectory denominata work.

WorkingFolder è utile per impostare la directory di lavoro corretta, in modo che i percorsi relativi possano essere usati dagli script di applicazione o da quelli di inizializzazione.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aggiornare gli endpoint e registrarli nel servizio di denominazione per la comunicazione
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Nell'esempio precedente l'elemento `Endpoint` specifica gli endpoint sui quali l'applicazione può restare in ascolto. In questo esempio, l'applicazione Node.js è in ascolto di HTTP sulla porta 3000.

È anche possibile richiedere a Service Fabric di pubblicare l'endpoint nel servizio Naming in modo che altri servizi possano individuare l'indirizzo dell'endpoint per questo servizio. Ciò consente la comunicazione tra servizi costituiti da eseguibili guest.
L'indirizzo dell'endpoint pubblicato presenta il formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` e `PathSuffix` sono attributi facoltativi. `IPAddressOrFQDN` è l'indirizzo IP o il nome di dominio completo del nodo in cui viene inserito l'eseguibile e viene calcolato automaticamente.

Nell'esempio seguente, dopo la distribuzione del servizio, in Service Fabric Explorer viene visualizzato un endpoint simile a `http://10.1.4.92:3000/myapp/` pubblicato per l'istanza del servizio. Oppure, se si tratta di un computer locale, viene visualizzato `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
È possibile usare questi indirizzi con il [proxy inverso](service-fabric-reverseproxy.md) per la comunicazione tra servizi.

### <a name="edit-the-application-manifest-file"></a>Modificare il file manifesto dell’applicazione
Dopo aver configurato il file `Servicemanifest.xml`, sarà necessario apportare alcune modifiche al file `ApplicationManifest.xml` per assicurarsi che vengano usati il tipo e il nome corretti per il servizio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
Nell'elemento `ServiceManifestImport` è possibile specificare uno o più servizi da includere nell'app. Per fare riferimento ai servizi viene usato l'elemento `ServiceManifestName`, che specifica il nome della directory in cui è presente il file `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurare la registrazione
Per gli eseguibili guest è utile poter visualizzare i log di console per determinare la presenza di eventuali errori negli script di configurazione e di applicazione.
Il reindirizzamento della console può essere configurato nel file `ServiceManifest.xml` tramite l'elemento `ConsoleRedirection`.

> [!WARNING]
> Non usare mai i criteri di reindirizzamento della console in un'applicazione distribuita nell'ambiente di produzione, perché ciò può incidere sul failover dell'applicazione. Usare questa opzione *solo* a scopo di sviluppo e debug locale.  
> 
> 

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

L'elemento `ConsoleRedirection` consente di reindirizzare l'output della console, di tipo stdout o stderr, a una directory di lavoro. In questo modo è possibile verificare che non siano presenti errori durante l'installazione o l'esecuzione dell'applicazione nel cluster di Service Fabric.

`FileRetentionCount` determina il numero di file salvati nella directory di lavoro. Un valore pari a 5, ad esempio, indica che i file di log per le cinque esecuzioni precedenti vengono archiviati nella directory di lavoro.

`FileMaxSizeInKb` specifica le dimensioni massime dei file di log.

I file di log vengono salvati in una directory di lavoro del servizio. Per determinare dove si trovano i file, è necessario usare Service Fabric Explorer per stabilire il nodo in cui è in esecuzione il servizio e la directory di lavoro in uso. Più avanti in questo articolo verrà illustrato questo processo.

## <a name="deployment"></a>Distribuzione
L'ultimo passaggio consiste nel [distribuire l'applicazione](service-fabric-deploy-remove-applications.md). Lo script di PowerShell seguente illustra come distribuire l'applicazione nel cluster di sviluppo locale e avviare un nuovo servizio di Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Comprimere il pacchetto](service-fabric-package-apps.md#compress-a-package) prima di copiarlo nell'archivio immagini se il pacchetto è grande o contiene molti file. Per altre informazioni, leggere [qui](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Un servizio Service Fabric può essere distribuito in varie "configurazioni", ad esempio può essere distribuito come istanza singola o come istanze multiple o può essere distribuito in modo tale che sia presente un'istanza del servizio in ogni nodo del cluster di Service Fabric.

Il parametro `InstanceCount` del cmdlet `New-ServiceFabricService` consente di specificare il numero di istanze del servizio da avviare nel cluster Service Fabric. È possibile impostare il valore `InstanceCount` in base al tipo di applicazione da distribuire. I due scenari più comuni sono:

* `InstanceCount = "1"`. In questo caso viene distribuita nel cluster una sola istanza del servizio. L'utilità di pianificazione di Service Fabric determina il nodo in cui il servizio dovrà essere distribuito.
* `InstanceCount ="-1"`. In questo caso viene distribuita un'istanza del servizio in ogni nodo del cluster di Service Fabric. Di conseguenza sarà presente un'unica istanza del servizio per ogni nodo del cluster.

Questa configurazione è utile per applicazioni front-end (ad esempio, un endpoint REST) perché le applicazioni client devono "connettersi" a qualsiasi nodo del cluster per usare l'endpoint. Questa configurazione può essere inoltre usata quando, ad esempio, tutti i nodi del cluster di Service Fabric sono connessi a un servizio di bilanciamento del carico. Il traffico del client può quindi essere distribuito nel servizio in esecuzione su tutti i nodi del cluster.

## <a name="check-your-running-application"></a>Verificare l'applicazione in esecuzione
In Esplora infrastruttura di servizi identificare il nodo in cui è in esecuzione il servizio. In questo esempio è in esecuzione in Node1:

![Nodo in cui è in esecuzione il servizio](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se si passa al nodo e si accede all'applicazione, è possibile visualizzare le informazioni essenziali sul nodo, incluso il percorso sul disco.

![Percorso sul disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se si passa alla directory usando Esplora server, si possono trovare la directory di lavoro e la cartella dei log del servizio, come illustrato nella schermata seguente.

![Percorso del log](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="creating-a-guest-executable-using-yeoman-for-service-fabric-on-linux"></a>Creazione di un guest eseguibile usando Yeoman per Service Fabric in Linux

La procedura per la creazione e distribuzione di un eseguibile guest in Linux è analoga alla distribuzione di un'applicazione java o csharp. 

1. In un terminale digitare `yo azuresfguest`.
2. Assegnare un nome all'applicazione.
3. Scegliere il tipo del primo servizio e assegnargli un nome. Scegliere **Guest Binary** (Guest binario) per un eseguibile guest (e **Guest Container** (Contenitore guest) per un contenitore) e specificare i dettagli, ad esempio il percorso del file eseguibile e i parametri da richiamare.

In Yeoman deve essere stato creato un pacchetto dell'applicazione con i file manifesto e dell'applicazione appropriati e con gli script di installazione e di disinstallazione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come creare il pacchetto di un eseguibile guest e come distribuirlo in Service Fabric. Per informazioni e attività correlate, vedere gli articoli seguenti.

* [Esempio per la creazione del pacchetto e la distribuzione di un file guest eseguibile ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), con un collegamento alla versione preliminare dello strumento per la creazione di pacchetti
* [Esempio di due eseguibili guest (C# e nodejs) che comunicano tramite il servizio Naming usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)
* [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)
* [Creare la prima applicazione Service Fabric in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)


