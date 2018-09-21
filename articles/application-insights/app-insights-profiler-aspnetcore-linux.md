---
title: Profilare app Web ASP.NET Core Linux di Azure con Application Insights Profiler | Microsoft Docs
description: Panoramica concettuale ed esercitazione dettagliata su come usare Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 6fcc11b0120c9d19cfc1482100ac68d04c9d625d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35635259"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilare app Web ASP.NET Core Linux di Azure con Application Insights Profiler

Questa funzionalità è attualmente in anteprima.

Determinare il tempo impiegato in ogni metodo dell'applicazione Web attiva con [Application Insights](app-insights-overview.md). Application Insights Profiler è ora disponibile per le app Web ASP.NET Core ospitate in Linux in Servizio app di Azure. Questa guida fornisce istruzioni dettagliate su come raccogliere tracce del profiler per app Web ASP.NET Core Linux.

Dopo aver completato questa procedura dettagliata, l'app potrà raccogliere tracce del profiler, ad esempio le tracce visualizzate nell'immagine. In questo esempio la traccia del profiler indica che una richiesta Web specifica è lenta a causa del tempo di attesa. Il *percorso critico* nel codice che rallenta l'app è contrassegnato da un'icona a forma di fiamma. Il metodo **About** nella sezione **HomeController** rallenta l'app Web perché chiama la funzione **Thread.Sleep**.

![Tracce del profiler](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Prerequisiti
Le istruzioni seguenti si applicano a tutti gli ambienti di sviluppo Windows, Linux e Mac:

* Installare [.NET Core SDK 2.1.2 o versione successiva](https://www.microsoft.com/net/download/windows/build).
* Installare Git seguendo le istruzioni disponibili in [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Introduzione - Installazione di Git).

## <a name="set-up-the-project-locally"></a>Configurare il progetto in locale

1. Aprire una finestra del prompt dei comandi nel computer. Le istruzioni seguenti si applicano a tutti gli ambienti di sviluppo Windows, Linux e Mac.

2. Creare un'applicazione Web ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Modificare la directory di lavoro scegliendo la cartella radice per il progetto.

4. Aggiungere il pacchetto NuGet per raccogliere le tracce del profiler:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Aggiungere una riga di codice nella sezione **HomeController.cs** per impostare un ritardo casuale di alcuni secondi:

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

6. Salvare le modifiche ed eseguirne il commit nel repository locale:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Creare l'app Web Linux per ospitare il progetto

1. Creare l'ambiente dell'app Web usando il servizio app in Linux:

    ![Creare l'app Web Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Creare le credenziali di distribuzione:

    > [!NOTE]
    > Registrare la password da usare in un secondo momento quando si distribuisce l'app Web.

    ![Creare le credenziali di distribuzione](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Scegliere le opzioni di distribuzione. Configurare un repository Git locale nell'app Web seguendo le istruzioni nel portale di Azure. Verrà automaticamente creato un repository Git.

    ![Configurare il repository Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Per altre opzioni di distribuzione, vedere [questo articolo](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Distribuire il progetto

1. Nella finestra del prompt dei comandi passare alla cartella radice del progetto. Aggiungere un repository Git remoto in modo che punti al repository nel servizio app:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Usare il **nome utente** usato per creare le credenziali di distribuzione.
    * Usare il **nome app** usato per creare l'app Web con il servizio app in Linux.

2. Distribuire il progetto eseguendo il push delle modifiche in Azure:

    ```
    git push azure master
    ```

L'output dovrebbe essere simile all'esempio seguente:

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

1. [Creare una risorsa di Application Insights](./app-insights-create-new-resource.md).

2. Copiare il valore **iKey** della risorsa di Application Insights e configurare le impostazioni seguenti nelle app Web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Configurare le impostazioni delle app](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    La modifica delle impostazioni delle app comporta il riavvio automatico del sito. Una volta applicate le nuove impostazioni, viene immediatamente avviata l'esecuzione del profiler per due minuti. Il profiler viene quindi eseguito per due minuti ogni ora.

3. Generare traffico nel sito Web. È possibile generare traffico aggiornando la pagina **About** del sito alcune volte.

4. Attendere da due a cinque minuti in modo che gli eventi possano essere aggregati in Application Insights.

5. Passare al riquadro **Prestazioni** di Application Insights nel portale di Azure. È possibile visualizzare le tracce del profiler in basso a destra nel riquadro.

    ![Visualizzare le tracce del profiler](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problemi noti

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>L'azione di abilitazione nel riquadro di configurazione del profiler non funziona

> [!NOTE]
> Se si ospita l'app usando il servizio app in Linux, non è necessario riabilitare il profiler nel riquadro **Prestazioni** del portale di Application Insights. È possibile includere il pacchetto NuGet nel progetto e impostare il valore **iKey** di Application Insights nelle impostazioni dell'app Web per abilitare il profiler.

Se si segue il flusso di lavoro di abilitazione per [Application Insights Profiler per Windows](./app-insights-profiler.md) e si seleziona **Abilita** nel riquadro **Configure Profiler** (Configura profiler), si verifica un errore. L'operazione di abilitazione cerca di installare la versione di Windows dell'agente del profiler nell'ambiente Linux.

È in corso la ricerca di una soluzione a questo problema.

![Non provare a riabilitare il profiler nel riquadro Prestazioni](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Passaggi successivi
Se si usano contenitori personalizzati ospitati da Servizio app di Azure, seguire le istruzioni in [Enable Service Profiler for containerized ASP.NET Core application](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) (Abilitare il profiler di servizi per l'applicazione ASP.NET Core in contenitore) per abilitare Application Insights Profiler.

Segnalare eventuali problemi o suggerimenti nel repository GitHub di Application Insights: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues) (ApplicationInsights-Profiler-AspNetCore: Problemi).
