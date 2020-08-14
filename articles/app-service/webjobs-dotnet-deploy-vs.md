---
title: Sviluppare e distribuire processi Web con Visual Studio
description: Informazioni su come sviluppare processi Web di Azure in Visual Studio e distribuirli nel servizio app Azure, inclusa la creazione di un'attività pianificata.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 14bb693ccaa1b1d16a1d07b7ee1cdeb4493960f5
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212879"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Sviluppare e distribuire processi Web con Visual Studio

Questo articolo illustra come usare Visual Studio per distribuire un progetto di applicazione console in un'app Web nel [servizio app Azure](overview.md) come [processo Web di Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Per informazioni su come distribuire processi Web usando il [portale di Azure](https://portal.azure.com), vedere [eseguire attività in background con processi web nel servizio app Azure](webjobs-create.md).

È possibile scegliere di sviluppare un processo Web che viene eseguito come un' [app .NET Core](#webjobs-as-net-core-console-apps) o un' [app .NET Framework](#webjobs-as-net-framework-console-apps). La versione 3. x di [Azure Webjobs SDK](webjobs-sdk-how-to.md) consente di sviluppare processi Web eseguiti come app .NET Core o .NET Framework app, mentre la versione 2. x supporta solo le .NET Framework. Il modo in cui si distribuisce un progetto di processi Web è diverso per i progetti .NET Core rispetto ai progetti .NET Framework.

È possibile pubblicare più processi Web in un'unica app Web, a condizione che ogni processo Web in un'app Web abbia un nome univoco.

## <a name="webjobs-as-net-core-console-apps"></a>Processi Web come app console .NET Core

Con la versione 3. x di Azure webjobs SDK è possibile creare e pubblicare processi Web come app console .NET Core. Per istruzioni dettagliate su come creare e pubblicare un'app console .NET Core in Azure come processo Web, vedere [Introduzione ad Azure Webjobs SDK per l'elaborazione in background guidata dagli eventi](webjobs-sdk-get-started.md).

> [!NOTE]
> I processi Web di .NET Core non possono essere collegati con i progetti Web. Se è necessario distribuire la processo Web con un'app Web, [creare i processi Web come un'app console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuire nel Servizio app di Azure

La pubblicazione di un processo Web .NET Core nel servizio app Azure da Visual Studio usa gli stessi strumenti di pubblicazione di un'app ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Processi Web come app console .NET Framework  

Se si usa Visual Studio per distribuire un progetto di app console .NET Framework abilitato per i processi Web, i file di runtime vengono copiati nella cartella appropriata nell'app Web (*App_Data/Jobs/Continuous* per i processi Web continui e *App_Data/Jobs/triggered* per i processi Web pianificati o su richiesta).

Visual Studio aggiunge gli elementi seguenti a un progetto abilitato per i processi Web:

* Pacchetto NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* File [webjob-publish-settings.json](#publishsettings) che contiene le impostazioni di distribuzione e dell'utilità di pianificazione. 

![Diagramma che illustra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web](./media/webjobs-dotnet-deploy-vs/convert.png)

È possibile aggiungere questi elementi a un progetto di applicazione console esistente o usare un modello per creare un nuovo progetto di applicazione console abilitato per i processi Web. 

Distribuire un progetto come processo Web da solo oppure collegarlo a un progetto Web in modo che venga distribuito automaticamente ogni volta che si distribuisce il progetto Web. Per collegare i progetti, Visual Studio include il nome del progetto abilitato per i processi Web in un file [webjobs-list.json](#webjobslist) nel progetto Web.

![Diagramma che mostra il collegamento del progetto processo Web al progetto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Prerequisiti

Installare Visual Studio 2017 o Visual Studio 2019 con il [carico di lavoro sviluppo di Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente

Sono disponibili due opzioni:

* [Abilitare la distribuzione automatica con un progetto Web](#convertlink).

  Configurare un progetto di applicazione console esistente in modo che venga distribuito automaticamente come processo Web quando si distribuisce un progetto Web. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

* [Abilitare la distribuzione senza un progetto Web](#convertnolink).

  Configurare un progetto di applicazione console esistente da distribuire come processo Web, senza un collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Potrebbe essere necessario eseguire questa operazione per ridimensionare le risorse di processo Web in modo indipendente dalle risorse dell'applicazione Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Abilitare la distribuzione automatica dei processi Web con un progetto Web

1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi selezionare **Aggiungi**  >  **progetto esistente come Azure processo Web**.
   
    ![Progetto esistente come processo Web Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Nell'elenco a discesa **nome progetto** selezionare il progetto di applicazione console da aggiungere come processo Web.
   
    ![Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) e quindi fare clic su **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Abilitare la distribuzione dei processi Web senza un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto di app console in **Esplora soluzioni**, quindi scegliere **pubblica come processo Web di Azure**. 
   
    ![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) con il progetto selezionato nella casella **Nome progetto** .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) e quindi fare clic su **OK**.
   
   Viene visualizzata la procedura guidata **Pubblica sito Web** . Se non si vuole eseguire subito la pubblicazione, chiudere la procedura guidata. Le impostazioni immesse vengono salvate in modo da poter essere usate quando si vuole [distribuire il progetto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Creare un nuovo progetto abilitato per i processi Web
Per creare un nuovo progetto abilitato per i processi Web, usare il modello di progetto di app console e abilitare la distribuzione dei processi Web come illustrato nella [sezione precedente](#convert). In alternativa, è possibile usare il modello nuovo-progetto di processi Web:

* [Usare il modello nuovo-progetto di processi Web per un processo Web indipendente](#createnolink)
  
    Creare un progetto e configurarlo per essere distribuito in modo indipendente come processo Web senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un'app Web in modo indipendente senza l'esecuzione dell'applicazione Web nell'app Web. Potrebbe essere necessario eseguire questa operazione per ridimensionare le risorse di processo Web in modo indipendente dalle risorse dell'applicazione Web.
* [Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web](#createlink)
  
    Creare un progetto configurato per la distribuzione automatica come processo Web quando si distribuisce un progetto Web nella stessa soluzione. Usare questa opzione quando si vuole eseguire il processo Web nella stessa app Web in cui viene eseguita l'applicazione Web correlata.

> [!NOTE]
> Il modello new-project di Processi Web installa automaticamente pacchetti NuGet e in *Program.cs* include codice per [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se non si vuole usare WebJobs SDK, rimuovere o modificare l'istruzione `host.RunAndBlock` in *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web indipendente
1. Selezionare **File** > **New** (Nuovo)  > **Project** (Progetto). Nella finestra di dialogo **Crete a nuovo progetto** cercare e selezionare **Azure processo Web (.NET Framework)** per C#.
   
2. Seguire le istruzioni precedenti per fare in modo che [l'app console proietti un progetto di processi Web indipendente](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web
1. Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi**  >  **nuovo progetto processo Web di Azure**.
   
    ![Voce del menu Nuovo progetto processo Web Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure](#configure) .
2. Completare la finestra di dialogo [Aggiungi processo Web Azure](#configure) e quindi fare clic su **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jssu file
Quando si configura un'app console per la distribuzione di processi Web, Visual Studio installa il pacchetto NuGet [Microsoft. Web. webjobs. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e archivia le informazioni di pianificazione in un *webjob-publish-settings.js* file nella cartella delle *proprietà* del progetto del progetto processi Web. Di seguito è riportato un esempio di tale file:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file è archiviato in [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e può essere visualizzato qui.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jssu file
Quando si collega un progetto abilitato per i processi Web a un progetto Web, Visual Studio archivia il nome del progetto processi Web in un file *webjobs-list.json* nella cartella *Proprietà* del progetto Web. L'elenco potrebbe contenere più progetti processi Web, come illustrato nell'esempio seguente:

```json
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
```

È possibile modificare questo file direttamente in Visual Studio con IntelliSense. Lo schema del file viene archiviato in [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Distribuire un progetto processi Web
Un progetto webjobs collegato a un progetto Web viene distribuito automaticamente con il progetto Web. Per informazioni sulla distribuzione di progetti web, vedere **guide alle procedure**  >  **distribuire l'app** nel portale di spostamento a sinistra.

Per distribuire un progetto processi Web in modo autonomo, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **pubblica come processo Web di Azure**. 

![Pubblica come processo Web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Per un processo Web indipendente viene visualizzata la stessa procedura guidata **Pubblica sito Web** usata per i progetti Web ma con meno impostazioni disponibili da modificare.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Finestra di dialogo Aggiungi processo Web di Azure
La finestra di dialogo **Aggiungi processo Web Azure** consente di immettere il nome del processo Web e l'impostazione della modalità di esecuzione per il processo Web. 

![Finestra di dialogo Aggiungi processo Web di Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Alcuni dei campi di questa finestra di dialogo corrispondono ai campi nella finestra di dialogo **Aggiungi processo Web** del portale di Azure. Per altre informazioni, vedere [eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md).

Informazioni sulla distribuzione di processo Web:

* Per informazioni sulla distribuzione da riga di comando, vedere [Abilitazione della riga di comando o del recapito continuo di processi Web Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Se si distribuisce un processo Web e quindi si decide di modificare il tipo di processo Web e ridistribuire, eliminare il *webjobs-publish-settings.jssu* file. In questo modo, in Visual Studio vengono rivisualizzate le opzioni di pubblicazione, quindi è possibile modificare il tipo di processo Web.

* Se si distribuisce un processo Web e successivamente si modifica la modalità di esecuzione da continua a non continua o viceversa, Visual Studio crea un nuovo processo Web in Azure quando si esegue nuovamente la distribuzione. Se si modificano altre impostazioni di pianificazione, ma si lascia la modalità di esecuzione identica o si passa da pianificazione a richiesta, Visual Studio aggiorna il processo esistente anziché crearne uno nuovo.

## <a name="webjob-types"></a>Tipi di processi Web

Il tipo di processo Web può essere *attivato* o *continuo*:

- Attivato (impostazione predefinita): un processo Web attivato viene avviato in base a un evento di binding, in base a una [pianificazione](#scheduling-a-triggered-webjob)o quando viene attivato manualmente (su richiesta). Viene eseguito in tutte le istanze in cui viene eseguita l'app Web, ma facoltativamente è possibile limitare la processo Web a una singola istanza.

- Continuous: un processo Web [continuo](#continuous-execution) inizia immediatamente dopo la creazione del processo Web. Questo tipo di processo Web è ideale per i processi senza binding o con esecuzione prolungata. Se il processo termina, è possibile riavviarlo.  

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Pianificazione di un processo Web attivato

Quando si pubblica un'app console in Azure, Visual Studio imposta il tipo di processo Web su **attivato** per impostazione predefinita e aggiunge un nuovo file *Settings. job* al progetto. Per i tipi processo Web attivati, è possibile usare questo file per impostare una pianificazione di esecuzione per il processo Web.

Usare il file *Settings. job* per impostare una pianificazione di esecuzione per la processo Web. L'esempio seguente viene eseguito ogni ora dalle 9.00 alle 17.00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Questo file si trova nella directory radice della cartella processi Web con lo script di processo Web, ad esempio `wwwroot\app_data\jobs\triggered\{job name}` o `wwwroot\app_data\jobs\continuous\{job name}` . Quando si distribuisce un processo Web da Visual Studio, contrassegnare le proprietà del file *Settings. job* in Visual Studio come **Copy se più recente**.

Se si [Crea un processo Web dalla portale di Azure](webjobs-create.md), il file *Settings. job* viene creato automaticamente.

#### <a name="cron-expressions"></a>Espressioni CRON

Processi Web usa le stesse espressioni CRON per la pianificazione del trigger timer in funzioni di Azure. Per altre informazioni sul supporto CRON, vedere [timer trigger for Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>informazioni di riferimento su Settings. job

Le impostazioni seguenti sono supportate da processi Web:

| **Impostazione** | **Tipo**  | **Descrizione** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tutti | Consente l'esecuzione del processo Web senza prima essere copiata in una cartella temporanea. Per ulteriori informazioni, vedere la pagina relativa alla [directory di lavoro processo Web](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Continua | Quando si aumenta la scalabilità orizzontale, eseguire processo Web in una singola istanza. Per altre informazioni, vedere [impostare un processo continuo come singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Attivato | Eseguire processo Web in base a una pianificazione basata su CRON. Per altre informazioni, vedere [espressioni NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Tutti | Consente di controllare il comportamento di arresto. Per altre informazioni, vedere l'articolo sull' [arresto normale dei processi](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Esecuzione continua

Se si abilita **Always on** in Azure, è possibile usare Visual Studio per modificare il processo Web per l'esecuzione continua:

1. Se non è già stato fatto, [pubblicare il progetto in Azure](#deploy-to-azure-app-service).

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. Nella scheda **pubblica** scegliere **modifica**. 

1. Nella finestra di dialogo **Impostazioni profilo** scegliere **continua** per **tipo processo Web**, quindi scegliere **Salva**.

    ![Finestra di dialogo Impostazioni di pubblicazione per un processo Web](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selezionare **pubblica** nella scheda **pubblica** per ripubblicare il processo Web con le impostazioni aggiornate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su WebJobs SDK](webjobs-sdk-how-to.md)
