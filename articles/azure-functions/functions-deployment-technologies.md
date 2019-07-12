---
title: Tecnologie di distribuzione in funzioni di Azure | Microsoft Docs
description: Scopri i diversi modi, è possibile distribuire codice in funzioni di Azure.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594403"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologie di distribuzione in funzioni di Azure

È possibile utilizzare tecnologie diverse per distribuire il codice del progetto funzioni di Azure in Azure. Questo articolo fornisce un elenco completo di queste tecnologie, vengono illustrate le tecnologie disponibili per i gusti delle funzioni, spiega cosa accade quando si usa ogni metodo e vengono forniti consigli per il metodo migliore da usare nei vari scenari . I vari strumenti che supportano la distribuzione in funzioni di Azure sono ottimizzati per la tecnologia più adatta in base al contesto.

## <a name="deployment-technology-availability"></a>Disponibilità di tecnologia di distribuzione

> [!IMPORTANT]
> Funzioni di Azure supporta più piattaforme sviluppo locale e l'hosting in Windows e Linux. Tre piani di hosting sono attualmente disponibili: [Consumo](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), e [dedicato (servizio App di Azure)](functions-scale.md#app-service-plan). Ogni piano ha comportamenti diversi. Non tutte le tecnologie di distribuzione disponibili per ogni versione di funzioni di Azure.

| Tecnologia di distribuzione | Utilizzo di Windows | Windows Premium (anteprima) | Windows dedicato  | Utilizzo di Linux (anteprima) | Linux dedicata |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| URL del pacchetto esterno<sup>1</sup> |✔|✔|✔|✔|✔|
| La distribuzione di ZIP |✔|✔|✔| |✔|
| Contenitore docker | | | | |✔|
| Distribuzione Web |✔|✔|✔| | |
| Controllo del codice sorgente |✔|✔|✔| |✔|
| Local Git<sup>1</sup> |✔|✔|✔| |✔|
| Cloud Sincronizza<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Modifica del portale |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> tecnologia di distribuzione che richiede [la sincronizzazione manuale del trigger](#trigger-syncing).  
<sup>2</sup> portale modifica è abilitata solo per i trigger HTTP e Timer per le funzioni in Linux usando il piano dedicato.

## <a name="key-concepts"></a>Concetti chiave

Alcuni concetti essenziali sono fondamentali per comprendere il funzionano delle distribuzioni in funzioni di Azure.

### <a name="trigger-syncing"></a>La sincronizzazione di trigger

Quando si modificano uno qualsiasi dei trigger, l'infrastruttura di funzioni deve essere consapevole delle modifiche. La sincronizzazione viene eseguita automaticamente per molte tecnologie di distribuzione. Tuttavia, in alcuni casi, è necessario sincronizzare manualmente il trigger. Quando si distribuiscono gli aggiornamenti facendo riferimento a un URL del pacchetto esterno, locale Git, sincronizzazione cloud o FTP, è necessario sincronizzare manualmente il trigger. È possibile sincronizzare i trigger in uno dei tre modi:

* Riavviare l'app per le funzioni nel portale di Azure
* Inviare una richiesta HTTP POST al `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` usando il [chiave master](functions-bindings-http-webhook.md#authorization-keys).
* Inviare una richiesta HTTP POST a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Sostituire i segnaposto con l'ID sottoscrizione, nome del gruppo di risorse e il nome dell'app per le funzioni.

## <a name="deployment-technology-details"></a>Informazioni dettagliate sulla tecnologia di distribuzione 

Metodi di distribuzione seguenti sono disponibili in funzioni di Azure.

### <a name="external-package-url"></a>URL del pacchetto esterno

È possibile utilizzare un URL del pacchetto esterno per fare riferimento a un file remoto pacchetto (zip) che contiene l'app per le funzioni. Il file viene scaricato dall'URL specificato e l'app viene eseguita [eseguiti dal pacchetto](run-functions-from-deployment-package.md) modalità.

>__Come usarlo:__ Aggiungere `WEBSITE_RUN_FROM_PACKAGE` alle impostazioni dell'applicazione. Il valore di questa impostazione deve essere un URL (il percorso del file del pacchetto specifico da eseguire). È possibile aggiungere impostazioni entrambe [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) oppure [tramite la CLI di Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se si Usa archiviazione Blob di Azure, usare un contenitore privato con un [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) agli funzioni l'accesso al pacchetto. In qualsiasi momento del riavvio dell'applicazione, recupera una copia del contenuto. Il riferimento deve essere valido per la durata dell'applicazione.

>__Quando usarlo:__ URL del pacchetto esterno è il metodo di distribuzione supportati solo per le funzioni di Azure in esecuzione su Linux nel piano a consumo (anteprima). Quando si aggiorna il file del pacchetto che fa riferimento a un'app per le funzioni, è necessario [sincronizzare manualmente i trigger](#trigger-syncing) per indicare ad Azure che l'applicazione è stato modificato.

### <a name="zip-deploy"></a>La distribuzione di ZIP

La distribuzione di zip di utilizzo per il push di un file con estensione zip che contiene l'app per le funzioni in Azure. Facoltativamente, è possibile impostare l'app di avvio [eseguiti dal pacchetto](run-functions-from-deployment-package.md) modalità.

>__Come usarlo:__ Distribuire tramite lo strumento client preferito: [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), oppure il [CLI Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Per distribuire manualmente un file con estensione zip per app per le funzioni, seguire le istruzioni riportate in [Distribuisci da un file con estensione zip o URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Quando si distribuisce tramite file zip, distribuire, è possibile impostare l'app venga eseguita [eseguiti dal pacchetto](run-functions-from-deployment-package.md) modalità. Per impostare la modalità di esecuzione dal pacchetto, impostare il `WEBSITE_RUN_FROM_PACKAGE` valore dell'impostazione dell'applicazione per `1`. È consigliabile la distribuzione con estensione zip. Produce tempi di caricamento per le applicazioni ed è il valore predefinito per Visual Studio Code, Visual Studio e la CLI di Azure.

>__Quando usarlo:__ La distribuzione di ZIP è la tecnologia di distribuzione consigliato per le funzioni di Azure in esecuzione su Windows e per le funzioni di Azure in esecuzione su Linux nel piano dedicato.

### <a name="docker-container"></a>Contenitore docker

È possibile distribuire un'immagine del contenitore Linux che contiene l'app per le funzioni.

>__Come usarlo:__ Creare un'app per Linux nel piano dedicato e specificare quale immagine del contenitore da eseguire. Questa operazione può essere eseguita in due modi:
>
>* Creare un'app per Linux in un piano di servizio App di Azure nel portale di Azure. Per la **Publish**, selezionare **immagine Docker**e quindi configurare il contenitore. Immettere il percorso in cui è ospitata l'immagine.
>* Creare un'app per Linux in un piano di servizio App tramite la CLI di Azure. Per altre informazioni, vedere [creare una funzione in Linux usando un'immagine personalizzata](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Per distribuire un'app esistente usando un contenitore personalizzato, in [Azure Functions Core Tools](functions-run-local.md), utilizzare il [ `func deploy` ](functions-run-local.md#publish) comando.

>__Quando usarlo:__ Usare l'opzione del contenitore Docker quando è necessario maggiore controllo sull'ambiente Linux in cui viene eseguita l'app per le funzioni. Questo meccanismo di distribuzione è disponibile solo per le funzioni in esecuzione su Linux in un piano di servizio App.

### <a name="web-deploy-msdeploy"></a>Distribuzione Web (MSDeploy)

La funzionalità distribuzione Web dei pacchetti e distribuisce le applicazioni di Windows a qualsiasi server IIS, incluse le App per le funzioni in esecuzione su Windows in Azure.

>__Come usarlo:__ Uso [Visual Studio tools per funzioni di Azure](functions-create-your-first-function-visual-studio.md). Cancella il **eseguito dal file di pacchetto (scelta consigliata)** casella di controllo.
>
>È anche possibile scaricare [Web distribuire 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e chiamare `MSDeploy.exe` direttamente.

>__Quando usarlo:__ La funzionalità distribuzione Web è supportata e non è presenti problemi, ma il meccanismo preferito prevede [zip distribuiti con eseguiti dal pacchetto abilitato](#zip-deploy). Per altre informazioni, vedere la [Guida di sviluppo di Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controllo del codice sorgente

Usare il codice sorgente per la connessione di app per le funzioni in un repository Git. Un aggiornamento al codice in tale repository attiva la distribuzione. Per altre informazioni, vedere la [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Come usarlo:__ Usare il centro di distribuzione nel portale di funzioni di Azure per configurare la pubblicazione dal controllo del codice sorgente. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

>__Quando usarlo:__ Controllo del codice sorgente è la procedura consigliata per i team che collaborano a loro App per le funzioni. Controllo del codice sorgente è un'opzione di distribuzione efficace che consente più sofisticate pipeline di distribuzione.

### <a name="local-git"></a>Repository Git locale

È possibile usare Git locale al codice push dal computer locale in funzioni di Azure tramite Git.

>__Come usarlo:__ Seguire le istruzioni in [distribuzione Git locale in Azure App Service](../app-service/deploy-local-git.md).

>__Quando usarlo:__ In generale, è consigliabile utilizzare un metodo di distribuzione diversi. Quando si pubblica dall'archivio Git locale, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronizzazione cloud

Usare il cloud le sincronizzazioni del contenuto da OneDrive e Dropbox a funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [sincronizzare il contenuto da una cartella nel cloud](../app-service/deploy-content-sync.md).

>__Quando usarlo:__ In generale, è consigliabile altri metodi di distribuzione. Quando si pubblica tramite sincronizzazione cloud, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="ftp"></a>FTP

È possibile utilizzare FTP per trasferire direttamente i file in funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [distribuire il contenuto usando FTP/s](../app-service/deploy-ftp.md).

>__Quando usarlo:__ In generale, è consigliabile altri metodi di distribuzione. Quando esegue la pubblicazione tramite FTP, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="portal-editing"></a>Modifica del portale

Nell'editor basato sul portale, è possibile modificare direttamente i file nelle app per le funzioni (distribuisce sostanzialmente ogni volta che si salvano le modifiche).

>__Come usarlo:__ Per essere in grado di modificare le funzioni nel portale di Azure, è necessario disporre [creati le funzioni nel portale di](functions-create-first-azure-function.md). Per mantenere un'unica fonte di verità, con qualsiasi altro metodo di distribuzione rende la funzione di sola lettura e impedisce la modifica del portale continuo. Per tornare a uno stato in cui è possibile modificare i file nel portale di Azure, è possibile attivare manualmente la modalità di modifica verso `Read/Write` e rimuovere tutte le impostazioni relative alla distribuzione dell'applicazione (ad esempio `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usarlo:__ Il portale è un buon metodo per iniziare a usare funzioni di Azure. Per più intensa attività di sviluppo, è consigliabile usare il client degli strumenti:
>
>* [Introduzione all'uso di Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Iniziare a usare Azure Functions Core Tools](functions-run-local.md)
>* [Introduzione all'uso di Visual Studio](functions-create-your-first-function-visual-studio.md)

La tabella seguente illustra i sistemi operativi e linguaggi di tale modifica del portale di supporto:

| | Utilizzo di Windows | Windows Premium (anteprima) | Windows dedicato | Utilizzo di Linux (anteprima) | Linux dedicata |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| Script C# |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (anteprima) | | | | | |
| PowerShell (anteprima) |✔|✔|✔| | |
| TypeScript (Node. js) | | | | | |

<sup>*</sup> Modifica del portale è abilitata solo per i trigger HTTP e Timer per le funzioni in Linux usando il piano dedicato.

## <a name="deployment-slots"></a>Slot di distribuzione

Quando si distribuisce l'app per le funzioni in Azure, è possibile distribuire in uno slot di distribuzione distinto anziché distribuire direttamente nell'ambiente di produzione. Per altre informazioni sugli slot di distribuzione, vedere [slot del servizio App di Azure](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Livelli di supporto per gli slot di distribuzione

Esistono due livelli di supporto per gli slot di distribuzione:

* **Disponibilità generale (GA)** : Completamente supportate e approvate per la produzione.
* **Anteprima**: Non è ancora supportato, ma si prevede di raggiungere lo stato di disponibilità a livello generale in futuro.

| Piano di hosting/sistema operativo | Livello di supporto |
| --------------- | ------ |
| Utilizzo di Windows | Anteprima |
| Windows Premium (anteprima) | Anteprima |
| Windows dedicato | Disponibilità generale |
| Utilizzo di Linux | Non supportate |
| Linux dedicata | Disponibilità generale |

## <a name="next-steps"></a>Passaggi successivi

Leggere questi articoli per altre informazioni sulla distribuzione di App per le funzioni: 

+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)
+ [Recapito continuo tramite Azure DevOps](functions-how-to-azure-devops.md)
+ [Distribuzioni con estensione zip per funzioni di Azure](deployment-zip-push.md)
+ [Eseguire funzioni di Azure da un file di pacchetto](run-functions-from-deployment-package.md)
+ [Automatizzare la distribuzione di risorse per app per le funzioni in funzioni di Azure](functions-infrastructure-as-code.md)
