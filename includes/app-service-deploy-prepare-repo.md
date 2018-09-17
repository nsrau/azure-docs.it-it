---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305615"
---
## <a name="prepare-your-repository"></a>Preparare il repository

Per ottenere build automatiche da server di compilazione Kudu di Servizio app, assicurarsi che la radice del repository includa i file corretti nel progetto.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _\*.sln_, _\*.csproj_ o _default.aspx_ |
| ASP.NET Core | file con estensione _\*.sln_ o _\*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con uno script di avvio |
| Python (solo Windows) | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<nome_processo>/run.\<estensione>_ in _App\_Data/jobs/continuous_ (per Processi Web continui) o _App\_Data/jobs/triggered_ (per Processi Web attivati). Per altre informazioni, vedere [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs) (Documentazione di Kudu sui Processi Web) |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Per personalizzare la distribuzione, includere un file _.deployment_ nella radice del repository. Per altre informazioni, vedere [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Personalizzazione delle distribuzioni) e [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Script di distribuzione personalizzata).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per la distribuzione tramite Git.
>
>

