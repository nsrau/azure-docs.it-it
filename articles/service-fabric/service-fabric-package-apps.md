---
title: Inserire un&quot;applicazione Azure Service Fabric di Azure in un pacchetto | Microsoft Docs
description: Come inserire un&quot;applicazione Service Fabric in un pacchetto prima della distribuzione in un cluster.
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
ms.date: 3/24/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 45bf19b4c8406cfc09624bef2b9c0f1c443d8fd6
ms.lasthandoff: 03/27/2017


---
# <a name="package-an-application"></a>Inserire un'applicazione in un pacchetto
In questo articolo viene descritto come inserire un'applicazione Service Fabric in un pacchetto e prepararla per la distribuzione.

## <a name="package-layout"></a>Layout del pacchetto
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

## <a name="use-setupentrypoint"></a>Usare SetupEntryPoint
Gli scenari tipici per l'utilizzo di **SetupEntryPoint** sono quando è necessario eseguire un file eseguibile prima dell'avvio del servizio o quando è necessario eseguire un'operazione con privilegi elevati. Ad esempio:

* Impostazione e inizializzazione di variabili di ambiente necessari per il file eseguibile del servizio. Questo non è limitato solo agli eseguibili scritti tramite i modelli di programmazione di Service Fabric. Ad esempio, npm.exe richiede alcune variabili di ambiente configurate per la distribuzione di un'applicazione node.js.
* Impostazione del controllo di accesso mediante l'installazione di certificati di sicurezza.

Per altre informazioni su come configurare **SetupEntryPoint**, vedere [Configurare i criteri per il punto di ingresso dell'installazione del servizio](service-fabric-application-runas-security.md)  

## <a name="configure"></a>Configurare 
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

## <a name="test-the-package"></a>Testare il pacchetto
È possibile verificare la struttura del pacchetto in modalità locale tramite PowerShell usando il comando [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) .
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

Se l'applicazione include [parametri applicazione](service-fabric-manage-multiple-environment-app-configuration.md) definiti, è possibile passarli in [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) per una convalida appropriata.

Se si conosce il cluster in cui verrà distribuita l'applicazione, si consiglia di passarlo nella stringa di connessione dell'archivio immagini. In questo caso, il pacchetto viene convalidato rispetto alle versioni precedenti dell'applicazione già in esecuzione nel cluster. Ad esempio, la convalida può rilevare se sia già stato distribuito un pacchetto con la stessa versione ma con contenuti differenti.  

Una volta che l'applicazione viene inserita correttamente in un pacchetto e passa la convalida, valutare se sia necessaria la compressione in base alle dimensioni e al numero dei file. 

## <a name="compress-a-package"></a>Comprimere un pacchetto
Quando un pacchetto è di grandi dimensioni o dispone di molti file, è possibile comprimerlo per velocizzare la distribuzione. La compressione riduce il numero di file e le dimensioni del pacchetto.
Il [caricamento del pacchetto dell'applicazione](service-fabric-deploy-remove-applications.md#upload-the-application-package) potrebbe impiegare più tempo rispetto al caricamento del pacchetto non compresso, tuttavia sarà possibile [registrare](service-fabric-deploy-remove-applications.md#register-the-application-package) e [rimuovere la registrazione](service-fabric-deploy-remove-applications.md#unregister-an-application-type) del tipo di applicazione con maggiore rapidità.

Il meccanismo di distribuzione è lo stesso per i pacchetti compressi e non. Se il pacchetto è compresso, viene archiviato come tale nell'archivio immagini del cluster e viene decompresso nel nodo prima dell'esecuzione dell'applicazione.
La compressione sostituisce il pacchetto di Service Fabric valido con la versione compressa. La cartella deve fornire le autorizzazioni di scrittura. La compressione di un pacchetto già compresso non apporta modifiche. 

È possibile comprimere un pacchetto eseguendo il comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) con lo switch `CompressPackage`. È possibile decomprimere il pacchetto con lo stesso comando, usando lo switch `UncompressPackage`.

Il comando seguente comprime il pacchetto senza copiarlo nell'archivio immagini. È possibile copiare un pacchetto compresso in uno o più cluster Service Fabric, in base alle esigenze, us il comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) senza il flag `SkipCopy`. Ora il pacchetto include i file compressi per i pacchetti `code`, `config` e `data`. Il manifesto dell'applicazione e i manifesti del servizio non sono compressi in quanto necessari per numerose operazioni interne, come la condivisione dei pacchetti e l'estrazione del nome e della versione del tipo di applicazione per determinate convalide.
La compressione dei manifesti renderebbe inefficienti tali operazioni.

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

In alternativa è possibile comprimere e copiare il pacchetto con [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) in un unico passaggio.
Se il pacchetto è grande, specificare un timeout sufficiente per consentire la compressione del pacchetto e il caricamento nel cluster.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, Service Fabric calcola i checksum per la convalida dei pacchetti dell'applicazione. Quando si usa la compressione, i checksum vengono calcolati nelle versioni compresse di ciascun pacchetto.
Se è stata copiata una versione non compressa del pacchetto dell'applicazione e si desidera usare la compressione per il medesimo pacchetto, è necessario modificare la versione del manifesto dell'applicazione per evitare la mancata corrispondenza del checksum.
Analogamente, se è stata caricata una versione compressa del pacchetto, è necessario aggiornare la versione del manifesto dell'applicazione per usare un pacchetto non compresso.

Il pacchetto è ora corretto, convalidato e compresso (se necessario). Pertanto, è pronto per la [distribuzione](service-fabric-deploy-remove-applications.md) in uno o più cluster di Service Fabric.

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

