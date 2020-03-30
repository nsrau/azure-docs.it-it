---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836850"
---
## <a name="prepare-your-repository"></a>Preparare il repository

Per ottenere compilazioni automatiche dal server di compilazione Kudu del servizio app di Azure, assicurarsi che la radice del repository contetti i file corretti nel progetto.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _\*.sln_, _\*.csproj_ o _default.aspx_ |
| ASP.NET Core | file con estensione _\*.sln_ o _\*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con uno script di avvio |
| Python | .py , _requirements.txt_o _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<job_name>/esecuzione. \<l'estensione>_ in _App\_Data/jobs/continuous_ per WebJobs continui o _Dati\_app/processi/attivati_ per I processi Web attivati. Per ulteriori informazioni, vedere la documentazione di [Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file *.deployment* nella radice del repository. Per ulteriori informazioni, vedere [Personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per essere distribuito tramite Git.
>

