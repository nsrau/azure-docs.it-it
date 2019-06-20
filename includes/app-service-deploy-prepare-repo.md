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
ms.openlocfilehash: 7ec4028c319749b6a3da019e1d320d3937e9c4b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180620"
---
## <a name="prepare-your-repository"></a>Preparare il repository

Per ottenere le build automatica dal server di compilazione Kudu del servizio App di Azure, assicurarsi che sia la radice del repository di file corretti nel progetto.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _\*.sln_, _\*.csproj_ o _default.aspx_ |
| ASP.NET Core | file con estensione _\*.sln_ o _\*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con uno script di avvio |
| Python | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<nome_processo>/run.\<estensione>_ in _App\_Data/jobs/continuous_ (per Processi Web continui) o _App\_Data/jobs/triggered_ (per Processi Web attivati). Per altre informazioni, vedere [documentazione di Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file _.deployment_ nella radice del repository. Per altre informazioni, vedere [personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per essere distribuita tramite Git.
>
>

