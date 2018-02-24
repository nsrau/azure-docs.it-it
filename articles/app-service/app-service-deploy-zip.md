---
title: Distribuire l'app nel servizio app di Azure con un file ZIP | Documentazione Microsoft
description: Informazioni su come distribuire l'app nel servizio app di Azure con un file ZIP.
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Distribuire l'app nel servizio app di Azure con un file ZIP

Questo articolo illustra come usare un file ZIP per distribuire l'app Web nel [servizio app di Azure](app-service-web-overview.md). 

La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua. Kudu supporta le funzionalità seguenti per la distribuzione tramite file ZIP: 

- Eliminazione dei file rimasti da una distribuzione precedente.
- Opzione per attivare il processo di compilazione predefinito, che include il ripristino del pacchetto.
- [Personalizzazione della distribuzione](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inclusa l'esecuzione di script di distribuzione.  
- Log di distribuzione. 

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>prerequisiti

Per seguire la procedura descritta in questo articolo:

* [Creare un'app del servizio app](/azure/app-service/) oppure usare un'app creata per un'altra esercitazione.

## <a name="create-a-project-zip-file"></a>Creare un file ZIP del progetto

>[!NOTE]
> Se i file sono stati scaricati in un file ZIP, occorre innanzitutto estrarli. Ad esempio, se è stato scaricato un file ZIP da GitHub, non è possibile distribuire il file così com'è. GitHub aggiunge altre directory annidate che non funzionano con il servizio app. 
>

In una finestra terminale locale, passare alla directory radice del progetto dell'app. 

Questa directory deve contenere il file di ingresso dell'app Web, ad esempio _index.html_, _index.php_ e _app.js_. Può inoltre contenere file di gestione del pacchetto come _project.json_, _composer.json_, _package.json_, _bower.json_ e _requirements.txt_.

Creare un archivio ZIP per tutti gli elementi del progetto. Il comando seguente usa lo strumento predefinito nel terminale:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Caricare il file ZIP in Cloud Shell

Se si sceglie di eseguire l'interfaccia della riga di comando di Azure dal terminale locale, invece, ignorare questo passaggio.

Seguire questa procedura per caricare il file ZIP in Cloud Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Per altre informazioni, vedere [Rendere persistenti i file in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Distribuire un file ZIP

Distribuire il file ZIP caricato nell'app Web usando il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Se si sceglie di non utilizzare Cloud Shell, assicurarsi che la versione dell'interfaccia della riga di comando di Azure sia 2.0.21 o una versione successiva. Per stabilire la versione in uso, eseguire il comando `az --version` nella finestra terminale locale. 

L'esempio seguente distribuisce il file ZIP caricato. Quando si utilizza un'installazione locale dell'interfaccia della riga di comando di Azure, specificare il percorso del file ZIP locale per `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Questo comando distribuisce i file e le directory del file ZIP nella cartella predefinita dell'applicazione del servizio app (`\home\site\wwwroot`) e riavvia l'app. Verranno eseguiti anche eventuali processi d compilazione personalizzati aggiuntivi presenti. Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Per visualizzare l'elenco delle distribuzioni per questa app, è necessario utilizzare le API REST (vedere la sezione successiva). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](app-service-deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Kudu: distribuzione da un file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenziali per la distribuzione del Servizio app di Azure](app-service-deploy-ftp.md)
