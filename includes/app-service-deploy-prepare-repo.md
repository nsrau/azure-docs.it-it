---
title: includere il file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67836850"
---
## <a name="prepare-your-repository"></a>Preparare il repository

Per ottenere le compilazioni automatiche dal server di compilazione app Azure Service Kudu, assicurarsi che nella radice del repository siano presenti i file corretti nel progetto.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _\*.sln_, _\*.csproj_ o _default.aspx_ |
| ASP.NET Core | _*. sln_ o _*. csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_o _package.jsin_ con uno script di avvio |
| Python | _ \* . py_, _requirements.txt_o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ in _ \_ dati app/processi/continua_ per i processi Web continui o _ \_ dati/processi dell'app/attivati_ per i processi Web attivati. Per ulteriori informazioni, vedere la [documentazione relativa ai processi Web Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file *.deployment* nella radice del repository. Per altre informazioni, vedere [personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [lo script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per essere distribuito tramite git.
>

