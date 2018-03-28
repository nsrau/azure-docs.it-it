---
title: Profilare app Web Linux di Azure ASP.NET Core con Application Insights Profiler | Microsoft Docs
description: Panoramica concettuale ed esercitazione dettagliata su come abilitare la profilatura
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2d7405baee84b53311f01e748ca7975147c107d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilare app Web Linux di Azure ASP.NET Core con Application Insights Profiler

Questa funzionalità è attualmente in anteprima

Determinare il tempo impiegato in ogni metodo dell'applicazione Web attiva con [Application Insights](app-insights-overview.md). Il profiler è ora disponibile per app Web ASP.NET Core ospitate in Linux in Servizi app. Questa guida fornisce istruzioni dettagliate su come raccogliere tracce del profiler per app Web Linux ASP.NET Core.

Dopo aver completato questa procedura dettagliata, l'app raccoglierà le tracce del profiler in modo simile a quello mostrato nello screenshot seguente. In questo esempio, la traccia del profiler indica che una richiesta Web specifica è lenta perché la maggior parte del tempo viene impiegata nell'attesa. Il percorso critico nel codice che ha rallentato l'app è preceduto da un'icona che raffigura una fiamma. Questo esempio mostra che il metodo `About` in `HomeController` ha rallentato l'app Web a causa della chiamata di `Thread.Sleep`.

![Tracce del profiler](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Prerequisiti
Istruzioni seguenti applicate a tutti gli ambienti di sviluppo Windows, Linux e Mac:

* Installare [.NET Core SDK 2.1.2 o versioni successive](https://www.microsoft.com/net/download/windows/build)
* Installare Git seguendo le istruzioni disponibili in [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Introduzione - Installazione di Git)

## <a name="setup-project-locally"></a>Configurare il progetto in locale

1. Aprire un prompt dei comandi nel computer. Le istruzioni seguenti sono valide per tutti gli ambienti di sviluppo Windows, Linux e Mac.

2. Creare un'applicazione Web ASP.NET Core MVC
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Nel prompt dei comandi cambiare directory per passare alla cartella radice del progetto

4. Aggiungere il pacchetto Nuget per la raccolta delle tracce del profiler.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Aggiungere una riga di codice per definire un ritardo casuale di pochi secondi in HomeController.cs

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. Salvare le modifiche ed eseguirne il commit nel repository locale

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Creare un'istanza di Servizio app di Azure per ospitare il progetto
1. Creare un ambiente Servizi app Linux

    ![Creare un ambiente Servizi app Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Creare le credenziali di distribuzione. Annotare la password, che sarà necessaria in seguito per la distribuzione dell'app.

    ![Creare le credenziali di distribuzione](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Scegliere l'opzione di distribuzione. Configurare un repository Git locale nell'app Web seguendo le istruzioni nel portale di Azure. Verrà automaticamente creato un repository Git.

    ![Configurare il repository Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Altre opzioni di distribuzione sono disponibili [qui](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)

## <a name="deploy-your-project"></a>Distribuire il progetto

1. Nel prompt dei comandi passare alla cartella root del progetto. Aggiungere il repository Git remoto in modo che punti a un ambiente Servizi app:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Usare il nome utente specificato nel passaggio "Creare le credenziali di distribuzione".
    * Usare il nome dell'app specificato nel passaggio di creazione del servizio app.

2. Distribuire il progetto tramite il push delle modifiche in Azure

    ```
    git push azure master
    ```
L'output sarà simile al seguente:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Aggiungere Application Insights per monitorare le app Web
1. [Creare una risorsa di Application Insights](./app-insights-create-new-resource.md)
2. Copiare il valore iKey della risorsa di Application Insights e configurare le impostazioni seguenti in Servizi app

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Configurare le impostazioni delle app](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    La modifica delle impostazioni delle app comporta il riavvio automatico del sito. Una volta applicate le nuove impostazioni, viene immediatamente avviata l'esecuzione del profiler per due minuti. Il profiler viene quindi eseguito per due minuti ogni ora.

3. Generare traffico nel sito Web. È possibile aggiornare la pagina ```About``` del sito diverse volte.

4. Attendere 2-5 minuti in modo che gli eventi possano essere aggregati ad Application Insights.

5. Passare al riquadro delle prestazioni di Application Insights nel portale di Azure. Le tracce del profiler saranno disponibili nell'angolo in basso a destra.

    ![Visualizzare le tracce](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="next-steps"></a>Passaggi successivi
Se si usano contenitori personalizzati ospitati da Servizi app, seguire le istruzioni in [Enable Service Profiler for containerized ASP.NET Core application](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) (Abilitare Profiler di servizi per l'applicazione ASP.NET Core in contenitore) per abilitare App Insights Profiler

In caso di problemi o per fornire suggerimenti, è possibile immettere una segnalazione nel repository GitHub: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues) (ApplicationInsights-Profiler-AspNetCore: problemi)
