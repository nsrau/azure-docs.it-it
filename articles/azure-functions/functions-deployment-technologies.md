---
title: Tecnologie di distribuzione in funzioni di Azure | Microsoft Docs
description: Informazioni sui diversi modi in cui è possibile distribuire il codice in funzioni di Azure.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: a0c34fcc70d92f98a6d72e4cd2fc78d34d863d55
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650465"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologie di distribuzione in funzioni di Azure

È possibile usare alcune tecnologie diverse per distribuire il codice del progetto di funzioni di Azure in Azure. Questo articolo fornisce un elenco completo di queste tecnologie, descrive le tecnologie disponibili per i diversi tipi di funzioni, spiega cosa accade quando si usa ogni metodo e fornisce consigli per il metodo migliore da usare in diversi scenari . I vari strumenti che supportano la distribuzione in funzioni di Azure sono ottimizzati per la tecnologia corretta in base al contesto. In generale, la distribuzione di zip è la tecnologia di distribuzione consigliata per funzioni di Azure.

## <a name="deployment-technology-availability"></a>Disponibilità della tecnologia di distribuzione

Funzioni di Azure supporta lo sviluppo locale multipiattaforma e l'hosting in Windows e Linux. Attualmente sono disponibili tre piani di hosting:

+ [Consumo](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicato (servizio app)](functions-scale.md#app-service-plan)

Ogni piano ha comportamenti diversi. Non tutte le tecnologie di distribuzione sono disponibili per ogni versione di funzioni di Azure. Nel grafico seguente vengono illustrate le tecnologie di distribuzione supportate per ogni combinazione di sistema operativo e piano di hosting:

| Tecnologia di distribuzione | Utilizzo di Windows | Windows Premium (anteprima) | Windows dedicato  | Consumo Linux | Linux Premium (anteprima) | Linux dedicato |
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

<sup>1</sup> tecnologia di distribuzione che richiede la [sincronizzazione manuale del trigger](#trigger-syncing).  
<sup>2</sup> la modifica del portale è abilitata solo per i trigger http e timer per le funzioni in Linux con piani Premium e dedicati.

## <a name="key-concepts"></a>Concetti chiave

Alcuni concetti chiave sono fondamentali per comprendere il funzionamento delle distribuzioni in funzioni di Azure.

### <a name="trigger-syncing"></a>Attivazione della sincronizzazione

Quando si modifica uno dei trigger, l'infrastruttura di funzioni deve essere in grado di riconoscere le modifiche. La sincronizzazione avviene automaticamente per molte tecnologie di distribuzione. Tuttavia, in alcuni casi, è necessario sincronizzare manualmente i trigger. Quando si distribuiscono gli aggiornamenti facendo riferimento a un URL di pacchetto esterno, git locale, Cloud Sync o FTP, è necessario sincronizzare manualmente i trigger. È possibile sincronizzare i trigger in uno dei tre modi seguenti:

* Riavviare l'app per le funzioni nella portale di Azure
* Inviare una richiesta HTTP post all' `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizzo della [chiave master](functions-bindings-http-webhook.md#authorization-keys).
* Inviare una richiesta HTTP POST a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app per le funzioni.

### <a name="remote-build"></a>Compilazione remota

Funzioni di Azure può eseguire automaticamente le compilazioni sul codice ricevuto dopo le distribuzioni zip. Queste compilazioni hanno un comportamento leggermente diverso a seconda che l'app sia in esecuzione in Windows o Linux. Le compilazioni remote non vengono eseguite quando un'app è stata impostata in precedenza per l'esecuzione in modalità di [esecuzione del pacchetto](run-functions-from-deployment-package.md) . Per informazioni su come usare la compilazione remota, passare a [zip deploy](#zip-deploy).

> [!NOTE]
> Se si verificano problemi con la compilazione remota, è possibile che l'app sia stata creata prima che la funzionalità venisse resa disponibile (1 agosto 2019). Provare a creare una nuova app per le funzioni.

#### <a name="remote-build-on-windows"></a>Compilazione remota in Windows

Tutte le app per le funzioni in esecuzione in Windows hanno una piccola app di gestione, il sito SCM (o [Kudu](https://github.com/projectkudu/kudu)). Questo sito gestisce gran parte della logica di distribuzione e compilazione per funzioni di Azure.

Quando un'app viene distribuita in Windows, vengono eseguiti i comandi specifici `dotnet restore` dellaC#lingua, `npm install` ad esempio () o (JavaScript).

#### <a name="remote-build-on-linux-preview"></a>Compilazione remota su Linux (anteprima)

Per abilitare la compilazione remota in Linux, è necessario impostare le [impostazioni dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings)seguenti:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Quando le app vengono compilate in remoto in Linux, vengono [eseguite dal pacchetto di distribuzione](run-functions-from-deployment-package.md).

> [!NOTE]
> La compilazione remota nel piano Linux dedicato (servizio app) è attualmente supportata solo per node. js e Python.

##### <a name="consumption-preview-plan"></a>Piano di utilizzo (anteprima)

Le app per le funzioni di Linux in esecuzione nel piano a consumo non dispongono di un sito SCM/Kudu, che limita le opzioni di distribuzione. Tuttavia, le app per le funzioni in Linux in esecuzione nel piano a consumo supportano le compilazioni remote.

##### <a name="dedicated-and-premium-preview-plans"></a>Piani dedicati e Premium (anteprima)

Le app per le funzioni in esecuzione in Linux nel [piano dedicato (servizio app)](functions-scale.md#app-service-plan) e nel [piano Premium](functions-scale.md#premium-plan) hanno anche un sito SCM/Kudu limitato.

## <a name="deployment-technology-details"></a>Dettagli sulla tecnologia di distribuzione

In funzioni di Azure sono disponibili i metodi di distribuzione seguenti.

### <a name="external-package-url"></a>URL del pacchetto esterno

È possibile usare un URL di pacchetto esterno per fare riferimento a un file di pacchetto remoto (con estensione zip) che contiene l'app per le funzioni. Il file viene scaricato dall'URL fornito e l'app viene eseguita in modalità [esecuzione da pacchetto](run-functions-from-deployment-package.md) .

>__Come usarlo:__ Aggiungere `WEBSITE_RUN_FROM_PACKAGE` le impostazioni dell'applicazione. Il valore di questa impostazione deve essere un URL, ovvero il percorso del file di pacchetto specifico che si desidera eseguire. È possibile aggiungere le impostazioni [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) o tramite [l'interfaccia della riga di comando di Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se si usa l'archiviazione BLOB di Azure, usare un contenitore privato con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) per concedere alle funzioni l'accesso al pacchetto. Ogni volta che l'applicazione viene riavviata, recupera una copia del contenuto. Il riferimento deve essere valido per la durata dell'applicazione.

>__Quando utilizzarlo:__ L'URL del pacchetto esterno è l'unico metodo di distribuzione supportato per funzioni di Azure in esecuzione in Linux nel piano a consumo, se l'utente non vuole che si verifichi una compilazione remota. Quando si aggiorna il file del pacchetto a cui fa riferimento un'app per le funzioni, è necessario [sincronizzare manualmente i trigger](#trigger-syncing) per indicare ad Azure che l'applicazione è stata modificata.

### <a name="zip-deploy"></a>Distribuzione zip

Usare la distribuzione zip per eseguire il push di un file con estensione zip che contiene l'app per le funzioni in Azure. Facoltativamente, è possibile impostare l'avvio [dell'esecuzione dell'app dal pacchetto](run-functions-from-deployment-package.md)o specificare che si verifica una [compilazione remota](#remote-build) .

>__Come usarlo:__ Eseguire la distribuzione usando lo strumento client preferito: [Vs code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)o l'interfaccia della riga di comando di [Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Per distribuire manualmente un file con estensione zip nell'app per le funzioni, seguire le istruzioni in [distribuire da un file zip o un URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

Per eseguire una distribuzione zip con una [compilazione remota](#remote-build), usare il seguente comando [degli strumenti principali](functions-run-local.md) :

```bash
func azure functionapp publish <app name> --build remote
```

In alternativa, è possibile impostare VS Code per eseguire una compilazione remota durante la distribuzione aggiungendo il flag '' azureFunctions. scmDoBuildDuringDeployment '. Per informazioni su come aggiungere un flag a VS Code, leggere le istruzioni nel [wiki dell'estensione funzioni di Azure](https://github.com/microsoft/vscode-azurefunctions/wiki).

>Quando si esegue la distribuzione tramite zip deploy, è possibile impostare l'applicazione per l' [esecuzione dal pacchetto](run-functions-from-deployment-package.md). Per eseguire dal pacchetto, impostare il `WEBSITE_RUN_FROM_PACKAGE` valore dell'impostazione dell' `1`applicazione su. Si consiglia la distribuzione di zip. Produce tempi di caricamento più rapidi per le applicazioni ed è il valore predefinito per VS Code, Visual Studio e l'interfaccia della riga di comando di Azure. 

>__Quando utilizzarlo:__ Zip Deploy è la tecnologia di distribuzione consigliata per funzioni di Azure.

### <a name="docker-container"></a>Contenitore Docker

È possibile distribuire un'immagine del contenitore Linux che contiene l'app per le funzioni.

>__Come usarlo:__ Creare un'app per le funzioni Linux nel piano Premium o dedicato e specificare l'immagine del contenitore da cui eseguire. Questa operazione può essere eseguita in due modi:
>
>* Creare un'app per le funzioni Linux in un piano di servizio app Azure nel portale di Azure. Per **Publish**selezionare **Docker image**e quindi Configure the container. Immettere il percorso in cui è ospitata l'immagine.
>* Creare un'app per le funzioni di Linux in un piano di servizio app usando l'interfaccia della riga di comando di Azure. Per informazioni, vedere [creare una funzione in Linux usando un'immagine personalizzata](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Per eseguire la distribuzione in un'app esistente usando un contenitore personalizzato, in [Azure Functions Core Tools](functions-run-local.md)usare il [`func deploy`](functions-run-local.md#publish) comando.

>__Quando utilizzarlo:__ Usare l'opzione contenitore Docker quando è necessario un maggiore controllo sull'ambiente Linux in cui viene eseguita l'app per le funzioni. Questo meccanismo di distribuzione è disponibile solo per le funzioni in esecuzione su Linux.

### <a name="web-deploy-msdeploy"></a>Distribuzione Web (MSDeploy)

Distribuzione Web i pacchetti e distribuisce le applicazioni Windows in qualsiasi server IIS, incluse le app per le funzioni in esecuzione in Windows in Azure.

>__Come usarlo:__ Usare [gli strumenti di Visual Studio per funzioni di Azure](functions-create-your-first-function-visual-studio.md). Deselezionare la casella **di controllo Esegui da file pacchetto (scelta consigliata)** .
>
>È anche possibile scaricare [distribuzione Web 3,6](https://www.iis.net/downloads/microsoft/web-deploy) e chiamare `MSDeploy.exe` direttamente.

>__Quando utilizzarlo:__ Distribuzione Web è supportato e non ha problemi, ma il meccanismo preferito è la [distribuzione zip con l'esecuzione dal pacchetto abilitato](#zip-deploy). Per ulteriori informazioni, vedere la [Guida allo sviluppo di Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controllo del codice sorgente

Usare il controllo del codice sorgente per connettere l'app per le funzioni a un repository git. Un aggiornamento del codice in tale repository attiva la distribuzione. Per ulteriori informazioni, vedere il [wiki di Kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Come usarlo:__ Per configurare la pubblicazione dal controllo del codice sorgente, usare centro distribuzione nell'area funzioni del portale. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

>__Quando utilizzarlo:__ L'uso del controllo del codice sorgente è la procedura consigliata per i team che collaborano alle app per le funzioni. Il controllo del codice sorgente è un'opzione di distribuzione efficace che consente pipeline di distribuzione più sofisticate.

### <a name="local-git"></a>Repository Git locale

È possibile usare git locale per eseguire il push del codice dal computer locale in funzioni di Azure usando git.

>__Come usarlo:__ Seguire le istruzioni riportate in [distribuzione git locale nel servizio app Azure](../app-service/deploy-local-git.md).

>__Quando utilizzarlo:__ In generale, è consigliabile usare un metodo di distribuzione diverso. Quando si esegue la pubblicazione da git locale, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronizzazione cloud

Usare la sincronizzazione cloud per sincronizzare il contenuto da Dropbox e OneDrive in funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni riportate in [sincronizzare il contenuto da una cartella Cloud](../app-service/deploy-content-sync.md).

>__Quando utilizzarlo:__ In generale, è consigliabile usare altri metodi di distribuzione. Quando si esegue la pubblicazione usando la sincronizzazione cloud, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="ftp"></a>FTP

È possibile usare FTP per trasferire direttamente i file in funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [distribuire il contenuto tramite FTP/s](../app-service/deploy-ftp.md).

>__Quando utilizzarlo:__ In generale, è consigliabile usare altri metodi di distribuzione. Quando si esegue la pubblicazione tramite FTP, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="portal-editing"></a>Modifica del portale

Nell'editor basato su portale è possibile modificare direttamente i file presenti nell'app per le funzioni (essenzialmente distribuendo ogni volta che si salvano le modifiche).

>__Come usarlo:__ Per poter modificare le funzioni nella portale di Azure, è necessario aver [creato le funzioni nel portale](functions-create-first-azure-function.md). Per mantenere un'unica origine di verità, l'utilizzo di qualsiasi altro metodo di distribuzione rende la funzione di sola lettura e impedisce la modifica continua del portale. Per tornare a uno stato in cui è possibile modificare i file nella portale di Azure, è possibile riattivare manualmente la modalità di `Read/Write` modifica e rimuovere le impostazioni dell'applicazione relative alla distribuzione (ad esempio `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando utilizzarlo:__ Il portale è un modo efficace per iniziare a usare funzioni di Azure. Per un lavoro di sviluppo più intenso, è consigliabile usare uno degli strumenti client seguenti:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (riga di comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Nella tabella seguente sono illustrati i sistemi operativi e i linguaggi che supportano la modifica del portale:

| | Utilizzo di Windows | Windows Premium (anteprima) | Windows dedicato | Consumo Linux | Linux Premium (anteprima)| Linux dedicato |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| Script C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (anteprima) | | | | | | |
| PowerShell (anteprima) |✔|✔|✔| | | |
| TypeScript (node. js) | | | | | | |

<sup>*</sup>La modifica del portale è abilitata solo per i trigger HTTP e timer per le funzioni in Linux con piani Premium e dedicati.

## <a name="deployment-slots"></a>Slot di distribuzione

Quando si distribuisce l'app per le funzioni in Azure, è possibile eseguire la distribuzione in uno slot di distribuzione separato invece che direttamente nell'ambiente di produzione. Per ulteriori informazioni sugli slot di distribuzione, vedere la documentazione relativa agli [slot di distribuzione di funzioni di Azure](../app-service/deploy-staging-slots.md) per informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

Leggere questi articoli per altre informazioni sulla distribuzione delle app per le funzioni: 

+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)
+ [Recapito continuo tramite Azure DevOps](functions-how-to-azure-devops.md)
+ [Distribuzioni zip per funzioni di Azure](deployment-zip-push.md)
+ [Eseguire le funzioni di Azure da un file di pacchetto](run-functions-from-deployment-package.md)
+ [Automatizzare la distribuzione delle risorse per l'app per le funzioni in funzioni di Azure](functions-infrastructure-as-code.md)
