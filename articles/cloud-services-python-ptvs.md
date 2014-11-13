<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Ruoli Web e ruolo di lavori Python con Python Tools 2.1 per Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Panoramica dell'uso di Python Tools per Visual Studio per creare servizi cloud di Azure, inclusi ruoli Web e ruoli di lavoro." metaCanonical="" services="" documentationCenter="Python" title="Ruoli Web e ruolo di lavori Python con Python Tools 2.1 per Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Ruoli Web e ruolo di lavori Python con Python Tools 2.1 per Visual Studio

Questa guida fornisce una panoramica dell'uso dei ruoli Web e di lavoro con [Python Tools per Visual Studio][Python Tools per Visual Studio].

-   [Prerequisiti][Prerequisiti]
-   [Cosa sono i ruoli Web e di lavoro Python?][Cosa sono i ruoli Web e di lavoro Python?]
-   [Creazione del progetto][Creazione del progetto]
-   [Esecuzione locale][Esecuzione locale]
-   [Pubblicazione in Azure][Pubblicazione in Azure]
-   [Passaggi successivi][Passaggi successivi]

## <a name="prerequisites"></a>Prerequisiti

-   Visual Studio 2012 o 2013
-   [Python Tools 2,1 per Visual Studio][Python Tools 2,1 per Visual Studio]
-   [Strumenti di Azure SDK per VS 2013][Strumenti di Azure SDK per VS 2013] o [Strumenti di Azure SDK per VS 2012][Strumenti di Azure SDK per VS 2012]
-   [Python 2.7 a 32 bit][Python 2.7 a 32 bit] o [Python 3.4 a 32 bit][Python 3.4 a 32 bit]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Cosa sono i ruoli Web e di lavoro Python?

In Azure sono disponibili tre modelli di calcolo per l'esecuzione di applicazioni: [Siti Web di Azure][Siti Web di Azure], [Macchine virtuali di Azure][Macchine virtuali di Azure] e [Servizi cloud di Azure][Servizi cloud di Azure]. Tutti e tre i modelli supportano Python. Servizi cloud, che include ruoli Web e di lavoro, fornisce la tecnologia *PaaS (Platform as a Service)*. Nell'ambito di un servizio cloud, un ruolo Web fornisce un server Web IIS (Internet Information Services) dedicato su cui ospitare applicazioni Web front-end, mentre un ruolo di lavoro consente di eseguire attività asincrone, a esecuzione prolungata o perpetue, indipendenti dall'interazione o dall'input degli utenti.

Per altre informazioni, vedere la pagina relativa alla [definizione di servizio cloud][definizione di servizio cloud].

<div class="dev-callout"><strong>Come creare un semplice sito Web</strong>
<p>Se si intende creare un semplice sito Web front-end, &egrave; possibile usare un sito Web di Azure semplificato. &Egrave; possibile procedere all'aggiornamento a un servizio cloud con facilit&agrave;, in base alla crescita del sito Web e all'insorgere di nuove esigenze. Vedere il <a href="/it-it/develop/python/">centro per sviluppatori di Python</a> per articoli sullo sviluppo di siti Web di Azure.</p>
</div>

## <a name="project-creation"></a>Creazione del progetto

In Visual Studio, è possibile selezionare **Servizio Cloud Azure** nella finestra di dialogo **Nuovo progetto** sotto **Python**.

![Finestra di dialogo Nuovo progetto][Finestra di dialogo Nuovo progetto]

Nella procedura guidata del servizio cloud di Azure, è possibile scegliere di creare nuovi ruoli Web e di lavoro.

![Finestra di dialogo del servizio cloud di Azure][Finestra di dialogo del servizio cloud di Azure]

Nel modello di ruolo di lavoro è disponibile il codice boilerplate per connettersi a un bus di servizio o a un account di archiviazione di Azure.

![Soluzione del servizio cloud][Soluzione del servizio cloud]

È possibile aggiungere ruoli di lavoro o Web a un servizio cloud esistente in qualsiasi momento. È possibile scegliere di aggiungere alla soluzione progetti esistenti oppure crearne uno nuovo.

![Comando per l'aggiunta di un ruolo][Comando per l'aggiunta di un ruolo]

Il servizio cloud può contenere ruoli implementati in linguaggi diversi. Ad esempio, è possibile avere un ruolo Web Python implementato usando Django, con ruoli di lavoro Python e C#. È possibile mettere facilmente in comunicazione i ruoli con code di bus di servizio o code di archiviazione.

## <a name="run-locally"></a>Esecuzione locale

Se si imposta il progetto servizio cloud come progetto di avvio e si preme F5, il servizio cloud verrà eseguito nell'emulatore locale di Azure.

Anche se PTVS supporta l'avvio nell'emulatore, il debug (punti di interruzione e così via) non funzionerà.

Per eseguire il debug dei ruoli di lavoro e Web, è possibile impostare il progetto di ruolo come progetto di avvio e ed eseguirne il debug. È anche possibile impostare più progetti di avvio. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**.

![Proprietà del progetto di avvio della soluzione][Proprietà del progetto di avvio della soluzione]

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Per pubblicare, fare clic con il pulsante destro del mouse sul progetto servizio cloud nella soluzione e scegliere **Pubblica**.

![Accesso per la pubblicazione di Microsoft Azure][Accesso per la pubblicazione di Microsoft Azure]

Nella pagina delle impostazioni selezionare il servizio cloud che si vuole pubblicare.

![Impostazioni di pubblicazione di Microsoft Azure][Impostazioni di pubblicazione di Microsoft Azure]

È possibile creare un nuovo servizio cloud se non ne è già disponibile uno.

![Finestra di dialogo Crea servizio cloud][Finestra di dialogo Crea servizio cloud]

È anche utile abilitare connessioni Desktop remoto a uno o più computer per eseguire il debug degli errori.

![Finestra di dialogo Configurazione Desktop remoto][Finestra di dialogo Configurazione Desktop remoto]

Dopo aver finito di configurare le impostazioni, fare clic su **Pubblica**.

Nella finestra di output verranno visualizzati alcuni stati, quindi apparirà la finestra Log attività di Microsoft Azure.

![Finestra Log attività di Microsoft Azure][Finestra Log attività di Microsoft Azure]

Saranno necessari alcuni minuti per completare la distribuzione, quindi i ruoli di lavoro e/o Web verranno eseguiti in Azure.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'uso di ruoli di lavoro e Web in Python Tools per Visual Studio, vedere la documentazione PTVS:

-   [Progetti servizio cloud][Progetti servizio cloud]

Per informazioni dettagliate sull'uso di servizi di Azure dai ruoli di lavoro e Web, ad esempio sull'uso dell'archiviazione o del bus di servizio di Azure, vedere le guide seguenti:

-   [Servizio BLOB][Servizio BLOB]
-   [Servizio tabelle][Servizio tabelle]
-   [Servizio di accodamento][Servizio di accodamento]
-   [Code del bus di servizio][Code del bus di servizio]
-   [Argomenti del bus di servizio][Argomenti del bus di servizio]

<!--Link references--> <!--External Link references-->

  [Python Tools per Visual Studio]: http://pytools.codeplex.com
  [Prerequisiti]: #prerequisites
  [Cosa sono i ruoli Web e di lavoro Python?]: #what-are-python-web-and-worker-roles
  [Creazione del progetto]: #project-creation
  [Esecuzione locale]: #run-locally
  [Pubblicazione in Azure]: #publish-to-azure
  [Passaggi successivi]: #next-steps
  [Python Tools 2,1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Strumenti di Azure SDK per VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Strumenti di Azure SDK per VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Siti Web di Azure]: /it-it/documentation/articles/fundamentals-application-models/#WebSites
  [Macchine virtuali di Azure]: /it-it/documentation/articles/fundamentals-application-models/#VMachine
  [Servizi cloud di Azure]: /it-it/documentation/articles/fundamentals-application-models/#CloudServices
  [definizione di servizio cloud]: /it-it/manage/services/cloud-services/what-is-a-cloud-service/
  [Finestra di dialogo Nuovo progetto]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Finestra di dialogo del servizio cloud di Azure]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Soluzione del servizio cloud]: ./media/cloud-services-python-ptvs/worker.png
  [Comando per l'aggiunta di un ruolo]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Proprietà del progetto di avvio della soluzione]: ./media/cloud-services-python-ptvs/startup.png
  [Accesso per la pubblicazione di Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Impostazioni di pubblicazione di Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Finestra di dialogo Crea servizio cloud]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Finestra di dialogo Configurazione Desktop remoto]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Finestra Log attività di Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Servizio BLOB]: /it-it/documentation/articles/storage-python-how-to-use-blob-storage/
  [Servizio tabelle]: /it-it/documentation/articles/storage-python-how-to-use-table-storage/
  [Servizio di accodamento]: /it-it/documentation/articles/storage-python-how-to-use-queue-storage/
  [Code del bus di servizio]: /it-it/documentation/articles/service-bus-python-how-to-use-queues/
  [Argomenti del bus di servizio]: /it-it/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
