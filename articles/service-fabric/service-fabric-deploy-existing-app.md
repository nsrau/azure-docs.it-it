<properties
   pageTitle="Distribuire un'applicazione esistente nell'infrastruttura di servizi di Azure | Microsoft Azure"
   description="Procedura dettagliata su come creare un pacchetto di un'applicazione esistente in modo che possa essere distribuito in un cluster di infrastruttura di servizi di Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Distribuire un'applicazione esistente dell'infrastruttura di servizi di Azure

Infrastruttura di servizi di Azure può essere usato per distribuire applicazioni esistenti, in modo che possano trarre vantaggio dal monitoraggio dello stato e da ALM (Application Lifecycle Management).

Questa esercitazione illustra i concetti di base e del processo relativi alla creazione di un pacchetto di un'applicazione esistente per la distribuzione in un cluster di infrastruttura di servizi.


## Breve panoramica di file di applicazioni e di manifesto del servizio

Prima di esaminare i dettagli della distribuzione di un'applicazione esistente, è utile comprendere il modello di distribuzione di infrastruttura di servizi. Il modello di distribuzione di infrastruttura di servizi si basa principalmente su due file:


* **Manifesto dell'applicazione**

  Il manifesto dell'applicazione viene utilizzato per descrivere l'applicazione e elenca i servizi che la compongono, nonché altri parametri, come ad esempio il numero di istanze, che consentono di definire la modalità di distribuzione dei servizi. Nel mondo Service Fabric, un'applicazione è ‘l'unità aggiornabile’. Un'applicazione può essere aggiornata come una singola unità in cui i potenziali errori (e i potenziali ripristini) vengono gestiti nella piattaforma in modo da garantire che il processo di aggiornamento abbia un esito completamente positivo o in caso contrario, che non lasci l'applicazione in uno stato sconosciuto/instabile.

  Il seguente è un esempio di un manifesto di un'applicazione:

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest> ```

* **Manifesto del servizio**

  Il manifesto del servizio descrive i componenti di un servizio. Include dati, come ad esempio nome e tipo di servizio (informazioni che Service Fabric utilizza per gestire il servizio), il relativo codice, componenti di dati e di configurazione più alcuni parametri aggiuntivi utilizzati per configurare il servizio una volta distribuito. Non esamineremo tutti i diversi parametri disponibili nel manifesto del servizio, ma esamineremo la sotto-categoria necessaria ad eseguire un'applicazione esistente in Service Fabric

  Il seguente è un esempio di un manifesto del servizio

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest> ```

## Struttura del file del pacchetto dell’applicazione
Per distribuire un'applicazione utilizzando, ad esempio, i powershell cmdlet, l'applicazione deve seguire una struttura di directory predefinita.

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

La radice contiene il file applicationmanifest.xml che definisce l'applicazione. Una sottodirectory per ogni servizio incluso nell'applicazione viene utilizzata per contenere tutti gli elementi necessari per il servizio: il servicemanifest.xml e, in genere 3 directory:

- *code*: contiene il codice del servizio
- *config*: contiene un file settings.xml (e altri file, se necessario) a cui il servizio può accedere in fase di esecuzione per recuperare le impostazioni di configurazione specifiche.
- *data*: potrebbe essere necessaria un'ulteriore directory per archiviare dati locali aggiuntivi del servizio. Nota: I dati devono essere utilizzati per archiviare solo i dati ephymeral, Service Fabric non copia/replica le modifiche alla directory dei dati se il servizio deve essere trasferito, ad esempio, durante il failover.

Nota: È possibile utilizzare qualsiasi nome arbitrario di directory per Code, Config e Data. È sufficiente assicurarsi di usare lo stesso valore nel file ApplicationManifest.

## il processo di creazione di un pacchetto di un'applicazione esistente

Il processo di creazione di un pacchetto di un'applicazione esistente si basa sul procedimento riportato di seguito:

- Creare la struttura di directory del pacchetto
- Aggiungere file di codice e di configurazione dell'applicazione
- aggiornare il file manifesto del servizio
- aggiornare il manifesto dell'applicazione


### Creare la struttura di directory del pacchetto
È possibile iniziare creando la struttura di directory come descritto in precedenza. Ho creato una directory e ho copiato l'applicazione e il manifesto del servizio da un progetto esistente che avevo precedentemente creato in Visual Studio.

![][1] ![][2]


### Aggiungere i file di codice e di configurazione dell'applicazione
Dopo aver creato la struttura di directory, è possibile aggiungere i file di codice e di configurazione dell’applicazione nella directory Code and Config. È inoltre possibile creare directory aggiuntive o sottodirectory nelle directory Code o Config. Service Fabric esegue xcopy del contenuto della directory radice dell'applicazione in modo che non esista alcuna struttura predefinita da utilizzare invece di creare due directory principali Code e Settings (ma se si desidera, è possibile scegliere nomi diversi, ulteriori dettagli nella sezione successiva).

>[AZURE.NOTE]\: assicurarsi di includere tutti i file/dipendenze necessarie all'applicazione. Service Fabric copia il contenuto del pacchetto dell'applicazione in tutti i nodi del cluster in cui i servizi dell'applicazione vengono distribuiti. Il pacchetto deve contenere tutto il codice necessario per eseguire l'applicazione. È consigliabile non presupporre che le dipendenze siano già installate.

### Modificare il file manifesto del servizio.
Il passaggio successivo consiste nel modificare il file manifesto del servizio per includere le informazioni seguenti:

- Il nome del tipo di servizio. Si tratta di un 'ID' usato dall'infrastruttura di servizi per identificare un servizio.
- Il comando da usare per avviare l'applicazione (ExeHost)
- Qualsiasi script da eseguire per installare/configurare l'applicazione (SetupEntrypoint).

Questo è un esempio di un file `servicemanifest.xnml` che "include in un pacchetto" un'applicazione node.js:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

Analizziamo ora le diverse parti del file che è necessario aggiornare:

### ServiceTypes:

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- È possibile scegliere qualsiasi nome per `ServiceTypeName`. Il valore viene usato nel file `applicationmanifest.xml` per identificare il servizio.
- È necessario specificare `UserImplicitHost = "true"`. Questo attributo indica a Service Fabric che il servizio si basa su un'applicazione autonoma, in modo che debba soltanto avviarla come processo e monitorarne l'integrità.

### CodePackage
Il CodePackage specifica il percorso (e versione) del codice del servizio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L'elemento `Name` viene usato per specificare il nome della directory nel pacchetto dell'applicazione che contiene il codice del servizio. `CodePackage` ha anche l'attributo `version` che può essere usato per specificare la versione del codice e potenzialmente per aggiornare il codice del servizio mediante l'infrastruttura ALM dell'infrastruttura di servizi.


### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


L'elemento `Entrypoint` nel file manifesto del servizio viene usato per specificare la modalità di avvio del servizio. L'elemento `ExeHost` specifica il file eseguibile e i relativi argomenti da usare per avviare il servizio.

- `Program`: specifica il nome del file eseguibile che deve essere eseguito per avviare il servizio.
- `Arguments`: specifica gli argomenti da passare al file eseguibile. Può essere un elenco di parametri con argomenti.
- `WorkingFolder`: specifica la directory di lavoro per il processo che sta per essere avviato. È possibile specificare due valori:
	- `CodeBase`: la directory di lavoro dovrà essere impostata sulla directory Code nel pacchetto dell'applicazione (directory `Code` nella struttura riportata di seguito).
	- `CodePackage`: la directory di lavoro verrà impostata sulla radice del pacchetto dell'applicazione (`MyServicePkg`).
- L'elemento `WorkingDirectory` è utile per impostare la directory di lavoro corretta in modo che i percorsi relativi possano essere usati dagli script di applicazione o da quelli di inizializzazione.

È possibile specificare anche un altro valore per l'elemento `WorkingFolder` (`Work`), ma questo valore non è particolarmente utile per lo scenario di inserimento di un'applicazione esistente.


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### Il punto di ingresso del programma di installazione

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

L'elemento `SetupEntrypoint` consente di specificare un file eseguibile o batch da eseguire prima dell'avvio del codice del servizio. È un elemento facoltativo perciò non è necessario includerlo se non esiste alcuna inizializzazione/installazione richiesta. Il punto di ingresso viene eseguito ogni volta che il servizio viene riavviato. Esiste solo un SetupEntrypoint, quindi gli script di installazione/configurazione devono essere raggruppati in un singoli file batch se l'installazione/configurazione dell'applicazione richiede più script. Analogamente all'elemento `Entrypoint`, l'elemento `SetupEntrypoint` può eseguire qualsiasi tipo di file, ovvero file eseguibili, file batch e cmdlet di Powershell. Nell'esempio precedente l'elemento `SetupEntrypoint` è basato su un file batch myAppsetup.cmd, che si trova nella sottodirectory degli script della directory Code, presupponendo che l'elemento `WorkingDirectory` sia impostato su `Code`.

## File manifesto dell'applicazione

Dopo avere configurato il file `servicemanifest.xml`, sarà necessario apportare alcune modifiche al file `applicationmanifest.xml` per assicurare che vengano usati il tipo e il nome corretti per il servizio.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

Nell'elemento `ServiceManifestImport` è possibile specificare uno o più servizi da includere nell'app. Per fare riferimento ai servizi viene usato l'elemento `ServiceManifestName` che specifica il nome della directory che include il file `servicemanifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

L'elemento `DefaultServices` nel file manifesto dell'applicazione viene usato per definire alcune proprietà del servizio.

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* L'elemento `ServiceTypeName` viene usato come 'ID' per il servizio. Nel contesto del trasferimento di un'applicazione esistente, è sufficiente che `ServiceTypeName` sia un identificatore univoco per il servizio.
* `StatelessService`: infrastruttura di servizi supporta due tipi di servizi, ovvero con e senza stato. Nel caso del trasferimento di un'applicazione esistente, il servizio è senza stato, quindi è necessario usare sempre l'elemento `StatelessService`.

Un servizio Service Fabric può essere distribuito in varie 'configurazioni', ad esempio può essere distribuito come un’istanza singola o come istanze multiple o può essere distribuito in modo tale che esista un'istanza del servizio in ogni nodo del cluster di Service Fabric. Nel file `applicationmanifest.xml` è possibile specificare come si vuole distribuire l'applicazione

* `InstanceCount`: consente di specificare il numero di istanze del servizio da avviare nel cluster di infrastruttura di servizi. È possibile impostare il valore `InstanceCount` in base al tipo di applicazione da distribuire. I due scenari più comuni sono:

	* `InstanCount = "1"`: in questo caso solo un'istanza del servizio verrà distribuita nel cluster. L’utilità di pianificazione di Service Fabric determina il nodo in cui il servizio dovrà essere distribuito. Un conteggio di istanza singola è utile anche per applicazioni che richiedono una configurazione diversa se vengono eseguiti in più istanze. In questo caso risulta più semplice definire più servizi in uno stesso file manifesto dell'applicazione e usare l'elemento `InstanceCount = "1"`. Il risultato finale sarà pertanto quello di disporre di più istanze dello stesso servizio, ma ciascuna con una configurazione specifica. Un valore superiore a uno per l'elemento `InstanceCount` può essere usato solo se l'obiettivo consiste nell'ottenere più istanze della stessa configurazione.

	* `InstanceCount ="-1"`: in questo caso solo un'istanza del servizio verrà distribuita in ogni nodo del cluster di infrastruttura di servizi. Il risultato finale sarà quello di avere una (e solo una) istanza del servizio per ogni nodo del cluster. Si tratta di una configurazione utile per applicazioni front-end (ad esempio: un endpoint REST) poiché le applicazioni client dovranno semplicemente “connettersi” a qualsiasi nodo del cluster per poter utilizzare l'endpoint. Questa configurazione può essere inoltre utilizzata quando, ad esempio, tutti i nodi del cluster di Service Fabric sono connessi a un servizio di bilanciamento del carico cosi che il traffico del client può essere distribuito nel servizio in esecuzione su tutti i nodi del cluster.


### Test
Per un'applicazione esistente è molto utile poter visualizzare i registri di console per scoprire se gli script di configurazione e applicazione mostrano eventuali errori. Il reindirizzamento della console può essere configurato nel file `servicemanifest.xml` usando l'elemento`ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* L'elemento `ConsoleRedirection` può essere usato per reindirizzare l'output della console, di tipo stdout e stderr, a una directory di lavoro, in modo da verificare che non siano presenti errori durante l'installazione o l'esecuzione dell'applicazione nel cluster di infrastruttura di servizi.

	* L'elemento `FileRetentionCount` determina il numero di file salvati nella directory di lavoro. Un valore, ad esempio, 5 indica che i file di log per le 5 esecuzioni precedenti vengono archiviati nella directory di lavoro.
	* L'elemento `FileMaxSizeInKb` specifica le dimensioni massime dei file di log.

I file di log vengono salvati in una delle directory di lavoro del servizio, per determinare dove si trovano i file, è necessario utilizzare Esplora risorse di Service Fabric per determinare in quale nodo è in esecuzione il servizio e quale directory di lavoro è attualmente utilizzata.

In Esplora risorse di Service Fabric, identificare il nodo in cui è in esecuzione il servizio

![][3]

Dopo aver individuato il nodo in cui è in esecuzione il servizio, è possibile scoprire quale directory di lavoro viene utilizzata

![][4]

Quando si seleziona il nome del servizio, nel riquadro destro è possibile visualizzare il codice del servizio e le impostazioni archiviate

![][5]

Se si fa clic sul collegamento nel campo ‘percorso del disco’ è possibile accedere alla directory in cui i servizi sono in esecuzione.

![][6]

La directory di log contiene tutti i file di log.

Nota: In questo esempio viene illustrato il caso di una singola istanza del servizio in esecuzione nel cluster. Se sono presenti più istanze, potrebbe essere necessario controllare il file di log su tutti i nodi in cui è in esecuzione il servizio.


## Passaggi successivi
Stiamo lavorando ad uno strumento che può essere utilizzato per creare il pacchetto di un'applicazione esistente semplicemente facendo riferimento alla radice della struttura di directory dell'applicazione. Lo strumento genera i file di manifesto e configura le impostazioni di base necessarie per ‘trasformare’ l'applicazione in un servizio Fabric Service.

Per altre informazioni su come sviluppare un'app di infrastruttura di servizi tradizionale, vedere [questa pagina](service-fabric-develop-your-service-index.md).

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO2-->