---
title: Sviluppare e distribuire processi Web usando Visual Studio - Azure
description: Informazioni su come sviluppare e distribuire processi Web di Azure in Servizio app di Azure usando Visual Studio.
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 8793485f2f1967e8c14ef335f14f81c9b946f974
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Sviluppare e distribuire processi Web usando Visual Studio - Servizio app di Azure

## <a name="overview"></a>Panoramica

Questo argomento illustra come usare Visual Studio per distribuire un progetto di applicazione console in un'app Web in [Servizio app](app-service-web-overview.md) come un [processo Web Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Per informazioni su come distribuire processi Web usando il [portale di Azure](https://portal.azure.com), vedere [Eseguire attività in background con processi Web](web-sites-create-web-jobs.md).

Quando distribuisce un progetto di applicazione console abilitato per i processi Web, Visual Studio esegue due attività:

* Copia i file di runtime nella cartella appropriata nell'app Web (*App_Data/jobs/continuous* per i processi Web in modalità continua, *App_Data/jobs/triggered* per i processi Web pianificati e su richiesta).
* Configura i [processi dell'utilità di pianificazione di Azure](#scheduler) per i processi Web pianificati per essere eseguiti a orari specifici. Tale operazione non è necessaria per l'esecuzione dei processi Web in modalità continua.

A un progetto abilitato per i processi Web vengono aggiunti gli elementi seguenti:

* Pacchetto NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* File [webjob-publish-settings.json](#publishsettings) che contiene le impostazioni di distribuzione e dell'utilità di pianificazione. 

![Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web](./media/websites-dotnet-deploy-webjobs/convert.png)

È possibile aggiungere questi elementi a un progetto di applicazione console esistente o usare un modello per creare un nuovo progetto di applicazione console abilitato per i processi Web. 

È possibile distribuire un progetto come processo Web indipendente o collegarlo a un progetto Web in modo tale che venga distribuito automaticamente ogni volta che viene distribuito il progetto Web. Per collegare i progetti, Visual Studio include il nome del progetto abilitato per i processi Web in un file [webjobs-list.json](#webjobslist) nel progetto Web.

![Diagramma che mostra il collegamento del progetto processo Web al progetto Web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>prerequisiti

Se si usa Visual Studio 2015, installare [Azure SDK per .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se si usa Visual Studio 2017, installare il [carico di lavoro di sviluppo di Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente

Sono disponibili due opzioni:

* [Abilitare la distribuzione automatica con un progetto Web](#convertlink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito automaticamente come processo Web quando viene distribuito un progetto Web. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

* [Abilitare la distribuzione senza un progetto Web](#convertnolink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito come processo Web indipendente senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.

### <a id="convertlink"></a> Abilitare la distribuzione automatica dei processi Web con un progetto Web

1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Progetto esistente come processo Web di Azure**.
   
    ![Progetto esistente come processo Web Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Nell'elenco a discesa **Nome progetto** selezionare il progetto di applicazione console da aggiungere come processo Web.
   
    ![Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**. 

### <a id="convertnolink"></a> Abilitare la distribuzione dei processi Web senza un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto di applicazione console in **Esplora soluzioni** e quindi scegliere **Pubblica come processo Web Azure...**. 
   
    ![Pubblica come processo Web Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) con il progetto selezionato nella casella **Nome progetto** .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.
   
   Viene visualizzata la procedura guidata **Pubblica sito Web** .  Se non si vuole eseguire subito la pubblicazione, chiudere la procedura guidata. Le impostazioni immesse vengono salvate in modo da poter essere usate quando si vuole [distribuire il progetto](#deploy).

## <a id="create"></a>Creare un nuovo progetto abilitato per i processi Web
Per creare un nuovo progetto abilitato per i processi Web, è possibile usare il modello del progetto di applicazione console e abilitare la distribuzione dei processi Web come descritto nella [sezione precedente](#convert). In alternativa, è possibile usare il modello nuovo-progetto di processi Web:

* [Usare il modello nuovo-progetto di processi Web per un processo Web indipendente](#createnolink)
  
    Creare un progetto e configurarlo per essere distribuito in modo indipendente come processo Web senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.
* [Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web](#createlink)
  
    Creare un progetto configurato in modo da essere distribuito automaticamente come processo Web quando viene distribuito un progetto Web nella stessa soluzione. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

> [!NOTE]
> Il modello new-project di Processi Web installa automaticamente pacchetti NuGet e in *Program.cs* include codice per [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se non si vuole usare WebJobs SDK, rimuovere o modificare l'istruzione `host.RunAndBlock` in *Program.cs*.
> 
> 

### <a id="createnolink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web indipendente
1. Fare clic su **File** > **Nuovo progetto** e quindi nella finestra di dialogo **Nuovo progetto** fare clic su **Cloud** > **Processo Web Azure (.NET Framework)**.
   
    ![Finestra di dialogo Nuovo progetto con il modello processo Web](./media/websites-dotnet-deploy-webjobs/np.png)
2. Seguire le istruzioni illustrate in precedenza per [rendere il progetto di applicazione console un progetto processi Web indipendente](#convertnolink).

### <a id="createlink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Nuovo progetto processo Web Azure**.
   
    ![Voce del menu Nuovo progetto processo Web Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.

## <a id="configure"></a>Finestra di dialogo Aggiungi processo Web Azure
La finestra di dialogo **Aggiungi processo Web Azure** consente di immettere il nome del processo Web ed eseguire l'impostazione della modalità per il processo Web. 

![Finestra di dialogo Aggiungi processo Web Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

I campi in questa finestra di dialogo corrispondono ai campi presenti nella finestra di dialogo **Aggiungi processo Web** del portale di Azure. Per ulteriori informazioni, vedere [attività in Background eseguito con WebJobs](web-sites-create-web-jobs.md).

> [!NOTE]
> * Per informazioni sulla distribuzione da riga di comando, vedere [Abilitazione della riga di comando o del recapito continuo di processi Web Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se si distribuisce un processo Web e quindi si decide di modificare il tipo di processo Web e ridistribuirlo, è necessario eliminare il file *webjobs-publish-settings.json*. In questo modo Visual Studio Mostra le opzioni di pubblicazione, è possibile modificare il tipo di processo Web.
> * Se si distribuisce un processo Web e successivamente si modifica la modalità di esecuzione da continua a non continua o viceversa, Visual Studio crea un nuovo processo Web in Azure quando si esegue nuovamente la distribuzione. Se si modificano le altre impostazioni di pianificazione lasciando invariata la modalità di esecuzione o passando dalla modalità pianificata a quella su richiesta o viceversa, Visual Studio aggiorna il processo esistente anziché crearne uno nuovo.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Quando si configura un'applicazione console per la distribuzione dei processi Web, Visual Studio installa il pacchetto NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e archivia le informazioni di pianificazione in un file *webjob-publish-settings.json* nella cartella *Proprietà* del progetto processi Web. Di seguito è riportato un esempio di tale file:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) da dove può essere visualizzato.  

## <a id="webjobslist"></a>webjobs-list.json
Quando si collega un progetto abilitato per i processi Web a un progetto Web, Visual Studio archivia il nome del progetto processi Web in un file *webjobs-list.json* nella cartella *Proprietà* del progetto Web. L'elenco potrebbe contenere più progetti processi Web, come illustrato nell'esempio seguente:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) da dove può essere visualizzato.

## <a id="deploy"></a>Distribuire un progetto processi Web
Un progetto processi Web collegato a un progetto Web viene distribuito automaticamente con il progetto Web. Per informazioni sulla distribuzione dei progetti Web, vedere **Guide alle procedure** > **Distribuzione in Azure** nel riquadro di spostamento a sinistra.

Per distribuire un progetto processi Web indipendente, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**, quindi scegliere **Pubblica come processo Web Azure...**. 

![Pubblica come processo Web Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Per un processo Web indipendente viene visualizzata la stessa procedura guidata **Pubblica sito Web** usata per i progetti Web ma con meno impostazioni disponibili da modificare.
