---
title: Sviluppare e distribuire WebJobs tramite VS
description: Informazioni su come sviluppare Azure WebJobs in Visual Studio e distribuirli nel servizio app di Azure, inclusa la creazione di un'attività pianificata.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d73c858bdbfee7c5b7c7e31b62b1f601b7b6838a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866450"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Sviluppare e distribuire processi Web usando Visual Studio - Servizio app di Azure

In questo articolo viene illustrato come utilizzare Visual Studio per distribuire un progetto Applicazione console in un'app Web nel [servizio app](overview.md) come processo Web di Azure.This article explains how to use Visual Studio to deploy a Console Application project to a web app in App Service as an [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Per informazioni su come distribuire processi Web usando il [portale di Azure](https://portal.azure.com), vedere [Eseguire attività in background con processi Web](webjobs-create.md).

È possibile pubblicare più processi Web in una singola app Web. Assicurarsi che ogni processo Web in un'app Web abbia un nome univoco.

La versione 3.x di [Azure WebJobs SDK](webjobs-sdk-how-to.md) consente di sviluppare WebJobs che vengono eseguiti come app .NET Core o .NET Framework, mentre la versione 2.x supporta solo .NET Framework. Il modo in cui si distribuisce un progetto WebJobs è diverso per i progetti .NET Core rispetto a quelli di .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs come app console .NET Core

Quando si usa la versione 3.x di WebJobs, è possibile creare e pubblicare WebJobs come app console .NET Core. Per istruzioni dettagliate sulla creazione e la pubblicazione di un'applicazione console .NET Core in Azure come processo Web, vedere Introduzione a [Azure WebJobs SDK per l'elaborazione in background basata](webjobs-sdk-get-started.md)su eventi.

> [!NOTE]
> .NET Core WebJobs non può essere collegato a progetti Web. Se è necessario distribuire il processo Web con un'app Web, è necessario [creare il processo Web come app console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuire nel Servizio app di Azure

La pubblicazione di un processo Web .NET Core nel servizio app da Visual Studio usa gli stessi strumenti della pubblicazione di un'app ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipi di processi Web

Per impostazione predefinita, un WebJob pubblicato da un progetto console .NET Core viene eseguito solo quando viene attivato o su richiesta. È inoltre possibile aggiornare il progetto in modo che [sia eseguito in base](#scheduled-execution) a una pianificazione o in modo continuo.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Esecuzione pianificata

Quando si pubblica un'applicazione console .NET Core in Azure, al progetto viene aggiunto un nuovo file *settings.job.* Utilizzare questo file per impostare una pianificazione di esecuzione per il processo WebJob. Per ulteriori informazioni, vedere [Pianificazione di un processo Web attivato](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Esecuzione continua

È possibile usare Visual Studio per modificare il webJob per l'esecuzione continua quando Always On è abilitato in Azure.You can use Visual Studio to change the WebJob to run continuously when Always On is enabled in Azure.

1. Se non è già stato fatto, [pubblicare il progetto](#deploy-to-azure-app-service)in Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella scheda **Pubblica** scegliere **Impostazioni**. 

1. Nella finestra di dialogo **Impostazioni profilo** scegliere **Continuo** per **Tipo di processo Web**e scegliere **Salva**.

    ![Finestra di dialogo Impostazioni di pubblicazione per un processo Web](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selezionare **Pubblica** per ripubblicare il processo Web con le impostazioni aggiornate.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs come app console di .NET Framework  

Quando Visual Studio distribuisce un progetto di applicazione console .NET Framework abilitato per WebJobs, copia i file di runtime nella cartella appropriata nell'app Web (*App_Data/jobs/continua* per I processi Web e *App_Data/processi/processi/attivati* per WebJobs pianificati o su richiesta).

A un progetto abilitato per i processi Web vengono aggiunti gli elementi seguenti:

* Pacchetto NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* File [webjob-publish-settings.json](#publishsettings) che contiene le impostazioni di distribuzione e dell'utilità di pianificazione. 

![Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web](./media/webjobs-dotnet-deploy-vs/convert.png)

È possibile aggiungere questi elementi a un progetto di applicazione console esistente o usare un modello per creare un nuovo progetto di applicazione console abilitato per i processi Web. 

È possibile distribuire un progetto come processo Web indipendente o collegarlo a un progetto Web in modo tale che venga distribuito automaticamente ogni volta che viene distribuito il progetto Web. Per collegare i progetti, Visual Studio include il nome del progetto abilitato per i processi Web in un file [webjobs-list.json](#webjobslist) nel progetto Web.

![Diagramma che mostra il collegamento del progetto processo Web al progetto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Prerequisiti

Se si usa Visual Studio 2015, installare [Azure SDK per .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se si usa Visual Studio 2017, installare il [carico di lavoro di sviluppo di Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a> Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente

Sono disponibili due opzioni:

* [Abilitare la distribuzione automatica con un progetto Web](#convertlink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito automaticamente come processo Web quando viene distribuito un progetto Web. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

* [Abilitare la distribuzione senza un progetto Web](#convertnolink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito come processo Web indipendente senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Abilitare la distribuzione automatica dei processi Web con un progetto Web

1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Progetto esistente come processo Web di Azure**.
   
    ![Progetto esistente come processo Web Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Nell'elenco a discesa **Nome progetto** selezionare il progetto di applicazione console da aggiungere come processo Web.
   
    ![Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Abilitare la distribuzione dei processi Web senza un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto Applicazione console in **Esplora soluzioni**, quindi scegliere Pubblica come processo Web di **Azure...**. 
   
    ![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) con il progetto selezionato nella casella **Nome progetto** .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.
   
   Viene visualizzata la procedura guidata **Pubblica sito Web** .  Se non si vuole eseguire subito la pubblicazione, chiudere la procedura guidata. Le impostazioni immesse vengono salvate in modo da poter essere usate quando si vuole [distribuire il progetto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Creare un nuovo progetto abilitato per i processi Web
Per creare un nuovo progetto abilitato per i processi Web, è possibile usare il modello del progetto di applicazione console e abilitare la distribuzione dei processi Web come descritto nella [sezione precedente](#convert). In alternativa, è possibile usare il modello nuovo-progetto di processi Web:

* [Utilizzare il modello di nuovo progetto WebJobs per un processo Web indipendente](#createnolink)
  
    Creare un progetto e configurarlo per essere distribuito in modo indipendente come processo Web senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.
* [Utilizzare il modello di nuovo progetto WebJobs per un processo Web collegato a un progetto Web](#createlink)
  
    Creare un progetto configurato in modo da essere distribuito automaticamente come processo Web quando viene distribuito un progetto Web nella stessa soluzione. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

> [!NOTE]
> Il modello new-project di Processi Web installa automaticamente pacchetti NuGet e in *Program.cs* include codice per [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se non si vuole usare WebJobs SDK, rimuovere o modificare l'istruzione `host.RunAndBlock` in *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web indipendente
1. Fare clic su **File** > **nuovo progetto**, quindi nella finestra di dialogo Nuovo **progetto** fare clic su **Cloud** > **Azure WebJob (.NET Framework)**.
   
    ![Finestra di dialogo Nuovo progetto con il modello processo Web](./media/webjobs-dotnet-deploy-vs/np.png)
2. Seguire le istruzioni illustrate in precedenza per [rendere il progetto di applicazione console un progetto processi Web indipendente](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Nuovo progetto processo Web Azure**.
   
    ![Voce del menu Nuovo progetto processo Web Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Finestra di dialogo Aggiungi processo Web di Azure
La finestra di dialogo **Aggiungi processo Web Azure** consente di immettere il nome del processo Web ed eseguire l'impostazione della modalità per il processo Web. 

![Finestra di dialogo Aggiungi processo Web Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

I campi in questa finestra di dialogo corrispondono ai campi presenti nella finestra di dialogo **Aggiungi processo Web** del portale di Azure. Per altre informazioni, vedere [Eseguire attività in background con Processi Web in Servizio app di Azure](webjobs-create.md).

> [!NOTE]
> * Per informazioni sulla distribuzione da riga di comando, vedere [Abilitazione della riga di comando o del recapito continuo di processi Web Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se si distribuisce un processo Web e quindi si decide di modificare il tipo di processo Web e ridistribuirlo, è necessario eliminare il file *webjobs-publish-settings.json*. In questo modo Visual Studio Mostra le opzioni di pubblicazione, è possibile modificare il tipo di processo Web.
> * Se si distribuisce un processo Web e successivamente si modifica la modalità di esecuzione da continua a non continua o viceversa, Visual Studio crea un nuovo processo Web in Azure quando si esegue nuovamente la distribuzione. Se si modificano le altre impostazioni di pianificazione lasciando invariata la modalità di esecuzione o passando dalla modalità pianificata a quella su richiesta o viceversa, Visual Studio aggiorna il processo esistente anziché crearne uno nuovo.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Quando si configura un'applicazione console per la distribuzione dei processi Web, Visual Studio installa il pacchetto NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e archivia le informazioni di pianificazione in un file *webjob-publish-settings.json* nella cartella *Proprietà* del progetto processi Web. Di seguito è riportato un esempio di tale file:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) file viene memorizzato in e può essere visualizzato.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
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

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) file viene memorizzato in e può essere visualizzato.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Distribuire un progetto processi Web
Un progetto processi Web collegato a un progetto Web viene distribuito automaticamente con il progetto Web. Per informazioni sulla distribuzione di progetti Web, vedere **Guide** > alle procedure**Distribuire l'app** nel riquadro di spostamento sinistro.

Per distribuire un progetto processi Web indipendente, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**, quindi scegliere **Pubblica come processo Web Azure...**. 

![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Per un processo Web indipendente viene visualizzata la stessa procedura guidata **Pubblica sito Web** usata per i progetti Web ma con meno impostazioni disponibili da modificare.

## <a name="scheduling-a-triggered-webjob"></a>Pianificazione di un processo Web attivato

WebJobs utilizza un file *settings.job* per determinare quando viene eseguito un WebJob. Utilizzare questo file per impostare una pianificazione di esecuzione per il processo WebJob. L'esempio seguente viene eseguito ogni ora dalle 9:00 alle 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Questo file deve trovarsi nella directory principale della cartella WebJobs, accanto `wwwroot\app_data\jobs\triggered\{job name}` allo `wwwroot\app_data\jobs\continuous\{job name}`stesso lato lo script del processo Web, ad esempio o . Quando si distribuisce un processo Web da Visual Studio, contrassegnare le proprietà del file `settings.job` come **Copia se più recente**. 

Quando si [crea un WebJob dal portale](webjobs-create.md)di Azure, viene creato automaticamente il file settings.job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Espressioni CRON

WebJobs uses the same CRON expressions for scheduling as the timer trigger in Azure Functions. Per ulteriori informazioni sul supporto CRON, consultate l'articolo di riferimento del [trigger timer.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>informazioni di riferimento su settings.job

Le seguenti impostazioni sono supportate da WebJobs:

| **Impostazione** | **Tipo**  | **Descrizione** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tutti | Consente l'esecuzione del processo sul posto senza essere prima copiato in una cartella temporanea. Per ulteriori informazioni, consultate [Directory di lavoro di WebJobs.](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory) |
| `is_singleton` | Continuo | Eseguire i processi Web su una singola istanza solo quando viene eseguito la scalabilità orizzontale. Per ulteriori informazioni, consultate [Impostare un processo continuo come singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Attivato | Eseguire il processo Web in base a una pianificazione basata su CRON. Per ulteriori informazioni, consultate l'articolo di riferimento sui [trigger timer.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) |
| `stopping_wait_time`| Tutti | Consente il controllo del comportamento di arresto. Per ulteriori informazioni, vedere [Arresto normale](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su WebJobs SDK](webjobs-sdk-how-to.md)
