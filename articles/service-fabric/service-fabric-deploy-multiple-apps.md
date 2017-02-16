---
title: Distribuire un&quot;applicazione Node.js che usa MongoDB | Documentazione Microsoft
description: "Procedura dettagliata sulla creazione di pacchetti di più eseguibili guest da distribuire in un cluster di Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/22/2016
ms.author: mfussell;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 72fcad2957d6fc5466719c1d275ae0f86f7fa302
ms.openlocfilehash: bf2e34a27806ca79f88db9a9f8d052cd7bfcc94d


---
# <a name="deploy-multiple-guest-executables"></a>Distribuire più eseguibili guest
Questo articolo descrive come creare pacchetti di più eseguibili guest e in che modo distribuirli in Azure Service Fabric. Per la creazione e la distribuzione di un pacchetto di Service Fabric, consultare l'articolo [Distribuire un eseguibile guest in Service Fabric](service-fabric-deploy-existing-app.md).

Questa procedura dettagliata illustra come distribuire un'applicazione con un front-end di Node.js che usa MongoDB come archivio dati, ma può essere adottata per qualsiasi applicazione che presenta dipendenze da un'altra applicazione.   

È possibile usare Visual Studio per generare il pacchetto dell'applicazione che contiene più eseguibili guest. Vedere [Uso di Visual Studio per creare il pacchetto di un'applicazione esistente](service-fabric-deploy-existing-app.md#use-visual-studio-to-package-an-existing-executable). Dopo aver aggiunto il primo eseguibile guest, fare clic con il tasto destro sul progetto dell'applicazione e selezionare **Aggiungi -> Nuovo servizio Service Fabric** per aggiungere il secondo progetto eseguibile guest alla soluzione. Nota: se si sceglie il collegamento all'origine nel progetto di Visual Studio, la compilazione della soluzione di Visual Studio assicura che il pacchetto dell'applicazione venga aggiornato in base alle modifiche nell'origine. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Creare manualmente i pacchetti dell'applicazione eseguibile guest multipla
In alternativa, è possibile distribuire manualmente l'eseguibile guest. Per quanto riguarda la creazione di pacchetti manuale, l'articolo descrive l'uso dello strumento di packaging di Service Fabric, disponibile all'indirizzo [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Creazione di un pacchetto dell'applicazione Node.js
Questo articolo presuppone che Node.js non sia installato nei nodi del cluster di Service Fabric. Sarà quindi necessario aggiungere Node.exe alla directory radice dell'applicazione nodo prima della creazione del pacchetto. La struttura di directory dell'applicazione Node.js (che usa il framework Web Express e il motore per la creazione di modelli Jade) dovrebbe essere simile alla seguente:

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

* **/source** : punta alla directory dell'applicazione da includere nel pacchetto.
* **/target** : definisce la directory in cui creare il pacchetto. Questa directory deve essere diversa dalla directory di origine.
* **/appname** : definisce il nome dell'applicazione esistente. È importante comprendere che questo nome equivale al nome del servizio nel manifesto e non al nome dell'applicazione di Service Fabric.
* **/exe**: definisce il file eseguibile che dovrebbe essere avviato da Service Fabric, in questo caso `node.exe`.
* **/ma** : definisce l'argomento usato per avviare il file eseguibile. Poiché Node.js non è installato, è necessario che Service Fabric avvii il server Web Node.js eseguendo `node.exe bin/www`.  `/ma:'bin/www'` indica allo strumento di creazione pacchetti di usare `bin/ma` come argomento per node.exe.
* **/AppType** : definisce il nome del tipo di applicazione di Service Fabric.

Se si passa alla directory specificata nel parametro /target, si noterà che lo strumento ha creato un pacchetto di Service Fabric pienamente funzionante, come illustrato di seguito:

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
Il file ServiceManifest.xml generato include ora una sezione che descrive come avviare il server Web Node.js, come illustrato nel frammento di codice seguente:

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
In questo esempio il server Web Node.js resta in ascolto sulla porta 3000 ed è quindi necessario aggiornare le informazioni sull'endpoint nel file ServiceManifest.xml, come illustrato di seguito.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Creazione di un pacchetto dell'applicazione MongoDB
Dopo aver creato il pacchetto dell'applicazione Node.js, è possibile proseguire e creare il pacchetto di MongoDB. Come accennato in precedenza, i passaggi da eseguire a questo punto non sono specifici di Node.js e MongoDB, ma si applicano a tutte le applicazioni di cui deve essere creato un pacchetto come singola applicazione di Service Fabric.  

Per creare un pacchetto di MongoDB, è opportuno assicurarsi di aver creato un pacchetto di Mongod.exe e Mongo.exe. Entrambi i file binari si trovano nella directory `bin` della directory di installazione di MongoDB. La struttura di directory è simile alla seguente.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric deve avviare MongoDB con un comando simile al seguente ed è quindi necessario usare il parametro `/ma` quando si crea il pacchetto di MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Se la directory dei dati di MongoDB viene inserita nella directory locale del nodo e si verifica un errore nel nodo, i dati non vengono mantenuti. È quindi consigliabile usare un'archiviazione durevole o implementare un set di repliche di MongoDB per evitare la perdita di dati.  
>
>

In PowerShell o nella shell dei comandi verrà eseguito lo strumento di creazione di pacchetti con i parametri seguenti:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Per aggiungere MongoDB al pacchetto dell'applicazione di Service Fabric, è necessario assicurarsi che il parametro /target punti alla stessa directory che contiene già il manifesto dell'applicazione insieme all'applicazione Node.js e che il nome usato sia lo stesso dell'elemento ApplicationType.

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
Come si può vedere, lo strumento ha aggiunto una nuova cartella MongoDB alla directory contenente i file binari di MongoDB. Se si apre il file `ApplicationManifest.xml` , si può notare che il pacchetto contiene ora l'applicazione Node.js e MongoDB. Il codice seguente illustra il contenuto del manifesto dell'applicazione.

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

### <a name="publishing-the-application"></a>Pubblicare l'applicazione
L'ultimo passaggio consiste nella pubblicazione dell'applicazione nel cluster locale di Service Fabric usando gli script di PowerShell seguenti:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Dopo aver pubblicato l'applicazione nel cluster locale, è possibile accedere all'applicazione Node.js nella porta specificata nel manifesto del servizio dell'applicazione Node.js, ad esempio http://localhost:3000.

In questa esercitazione si è appreso come distribuire facilmente due applicazioni esistenti come una singola applicazione di Service Fabric e come distribuirle in Service Fabric in modo da sfruttare i vantaggi di alcune delle funzionalità di Service Fabric, come la disponibilità elevata e l'integrazione con il sistema di integrità.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Aggiunta di più eseguibili guest a un'applicazione esistente usando Yeoman in Linux

Per aggiungere un altro servizio a un'applicazione già creata mediante `yo`, seguire questa procedura: 
1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfguest:AddService` e specificare i dettagli necessari.



## <a name="next-steps"></a>Passaggi successivi
* Per avere informazioni sulla distribuzione di contenitori, consultare [Panoramica di Service Fabric e contenitori](service-fabric-containers-overview.md)



<!--HONumber=Dec16_HO1-->


