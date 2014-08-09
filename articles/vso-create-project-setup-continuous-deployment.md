<properties linkid="create-vso-project-setup-continuous-deployment" urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="How to create a Visual Studio Online team project and setup Continuous Deployment - Windows Azure" metaKeywords="Visual Studio Online create team project, continuous deployment to Azure" description="Learn how to create a Visual Studio Online team project and configure it for continuous deployment to Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="" editor="" />

Creazione di un progetto di Visual Studio Online e configurazione della distribuzione continua in Azure
=======================================================================================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Tramite il portale di gestione di Azure è possibile creare un progetto team in Visual Studio Online e configurare l'applicazione Web per la distribuzione continua in un sito Web.

Sommario
--------

-   [Come creare un progetto team](#create_team_project)
-   [Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git](#create_web_app)
-   [Come configurare la distribuzione continua](#continuous_deployment)

Come creare un progetto team
----------------------------

1.  Accedere al portale di gestione.
2.  Fare clic su **New** nell'angolo inferiore sinistro.
3.  Fare clic su **Team Project**.
4.  Assegnare un nome al progetto team. Si noti che non sarà possibile modificare il nome del progetto team dopo la creazione.
5.  Selezionare il tipo di controllo della versione da utilizzare per il progetto. è possibile scegliere Git (un sistema di controllo della versione distribuito) o Team Foundation Version Control (un sistema di controllo della versione centralizzato). In caso di dubbi sul sistema da utilizzare, fare clic [qui](http://msdn.microsoft.com/it-it/library/ms181368.aspx) per ulteriori informazioni.
6.  Scegliere il modello di processo. Per un confronto tra i modelli di processo, vedere [Utilizzare elementi del progetto team](http://msdn.microsoft.com/it-it/library/ms400752.aspx).
7.  Scegliere l'account di Visual Studio Online da utilizzare per creare questo progetto team, aggiungere utenti e monitorare l'utilizzo delle risorse.
8.  Lasciare la casella di controllo **Add to Startboard** selezionata, in modo che il nuovo progetto team venga visualizzato automaticamente nello startboard.
9.  Fare clic su **Create**.

Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git
------------------------------------------------------------------------------------

1.  Dallo startboard fare clic sul nuovo progetto team.
2.  Nella finestra **Code**, nella parte **Repositories**, fare clic sull'archivio Git denominato come il progetto team.
3.  Nel blade dell'archivio o del branch fare clic sul comando a livello di blade **Visual Studio** per aprire il nuovo archivio in Visual Studio. Nel Web browser può essere visualizzata una richiesta di autorizzazione all'avvio di Visual Studio.
4.  Nella finestra dello strumento Team Explorer di Visual Studio fare clic su **Eseguire la clonazione di questo repository** per configurare un clone locale del nuovo archivio nel disco locale.
5.  Nella sezione **Soluzioni** della home page di Team Explorer fare clic su **Nuovo...** per creare un nuovo progetto nell'archivio appena clonato.
6.  Nella finestra di dialogo Nuovo progetto espandere il nodo Visual Basic o Visual C\#, in base al linguaggio di programmazione utilizzato, quindi selezionare **Web**.
7.  Fare clic su **Applicazione Web ASP.NET** nell'elenco dei modelli di progetto disponibili e immettere un nome per la nuova applicazione Web.
8.  Fare clic su **OK**.
9.  Tornare alla finestra dello strumento Team Explorer, passare alla pagina Modifiche e immettere un messaggio per il commit.
10. Fare clic sulla freccia a discesa relativa al pulsante **Commit** e selezionare **Commit e sincronizzazione** per eseguire il commit delle modifiche nell'archivio remoto clonato in precedenza.

Come configurare la distribuzione continua
------------------------------------------

1.  Accedere al portale di gestione.
2.  Dallo startboard fare clic sul progetto team creato in precedenza.
3.  Nella finestra **Build** fare clic sulla parte **Set up continuous deployment**.
4.  Selezionare il sito Web in cui si desidera distribuire l'applicazione Web.
5.  Selezionare l'archivio in cui si trova il codice sorgente. A questo punto il progetto team dovrebbe contenere un unico archivio.
6.  Selezionare il branch da compilare. Visual Studio Online analizzerà il contenuto del commit più recente in questo branch alla ricerca di un singolo file di soluzione Visual Studio (con estensione sln). Se ne viene individuato uno, verrà configurata una nuova definizione di compilazione (il cui nome termina con "\_CD") per compilare la soluzione. Se non viene individuato un file di soluzione o se ne viene individuato più di uno, verrà comunque configurata una nuova definizione di compilazione, ma sarà disabilitata e sarà necessario modificarla in Visual Studio per specificare la soluzione da compilare.
7.  Fare clic su **Create**.
8.  Visual Studio Online creerà una nuova definizione di compilazione per compilare e distribuire il progetto di applicazione e tenterà inserire in coda una nuova compilazione di tale definizione.

### Per verificare che la distribuzione sia stata completata correttamente

1.  Dallo startboard fare clic sul progetto team creato in precedenza.
2.  Nella finestra **Build** fare clic sulla parte **Latest build** per aprire il blade di compilazione.
3.  Nel blade di compilazione fare clic sul primo elemento nella parte **Deployments** per aprire il sito Web associato.
4.  Nel blade del sito Web fare clic sul comando a livello di blade **Browse** per esplorare il sito Web e verificare la distribuzione dell'applicazione Web.

