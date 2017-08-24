---
title: Usare .NET Core in un'app Web in Linux | Microsoft Docs
description: Usare .NET Core in un'app Web in Linux.
keywords: Servizio app di Azure, app Web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Usare .NET Core in un'app Web di Azure in Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Un'[app Web](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) in Linux offre un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione basato sul sistema operativo Linux. Questa esercitazione contiene istruzioni dettagliate che illustrano come creare un'app [.NET Core](https://docs.microsoft.com/aspnet/core/) nell'app Web di Azure in Linux. 

![App Web in Linux][10]

È possibile eseguire queste procedure con un computer Mac, Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti ##

Per completare questa esercitazione: 

* Installare [.NET Core SDK](https://www.microsoft.com/net/download/core).
* Installare [Git](https://git-scm.com/downloads).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>Creare un'applicazione .NET Core locale ##

Avviare una nuova sessione terminal. Creare una directory denominata `hellodotnetcore` e passare dalla directory corrente ad essa. Digitare quindi quanto segue: 

```
dotnet new web
``` 

  Questo comando crea tre file (*hellodotnetcore.csproj*, *Program.cs* e *Startup.cs*) e una cartella vuota (*wwwroot/*) nella directory corrente. Il contenuto del file `.csproj` dovrebbe avere un aspetto simile al seguente: 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

Poiché di tratta di un'applicazione Web, al file *hellodotnetcore.csproj* è stato automaticamente aggiunto un riferimento a un pacchetto di ASP.NET Core. Il numero di versione del pacchetto viene impostato in base al framework selezionato. Questo esempio fa riferimento ad ASP.NET Core versione 1.1.2 perché è in uso .NET Core 1.1.

## <a name="build-and-test-the-application-locally"></a>Creare e testare l'applicazione in locale ##

È possibile compilare ed eseguire l'app .NET Core con il comando `dotnet restore` seguito dal comando `dotnet run`, come illustrato di seguito:

```
dotnet restore
dotnet run
```


All'avvio dell'applicazione, viene visualizzato un messaggio con cui si indica che l'app è in attesa di richieste in ingresso su una porta. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Testare l'app accedendo a `http://localhost:5000/` con il browser in uso. Se tutto funziona correttamente, viene visualizzato il testo "Hello World!".

![Eseguire il test con il browser][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Creare un'app .NET Core nel portale di Azure ##

Per prima cosa, è necessario creare un'app Web vuota. Accedere al [portale di Azure](https://portal.azure.com/) e creare una nuova [app Web in Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![Creazione di un'app Web][1]

Quando viene visualizzata la pagina **Crea**, specificare i dettagli relativi all'app Web:

![Scelta di uno stack di runtime .NET Core][2]

Usare la tabella seguente come guida per compilare la pagina **Crea** e quindi selezionare **OK** e **Crea** per creare l'app.

| Impostazione      | Valore consigliato  | Descrizione                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| Nome app | hellodotnetcore  | Nome dell'app. Il nome deve essere univoco. |
| Subscription | Scelta di una sottoscrizione esistente | Sottoscrizione di Azure. |
| Gruppo di risorse | myResourceGroup |  Gruppo di risorse di Azure che conterrà l'app Web. |
| Piano di servizio app | Nome del piano di servizio app esistente |  Piano di servizio app.  |
| Configura contenitore | .NET Core 1.1 | Tipo di contenitore per questa app Web: predefinito, Docker o registro di sistema privato. |
| Origine immagine  | Predefinito  |  Origine dell'immagine. |
| Stack di runtime  | .NET Core 1.1  | Stack di runtime e versione.  |

## <a name="deploy-your-application-via-git"></a>Distribuire l'applicazione tramite Git ##

Usare Git per distribuire l'applicazione .NET Core nell'app Web del Servizio app di Azure.

Il nuovo sito Web di Azure ha già una distribuzione Git configurata. È possibile trovare l'URL di distribuzione Git passando all'URL seguente dopo l'inserimento del nome dell'app Web:

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

L'URL Git ha il formato seguente, in base al nome dell'app Web:

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

Eseguire i comandi seguenti per distribuire l'applicazione locale nel sito Web di Azure: 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

Non è necessario eseguire il push dei file presenti nelle directory *bin /* e *obj /* perché l'applicazione viene compilata nel cloud quando viene eseguito il push dei file di origine dell'applicazione in Azure. Al termine del processo di compilazione, i file binari vengono copiati nella directory dell'applicazione in */home/site/wwwroot/*.

Verificare che per le operazioni di distribuzione in remoto venga segnalato l'esito positivo. Le operazioni di push possono richiedere alcuni minuti perché la risoluzione del pacchetto e il processo di compilazione vengono eseguiti nel cloud. Verranno visualizzati alcuni messaggi di stato, di cui uno informa che i file sono stati copiati. L'output dovrebbe essere simile al seguente:

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

Dopo aver completato la distribuzione, riavviare l'app Web affinché la distribuzione venga applicata. A questo scopo, accedere al portale di Azure e passare alla pagina **Panoramica** dell'app Web. Selezionare il pulsante **Riavvia** nella pagina. Quando viene visualizzata una finestra popup, selezionare **Sì** per confermare. È quindi possibile passare all'app Web, come illustrato di seguito:

![Esplorazione dell'app .NET Core distribuita nel servizio app di Azure in Linux][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti su App Web del Servizio app di Azure su Linux](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

