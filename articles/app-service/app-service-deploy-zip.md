---
title: Distribuire l'applicazione di servizio App di Azure con un file ZIP | Documenti Microsoft
description: Informazioni su come distribuire l'applicazione di servizio App di Azure con un file ZIP.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Distribuire l'applicazione di servizio App di Azure con un file ZIP

In questo articolo viene illustrato come utilizzare un file ZIP per distribuire l'applicazione web per [Azure App Service](app-service-web-overview.md). 

Questa distribuzione di file ZIP utilizza lo stesso servizio Kudu che rafforza continua basata sull'integrazione con le distribuzioni. Per la distribuzione del file ZIP, kudu supporta le funzionalità seguenti: 

- L'eliminazione dei file rimasti da una distribuzione precedente.
- Opzione per attivare il processo di compilazione predefinito, che include il ripristino del pacchetto.
- [Personalizzazione distribuzione](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), tra cui l'esecuzione di script di distribuzione.  
- Registri di distribuzione. 

Per ulteriori informazioni, vedere [documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo:

* [Creare un'app del servizio app](/azure/app-service/) oppure usare un'app creata per un'altra esercitazione.

## <a name="create-a-project-zip-file"></a>Creare un file ZIP di progetto

>[!NOTE]
> Se hai scaricato i file in un file ZIP, estrarre innanzitutto i file. Ad esempio, se un file ZIP scaricato da GitHub, è possibile distribuire il file come-è. GitHub aggiunge ulteriori directory annidate, che non funzionano con il servizio App. 
>

In una finestra terminal locale, passare alla directory radice del progetto dell'app. 

Questa directory deve contenere il file di voce per l'app web, ad esempio _index.html_, _index.php_, e _app.js_. Può inoltre contenere file di gestione del pacchetto come _Project_, _composer.json_, _package. JSON_, _bower. JSON_e _requirements.txt_.

Creare un archivio ZIP di tutti gli elementi del progetto. Il comando seguente usa lo strumento predefinito nel terminale:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Caricare il file ZIP alla Shell Cloud

Se si sceglie di eseguire l'interfaccia CLI di Azure dal terminale locale, invece, ignorare questo passaggio.

Seguire questa procedura per caricare il file ZIP per la Shell di Cloud. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Per ulteriori informazioni, vedere [mantenere i file nella Shell di Cloud di Azure](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Distribuire un file ZIP

Distribuire il file ZIP caricato all'app web utilizzando il [az webapp distribuzione origine configurazione-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) comando. Se si sceglie di non utilizzare la Shell di Cloud, assicurarsi che la versione di CLI di Azure è 2.0.21 o versione successiva. Per stabilire quale versione è disponibile, eseguire `az --version` comando nella finestra del terminale locale. 

Nell'esempio seguente consente di distribuire il file ZIP che è stata caricata. Quando si utilizza un'installazione locale di CLI di Azure, specificare il percorso del file ZIP locale per `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Questo comando consente di distribuire i file e directory di file ZIP file nella cartella dell'applicazione di servizio App predefinita (`\home\site\wwwroot`) e riavvia l'app. Se è configurato alcun processo di compilazione personalizzato aggiuntivo, viene eseguito anche. Per ulteriori informazioni, vedere [documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Per visualizzare l'elenco delle distribuzioni per questa applicazione, è necessario utilizzare le API REST (vedere la sezione successiva). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](app-service-deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Kudu: La distribuzione da un file zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenziali per la distribuzione del Servizio app di Azure](app-service-deploy-ftp.md)
