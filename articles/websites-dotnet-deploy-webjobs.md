<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Come distribuire i processi Web Azure in Siti Web di Azure

Questo argomento illustra come usare Visual Studio per distribuire un progetto di applicazione console in un sito Web di Azure come [processo Web Azure][processo Web Azure]. Un metodo alternativo per distribuire i processi Web consiste nell'usare il portale di gestione di Azure. Per informazioni su tale metodo, vedere la pagina relativa all'[uso dei processi Web per eseguire le attività in background in Siti Web di Microsoft Azure][uso dei processi Web per eseguire le attività in background in Siti Web di Microsoft Azure].

## Sommario

-   [Panoramica][Panoramica]
-   [Prerequisiti][Prerequisiti]
-   [Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente][Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente]
-   [Creare un nuovo progetto abilitato per i processi Web][Creare un nuovo progetto abilitato per i processi Web]
-   [Finestra di dialogo Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure]
-   [File webjob-publish-settings.json][File webjob-publish-settings.json]
-   [File webjobs-list.json][File webjobs-list.json]
-   [Distribuire un progetto processi Web][Distribuire un progetto processi Web]
-   [Passaggi successivi][Passaggi successivi]

## <span id="overview"></span></a>Panoramica

Quando distribuisce un progetto di applicazione console abilitato per i processi Web, Visual Studio esegue due attività:

-   Copia i file di runtime nella cartella appropriata nel sito Web di Azure (*App\_Data/jobs/continuous* per i processi Web in modalità continua, *App\_Data/jobs/triggered* per i processi Web pianificati e su richiesta).
-   Configura i [processi dell'utilità di pianificazione di Azure][processi dell'utilità di pianificazione di Azure] per i processi Web pianificati per essere eseguiti a orari specifici. Tale operazione non è necessaria per l'esecuzione dei processi Web in modalità continua.

A un progetto abilitato per i processi Web vengono aggiunti gli elementi seguenti:

-   Pacchetto NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish].
-   File [webjob-publish-settings.json][File webjob-publish-settings.json] che contiene le impostazioni di distribuzione e dell'utilità di pianificazione.

![Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web][Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web]

È possibile aggiungere questi elementi a un progetto di applicazione console esistente o usare un modello per creare un nuovo progetto di applicazione console abilitato per i processi Web.

È possibile distribuire un progetto come processo Web indipendente o collegarlo a un progetto Web in modo tale che venga distribuito automaticamente ogni volta che viene distribuito il progetto Web. Per collegare i progetti, Visual Studio include il nome del progetto abilitato per i processi Web in un file [webjobs-list.json][File webjobs-list.json] nel progetto Web.

![Diagramma che mostra il collegamento del progetto processo Web al progetto Web][Diagramma che mostra il collegamento del progetto processo Web al progetto Web]

## Prerequisiti

Le funzionalità di distribuzione dei processi Web sono disponibili in Visual Studio 2013 quando si installa Azure SDK versione 2.4 o versione successiva:

-   [Azure SDK per Visual Studio 2013][Azure SDK per Visual Studio 2013].

Le funzionalità di distribuzione dei processi Web sono incluse anche in [Update 3][Update 3].

## <span id="convert"></span></a>Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente

Sono disponibili due opzioni:

-   [Abilitare la distribuzione automatica con un progetto Web][Abilitare la distribuzione automatica con un progetto Web].

    Configurare un progetto di applicazione console esistente in modo tale che venga distribuito automaticamente come processo Web quando viene distribuito un progetto Web. Usare questa opzione quando si vuole eseguire il processo Web nello stesso sito Web in cui viene eseguita l'applicazione Web correlata.

-   [Abilitare la distribuzione senza un progetto Web][Abilitare la distribuzione senza un progetto Web].

    Configurare un progetto di applicazione console esistente in modo tale che venga distribuito come processo Web indipendente senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un sito Web in modo indipendente senza l'esecuzione dell'applicazione Web nel sito Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.

### <span id="convertlink"></span></a> Abilitare la distribuzione automatica dei processi Web con un progetto Web

1.  Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** \> **Progetto esistente come processo Web Azure**.

    ![Progetto esistente come processo Web Azure][Progetto esistente come processo Web Azure]

    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure].

2.  Nell'elenco a discesa **Nome progetto** selezionare il progetto di applicazione console da aggiungere come processo Web.

    ![Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure][Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure]

3.  Completare la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure], quindi fare clic su **OK**.

### <span id="convertnolink"></span></a> Abilitare la distribuzione dei processi Web senza un progetto Web

1.  Fare clic con il pulsante destro del mouse sul progetto di applicazione console in **Esplora soluzioni**, quindi scegliere **Pubblica come processo Web Azure**.

    ![Pubblica come processo Web Azure][Pubblica come processo Web Azure]

    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure] con il progetto selezionato nella casella **Nome progetto**.

2.  Completare la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure], quindi fare clic su **OK**.

    Viene visualizzata la procedura guidata **Pubblica sito Web**. Se non si vuole eseguire subito la pubblicazione, chiudere la procedura guidata. Le impostazioni immesse vengono salvate in modo da poter essere usate quando si vuole [distribuire il progetto][Distribuire un progetto processi Web].

## <span id="create"></span></a>Creare un nuovo progetto abilitato per i processi Web

Per creare un nuovo progetto abilitato per i processi Web, è possibile usare il modello del progetto di applicazione console e abilitare la distribuzione dei processi Web come descritto nella [sezione precedente][Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente]. In alternativa, è possibile usare il modello nuovo-progetto di processi Web:

-   [Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web][Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web]

    Creare un progetto configurato in modo da essere distribuito automaticamente come processo Web quando viene distribuito un progetto Web nella stessa soluzione. Usare questa opzione quando si vuole eseguire il processo Web nello stesso sito Web in cui viene eseguita l'applicazione Web correlata.

-   [Usare il modello nuovo-progetto di processi Web per un processo Web indipendente][Usare il modello nuovo-progetto di processi Web per un processo Web indipendente]

    Creare un progetto e configurarlo per essere distribuito in modo indipendente come processo Web senza alcun collegamento a un progetto Web. Usare questa opzione quando si vuole eseguire un processo Web in un sito Web in modo indipendente senza l'esecuzione dell'applicazione Web nel sito Web. Questa opzione potrebbe essere utile per scalare le risorse del processo Web indipendentemente dalle risorse dell'applicazione Web.

Nella versione SDK 2.4 il modello nuovo-progetto di processi Web non è molto più facile da usare rispetto alla creazione di un progetto di applicazione console e all'abilitazione della distribuzione dei processi Web. In futuro, il modello nuovo-progetto di processi Web sarà più pratico per lo sviluppo di [WebJobs SDK][WebJobs SDK], perché installerà automaticamente i pacchetti NuGet appropriati di WebJobs SDK. Fino ad allora, è possibile configurare un progetto in modo da usare WebJobs SDK installando i pacchetti manualmente come illustrato nell'[esercitazione di WebJobs SDK][WebJobs SDK].

### <span id="createlink"></span></a> Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web

-   Fare clic con il pulsante destro del mouse sul progetto Web in **Esplora soluzioni**, quindi scegliere **Aggiungi** \> **Nuovo progetto processo Web Azure**.

    ![Voce del menu Nuovo progetto processo Web Azure][Voce del menu Nuovo progetto processo Web Azure]

    Viene visualizzata la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure].

-   Completare la finestra di dialogo [Aggiungi processo Web Azure][Finestra di dialogo Aggiungi processo Web Azure], quindi fare clic su **OK**.

### <span id="createnolink"></span></a> Usare il modello nuovo-progetto di processi Web per un processo Web indipendente

1.  Fare clic su **File** \> **Nuovo progetto**, quindi nella finestra di dialogo **Nuovo progetto** fare clic su **Cloud** \> **Processo Web Microsoft Azure**.

    ![Finestra di dialogo Nuovo progetto con il modello processo Web][Finestra di dialogo Nuovo progetto con il modello processo Web]

2.  Seguire le istruzioni illustrate in precedenza per [rendere il progetto di applicazione console un progetto processi Web indipendente][Abilitare la distribuzione senza un progetto Web].

## <span id="configure"></span></a>Finestra di dialogo Aggiungi processo Web Azure

La finestra di dialogo **Aggiungi processo Web Azure** consente di immettere il nome del processo Web e le impostazioni di pianificazione per il processo Web.

![Finestra di dialogo Aggiungi processo Web Azure][1]

I campi in questa finestra di dialogo corrispondono ai campi presenti nella finestra di dialogo **Nuovo processo** del portale di gestione di Azure. Per altre informazioni, vedere la pagina relativa all'[uso dei processi Web per eseguire le attività in background in Siti Web di Microsoft Azure][uso dei processi Web per eseguire le attività in background in Siti Web di Microsoft Azure].

Per un processo Web pianificato (non per i processi Web in modalità continua), Visual Studio crea una raccolta di processi dell'[utilità di pianificazione di Azure][utilità di pianificazione di Azure], se non ne esiste ancora una, e crea un processo nella raccolta:

-   La raccolta di processi dell'utilità di pianificazione viene denominata *WebJobs-{nomearea}*, dove *{nomearea}* si riferisce all'area in cui è ospitato il sito Web. Ad esempio: WebJobs-WestUS.
-   Il processo dell'utilità di pianificazione viene denominato *{nomesitoweb}-{nomeprocessoweb}*. Ad esempio: MyWebSite-MyWebJob.

> [WACOM.NOTE]
>
> -   La distribuzione da riga di comando con MSBuild non configura la pianificazione per i processi Web pianificati. Questa funzionalità funziona solo quando la distribuzione viene eseguita con Visual Studio.
> -   Se si configura un **Processo ricorrente** e si imposta una frequenza di ricorrenza su un numero di minuti, il servizio dell'utilità di pianificazione di Azure non è gratuito. Altre frequenze (ore, giorni e così via) sono gratuite.
> -   Se si distribuisce un processo Web e successivamente si modifica la modalità di esecuzione da continua a non continua o viceversa, Visual Studio crea un nuovo processo Web in Azure quando si esegue nuovamente la distribuzione. Se si modificano le altre impostazioni di pianificazione lasciando invariata la modalità di esecuzione o passando dalla modalità pianificata a quella su richiesta o viceversa, Visual Studio aggiorna il processo esistente anziché crearne uno nuovo.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Quando si configura un'applicazione console per la distribuzione dei processi Web, Visual Studio installa il pacchetto NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish]
e archivia le informazioni di pianificazione in un file *webjob-publish-settings.json* nel cartella *Proprietà* del progetto processi Web. Di seguito è riportato un esempio di tale file:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [][]<http://schemastore.org></a> da dove può essere visualizzato.

> [WACOM.NOTE]
>
> -   Se si configura un **Processo ricorrente** e si imposta una frequenza di ricorrenza su un numero di minuti, il servizio dell'utilità di pianificazione di Azure non è gratuito. Altre frequenze (ore, giorni e così via) sono gratuite.

## <span id="webjobslist"></span></a>webjobs-list.json

Quando si collega un progetto abilitato per i processi Web in un progetto Web, Visual Studio archivia il nome del progetto processi Web in un file *webjobs-list.json* nella cartella *Proprietà* del progetto Web. L'elenco può contenere più progetti processi Web, come illustrato nell'esempio seguente:

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

È possibile modificare questo file direttamente e Visual Studio fornisce IntelliSense. Lo schema del file viene archiviato in [][2]<http://schemastore.org></a> da dove può essere visualizzato.

## <span id="deploy"></span></a>Distribuire un progetto processi Web

Un progetto processi Web collegato a un progetto Web viene distribuito automaticamente con il progetto Web. Per informazioni sulla distribuzione dei progetti Web, vedere [Come distribuire un sito Web di Azure][Come distribuire un sito Web di Azure].

Per distribuire un progetto processi Web indipendente, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**, quindi scegliere **Pubblica come processo Web Azure**.

![Pubblica come processo Web Azure][Pubblica come processo Web Azure]

Per un processo Web indipendente viene visualizzata la stessa procedura guidata **Pubblica sito Web** usata per i progetti Web ma con meno impostazioni disponibili da modificare.

## <span id="nextsteps"></span></a>Passaggi successivi

Per altre informazioni sui processi Web Azure e su WebJobs SDK, vedere [Processi Web Azure - Risorse consigliate][Processi Web Azure - Risorse consigliate].

  [processo Web Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
  [uso dei processi Web per eseguire le attività in background in Siti Web di Microsoft Azure]: /it-it/documentation/articles/web-sites-create-web-jobs/
  [Panoramica]: #overview
  [Prerequisiti]: #prerequisites
  [Abilitare la distribuzione dei processi Web per un progetto di applicazione console esistente]: #convert
  [Creare un nuovo progetto abilitato per i processi Web]: #create
  [Finestra di dialogo Aggiungi processo Web Azure]: #configure
  [File webjob-publish-settings.json]: #publishsettings
  [File webjobs-list.json]: #webjobslist
  [Distribuire un progetto processi Web]: #deploy
  [Passaggi successivi]: #next-steps
  [processi dell'utilità di pianificazione di Azure]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Diagramma che mostra gli elementi aggiunti a un'app console per abilitare la distribuzione come processo Web]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Diagramma che mostra il collegamento del progetto processo Web al progetto Web]: ./media/websites-dotnet-deploy-webjobs/link.png
  [Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Abilitare la distribuzione automatica con un progetto Web]: #convertlink
  [Abilitare la distribuzione senza un progetto Web]: #convertnolink
  [Progetto esistente come processo Web Azure]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Selezione del progetto nella finestra di dialogo Aggiungi processo Web Azure]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Pubblica come processo Web Azure]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Usare il modello nuovo-progetto di processi Web per un processo Web collegato a un progetto Web]: #createlink
  [Usare il modello nuovo-progetto di processi Web per un processo Web indipendente]: #createnolink
  [WebJobs SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Voce del menu Nuovo progetto processo Web Azure]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [Finestra di dialogo Nuovo progetto con il modello processo Web]: ./media/websites-dotnet-deploy-webjobs/np.png
  [1]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [utilità di pianificazione di Azure]: /en-us/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [2]: http://schemastore.org/schemas/json/webjobs-list.json
  [Come distribuire un sito Web di Azure]: /it-it/documentation/articles/websites-dotnet-deploy/
  [Processi Web Azure - Risorse consigliate]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
