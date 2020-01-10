---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769668"
---
## <a name="rest"></a>Distribuire il file con estensione zip con le API REST 

È possibile usare le [API REST del servizio distribuzione](https://github.com/projectkudu/kudu/wiki/REST-API) per distribuire il file ZIP nella propria app in Azure. Per eseguire la distribuzione, inviare una richiesta POST all'indirizzo https://<app_name>.scm.azurewebsites.net/api/zipdeploy. La richiesta POST deve contenere il file ZIP nel corpo del messaggio. Le credenziali di distribuzione per l'app vengono fornite nella richiesta usando l'autenticazione di base HTTP. Per altre informazioni, vedere le [informazioni di riferimento sulla distribuzione push tramite file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Per l'autenticazione HTTP di base sono necessarie le credenziali di distribuzione del servizio app. Per informazioni su come impostare le credenziali di distribuzione, vedere [Impostare e reimpostare le credenziali a livello di utente](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

L'esempio seguente usa lo strumento cURL per distribuire un file con estensione zip. Sostituire i segnaposto `<deployment_user>`, `<zip_file_path>` e `<app_name>`. Quando richiesto da cURL, digitare la password.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Questa richiesta attiva la distribuzione push dal file ZIP caricato. È possibile esaminare le distribuzioni correnti e passate usando l'endpoint `https://<app_name>.scm.azurewebsites.net/api/deployments`, come illustrato nell'esempio cURL seguente. Anche in questo caso, sostituire il segnaposto `<app_name>` con il nome dell'app e `<deployment_user>` con il nome utente delle credenziali di distribuzione.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Con PowerShell

L'esempio seguente usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) per caricare il file zip. Sostituire i segnaposto `<group-name>`, `<app-name>` e `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Questa richiesta attiva la distribuzione push dal file ZIP caricato. 

Per esaminare le distribuzioni correnti e precedenti, eseguire i comandi seguenti. Anche in questo caso, sostituire i segnaposto `<deployment-user>`, `<deployment-password>`e `<app-name>`.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
