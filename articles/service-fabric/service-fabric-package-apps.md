---
title: Inserire un'applicazione Azure Service Fabric di Azure in un pacchetto | Microsoft Docs
description: Come inserire un'applicazione Service Fabric in un pacchetto prima della distribuzione in un cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: 0db49b9bd50c640175292671f813d23d960b52e1
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="package-an-application"></a>Inserire un'applicazione in un pacchetto
L'articolo descrive come inserire un'applicazione di Service Fabric in un pacchetto e prepararla per la distribuzione.

## <a name="package-layout"></a>Layout del pacchetto
Il manifesto dell'applicazione, uno o più manifesti dei servizi e gli altri file del pacchetto necessari devono essere organizzati in un layout specifico per la distribuzione in un cluster di Service Fabric. I manifesti di esempio di questo articolo dovrebbero essere organizzati nella struttura di directory seguente:

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

## <a name="use-setupentrypoint"></a>Usare SetupEntryPoint
Gli scenari tipici per l'utilizzo di **SetupEntryPoint** sono quando è necessario eseguire un file eseguibile prima dell'avvio del servizio o quando è necessario eseguire un'operazione con privilegi elevati. Ad esempio: 

* Impostazione e inizializzazione di variabili di ambiente necessari per il file eseguibile del servizio. Questo non è limitato solo agli eseguibili scritti tramite i modelli di programmazione di Service Fabric. Ad esempio, npm.exe richiede alcune variabili di ambiente configurate per la distribuzione di un'applicazione node.js.
* Impostazione del controllo di accesso mediante l'installazione di certificati di sicurezza.

Per altre informazioni su come configurare **SetupEntryPoint**, vedere [Configurare i criteri per il punto di ingresso dell'installazione del servizio](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>Configurare
### <a name="build-a-package-by-using-visual-studio"></a>Creare un pacchetto mediante Visual Studio
Se si usa Visual Studio 2015 per creare un'applicazione, è possibile usare il comando Pacchetto per creare automaticamente un pacchetto corrispondente al layout descritto precedentemente.

Per creare un pacchetto, fare clic con il pulsante destro sul progetto dell'applicazione in Esplora soluzioni e scegliere il comando Pacchetto, come mostrato di seguito:

![Inserire un'applicazione in un pacchetto con Visual Studio][vs-package-command]

Dopo avere completato la creazione del pacchetto, è possibile vedere la posizione del pacchetto nella finestra **Output** . Il passaggio di creazione del pacchetto viene eseguito automaticamente con la distribuzione o il debug di un'applicazione in Visual Studio.

### <a name="build-a-package-by-command-line"></a>Creare un pacchetto dalla riga di comando
È anche possibile creare un pacchetto dell'applicazione a livello di codice usando `msbuild.exe`. Tale operazione viene eseguita da Visual Studio e quindi l'output è lo stesso.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testare il pacchetto
È possibile verificare la struttura del pacchetto in modalità locale tramite PowerShell usando il comando [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Questo comando consente di verificare i problemi di analisi dei manifesti e tutti i riferimenti. Questo comando si limita a verificare la correttezza strutturale delle directory e i file nel pacchetto.
Non verifica il codice o i contenuti del pacchetto di dati oltre a controllare che tutti i file necessari siano presenti.

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

Se l'applicazione include [parametri applicazione](service-fabric-manage-multiple-environment-app-configuration.md) definiti, è possibile passarli in [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) per una convalida appropriata.

Se si conosce il cluster in cui verrà distribuita l'applicazione, si consiglia di passarlo nel parametro `ImageStoreConnectionString`. In questo caso, il pacchetto viene convalidato rispetto alle versioni precedenti dell'applicazione già in esecuzione nel cluster. Ad esempio, la convalida può rilevare se sia già stato distribuito un pacchetto con la stessa versione ma con contenuti differenti.  

Dopo aver correttamente inserito l'applicazione nel pacchetto e aver eseguito la convalida, può essere opportuno comprimere il pacchetto per velocizzare le operazioni di distribuzione.

## <a name="compress-a-package"></a>Comprimere un pacchetto
Quando un pacchetto è di grandi dimensioni o dispone di molti file, è possibile comprimerlo per velocizzare la distribuzione. La compressione riduce il numero di file e le dimensioni del pacchetto.
Nel caso di un pacchetto dell'applicazione compresso, [il relativo caricamento](service-fabric-deploy-remove-applications.md#upload-the-application-package) può richiedere tempi più lunghi rispetto al caso di un pacchetto non compresso, soprattutto se la compressione viene eseguita durante la copia. Con la compressione, le operazioni di [registrazione](service-fabric-deploy-remove-applications.md#register-the-application-package) e [annullamento della registrazione del tipo di applicazione](service-fabric-deploy-remove-applications.md#unregister-an-application-type) sono più veloci.

Il meccanismo di distribuzione è lo stesso per i pacchetti compressi e non. Se il pacchetto è compresso, viene archiviato come tale nell'archivio immagini del cluster e viene decompresso nel nodo prima dell'esecuzione dell'applicazione.
La compressione sostituisce il pacchetto di Service Fabric valido con la versione compressa. La cartella deve fornire le autorizzazioni di scrittura. La compressione di un pacchetto già compresso non apporta modifiche.

È possibile comprimere un pacchetto eseguendo il comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) con lo switch `CompressPackage`. È possibile decomprimere il pacchetto con lo stesso comando, usando lo switch `UncompressPackage`.

Il comando seguente comprime il pacchetto senza copiarlo nell'archivio immagini. È possibile copiare un pacchetto compresso in uno o più cluster Service Fabric, in base alle esigenze, us il comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) senza il flag `SkipCopy`.
Ora il pacchetto include i file compressi per i pacchetti `code`, `config` e `data`. Il manifesto dell'applicazione e i manifesti del servizio non vengono compressi, perché sono necessari per molte operazioni interne. Ad esempio, la condivisione del pacchetto e l'estrazione del nome e della versione del tipo di applicazione per determinate convalide sono tutte operazioni che richiedono l'accesso ai manifesti. La compressione dei manifesti renderebbe inefficienti tali operazioni.

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
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

In alternativa è possibile comprimere e copiare il pacchetto con [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) in un unico passaggio.
Se il pacchetto è grande, specificare un timeout sufficiente per consentire la compressione del pacchetto e il caricamento nel cluster.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, Service Fabric calcola i checksum per la convalida dei pacchetti dell'applicazione. Quando si usa la compressione, i checksum vengono calcolati nelle versioni compresse di ciascun pacchetto. La generazione di un nuovo file zip dal pacchetto dell'applicazione crea checksum diversi. Per evitare errori di convalida, usare il [provisioning di un pacchetto Diff](service-fabric-application-upgrade-advanced.md). Con questa opzione, non includere nella nuova versione i pacchetti rimasti invariati. Al contrario, fare riferimento a questi pacchetti direttamente dal nuovo manifesto del servizio.

Se il provisioning del pacchetto Diff non è un'opzione percorribile ed è necessario includere i pacchetti, generare nuove versioni per i pacchetti `code`, `config` e `data` per evitare checksum non corrispondenti. La generazione di nuove versioni dei pacchetti rimasti invariati è necessaria quando si usa un pacchetto compresso, indipendentemente dal fatto che la versione precedente usi o meno la compressione.

Il pacchetto è ora corretto, convalidato e compresso (se necessario). Pertanto, è pronto per la [distribuzione](service-fabric-deploy-remove-applications.md) in uno o più cluster di Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Comprimere i pacchetti quando si distribuisce con Visual Studio
È possibile indicare a Visual Studio di comprimere i pacchetti in fase di distribuzione, aggiungendo l'elemento `CopyPackageParameters` al profilo di pubblicazione, e di impostare l'attributo `CompressPackage` su `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Creare un file sfpkg
A partire dalla versione 6.1, Service Fabric consente di eseguire il provisioning da un archivio esterno.
Con questa opzione, il pacchetto dell'applicazione non deve essere copiato nell'archivio immagini. Al contrario, è possibile creare un file `sfpkg` e caricarlo in un archivio esterno, quindi specificare l'URI del download in Service Fabric durante il provisioning. È possibile eseguire il provisioning dello stesso pacchetto in più cluster. Il provisioning dall'archivio esterno consente di ridurre i tempi necessari per copiare il pacchetto in ogni cluster.

`sfpkg` è un file zip con estensione sfpkg che contiene il pacchetto dell'applicazione iniziale.
All'interno, il pacchetto dell'applicazione può essere compresso o meno. La compressione del pacchetto dell'applicazione all'interno del file zip viene eseguita a livello di codice, configurazione e pacchetto dati, come [illustrato in precedenza](service-fabric-package-apps.md#compress-a-package).

Per creare un file `sfpkg`, iniziare con una cartella contenente il pacchetto dell'applicazione originale, compresso o meno. Usare quindi una qualsiasi utilità per comprimere la cartella con estensione sfpkg, ad esempio [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Il file `sfpkg` deve essere caricato nell'archivio esterno fuori banda, all'esterno di Service Fabric. L'archivio esterno può essere qualsiasi archivio che espone un endpoint REST http o https. Durante il provisioning, Service Fabric esegue un'operazione GET per scaricare il pacchetto dell'applicazione `sfpkg`, pertanto l'archivio deve consentire l'accesso in lettura per il pacchetto.

Per eseguire il provisioning del pacchetto, usare il provisioning esterno, che richiede l'URI del download e le informazioni sul tipo di applicazione.

>[!NOTE]
> Il provisioning basato sul percorso relativo dell'archivio immagini non supporta attualmente i file `sfpkg`. Di conseguenza, il file `sfpkg` non deve essere copiato nell'archivio immagini.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire e rimuovere applicazioni con PowerShell][10]: descrive come usare PowerShell per gestire le istanze dell'applicazione

[Gestire i parametri dell'applicazione per più ambienti][11]: descrive come configurare parametri e variabili di ambiente per istanze di applicazione diverse.

[Configurare i criteri di sicurezza per l'applicazione][12]: descrive come eseguire i servizi nell'ambito dei criteri di sicurezza per limitare l'accesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
