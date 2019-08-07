---
title: Sviluppare e distribuire processi Web usando Visual Studio - Azure
description: Informazioni su come sviluppare e distribuire processi Web di Azure in Servizio app di Azure usando Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d50acc50880229626c847d41d9abe9a9e13d9c6e
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736123"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Sviluppare e distribuire processi Web usando Visual Studio - Servizio app di Azure

Questo articolo illustra come usare Visual Studio per distribuire un progetto di applicazione console in un'app Web nel [servizio app](overview.md) come [processo Web di Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Per informazioni su come distribuire processi Web usando il [portale di Azure](https://portal.azure.com), vedere [Eseguire attività in background con processi Web](webjobs-create.md).

È possibile pubblicare più processi Web in una singola app Web. Verificare che ogni processo Web in un'app Web abbia un nome univoco.

La versione 3. x di [Azure Webjobs SDK](webjobs-sdk-how-to.md) consente di sviluppare processi Web eseguiti come app .NET Core o .NET Framework app, mentre la versione 2. x supporta solo le .NET Framework. Il modo in cui si distribuisce un progetto di processi Web è diverso per i progetti .NET Core rispetto a quelli .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Processi Web come app console .NET Core

Quando si usa la versione 3. x dei processi Web, è possibile creare e pubblicare processi Web come app console .NET Core. Per istruzioni dettagliate per la creazione e la pubblicazione di un'applicazione console .NET Core in Azure come processo Web, vedere [Introduzione ad Azure Webjobs SDK per l'elaborazione in background guidata dagli eventi](webjobs-sdk-get-started.md).

> [!NOTE]
> I processi Web di .NET Core non possono essere collegati con i progetti Web. Se è necessario distribuire processo Web con un'app Web, è necessario [creare il processo Web come app console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuisci nel servizio app Azure

La pubblicazione di un processo Web .NET Core nel servizio app da Visual Studio usa gli stessi strumenti di pubblicazione di un'app ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipi di processi Web

Per impostazione predefinita, un processo Web pubblicato da un progetto console .NET Core viene eseguito solo se attivato o su richiesta. È anche possibile aggiornare il progetto in modo che [venga eseguito in base a una pianificazione o eseguito in modo](#scheduled-execution) continuo.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Esecuzione pianificata

Quando si pubblica un'applicazione console .NET Core in Azure, al progetto viene aggiunto un nuovo file *Settings. job* . Usare questo file per impostare una pianificazione di esecuzione per la processo Web. Per altre informazioni, vedere [pianificazione di un processo Web attivato](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Esecuzione continua

È possibile usare Visual Studio per modificare il processo Web per l'esecuzione continua quando Always On è abilitato in Azure.

1. Se non è già stato fatto, [pubblicare il progetto in Azure](#deploy-to-azure-app-service).

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella scheda **pubblica** scegliere **Impostazioni**. 

1. Nella finestra di dialogo **Impostazioni profilo** scegliere **continuo** per **tipo di processo Web**e scegliere **Salva**.

    ![Finestra di dialogo Impostazioni di pubblicazione per un processo Web](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selezionare **pubblica** per ripubblicare il processo Web con le impostazioni aggiornate.

## <a name="webjobs-as-net-framework-console-apps"></a>Processi Web come app console .NET Framework  

Quando Visual Studio distribuisce un progetto di applicazione console .NET Framework abilitato per i processi Web, copia i file di runtime nella cartella appropriata nell'app Web (*App_Data/Jobs/Continuous* per i processi Web continui e *App_Data/Jobs/attivata* per Processi Web pianificati o su richiesta).

A un progetto abilitato per i processi Web vengono aggiunti gli elementi seguenti:

* Pacchetto NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* File [webjob-publish-settings.json](#publishsettings) che contiene le impostazioni di distribuzione e dell'utilità di pianificazione. 

![Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web](./media/webjobs-dotnet-deploy-vs/convert.png)

È possibile aggiungere questi elementi a un progetto di applicazione console esistente o usare un modello per creare un nuovo progetto di applicazione console abilitato per i processi Web. 

È possibile distribuire un progetto come processo Web indipendente o collegarlo a un progetto Web in modo tale che venga distribuito automaticamente ogni volta che viene distribuito il progetto Web. Per collegare i progetti, Visual Studio include il nome del progetto abilitato per i processi Web in un file [webjobs-list.json](#webjobslist) nel progetto Web.

![Diagramma che mostra il collegamento del progetto processo Web al progetto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Prerequisiti

Se si usa Visual Studio 2015, installare [Azure SDK per .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se si usa Visual Studio 2019, installare il [carico di lavoro sviluppo di Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a id="convert"></a> Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente

Sono disponibili due opzioni:

* [Abilitare la distribuzione automatica con un progetto Web](#convertlink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito automaticamente come processo Web quando viene distribuito un progetto Web. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

* [Abilitare la distribuzione senza un progetto Web](#convertnolink).

  Configurare un progetto di applicazione console esistente in modo tale che venga distribuito come processo Web indipendente senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.

#### <a id="convertlink"></a> Abilitare la distribuzione automatica dei processi Web con un progetto Web

1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Progetto esistente come processo Web di Azure**.
   
    ![Progetto esistente come processo Web Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Nell'elenco a discesa **Nome progetto** selezionare il progetto di applicazione console da aggiungere come processo Web.
   
    ![Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**. 

#### <a id="convertnolink"></a> Abilitare la distribuzione dei processi Web senza un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto di applicazione console in **Esplora soluzioni** e quindi scegliere **Pubblica come processo Web Azure...** . 
   
    ![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) con il progetto selezionato nella casella **Nome progetto** .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.
   
   Viene visualizzata la procedura guidata **Pubblica sito Web** .  Se non si vuole eseguire subito la pubblicazione, chiudere la procedura guidata. Le impostazioni immesse vengono salvate in modo da poter essere usate quando si vuole [distribuire il progetto](#deploy).

### <a id="create"></a>Creare un nuovo progetto abilitato per i processi Web
Per creare un nuovo progetto abilitato per i processi Web, è possibile usare il modello del progetto di applicazione console e abilitare la distribuzione dei processi Web come descritto nella [sezione precedente](#convert). In alternativa, è possibile usare il modello nuovo-progetto di processi Web:

* [Usare il modello nuovo-progetto di processi Web per un processo Web indipendente](#createnolink)
  
    Creare un progetto e configurarlo per essere distribuito in modo indipendente come processo Web senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.
* [Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web](#createlink)
  
    Creare un progetto configurato in modo da essere distribuito automaticamente come processo Web quando viene distribuito un progetto Web nella stessa soluzione. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

> [!NOTE]
> Il modello new-project di Processi Web installa automaticamente pacchetti NuGet e in *Program.cs* include codice per [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se non si vuole usare WebJobs SDK, rimuovere o modificare l'istruzione `host.RunAndBlock` in *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web indipendente
1. Fare clic su **File** > **Nuovo progetto** e quindi nella finestra di dialogo **Nuovo progetto** fare clic su **Cloud** > **Processo Web Azure (.NET Framework)** .
   
    ![Finestra di dialogo Nuovo progetto con il modello processo Web](./media/webjobs-dotnet-deploy-vs/np.png)
2. Seguire le istruzioni illustrate in precedenza per [rendere il progetto di applicazione console un progetto processi Web indipendente](#convertnolink).

#### <a id="createlink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** > **Nuovo progetto processo Web Azure**.
   
    ![Voce del menu Nuovo progetto processo Web Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) , quindi fare clic su **OK**.

### <a id="configure"></a>Finestra di dialogo Aggiungi processo Web Azure
La finestra di dialogo **Aggiungi processo Web Azure** consente di immettere il nome del processo Web ed eseguire l'impostazione della modalità per il processo Web. 

![Finestra di dialogo Aggiungi processo Web Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

I campi in questa finestra di dialogo corrispondono ai campi presenti nella finestra di dialogo **Aggiungi processo Web** del portale di Azure. Per ulteriori informazioni, vedere [attività in Background eseguito con WebJobs](webjobs-create.md).

> [!NOTE]
> * Per informazioni sulla distribuzione da riga di comando, vedere [Abilitazione della riga di comando o del recapito continuo di processi Web Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se si distribuisce un processo Web e quindi si decide di modificare il tipo di processo Web e ridistribuirlo, è necessario eliminare il file *webjobs-publish-settings.json*. In questo modo Visual Studio Mostra le opzioni di pubblicazione, è possibile modificare il tipo di processo Web.
> * Se si distribuisce un processo Web e successivamente si modifica la modalità di esecuzione da continua a non continua o viceversa, Visual Studio crea un nuovo processo Web in Azure quando si esegue nuovamente la distribuzione. Se si modificano le altre impostazioni di pianificazione lasciando invariata la modalità di esecuzione o passando dalla modalità pianificata a quella su richiesta o viceversa, Visual Studio aggiorna il processo esistente anziché crearne uno nuovo.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
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

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) da dove può essere visualizzato.  

### <a id="webjobslist"></a>webjobs-list.json
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

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) da dove può essere visualizzato.

### <a id="deploy"></a>Distribuire un progetto processi Web
Un progetto processi Web collegato a un progetto Web viene distribuito automaticamente con il progetto Web. Per informazioni sulla distribuzione dei progetti Web, vedere **Guide alle procedure** > **Distribuzione in Azure** nel riquadro di spostamento a sinistra.

Per distribuire un progetto processi Web indipendente, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**, quindi scegliere **Pubblica come processo Web Azure...** . 

![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Per un processo Web indipendente viene visualizzata la stessa procedura guidata **Pubblica sito Web** usata per i progetti Web ma con meno impostazioni disponibili da modificare.

## <a name="scheduling-a-triggered-webjob"></a>Pianificazione di un processo Web attivato

Processi Web usa un file *Settings. job* per determinare quando viene eseguito un processo Web. Usare questo file per impostare una pianificazione di esecuzione per la processo Web. L'esempio seguente viene eseguito ogni ora dalle 9.00 alle 17.00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Questo file deve trovarsi nella radice della cartella processi Web, oltre a affiancare lo script di processo Web, ad esempio `wwwroot\app_data\jobs\triggered\{job name}` o `wwwroot\app_data\jobs\continuous\{job name}`. Quando si distribuisce un processo Web da Visual Studio, contrassegnare le proprietà del file `settings.job` come **Copia se più recente**. 

Quando si [Crea un processo Web dalla portale di Azure](webjobs-create.md), il file Settings. job viene creato automaticamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Espressioni CRON

Processi Web usa le stesse espressioni CRON per la pianificazione del trigger timer in funzioni di Azure. Per altre informazioni sul supporto CRON, vedere l' [articolo di riferimento sul trigger del timer](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

### <a name="settingjob-reference"></a>impostazione. riferimento al processo

Le impostazioni seguenti sono supportate da processi Web:

| **Impostazione** | **Tipo**  | **Descrizione** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tutti | Consente di eseguire il processo senza essere prima copiato in una cartella temporanea. Per altre informazioni, vedere la pagina relativa alla [directory di lavoro dei processi](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)Web. |
| `is_singleton` | Continuo | Quando si aumenta la scalabilità orizzontale, eseguire i processi Web solo in una singola istanza. Per altre informazioni, vedere [impostare un processo continuo come singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Attivati | Eseguire processo Web in base a una pianificazione basata su CRON. Per altre informazioni, vedere l' [articolo di riferimento sul trigger del timer](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Tutti | Consente di controllare il comportamento di arresto. Per altre informazioni, vedere [arresto normale](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su WebJobs SDK](webjobs-sdk-how-to.md)
