---
title: includere il file
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
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
| Node.js | _Server. js_, _app. js_o _Package. JSON_ con uno script di avvio |
| Python | . py, _requirements. txt_o _Runtime. txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<job_name>/Run. estensione \<>_ in _dati\_/processi dell'app/continua_ per i processi Web continui _o\_dati/processi dell'app/attivati_ per i processi Web attivati. Per ulteriori informazioni, vedere la [documentazione relativa ai processi Web Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file *.deployment* nella radice del repository. Per altre informazioni, vedere [personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [lo script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Il progetto è immediatamente pronto per essere distribuito tramite git.
>

