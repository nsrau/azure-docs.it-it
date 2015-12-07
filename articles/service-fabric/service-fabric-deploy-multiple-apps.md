<properties
   pageTitle="Distribuire un'applicazione Node.js con MongoDB | Microsoft Azure"
   description="Procedura dettagliata sulla creazione di pacchetti di più applicazioni da distribuire in un cluster dell'infrastruttura di servizi di Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>


# Distribuire più applicazioni personalizzate

Questo articolo illustra come creare pacchetti di più applicazioni e distribuirle nell'infrastruttura di servizi usando la versione di anteprima dello strumento per la creazione dei pacchetti dell'infrastruttura di servizi, disponibile all'indirizzo http://aka.ms/servicefabricpacktool.

Per la creazione manuale di un pacchetto dell'infrastruttura dei servizi, leggere l'articolo sulla [distribuzione di un'applicazione esistente nell'infrastruttura di servizi di Azure](service-fabric-deploy-existing-app.md).

Questa procedura dettagliata illustra come distribuire un'applicazione con un front-end di Node.js che usa MongoDB come archivio dati, tuttavia è possibile applicare questi passaggi a qualsiasi applicazione che presenta dipendenze da un'altra applicazione.

## Creare un pacchetto dell'applicazione Node.js

Questo articolo presuppone che Node.js non sia installato nei nodi del cluster dell'infrastruttura di servizi. Di conseguenza, è necessario aggiungere node.exe alla directory radice dell'applicazione nodo prima della creazione del pacchetto. La struttura di directory dell'applicazione Node.js (che usa il framework Web Express e il motore per la creazione di modelli Jade) dovrebbe essere simile alla seguente:

```
|-- NodeApplication
	|-- bin
        |-- www
	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
	|-- public
        |-- images
        |-- etc.
	|-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Il passaggio successivo consiste nella creazione di un pacchetto per l'applicazione Node.js. Il codice seguente crea un pacchetto dell'applicazione dell'infrastruttura di servizi contenente l'applicazione Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Di seguito è riportata una descrizione dei parametri in uso:

- **/source**: punta alla directory dell'applicazione da includere nel pacchetto.
- **/target**: definisce la directory in cui creare il pacchetto. Questa directory deve essere diversa dalla directory di destinazione.
- **/appname**: definisce il nome dell'applicazione esistente. È importante comprendere che questo nome equivale al nome del servizio nel manifesto e non al nome dell'applicazione dell'infrastruttura di servizi.
- **/exe**: definisce il file eseguibile che dovrebbe essere avviato dall'infrastruttura di servizi, in questo caso `node.exe`.
- **/ma**: definisce l'argomento usato per avviare il file eseguibile. Poiché Node.js non è installato, è necessario che l'infrastruttura di servizi avvii il server Web Node.js eseguendo `node.exe bin/www`. `/ma:'bin/www'` indica allo strumento di creazione di pacchetti di usare `bin/ma` come argomento per node.exe.
- **/AppType**: definisce il nome del tipo di applicazione dell'infrastruttura di servizi. Se non

Se si passa alla directory specificata nel parametro /target, si noterà che lo strumento ha creato un pacchetto dell'infrastruttura di servizi pienamente funzionante, come illustrato di seguito:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
		      |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
		      |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Il file ServiceManifest.xml generato ora include una sezione che descrive come avviare il server Web Node.js, come illustrato nel frammento di codice seguente:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
In questo esempio il server Web Node.js resta in ascolto sulla porta 3000, pertanto è necessario aggiornare le informazioni sull'endpoint nel file ServiceManifest.xml, come illustrato di seguito.

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
Dopo aver creato il pacchetto dell'applicazione Node.js, è possibile proseguire e creare il pacchetto di MongoDB. Come accennato in precedenza, i passaggi da eseguire a questo punto non sono specifici di Node.js e MongoDB, ma si applicano a tutte le applicazioni di cui deve essere creato un pacchetto come applicazione dell'infrastruttura di servizi.

Per creare un pacchetto di MongoDB, è opportuno assicurarsi di aver creato un pacchetto di mongod.exe e mongo.exe. Entrambi i file binari si trovano nella directory `bin` della directory di installazione di MongoDB. La struttura di directory è simile alla seguente.

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- etc.
```
L'infrastruttura di servizi deve avviare MongoDB con un comando simile al seguente, pertanto quando si creano il pacchetto di MongoDB è necessario usare il parametro `/ma`.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE]Se si verifica un errore del nodo, nel caso in cui la directory dei dati di MongoDB venga inserita nella directory locale del nodo, i dati non vengono mantenuti. È consigliabile usare un'archiviazione durevole o implementare un set di repliche di MongoDB per evitare la perdita di dati.

In PowerShell o nella shell dei comandi verrà eseguito lo strumento di creazione di pacchetti con i parametri seguenti:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Per aggiungere MongoDB al pacchetto dell'applicazione dell'infrastruttura di servizi è necessario assicurarsi che il parametro /target punti alla stessa directory che contiene già il manifesto dell'applicazione insieme all'applicazione Node.js e che il nome usato sia lo stesso dell'elemento ApplicationType.

Passare alla directory ed esaminare gli elementi creati dallo strumento.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
		    |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Come si può vedere, lo strumento ha aggiunto una nuova cartella MongoDB alla directory contenente i file binari di MongoDB. Se si apre il file `ApplicationManifest.xml`, si può notare che il pacchetto ora contiene l'applicazione Node.js e MongoDB. Il codice seguente illustra il contenuto del manifesto dell'applicazione.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

L'ultimo passaggio consiste nella pubblicazione dell'applicazione nel cluster locale dell'infrastruttura di servizi usando gli script di PowerShell seguenti:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStore' -ApplicationPackagePathInImageStore 'Store\NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Dopo aver pubblicato l'applicazione nel cluster locale, è possibile accedere all'applicazione Node.js nella porta specificata nel manifesto del servizio dell'applicazione Node.js, ad esempio http://localhost:3000.

In questa esercitazione si è appreso come distribuire facilmente due applicazioni esistenti come un'applicazione dell'infrastruttura di servizi e come distribuirle nell'infrastruttura di servizi in modo da sfruttare i vantaggi di alcune delle funzionalità dell'infrastruttura di servizi come la disponibilità elevata e l'integrazione con il sistema di integrità.

## Passaggi successivi

Informazioni su come [creare manualmente un pacchetto di una singola applicazione](service-fabric-deploy-existing-app.md).

<!---HONumber=AcomDC_1125_2015-->