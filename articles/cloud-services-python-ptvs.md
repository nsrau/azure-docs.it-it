<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Ruoli Web e ruolo di lavori Python con Python Tools 2.1 per Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Overview of using Python Tools for Visual Studio to create Azure cloud services including web roles and worker roles." metaCanonical="" services="" documentationCenter="Python" title="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Ruoli Web e ruolo di lavori Python con Python Tools 2.1 per Visual Studio

Questa guida fornisce una panoramica dell'uso dei ruoli Web e di lavoro con [Python Tools per Visual Studio][].

+ [Prerequisiti](#prerequisites)
+ [Cosa sono i ruoli Web e di lavoro Python?](#what-are-python-web-and-worker-roles)
+ [Creazione del progetto](#project-creation)
+ [Eseguire in locale](#run-locally)
+ [Pubblicare in Azure](#publish-to-azure)
+ [Passaggi successivi](#next-steps)

##<a name="prerequisites"></a>Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per Visual Studio 2013][] o [Strumenti di Azure SDK per Visual Studio 2012][]
 - [Python 2.7 a 32 bit][] o [Python 3.4 a 32 bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="what-are-python-web-and-worker-roles"></a>Cosa sono i ruoli Web e di lavoro Python?

In Azure sono disponibili tre modelli di calcolo per l'esecuzione di applicazioni: [Siti Web di Azure][execution model-web sites], [Macchine virtuali di Azure][execution model-vms] e [Servizi cloud di Azure][execution model-cloud services]. Tutti e tre i modelli supportano Python. Servizi cloud, che include ruoli Web e di lavoro, fornisce la *piattaforma distribuita come servizio (PaaS)*. Nell'ambito di un servizio cloud, un ruolo Web fornisce un server Web IIS (Internet Information Services) dedicato su cui ospitare applicazioni Web front-end, mentre un ruolo di lavoro consente di eseguire attività asincrone, a esecuzione prolungata o perpetue, indipendenti dall'interazione o dall'input degli utenti.

Per altre informazioni, vedere la pagina relativa alla [definizione di servizio cloud].

<div class="dev-callout"><strong>Come creare un semplice sito Web</strong>
<p>Se si intende creare un semplice sito Web front-end, è possibile usare un sito Web di Azure semplificato. È possibile procedere all'aggiornamento a un servizio cloud con facilità, in base alla crescita del sito Web e all'insorgere di nuove esigenze. Vedere il <a href="/it-it/develop/python/">centro per sviluppatori di Python</a> per articoli sullo sviluppo di siti Web di Azure.</p>
</div>
<br />


##<a name="project-creation"></a>Creazione del progetto

In Visual Studio, è possibile selezionare **Servizio Cloud Azure** nella finestra di dialogo **Nuovo progetto** sotto **Python**. 

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Nella procedura guidata del servizio cloud di Azure, è possibile scegliere di creare nuovi ruoli Web e di lavoro.

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

Nel modello di ruolo di lavoro è disponibile il codice boilerplate per connettersi a un bus di servizio o a un account di archiviazione di Azure.

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

È possibile aggiungere ruoli di lavoro o Web a un servizio cloud esistente in qualsiasi momento.  È possibile scegliere di aggiungere alla soluzione progetti esistenti oppure crearne uno nuovo. 

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Il servizio cloud può contenere ruoli implementati in linguaggi diversi.  Ad esempio, è possibile avere un ruolo Web Python implementato usando Django, con ruoli di lavoro Python e C#.  È possibile mettere facilmente in comunicazione i ruoli con code di bus di servizio o code di archiviazione.

##<a name="run-locally"></a>Esecuzione locale

Se si imposta il progetto servizio cloud come progetto di avvio e si preme F5, il servizio cloud verrà eseguito nell'emulatore locale di Azure.

Anche se PTVS supporta l'avvio nell'emulatore, il debug (punti di interruzione e così via) non funzionerà.

Per eseguire il debug dei ruoli di lavoro e Web, è possibile impostare il progetto di ruolo come progetto di avvio e ed eseguirne il debug.  È anche possibile impostare più progetti di avvio.  Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**.

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

##<a name="publish-to-azure"></a>Pubblicazione in Azure

Per pubblicare, fare clic con il pulsante destro del mouse sul progetto servizio cloud nella soluzione e scegliere **Pubblica**.

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

Nella pagina delle impostazioni selezionare il servizio cloud che si vuole pubblicare.

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

È possibile creare un nuovo servizio cloud se non ne è già disponibile uno.

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

È anche utile abilitare connessioni Desktop remoto a uno o più computer per eseguire il debug degli errori.

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Dopo aver finito di configurare le impostazioni, fare clic su **Pubblica**.

Nella finestra di output verranno visualizzati alcuni stati, quindi apparirà la finestra Log attività di Microsoft Azure.

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

Saranno necessari alcuni minuti per completare la distribuzione, quindi i ruoli di lavoro e/o Web verranno eseguiti in Azure.

##<a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'uso di ruoli di lavoro e Web in Python Tools per Visual Studio, vedere la documentazione PTVS:

- [Progetti servizio cloud][]

Per informazioni dettagliate sull'uso di servizi di Azure dai ruoli di lavoro e Web, ad esempio sull'uso dell'archiviazione o del bus di servizio di Azure, vedere le guide seguenti:
 
- [Servizio BLOB][]
- [Servizio tabelle][]
- [Servizio di accodamento][]
- [Code del bus di servizio][]
- [Argomenti del bus di servizio][]


<!--Link references-->

[Informazioni sul servizio cloud]: /it-it/manage/services/cloud-services/what-is-a-cloud-service/
[modello di esecuzione-Siti Web]: /it-it/documentation/articles/fundamentals-application-models/#WebSites
[modello di esecuzione-vms]: /it-it/documentation/articles/fundamentals-application-models/#VMachine
[modello di esecuzione-Servizi cloud]: /it-it/documentation/articles/fundamentals-application-models/#CloudServices
[Centro per sviluppatori di Python]: /it-it/develop/python/

[Servizio BLOB]: /it-it/documentation/articles/storage-python-how-to-use-blob-storage/
[Servizio di accodamento]: /it-it/documentation/articles/storage-python-how-to-use-queue-storage/
[Servizio tabelle]: /it-it/documentation/articles/storage-python-how-to-use-table-storage/
[Code del bus di servizio]: /it-it/documentation/articles/service-bus-python-how-to-use-queues/
[Argomenti del bus di servizio]: /it-it/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/


<!--External Link references-->

[Python Tools per Visual Studio]: http://pytools.codeplex.com
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation 
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti di Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191

<!--HONumber=35_1-->
