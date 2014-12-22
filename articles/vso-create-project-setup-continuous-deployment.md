<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="Come creare un progetto team di Visual Studio Online e configurare la distribuzione continua - Microsoft Azure" metaKeywords="Visual Studio Online create team project, continuous deployment to Azure" description="Learn how to create a Visual Studio Online team project and configure it for continuous deployment to Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#Creazione di un progetto di Visual Studio Online e configurazione della distribuzione continua in Azure 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Tramite il portale di gestione di Azure è possibile creare un progetto team in Visual Studio Online e configurare l'applicazione Web per la distribuzione continua in un sito Web.

##Sommario##

* [Come creare un progetto team](#create_team_project)
* [Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git](#create_web_app)
* [Come configurare la distribuzione continua](#continuous_deployment)

## <a name="create_team_project"></a>Come creare un progetto team

1. Accedere al portale di gestione.
2. Fare clic su **Nuovo** nell'angolo inferiore sinistro.
3. Fare clic su **Progetto team**.
4. Assegnare un nome al progetto team. Si noti che non sarà possibile modificare il nome del progetto team dopo la creazione.
5. Selezionare il tipo di controllo della versione da usare per il progetto. è possibile scegliere Git (un sistema di controllo della versione distribuito) o Team Foundation Version Control (un sistema di controllo della versione centralizzato). In caso di dubbi sul sistema da usare, Altre informazioni [qui](http://msdn.microsoft.com/it-it/library/ms181368.aspx).
6. Scegliere il modello di processo. Per un confronto tra i modelli di processo, vedere [Usare elementi del progetto team](http://msdn.microsoft.com/it-it/library/ms400752.aspx).
7. Scegliere l'account di Visual Studio Online da usare per creare questo progetto team, aggiungere utenti e monitorare l'uso delle risorse.
8. Lasciare selezionata la casella di controllo **Aggiungi alla Schermata iniziale**, in modo che il nuovo progetto team venga visualizzato automaticamente nella schermata iniziale.
9. Fare clic su **Crea**.

## <a name="create_web_app"></a>Come creare una nuova applicazione Web e aggiungerla al controllo della versione Git

1. Nella schermata iniziale fare clic sul nuovo progetto team.
2. Nella sezione **Codice**, nella parte **Repository**, fare clic sul repository Git denominato in base al progetto team.
3. Nel pannello del repository o del branch fare clic sul comando a livello di pannello **Visual Studio** per aprire il nuovo repository in Visual Studio. Nel Web browser può essere visualizzata una richiesta di autorizzazione all'avvio di Visual Studio.
4.  Nella finestra dello strumento Team Explorer di Visual Studio fare clic su **Eseguire la clonazione di questo repository** per configurare un clone locale del nuovo repository nel disco locale.
5.  Nella sezione **Soluzioni**della home page di Team Explorer fare clic su **Nuovo** per creare un nuovo progetto nel repository appena clonato.
6.  Nella finestra di dialogo Nuovo progetto espandere il nodo Visual Basic o Visual C#, in base al linguaggio di programmazione usato, quindi selezionare **Web**.
7.  Fare clic su **Applicazione Web ASP.NET** nell'elenco dei modelli di progetto disponibili e immettere un nome per la nuova applicazione Web.
8.  Fare clic su **OK**.
9.  Tornare alla finestra dello strumento Team Explorer, passare alla pagina Modifiche e immettere un messaggio per il commit.
10.  Fare clic sulla freccia a discesa sul pulsante **Commit** e selezionare **Commit e sincronizzazione** per eseguire il commit delle modifiche nel repository remoto clonato in precedenza.

## <a name="continuous_deployment"></a>Come configurare la distribuzione continua

1. Accedere al portale di gestione.
2. Nella schermata iniziale fare clic sul progetto team creato in precedenza.
3. Nella sezione **Compilazione** fare clic sulla parte **Configura distribuzione continua**.
4. Selezionare il sito Web in cui si desidera distribuire l'applicazione Web.
5. Selezionare il repository in cui si trova il codice sorgente. A questo punto il progetto team dovrebbe contenere un unico repository.
6. Selezionare il branch da compilare. Visual Studio Online analizzerà il contenuto del commit più recente in questo branch alla ricerca di un singolo file di soluzione Visual Studio (con estensione sln). Se ne viene individuato uno, verrà configurata una nuova definizione di compilazione (il cui nome termina con "_CD") per compilare la soluzione. Se non viene individuato un file di soluzione o se ne viene individuato più di uno, verrà comunque configurata una nuova definizione di compilazione, ma sarà disabilitata e sarà necessario modificarla in Visual Studio per specificare la soluzione da compilare. 
7. Fare clic su **Crea**.
8. Visual Studio Online creerà una nuova definizione di compilazione per compilare e distribuire il progetto di applicazione e tenterà inserire in coda una nuova compilazione di tale definizione.

###Per verificare che la distribuzione sia stata completata correttamente###

1. Nella schermata iniziale fare clic sul progetto team creato in precedenza.
2. Nella sezione **Compilazione** fare clic sulla parte **Compilazione più recente** per aprire il pannello di compilazione.
3. Nel pannello di compilazione fare clic sul primo elemento nella parte **Distribuzioni** per aprire il sito Web associato.
4. Nel pannello del sito Web fare clic sul comando a livello di pannello **Sfoglia** per esplorare il sito Web e verificare la distribuzione dell'applicazione Web.
