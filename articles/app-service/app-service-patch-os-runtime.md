---
title: Applicazione di patch a sistema operativo e runtime in Servizio app di Azure | Microsoft Docs
description: Descrive in che modo Servizio app di Azure aggiorna il sistema operativo e i runtime e come ottenere annunci relativi agli aggiornamenti.
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.openlocfilehash: 869bd0e3f684ff4a2291e189cf247daedfb74922
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Applicazione di patch a sistema operativo e runtime in Servizio app di Azure

Questo articolo illustra come ottenere determinate informazioni sulla versione per il sistema operativo o il software nel [servizio app](app-service-web-overview.md). 

Il servizio app è una soluzione PaaS (piattaforma distribuita come servizio), che significa che il sistema operativo e lo stack di applicazioni sono gestiti da Azure, mentre l'utente gestisce solo l'applicazione e i relativi dati. Per un maggiore controllo sul sistema operativo e sullo stack di applicazioni, usare [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/). Tenendo presente questo aspetto, per un utente del servizio app è comunque utile conoscere informazioni come:

-   Come e quando vengono applicati gli aggiornamenti del sistema operativo?
-   Come vengono applicate nel servizio app le patch per le vulnerabilità significative (ad esempio per gli attacchi zero-day)?
-   Quali versioni del sistema operativo e del runtime eseguono le app?

Per motivi di sicurezza, alcuni dati relativi alle informazioni di sicurezza non vengono pubblicati. Lo scopo dell'articolo è però quello di mitigare le preoccupazioni offrendo la massima trasparenza sul processo e informazioni su come mantenersi aggiornati sugli annunci correlati alla sicurezza o sugli aggiornamenti del runtime.

## <a name="how-and-when-are-os-updates-applied"></a>Come e quando vengono applicati gli aggiornamenti del sistema operativo?

Azure gestisce l'applicazione di patch del sistema operativo su due livelli, i server fisici e le macchine virtuali (VM) guest che eseguono le risorse del servizio app. In entrambi i casi l'aggiornamento avviene mensilmente, in linea con la pianificazione mensile per le [patch](https://technet.microsoft.com/security/bulletins.aspx). Questi aggiornamenti vengono applicati automaticamente in modo da garantire il contratto di servizio a disponibilità elevata di Azure. 

Per informazioni dettagliate su come vengono applicati gli aggiornamenti, vedere [Demystifying the magic behind App Service OS updates](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/) (I segreti sugli aggiornamenti del sistema operativo del servizio app svelati).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Come vengono gestite in Azure le vulnerabilità significative?

Quando gravi vulnerabilità richiedono l'applicazione immediata di patch, ad esempio in caso di [vulnerabilità zero-day](https://wikipedia.org/wiki/Zero-day_(computing)), gli aggiornamenti ad alta priorità vengono gestiti valutando caso per caso.

Per tenersi aggiornati sugli annunci di sicurezza critici in Azure, visitare il [blog sulla sicurezza di Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Quando vengono aggiornati, aggiunti o deprecati i runtime di linguaggio supportati?

Nuove versioni stabili dei runtime di linguaggio supportati (principale, secondaria o patch) vengono aggiunte periodicamente alle istanze del servizio app. Alcuni aggiornamenti sovrascrivono l'installazione esistente, mentre altri vengono installati affiancati alle versioni esistenti. In caso di sovrascrittura, l'app viene eseguita automaticamente nel runtime aggiornato. In caso di installazione affiancata, è necessario eseguire manualmente la migrazione dell'app per sfruttare i vantaggi di una nuova versione del runtime. Per altre informazioni, vedere una delle sezioni secondarie.

Gli annunci relativi a runtime aggiornati e deprecati sono disponibili qui:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Le informazioni fornite si applicano ai runtime di linguaggio integrati in un'app del servizio app. Un runtime personalizzato caricato nel servizio app, ad esempio, rimane invariato a meno che non lo si aggiorni manualmente.
>
>

### <a name="new-patch-updates"></a>Nuove patch di aggiornamento

Le patch di aggiornamento per la versione di .NET, PHP, Java SDK o Tomcat/Jetty vengono applicate automaticamente sovrascrivendo l'installazione esistente con la nuova versione. Le patch di aggiornamento di Node.js vengono installate affiancate alle versioni esistenti (in modo analogo alle versioni principale e secondaria nella sezione successiva). Le nuove versioni delle patch di Python possono essere installate manualmente tramite le [estensioni del sito](https://www.siteextensions.net/packages?q=Tags%3A%22python%22), affiancate alle installazioni predefinite di Python.

### <a name="new-major-and-minor-versions"></a>Nuove versioni principali e secondarie

Quando viene aggiunta una nuova versione principale o secondaria, questa viene installata affiancata alle versioni esistenti. È possibile aggiornare manualmente l'app alla nuova versione. Se la versione del runtime è stata definita in un file di configurazione (ad esempio `web.config` e `package.json`), è necessario eseguire l'aggiornamento con lo stesso metodo. Se per configurare la versione del runtime è stata usata un'impostazione del servizio app, è possibile modificare la versione nel [portale di Azure](https://portal.azure.com) o eseguendo un comando dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in [Cloud Shell](../cloud-shell/overview.md), come illustrato negli esempi seguenti:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Versioni deprecate

Quando una versione precedente diventa deprecata, viene annunciata la data di rimozione per consentire di pianificare l'aggiornamento della versione del runtime di conseguenza. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Come è possibile eseguire query relative allo stato di aggiornamento del sistema operativo e del runtime nelle istanze in uso?

Mentre l'accesso alle informazioni critiche del sistema operativo non è consentito (vedere [Funzionalità del sistema operativo in Servizio app di Azure](web-sites-available-operating-system-functionality.md)), la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) consente di eseguire query sull'istanza del servizio app relative alla versione del sistema operativo e alle versioni del runtime. 

La tabella seguente illustra come ottenere informazioni sulle versioni di Windows e del runtime di linguaggio che le app eseguono:

| Informazioni | Posizione |
|-|-|
| Versione di Windows | Vedere `https://<appname>.scm.azurewebsites.net/Env.cshtml` (nelle informazioni di sistema) |
| Versione di .NET | In `https://<appname>.scm.azurewebsites.net/DebugConsole` eseguire il comando seguente al prompt dei comandi: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Versione di .NET Core | In `https://<appname>.scm.azurewebsites.net/DebugConsole` eseguire il comando seguente al prompt dei comandi: <br> `dotnet --version` |
| Versione di PHP | In `https://<appname>.scm.azurewebsites.net/DebugConsole` eseguire il comando seguente al prompt dei comandi: <br> `php --version` |
| Versione predefinita di Node.js | In [Cloud Shell](../cloud-shell/overview.md) eseguire il comando seguente: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Versione di Python | In `https://<appname>.scm.azurewebsites.net/DebugConsole` eseguire il comando seguente al prompt dei comandi: <br> `python --version` |

> [!NOTE]
> L'accesso al percorso del Registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, dove sono archiviate le informazioni sulle [patch "KB"]((https://technet.microsoft.com/security/bulletins.aspx)), è bloccato.
>
>

## <a name="more-resources"></a>Altre risorse

[Centro protezione: Sicurezza](https://www.microsoft.com/TrustCenter/Security/default.aspx)  
[64 bit ASP.NET Core on Azure App Service (ASP.NET Core a 64 bit in Servizio app di Azure)](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
