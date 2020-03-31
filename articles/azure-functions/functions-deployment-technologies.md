---
title: Tecnologie di distribuzione in Funzioni di AzureDeployment technologies in Azure Functions
description: Informazioni sui diversi modi in cui è possibile distribuire il codice in Funzioni di Azure.Learn the different ways you can deploy code to Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277128"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologie di distribuzione in Funzioni di AzureDeployment technologies in Azure Functions

È possibile usare alcune tecnologie diverse per distribuire il codice del progetto Funzioni di Azure in Azure.You can use a few different technologies to deploy your Azure Functions project code to Azure. In questo articolo viene fornito un elenco completo di tali tecnologie, vengono descritte le tecnologie disponibili per le quali sono disponibili le funzioni, viene illustrato cosa accade quando si utilizza ogni metodo e vengono forniti suggerimenti per il metodo migliore da utilizzare in vari scenari . I vari strumenti che supportano la distribuzione in Funzioni di Azure sono sintonizzati sulla tecnologia giusta in base al contesto. In general, zip deployment is the recommended deployment technology for Azure Functions.

## <a name="deployment-technology-availability"></a>Disponibilità della tecnologia di distribuzione

Azure Functions supports cross-platform local development and hosting on Windows and Linux. Attualmente sono disponibili tre piani di hosting:

+ [Consumo](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicato (servizio app)](functions-scale.md#app-service-plan)

Ogni piano ha comportamenti diversi. Non tutte le tecnologie di distribuzione sono disponibili per ogni versione di Funzioni di Azure.Non all deployment technologies are available for each flavor of Azure Functions. Il grafico seguente mostra quali tecnologie di distribuzione sono supportate per ogni combinazione di sistema operativo e piano di hosting:

| Tecnologia di distribuzione | Consumo di Windows | Windows Premium | Windows dedicato  | Consumo Di Linux | Linux Premium | Linux dedicato |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL pacchetto esterno<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Distribuzione zip |✔|✔|✔|✔|✔|✔|
| Contenitore Docker | | | | |✔|✔|
| Distribuzione Web |✔|✔|✔| | | |
| Controllo del codice sorgente |✔|✔|✔| |✔|✔|
| Git locale<sup>1</sup> |✔|✔|✔| |✔|✔|
| Sincronizzazione cloud<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Modifica del portale |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Tecnologia di distribuzione che richiede la [sincronizzazione manuale dei trigger.](#trigger-syncing)  
<sup>2</sup> La modifica del portale è abilitata solo per i trigger HTTP e Timer per Funzioni su Linux utilizzando piani Premium e Dedicated.

## <a name="key-concepts"></a>Concetti chiave

Alcuni concetti chiave sono fondamentali per comprendere il funzionamento delle distribuzioni in Funzioni di Azure.Some key concepts are critical to understanding how deployments work in Azure Functions.

### <a name="trigger-syncing"></a>Sincronizzazione dei triggerTrigger syncing

Quando si modifica uno dei trigger, l'infrastruttura di funzioni deve essere a conoscenza delle modifiche. La sincronizzazione avviene automaticamente per molte tecnologie di distribuzione. Tuttavia, in alcuni casi, è necessario sincronizzare manualmente i trigger. Quando si distribuiscono gli aggiornamenti facendo riferimento a un URL del pacchetto esterno, Git locale, sincronizzazione cloud o FTP, è necessario sincronizzare manualmente i trigger. È possibile sincronizzare i trigger in uno dei tre modi seguenti:You can sync triggers in one of three ways:

* Riavviare l'app per le funzioni nel portale di AzureRestart your function app in the Azure portal
* Inviare una richiesta `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` HTTP POST all'utilizzo della [chiave master](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Inviare una richiesta `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`HTTP POST a . Sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app per le funzioni.

### <a name="remote-build"></a>Compilazione remota

Funzioni di Azure possono eseguire automaticamente compilazioni sul codice ricevuto dopo distribuzioni zip. Queste build si comportano in modo leggermente diverso a seconda che l'app sia in esecuzione su Windows o Linux.These builds have have have slightly differentand depending on whether your app is running on Windows or Linux. Le compilazioni remote non vengono eseguite quando un'app è stata precedentemente impostata per l'esecuzione in modalità [Esegui dal pacchetto.](run-functions-from-deployment-package.md) Per informazioni su come utilizzare la compilazione remota, passare a [zip deploy](#zip-deploy).

> [!NOTE]
> Se si verificano problemi con la compilazione remota, è possibile che l'app sia stata creata prima che la funzionalità sia stata resa disponibile (1 agosto 2019). Provare a creare una nuova `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` app per le funzioni o a eseguire per aggiornare l'app per le funzioni. Questo comando potrebbe richiedere due tentativi per avere esito positivo.

#### <a name="remote-build-on-windows"></a>Compilazione remota in Windows

Tutte le app per le funzioni in esecuzione in Windows dispongono di una piccola app di gestione, ovvero il sito SCM (o [Kudu).](https://github.com/projectkudu/kudu) Questo sito gestisce gran parte della logica di distribuzione e compilazione per Funzioni di Azure.This site handles much of the deployment and build logic for Azure Functions.

Quando un'app viene distribuita in Windows, `dotnet restore` vengono eseguiti `npm install` comandi specifici del linguaggio, ad esempio (C)o (JavaScript).

#### <a name="remote-build-on-linux"></a>Compilazione remota su Linux

Per abilitare la compilazione remota in Linux, è necessario impostare le impostazioni [dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings) seguenti:To enable remote build on Linux, the following application settings must be set:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Per impostazione predefinita, sia Azure Functions Core Tools che Azure Functions Extension for Visual Studio Code eseguono compilazioni remote durante la distribuzione in Linux.By default, both [Azure Functions Core Tools](functions-run-local.md) and the Azure Functions Extension for Visual Studio [Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) perform remote builds when deploying to Linux. Per questo motivo, entrambi gli strumenti creano automaticamente queste impostazioni in Azure.Because this because, both tools automatically create these settings for you in Azure. 

Quando le app vengono create in modalità remota su Linux, vengono eseguite dal pacchetto di [distribuzione.](run-functions-from-deployment-package.md) 

##### <a name="consumption-plan"></a>Piano a consumo

Le app per le funzioni Linux in esecuzione nel piano di consumo non dispongono di un sito SCM/Kudu, che limita le opzioni di distribuzione. Tuttavia, le app per le funzioni in Linux in esecuzione nel piano consumo supportano le compilazioni remote.

##### <a name="dedicated-and-premium-plans"></a>Piani dedicati e Premium

Le app per le funzioni eseguite su Linux nel [piano dedicato (servizio app)](functions-scale.md#app-service-plan) e nel [piano Premium](functions-scale.md#premium-plan) dispongono anche di un sito SCM/Kudu limitato.

## <a name="deployment-technology-details"></a>Dettagli sulla tecnologia di distribuzione

In Funzioni di Azure sono disponibili i metodi di distribuzione seguenti.

### <a name="external-package-url"></a>URL pacchetto esterno

Puoi usare un URL del pacchetto esterno per fare riferimento a un file di pacchetto remoto (con estensione zip) che contiene l'app per le funzioni. Il file viene scaricato dall'URL fornito e l'app viene eseguita in modalità [Esegui da pacchetto.](run-functions-from-deployment-package.md)

>__Come usarlo:__ Aggiungere `WEBSITE_RUN_FROM_PACKAGE` alle impostazioni dell'applicazione. Il valore di questa impostazione deve essere un URL (il percorso del file del pacchetto specifico che si desidera eseguire). È possibile aggiungere impostazioni [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) o [tramite l'interfaccia della riga di comando](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)di Azure. 
>
>Se si usa l'archiviazione BLOB di Azure, usare un contenitore privato con una firma di [accesso condiviso per](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) concedere a Funzioni l'accesso al pacchetto. Ogni volta che l'applicazione viene riavviata, recupera una copia del contenuto. Il riferimento deve essere valido per tutta la durata dell'applicazione.

>__Quando usarlo:__ L'URL del pacchetto esterno è l'unico metodo di distribuzione supportato per Funzioni di Azure in esecuzione su Linux nel piano Consumo, se l'utente non desidera che si verifichi una [compilazione remota.](#remote-build) Quando si aggiorna il file del pacchetto a cui fa riferimento un'app per le funzioni, è necessario [sincronizzare manualmente i trigger](#trigger-syncing) per indicare ad Azure che l'applicazione è stata modificata.

### <a name="zip-deploy"></a>Distribuzione zip

Usare la distribuzione zip per eseguire il push di un file con estensione zip che contiene l'app per le funzioni in Azure.Use zip deploy to push a .zip file that contains your function app to Azure. Facoltativamente, puoi impostare l'app in modo che inizi [l'esecuzione dal pacchetto](run-functions-from-deployment-package.md)o specifichi che si verifica una [compilazione remota.](#remote-build)

>__Come usarlo:__ Eseguire la distribuzione utilizzando lo strumento client preferito: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure)o dalla riga di comando tramite Strumenti di base di Funzioni di [Azure](functions-run-local.md#project-file-deployment). Per impostazione predefinita, questi strumenti utilizzano la distribuzione zip ed [eseguiti dal pacchetto](run-functions-from-deployment-package.md). Strumenti di base e l'estensione del codice di Visual Studio abilitano entrambi la [compilazione remota](#remote-build) durante la distribuzione in Linux.Core Tools and the Visual Studio Code extension both enable remote build when deploying to Linux. Per distribuire manualmente un file con estensione zip nell'app per le funzioni, seguire le istruzioni in [Distribuire da un file con estensione zip o da un URL.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

>Quando si esegue la distribuzione tramite la distribuzione zip, è possibile impostare l'app per [l'esecuzione dal pacchetto](run-functions-from-deployment-package.md). Per eseguire da package, impostare il valore dell'impostazione dell'applicazione `WEBSITE_RUN_FROM_PACKAGE` su `1`. Si consiglia la distribuzione zip. Fornisce tempi di caricamento più rapidi per le applicazioni ed è l'impostazione predefinita per VISUAL Code, Visual Studio e l'interfaccia della riga di comando di Azure.It yields faster loading times for your applications, it's the default for VS Code, Visual Studio, and the Azure CLI. 

>__Quando usarlo:__ Zip deploy is the recommended deployment technology for Azure Functions.

### <a name="docker-container"></a>Contenitore Docker

È possibile distribuire un'immagine contenitore Linux che contiene l'app per le funzioni.

>__Come usarlo:__ Creare un'app per le funzioni Linux nel piano Premium o Dedicato e specificare l'immagine del contenitore da cui eseguire. Questa operazione può essere eseguita in due modi:
>
>* Creare un'app per le funzioni Linux in un piano di servizio app di Azure nel portale di Azure.Create a Linux function app on an Azure App Service plan in the Azure portal. In **Pubblica**selezionare **Immagine Docker**, quindi configurare il contenitore. Immettere la posizione in cui è ospitata l'immagine.
>* Creare un'app per le funzioni Linux in un piano di servizio app usando l'interfaccia della riga di comando di Azure.Create a Linux function app on an App Service plan by using the Azure CLI. Per ulteriori informazioni, consultate [Creare una funzione in Linux usando un'immagine personalizzata.](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Per distribuire in un'app esistente usando un contenitore personalizzato, in [Strumenti di base di Funzioni](functions-run-local.md)di Azure usare il [`func deploy`](functions-run-local.md#publish) comando.

>__Quando usarlo:__ Usare l'opzione Contenitore Docker quando è necessario un maggiore controllo sull'ambiente Linux in cui viene eseguita l'app per le funzioni. Questo meccanismo di distribuzione è disponibile solo per le funzioni in esecuzione su Linux.This deployment mechanism is available only for Functions running on Linux.

### <a name="web-deploy-msdeploy"></a>Distribuzione Web (MSDeploy)

Web Distribuire pacchetti e distribuire le applicazioni Windows in qualsiasi server IIS, incluse le app per le funzioni in esecuzione in Windows in Azure.

>__Come usarlo:__ Usare gli strumenti di [Visual Studio per Funzioni](functions-create-your-first-function-visual-studio.md)di Azure . Deselezionare la casella di controllo Esegui da file di **pacchetto (scelta consigliata).**
>
>È inoltre possibile scaricare Web Deploy `MSDeploy.exe` [3.6](https://www.iis.net/downloads/microsoft/web-deploy) e chiamare direttamente.

>__Quando usarlo:__ Distribuzione Web è supportata e non presenta problemi, ma il meccanismo preferito è [la distribuzione zip con l'opzione Esegui dal pacchetto abilitata](#zip-deploy). Per ulteriori informazioni, vedere la [Guida allo sviluppo](functions-develop-vs.md#publish-to-azure)di Visual Studio .

### <a name="source-control"></a>Controllo del codice sorgente

Usare il controllo del codice sorgente per connettere l'app per le funzioni a un repository Git.Use source control to connect your function app to a Git repository. Un aggiornamento al codice in tale repository attiva la distribuzione. Per ulteriori informazioni, vedere il [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Come usarlo:__ Utilizzare Centro distribuzione nell'area Funzioni del portale per impostare la pubblicazione dal controllo del codice sorgente. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

>__Quando usarlo:__ L'uso del controllo del codice sorgente è la procedura consigliata per i team che collaborano alle app per le funzioni. Il controllo del codice sorgente è una buona opzione di distribuzione che consente pipeline di distribuzione più sofisticate.

### <a name="local-git"></a>Repository Git locale

È possibile usare Git locale per eseguire il push del codice dal computer locale a Funzioni di Azure usando Git.You can use local Git to push code from your local machine to Azure Functions by using Git.

>__Come usarlo:__ Seguire le istruzioni in [Distribuzione Git locale nel servizio app di Azure](../app-service/deploy-local-git.md).

>__Quando usarlo:__ In generale, è consigliabile usare un metodo di distribuzione diverso. Quando si esegue la pubblicazione da Git locale, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronizzazione cloud

Usa la sincronizzazione cloud per sincronizzare i tuoi contenuti da Dropbox e OneDrive a Funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [Sincronizzare](../app-service/deploy-content-sync.md)il contenuto da una cartella cloud .

>__Quando usarlo:__ In generale, è consigliabile altri metodi di distribuzione. Quando si pubblica utilizzando la sincronizzazione cloud, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="ftp"></a>FTP

È possibile usare FTP per trasferire direttamente i file in Funzioni di Azure.You can use FTP to directly transfer files to Azure Functions.

>__Come usarlo:__ Seguire le istruzioni in [Distribuire contenuto tramite FTP/s](../app-service/deploy-ftp.md).

>__Quando usarlo:__ In generale, è consigliabile altri metodi di distribuzione. Quando si pubblica tramite FTP, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="portal-editing"></a>Modifica del portale

Nell'editor basato sul portale è possibile modificare direttamente i file presenti nell'app per le funzioni (essenzialmente la distribuzione ogni volta che si salvano le modifiche).

>__Come usarlo:__ Per poter modificare le funzioni nel portale di Azure, è necessario aver [creato le funzioni nel portale.](functions-create-first-azure-function.md) Per conservare una singola fonte di verità, l'utilizzo di qualsiasi altro metodo di distribuzione rende la funzione di sola lettura e impedisce la continuazione della modifica del portale. Per tornare a uno stato in cui è possibile modificare i file nel `Read/Write` portale di Azure, è possibile `WEBSITE_RUN_FROM_PACKAGE`ripristinare manualmente la modalità di modifica e rimuovere le impostazioni dell'applicazione correlate alla distribuzione (ad esempio ). 

>__Quando usarlo:__ Il portale è un buon modo per iniziare a usare Funzioni di Azure.The portal is a good way to get started with Azure Functions. Per operazioni di sviluppo più intense, si consiglia di utilizzare uno dei seguenti strumenti client:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Strumenti di base di Funzioni di Azure (riga di comando)Azure Functions Core Tools (command line)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Nella tabella seguente vengono illustrati i sistemi operativi e le lingue che supportano la modifica del portale:

| | Consumo di Windows | Windows Premium | Windows dedicato | Consumo Di Linux | Linux Premium | Linux dedicato |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Script C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (anteprima) | | | | | | |
| PowerShell (anteprima) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>La modifica del portale è abilitata solo per i trigger HTTP e Timer per Funzioni su Linux che utilizzano piani Premium e dedicati.

## <a name="deployment-slots"></a>Slot di distribuzione

Quando si distribuisce l'app per le funzioni in Azure, è possibile eseguire la distribuzione in uno slot di distribuzione separato anziché direttamente nell'ambiente di produzione. Per altre informazioni sugli slot di distribuzione, vedere la documentazione relativa [ai slot](../app-service/deploy-staging-slots.md) di distribuzione di Funzioni di Azure per informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

Leggere questi articoli per altre informazioni sulla distribuzione delle app per le funzioni:Read these articles to learn more about deploying your function apps: 

+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)
+ [Recapito continuo tramite DevOps di AzureContinuous delivery by using Azure DevOps](functions-how-to-azure-devops.md)
+ [Distribuzioni zip per funzioni di Azure](deployment-zip-push.md)
+ [Eseguire Funzioni di Azure da un file di pacchetto](run-functions-from-deployment-package.md)
+ [Automatizzare la distribuzione delle risorse per l'app per le funzioni in Funzioni di AzureAutomate resource deployment for your function app in Azure Functions](functions-infrastructure-as-code.md)
