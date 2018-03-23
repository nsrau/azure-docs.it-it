---
title: Distribuire l'app in Servizio app di Azure con un file ZIP o WAR | Microsoft Docs
description: Informazioni su come distribuire l'app in Servizio app di Azure con un file ZIP (o un file WAR per sviluppatori Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: 41fb529f6b4ae923f2920919306324c86a2baa45
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuire l'app in Servizio app di Azure con un file ZIP o WAR

Questo articolo descrive come usare un file ZIP o WAR per distribuire l'app Web in [Servizio app di Azure](app-service-web-overview.md). 

La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua. Kudu supporta le funzionalità seguenti per la distribuzione tramite file ZIP: 

- Eliminazione dei file rimasti da una distribuzione precedente.
- Opzione per attivare il processo di compilazione predefinito, che include il ripristino del pacchetto.
- [Personalizzazione della distribuzione](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), inclusa l'esecuzione di script di distribuzione.  
- Log di distribuzione. 

Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

La distribuzione del file WAR distribuisce il file [WAR](https://wikipedia.org/wiki/WAR_(file_format)) in Servizio app di Azure per l'esecuzione dell'app Web Java. Vedere [Distribuire il file WAR](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

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

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuire il file ZIP con l'interfaccia della riga di comando di Azure

Assicurarsi che la versione dell'interfaccia della riga di comando di Azure sia 2.0.21 o una versione successiva. Per stabilire la versione in uso, eseguire il comando `az --version` nella finestra del terminale.

Distribuire il file ZIP caricato nell'app Web usando il comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip).  

L'esempio seguente distribuisce il file ZIP caricato. Quando si utilizza un'installazione locale dell'interfaccia della riga di comando di Azure, specificare il percorso del file ZIP locale per `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Questo comando distribuisce i file e le directory del file ZIP nella cartella predefinita dell'applicazione del servizio app (`\home\site\wwwroot`) e riavvia l'app. Verranno eseguiti anche eventuali processi d compilazione personalizzati aggiuntivi presenti. Per altre informazioni, vedere [Documentazione Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuire il file WAR

Per distribuire un file WAR in Servizio app di Azure, inviare una richiesta POST a https://<nome_app>.scm.azurewebsites.net/api/wardeploy. La richiesta POST deve contenere il file WAR nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP. 

Per l'autenticazione HTTP di base, sono necessarie le credenziali di distribuzione di Servizio app di Azure. Per informazioni su come impostare le credenziali di distribuzione, vedere [Impostare e reimpostare le credenziali a livello di utente](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

L'esempio seguente usa lo strumento cURL per distribuire un file WAR. Sostituire i segnaposto `<username>`, `<war_file_path>` e `<app_name>`. Quando richiesto da cURL, digitare la password.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Con PowerShell

L'esempio seguente usa [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) per inviare una richiesta contenente il file WAR. Sostituire i segnaposto `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` e `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

## <a name="next-steps"></a>Passaggi successivi

Per scenari di distribuzione più avanzati, provare a eseguire la [distribuzione in Azure con Git](app-service-deploy-local-git.md). La distribuzione basata su Git in Azure abilita il controllo della versione, il ripristino del pacchetto, MSBuild e altro ancora.

## <a name="more-resources"></a>Altre risorse

* [Kudu: distribuzione da un file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenziali per la distribuzione del Servizio app di Azure](app-service-deploy-ftp.md)
