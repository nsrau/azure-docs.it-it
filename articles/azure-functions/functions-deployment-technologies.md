---
title: Tecnologie di distribuzione in funzioni di Azure | Microsoft Docs
description: Informazioni su vantaggi e svantaggi dei diversi modi, che è possibile distribuire codice in funzioni di Azure.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 118daf02ab59646f2926071763aa4d7e97846e04
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508229"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologie di distribuzione in funzioni di Azure

Esistono alcune tecnologie diverse, che è possibile usare per distribuire il codice del progetto funzioni di Azure in Azure. Questo articolo fornisce un elenco completo di queste tecnologie, indica a quali tecnologie disponibili per i gusti delle funzioni, viene spiegato ciò che accade in effetti quando ogni metodo viene utilizzato e vengono forniti consigli per il metodo migliore da usare in vari scenari. I vari strumenti che supportano la distribuzione in funzioni di Azure sono ottimizzati per la tecnologia più adatta in base al contesto.

## <a name="deployment-technology-availability"></a>Disponibilità di tecnologia di distribuzione

> [!IMPORTANT]
> Funzioni di Azure supporta tra lo sviluppo locale di piattaforma e l'hosting nei due sistemi operativi: Windows e Linux. Sono disponibili tre piani di hosting attualmente disponibili, ognuno con i diversi comportamenti - [consumi](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), e [dedicato (servizio App)](functions-scale.md#app-service-plan). Non tutte le tecnologie di distribuzione disponibili per ogni versione di funzioni di Azure.

| Tecnologia di distribuzione | Utilizzo di Windows | Premium di Windows (anteprima) | Windows dedicato  | Utilizzo di Linux (anteprima) | Linux dedicata |
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
<sup>2</sup> portale modifica è abilitata per solo i trigger di Timer e HTTP per le funzioni in Linux usando il piano dedicato.

## <a name="key-concepts"></a>Concetti chiave

Prima di continuare, è importante conoscere alcuni concetti che risulteranno fondamentali per comprendere il funzionano delle distribuzioni in funzioni di Azure.

### <a name="trigger-syncing"></a>La sincronizzazione di trigger

Quando si modificano uno qualsiasi dei trigger, l'infrastruttura di funzioni deve essere a conoscenza di queste modifiche. Questa sincronizzazione viene eseguita automaticamente per molte tecnologie di distribuzione. Tuttavia, in alcuni casi è necessario sincronizzare manualmente il trigger. Quando si distribuiscono gli aggiornamenti con un URL del pacchetto esterno, locale Git, sincronizzazione cloud o FTP, è necessario assicurarsi di sincronizzare manualmente il trigger. È possibile sincronizzare i trigger in uno dei tre modi:

* Riavviare l'app per le funzioni nel portale di Azure
* Inviare una richiesta HTTP POST al `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` usando il [chiave master](functions-bindings-http-webhook.md#authorization-keys).
* Inviare una richiesta HTTP POST a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Sostituire i segnaposto con l'ID sottoscrizione, nome del gruppo di risorse e il nome dell'app per le funzioni.

## <a name="deployment-technology-details"></a>Informazioni dettagliate sulla tecnologia di distribuzione  

Questi metodi di distribuzione seguenti sono supportati da funzioni di Azure.

### <a name="external-package-url"></a>URL del pacchetto esterno

Consente di fare riferimento a un file remoto pacchetto (zip) che contiene l'app per le funzioni. Il file viene scaricato dall'URL specificato e l'app viene eseguita [Run-da-Package](run-functions-from-deployment-package.md) modalità.

>__Come usarlo:__ Aggiungere `WEBSITE_RUN_FROM_PACKAGE` alle impostazioni dell'applicazione. Il valore di questa impostazione deve essere un URL - il percorso del file di pacchetto specifico da eseguire. È possibile aggiungere impostazioni entrambe [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) oppure [tramite la CLI di Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Se si Usa archiviazione blob di Azure, è consigliabile usare un contenitore privato con un [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) agli funzioni l'accesso al pacchetto. In qualsiasi momento del riavvio dell'applicazione recupera una copia del contenuto, il che significa che il riferimento deve essere valido per la durata dell'applicazione.

>__Quando usarlo:__ Questo è il metodo di distribuzione solo supportato per le funzioni di Azure in esecuzione su Linux nel piano a consumo (anteprima). Quando si aggiorna il file del pacchetto fa riferimento a un'app per le funzioni, è necessario [sincronizzare manualmente i trigger](#trigger-syncing) per indicare ad Azure che l'applicazione è stato modificato.

### <a name="zip-deploy"></a>La distribuzione di ZIP

Consente di eseguire il push di un file zip contenente l'app per le funzioni in Azure. Facoltativamente, è inoltre possibile specificare per avviare l'app [Run-da-Package](run-functions-from-deployment-package.md) modalità.

>__Come usarlo:__ Eseguire la distribuzione usando i tuoi Preferiti dello strumento client, [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), o la [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Per distribuire manualmente un file zip per app per le funzioni, seguire le istruzioni disponibili alla [distribuzione da un file con estensione zip o un url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Inoltre, quando si distribuisce la distribuzione tramite file zip, gli utenti possono specificare per eseguire le app da in [Run-da-Package](run-functions-from-deployment-package.md) modalità impostando il `WEBSITE_RUN_FROM_PACKAGE` valore dell'impostazione dell'applicazione come `1`. Questa opzione viene suggerita e produce tempi di caricamento per le applicazioni. Questa operazione viene eseguita per impostazione predefinita per gli strumenti client precedente.

>__Quando usarlo:__ Questa è la tecnologia di distribuzione consigliato per le funzioni di Azure in esecuzione su Windows e funzioni di Azure in esecuzione su Linux nel piano dedicato.

### <a name="docker-container"></a>Contenitore docker

Distribuire un'immagine del contenitore Linux che contiene l'app per le funzioni.

>__Come usarlo:__ Creare un'app per Linux nel piano dedicato e specificare l'immagine contenitore da eseguire da. Questa operazione può essere eseguita in due modi:
>
>* Creare un'app per Linux in un piano di servizio App nel portale di Azure. Selezionare **immagine Docker** per **Publish**e configurare il contenitore, che fornisce la posizione in cui viene ospitata l'immagine.
>* Creare un'app per Linux in un piano di servizio App tramite la CLI di Azure. Informazioni su come esaminando [creare una funzione in Linux usando un'immagine personalizzata](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Per distribuire un'app esistente di un contenitore personalizzato, usare il [ `func deploy` ](functions-run-local.md#publish) del comando il [Azure Functions Core Tools](functions-run-local.md).

>__Quando usarlo:__ Usare questa opzione quando è necessario un maggiore controllo sull'ambiente Linux in cui viene eseguita l'app per le funzioni. Questo meccanismo di distribuzione è disponibile solo per le funzioni in esecuzione su Linux in un piano di servizio App.

### <a name="web-deploy-msdeploy"></a>Distribuzione Web (MSDeploy)

I pacchetti e distribuisce le applicazioni di Windows a qualsiasi server IIS, incluse le App per le funzioni in esecuzione su Windows in Azure.

>__Come usarlo:__ Usare la [Visual Studio tools per funzioni di Azure](functions-create-your-first-function-visual-studio.md)e deselezionare il `Run from package file (recommended)` casella.
>
> È anche possibile scaricare [Web distribuire 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e chiamare `MSDeploy.exe` direttamente.

>__Quando usarlo:__ Questa tecnologia di distribuzione è supportata e non è presenti problemi, ma ora è il meccanismo preferito [Zipdeploy eseguiti dal pacchetto abilitato](#zip-deploy). Per altre informazioni, visitare il [Guida di sviluppo di Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controllo del codice sorgente

Consente di connettere l'app per le funzioni in un repository git in modo che tutti gli aggiornamenti al codice in tale repository attiva la distribuzione. Per altre informazioni, esaminare i [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Come usarlo:__ Usare il centro di distribuzione nel portale di funzioni di Azure per configurare la pubblicazione dal controllo del codice sorgente. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

>__Quando usarlo:__ L'uso di controllo del codice sorgente è la procedura consigliata per i team di collaborare alle app di funzione e si tratta di un'ottima opzione che consente più sofisticate pipeline di distribuzione.

### <a name="local-git"></a>Archivio git locale

Consente il push di codice dal computer locale le funzioni di Azure tramite Git.

>__Come usarlo:__ Seguire le istruzioni in [distribuzione Git locale nel servizio App di Azure di](../app-service/deploy-local-git.md).

>__Quando usarlo:__ In generale, sono consigliati altri metodi di distribuzione. Quando si pubblica dall'archivio git locale, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronizzazione cloud

Consente di sincronizzare il contenuto da OneDrive e Dropbox a funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [sincronizzare il contenuto da una cartella nel cloud](../app-service/deploy-content-sync.md).

>__Quando usarlo:__ In generale, sono consigliati altri metodi di distribuzione. Durante la pubblicazione con sincronizzazione cloud, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="ftp"></a>FTP

È possibile direttamente i file in funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [distribuire il contenuto usando FTP/s](../app-service/deploy-ftp.md).

>__Quando usarlo:__ In generale, sono consigliati altri metodi di distribuzione. Quando si pubblica con il protocollo FTP, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="portal-editing"></a>Modifica del portale

Usando l'editor basato sul portale consente di modificare direttamente i file nella tua app di funzione (distribuisce sostanzialmente ogni volta che si fa clic su **salvare**).

>__Come usarlo:__ Per essere in grado di modificare le funzioni nel portale di Azure, è necessario disporre [creati le funzioni nel portale di](functions-create-first-azure-function.md). Con qualsiasi altro metodo di distribuzione rende una funzione di sola lettura e impedisce continuo del portale di modifica, per mantenere un'unica fonte di veridicità. Per tornare a uno stato in cui è possibile modificare i file usando il portale di Azure, è possibile attivare manualmente la modalità di modifica verso `Read/Write` e rimuovere tutte le impostazioni relative alla distribuzione dell'applicazione (ad esempio `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usarlo:__ Il portale è un ottimo modo per iniziare a usare funzioni di Azure, ma per sviluppare soluzioni più intenso è il client usando gli strumenti sono consigliabile:
>
>* [Introduzione all'uso di Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Iniziare a usare Azure Functions Core Tools](functions-run-local.md)
>* [Introduzione all'uso di Visual Studio](functions-create-your-first-function-visual-studio.md)

Nella tabella seguente mostra i sistemi operativi e le lingue per cui è supportata la modifica del portale:

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

<sup>*</sup> Modifica del portale è abilitata per solo i trigger di Timer e HTTP per le funzioni in Linux usando il piano dedicato.

## <a name="deployment-slots"></a>Slot di distribuzione

Quando si distribuisce l'app per le funzioni in Azure, è possibile distribuire in uno slot di distribuzione distinto anziché direttamente nell'ambiente di produzione. Per altre informazioni sugli slot di distribuzione, vedere [la documentazione di Azure App Service slot](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Livelli di supporto per gli slot di distribuzione

Esistono due livelli di supporto:

* _Disponibile a livello generale_ - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* _Anteprima_ - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.

| Piano di Hosting/sistema operativo | Livello di supporto |
| --------------- | ------ |
| Utilizzo di Windows | Anteprima |
| Windows Premium (anteprima) | Anteprima |
| Windows dedicato | Disponibile a livello generale |
| Utilizzo di Linux | Non supportato |
| Linux dedicata | Disponibile a livello generale |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione di App per le funzioni disponibili negli articoli seguenti: 

+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)
+ [Distribuzione continua tramite Azure DevOps](functions-how-to-azure-devops.md)
+ [Distribuzioni con estensione zip per funzioni di Azure](deployment-zip-push.md)
+ [Eseguire funzioni di Azure da un file di pacchetto](run-functions-from-deployment-package.md)
+ [Automatizzare la distribuzione di risorse per app per le funzioni in funzioni di Azure](functions-infrastructure-as-code.md)
