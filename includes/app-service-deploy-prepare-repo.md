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
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836850"
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
| WebJobs | _\<nome_processo > / run. \<estensione >_ sotto _App\_dati/processi/continuous_ per i processi Web continui, oppure _App\_dati/processi/triggered_ per attivata Processi Web. Per altre informazioni, vedere [documentazione di Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file *.deployment* nella radice del repository. Per altre informazioni, vedere [personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per essere distribuita tramite Git.
>

