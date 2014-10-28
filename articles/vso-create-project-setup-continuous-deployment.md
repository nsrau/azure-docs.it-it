<properties linkid="create-vso-project-setup-continuous-deployment" urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="How to create a Visual Studio Online team project and setup Continuous Deployment - Windows Azure" metaKeywords="Visual Studio Online create team project, continuous deployment to Azure" description="Learn how to create a Visual Studio Online team project and configure it for continuous deployment to Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb"></tags>

# Creazione di un progetto di Visual Studio Online e configurazione della distribuzione continua in Azure

[WACOM.INCLUDE [disclaimer][disclaimer]]

Tramite il portale di gestione di Azure è possibile creare un progetto team in Visual Studio Online e configurare l'applicazione Web per la distribuzione continua in un sito Web.

## Sommario

-   [Come creare un progetto team][Come creare un progetto team]
-   [Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git][Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git]
-   [Come configurare la distribuzione continua][Come configurare la distribuzione continua]

## <a name="create_team_project"></a>Come creare un progetto team

1.  Accedere al portale di gestione.
2.  Fare clic su **New** nell'angolo inferiore sinistro.
3.  Fare clic su **Team Project**.
4.  Assegnare un nome al progetto team. Si noti che non sarà possibile modificare il nome del progetto team dopo la creazione.
5.  Selezionare il tipo di controllo della versione da usare per il progetto. è possibile scegliere Git (un sistema di controllo della versione distribuito) o Team Foundation Version Control (un sistema di controllo della versione centralizzato). In caso di dubbi sul sistema da usare, fare clic [qui][qui] per altre informazioni.
6.  Scegliere il modello di processo. Per un confronto tra i modelli di processo, vedere [Usare elementi del progetto team][Usare elementi del progetto team].
7.  Scegliere l'account di Visual Studio Online da usare per creare questo progetto team, aggiungere utenti e monitorare l'uso delle risorse.
8.  Lasciare la casella di controllo **Aggiungi alla Schermata iniziale** selezionata, in modo che il nuovo progetto team venga visualizzato automaticamente nella schermata iniziale.
9.  Fare clic su **Create**.

## <a name="create_web_app"></a>Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git

1.  Nella schermata iniziale fare clic sul nuovo progetto team.
2.  Nella finestra **Code**, nella parte **Repositories**, fare clic sul repository Git denominato come il progetto team.
3.  Nel pannello del repository o del branch fare clic sul comando a livello di pannello **Visual Studio** per aprire il nuovo repository in Visual Studio. Nel Web browser può essere visualizzata una richiesta di autorizzazione all'avvio di Visual Studio.
4.  Nella finestra dello strumento Team Explorer di Visual Studio fare clic su **Eseguire la clonazione di questo repository** per configurare un clone locale del nuovo repository nel disco locale.
5.  Nella sezione **Soluzioni** della home page di Team Explorer fare clic su **Nuovo** per creare un nuovo progetto nel repository appena clonato.
6.  Nella finestra di dialogo Nuovo progetto espandere il nodo Visual Basic o Visual C#, in base al linguaggio di programmazione utilizzato, quindi selezionare **Web**.
7.  Fare clic su **Applicazione Web ASP.NET** nell'elenco dei modelli di progetto disponibili e immettere un nome per la nuova applicazione Web.
8.  Fare clic su **OK**.
9.  Tornare alla finestra dello strumento Team Explorer, passare alla pagina Modifiche e immettere un messaggio per il commit.
10. Fare clic sulla freccia a discesa relativa al pulsante **Commit** e selezionare **Commit e sincronizzazione** per eseguire il commit delle modifiche nel repository remoto clonato in precedenza.

## <a name="continuous_deployment"></a>Come configurare la distribuzione continua

1.  Accedere al portale di gestione.
2.  Nella schermata iniziale fare clic sul progetto team creato in precedenza.
3.  Nella finestra **Build** fare clic sulla parte **Set up continuous deployment**.
4.  Selezionare il sito Web in cui si desidera distribuire l'applicazione Web.
5.  Selezionare il repository in cui si trova il codice sorgente. A questo punto il progetto team dovrebbe contenere un unico repository.
6.  Selezionare il branch da compilare. Visual Studio Online analizzerà il contenuto del commit più recente in questo branch alla ricerca di un singolo file di soluzione Visual Studio (con estensione sln). Se ne viene individuato uno, verrà configurata una nuova definizione di compilazione (il cui nome termina con "\_CD") per compilare la soluzione. Se non viene individuato un file di soluzione o se ne viene individuato più di uno, verrà comunque configurata una nuova definizione di compilazione, ma sarà disabilitata e sarà necessario modificarla in Visual Studio per specificare la soluzione da compilare.
7.  Fare clic su **Create**.
8.  Visual Studio Online creerà una nuova definizione di compilazione per compilare e distribuire il progetto di applicazione e tenterà inserire in coda una nuova compilazione di tale definizione.

### Per verificare che la distribuzione sia stata completata correttamente

1.  Nella schermata iniziale fare clic sul progetto team creato in precedenza.
2.  Nella finestra **Build** fare clic sulla parte **Latest build** per aprire il pannello di compilazione.
3.  Nel pannello di compilazione fare clic sul primo elemento nella parte **Deployments** per aprire il sito Web associato.
4.  Nel pannello del sito Web fare clic sul comando a livello di pannello **Browse** per esplorare il sito Web e verificare la distribuzione dell'applicazione Web.

  [disclaimer]: ../includes/disclaimer.md
  [Come creare un progetto team]: #create_team_project
  [Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git]: #create_web_app
  [Come configurare la distribuzione continua]: #continuous_deployment
  [qui]: http://msdn.microsoft.com/it-it/library/ms181368.aspx
  [Usare elementi del progetto team]: http://msdn.microsoft.com/it-it/library/ms400752.aspx
