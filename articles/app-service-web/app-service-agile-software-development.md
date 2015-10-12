<properties
	pageTitle="Agile Software Development con il servizio app di Azure"
	description="Informazioni su come creare applicazioni complesse con scalabilità elevata con il servizio app di Azure in modo da supportare Agile Software Development."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="cephalin"/>


# Agile Software Development con il servizio app di Azure #

In questa esercitazione, si imparerà a creare applicazioni complesse con scalabilità elevata con il [servizio app di Azure](/services/app-service/) in modo da supportare [Agile Software Development](https://en.wikipedia.org/wiki/Agile_software_development). L'esercitazione presuppone che si sappia già come [distribuire applicazioni complesse in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md).

Le limitazioni nei processi tecnici spesso possono rappresentare un ostacolo alla corretta implementazione di metodologie Agile. Il servizio app di Azure, grazie a funzionalità come la [pubblicazione continua](web-sites-publish-source-control.md), gli [ambienti di gestione temporanea](web-sites-staged-publishing.md) (slot) e il [monitoraggio](web-sites-monitor.md), se attentamente associato all'orchestrazione e alla gestione della distribuzione in [Gestione risorse di Azure](resource-group-overview.md), può essere parte di un'ottima soluzione per gli sviluppatori che adottano Agile Software Development.

La seguente tabella contiene un breve elenco di requisiti associati allo sviluppo Agile, ciascuno dei quali viene abilitato dai servizi di Azure.

| Requisito | Abilitazione mediante Azure |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| - Compilazione con ogni commit<br>- Compilazione automatica e rapida | Quando viene configurato con la distribuzione continua, il servizio app di Azure può funzionare come una compilazione con esecuzione live basata su un ramo di sviluppo. Ogni volta che viene effettuato il push del codice nel ramo, il codice viene compilato automaticamente ed eseguito in Azure.|
| - Compilazioni testate automaticamente | I test di carico, i test Web e così via possono essere distribuiti con il modello di Gestione risorse di Azure.|
| - Esecuzione di test in un clone dell'ambiente di produzione | I modelli di Gestione risorse di Azure possono essere usati per creare cloni dell'ambiente di produzione di Azure (inclusi le impostazioni delle app, i modelli di stringa di connessione, la scalabilità e così via) per eseguire test in modo rapido e prevedibile.|
| - Facile visualizzazione del risultato dell'ultima compilazione | La distribuzione continua in Azure da un repository consente di testare il nuovo codice in un'applicazione live immediatamente dopo il commit delle modifiche. |
| - Commit giornaliero al ramo principale<br>- Automatizzazione della distribuzione | L'integrazione continua di un'applicazione di produzione con il ramo principale di un repository distribuisce automaticamente nell'ambiente di produzione ogni commit/unione nel ramo principale. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Contenuto dell'esercitazione ##

Viene illustrato in dettaglio un tipico flusso di lavoro sviluppo-test-gestione temporanea-produzione per pubblicare nuove modifiche all'applicazione di esempio [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), costituita da due [app Web](/services/app-service/web/), un front-end (FE) e un back-end (BE) dell'API Web, e da un [database SQL](/services/sql-database/). Verrà usata l'architettura di distribuzione illustrata di seguito:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Spiegazione dell'immagine:

-	L'architettura di distribuzione è divisa in tre ambienti separati (o [gruppi di risorse](resource-group-overview.md) in Azure), ciascuno con il proprio [piano di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md), impostazioni di [scalabilità](web-sites-scale.md) e database SQL. 
-	Ogni ambiente può essere gestito separatamente. Possono anche essere presenti in sottoscrizioni diverse.
-	La gestione temporanea e la produzione vengono implementate come due slot della stessa app del servizio app. Il ramo master è configurato per l'integrazione continuata con lo slot di gestione temporanea.
-	Quando un commit al ramo master viene verificato nello slot di gestione temporanea (con i dati di produzione), l'app di gestione temporanea verificata viene scambiata nello slot di produzione [senza tempi di inattività](web-sites-staged-publishing.md).

L'ambiente di produzione e di gestione temporanea viene definito dal modello in [*&lt;radice\_repository>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Gli ambienti di sviluppo e di test vengono definiti dal modello in [*&lt;radice\_repository>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Si userà anche la tipica strategia di diramazione, in cui il codice viene spostato dal ramo di sviluppo fino al ramo di test, quindi al ramo master (compiendo, per così dire, un salto di qualità).

![](./media/app-service-agile-software-development/what-2-branches.png)

## Prerequisiti ##

-	Un account Azure
-	Un account [GitHub](https://github.com/)
-	Git Shell (installato con [GitHub per Windows](https://windows.github.com/)): consente di eseguire comandi sia Git che PowerShell nella stessa sessione 
-	Ultimi bit di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi)
-	Conoscenza di base degli argomenti seguenti:
	-	Distribuzione di modelli di [Gestione risorse di Azure](resource-group-overview.md) (vedere anche [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio le app Web. È possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
>
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Configurare l'ambiente di produzione ##

>[AZURE.NOTE]Lo script usato in questa esercitazione configurerà automaticamente la pubblicazione continua dal repository GitHub. A questo scopo, è necessario che le credenziali GitHub siano già archiviate in Azure, altrimenti la distribuzione con script ha esito negativo quando si tenta di configurare le impostazioni di controllo del codice sorgente per le app Web.
>
>Per archiviare le credenziali GitHub in Azure, creare un'app Web nel [Portale di anteprima di Azure](https://portal.azure.com) e [configurare la distribuzione GitHub](web-sites-publish-source-control.md#Step7). È necessario eseguire questa operazione una sola volta.

In uno scenario tipico di sviluppo, in Azure è in esecuzione un'applicazione a cui si desidera apportare modifiche tramite la pubblicazione continua. In questo scenario, è necessario distribuire nell'ambiente di produzione un modello sviluppato, testato e usato, che verrà configurato in questa sezione.

1.	Creare la biforcazione del repository [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Per informazioni sulla creazione della biforcazione, vedere la pagina relativa alla [biforcazione di un repository](https://help.github.com/articles/fork-a-repo/). Una volta creata la biforcazione, è possibile visualizzarla nel browser.
 
	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Aprire una sessione di Git Shell. Se non si dispone ancora di Git Shell, installare ora [GitHub per Windows](https://windows.github.com/).

3.	Creare un clone locale della biforcazione eseguendo il comando seguente:

		git clone https://github.com/<your_fork>/ToDoApp.git 

4.	Una volta creato il clone locale, passare a *&lt;radice\_repository>*\\ARMTemplates ed eseguire lo script deploy.ps1 come segue:

		.\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.	Quando richiesto, digitare il nome utente e la password desiderati per l'accesso al database.

	Verrà visualizzato l'avanzamento del provisioning delle varie risorse di Azure. Al termine della distribuzione, lo script avvierà l'applicazione nel browser ed emetterà un breve segnale acustico.

	![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
	>[AZURE.TIP]Esaminare *&lt;radice\_repository>*\\ARMTemplates\\Deploy.ps1, per osservare come le risorse vengano generate con ID univoci. È possibile usare lo stesso approccio per creare cloni della stessa distribuzione senza doversi preoccupare dei conflitti tra i nomi di risorse.
 
6.	Tornare alla sessione di Git Shell ed eseguire:

		.\swap –Name ToDoApp<unique_string>master

	![](./media/app-service-agile-software-development/production-4-swap.png)

7.	Al termine dello script, tornare indietro per passare all'indirizzo del front-end (http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/) per visualizzare l'applicazione in esecuzione nell'ambiente di produzione.
 
5.	Accedere al [portale di anteprima di Azure](https://portal.azure.com) per vedere gli elementi creati.

	Dovrebbero essere visibili due app Web nello stesso gruppo di risorse, una con il suffisso `Api` nel nome. Osservando il gruppo di risorse, si noteranno anche il server e il database SQL, il piano del servizio app e gli slot di gestione temporanea per le app Web. Esplorare le diverse risorse e confrontarle con *&lt;radice\_repository>*\\ARMTemplates\\ProdAndStage.json per verificare come vengono configurate nel modello.

	![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

L'ambiente di produzione è stato impostato. In seguito si preparerà un nuovo aggiornamento dell'applicazione.

## Creare i rami di sviluppo e di test ##

Ora che un'applicazione complessa è in esecuzione nell'ambiente di produzione in Azure, si creerà un aggiornamento dell'applicazione secondo la metodologia Agile. In questa sezione, si creeranno i rami di sviluppo e di test che saranno necessari per eseguire gli aggiornamenti richiesti.

1.	Creare innanzitutto l'ambiente di test. Nella sessione di Git Shell eseguire i seguenti comandi per creare l'ambiente per un nuovo ramo denominato **NewUpdate**. 

		git checkout -b NewUpdate
		git push origin NewUpdate 
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.	Quando richiesto, digitare il nome utente e la password desiderati per l'accesso al database.

	Al termine della distribuzione, lo script avvierà l'applicazione nel browser ed emetterà un breve segnale acustico. Ora è disponibile un nuovo ramo con il proprio ambiente di test. Prima di proseguire, esaminare alcune caratteristiche di questo ambiente di test:

	-	È possibile crearlo in qualsiasi sottoscrizione di Azure. Ciò significa che l'ambiente di produzione può essere gestito separatamente dall'ambiente di test.
	-	L'ambiente di test è in esecuzione in Azure.
	-	L'ambiente di test è identico all'ambiente di produzione, fatta eccezione per gli slot di gestione temporanea e le impostazioni di scalabilità, come si può intuire dal fatto che queste sono le uniche differenze tra ProdandStage.json e Dev.json.
	-	È possibile gestire l'ambiente di test nel piano di servizio app, con un livello di prezzo diverso (ad esempio, **Gratuito**).
	-	Eliminare questo ambiente di test sarà semplice come eliminare il gruppo di risorse. Questa operazione verrà illustrata [più avanti](#delete).

2.	Procedere con la creazione di un ramo di sviluppo eseguendo i comandi seguenti:

		git checkout -b Dev
		git push origin Dev
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.	Quando richiesto, digitare il nome utente e la password desiderati per l'accesso al database.

	Prima di proseguire, esaminare alcune caratteristiche di questo ambiente di sviluppo:

	-	L'ambiente di sviluppo ha una configurazione identica all'ambiente di test perché viene distribuito usando lo stesso modello.
	-	Ogni ambiente di sviluppo può essere creato nella sottoscrizione di Azure dello sviluppatore, permettendo così di gestire separatamente l'ambiente di test.
	-	L'ambiente di sviluppo è in esecuzione in Azure.
	-	Eliminare questo ambiente di sviluppo è semplice come eliminare il gruppo di risorse. Questa operazione verrà illustrata [più avanti](#delete).

>[AZURE.NOTE]Quando al nuovo aggiornamento lavorano più sviluppatori, ognuno può creare facilmente un ramo e un ambiente di sviluppo dedicato effettuando le operazioni seguenti:
>
>1.	Creare la propria biforcazione del repository in GitHub (vedere la pagina relativa alla [biforcazione di un repository](https://help.github.com/articles/fork-a-repo/)).
>2.	Clonare la biforcazione nel proprio computer locale.
>3.	Eseguire gli stessi comandi per creare il proprio ambiente e ramo di sviluppo.

Al termine, la biforcazione GitHub dovrebbe avere tre rami:

![](./media/app-service-agile-software-development/test-1-github-view.png)

E dovrebbero essere presenti sei app Web (tre set di due) in tre gruppi di risorse separati:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE]Si noti che ProdandStage.json specifica l'ambiente di produzione per usare il livello di prezzo **Standard**, che garantisce la scalabilità dell'applicazione di produzione.

## Compilare e testare ogni commit ##

I file di modello ProdAndStage.json e Dev.json specificano già i parametri di controllo del codice sorgente, che, per impostazione predefinita, configurano la pubblicazione continua per l'app Web. Pertanto, ogni commit al ramo GitHub attiva una distribuzione automatica in Azure da tale ramo. Ecco come funziona ora la configurazione.

1.	Assicurarsi di essere nel ramo Dev del repository locale. A tale scopo, eseguire il comando seguente in Git Shell:

		git checkout Dev

2.	Apportare una semplice modifica al livello dell'interfaccia utente dell'app modificando il codice in modo da usare gli elenchi [Bootstrap](http://getbootstrap.com/components/). Aprire *&lt;radice\_repository>*\\src\\MultiChannelToDo.Web\\app\\index.cshtml e apportare la modifica evidenziata sotto:

	![](./media/app-service-agile-software-development/commit-1-changes.png)

	>[AZURE.NOTE]Se non è possibile leggere l'immagine precedente:
	>
	>- Nella riga 18 sostituire `check-list` con `list-group`.
	>- Nella riga 19 sostituire `class="check-list-item"` con `class="list-group-item"`.

3.	Salvare la modifica. Tornare in Git Shell ed eseguire i comandi seguenti:

		cd <repository_root>
		git add .
		git commit -m "changed to bootstrap style"
		git push origin Dev
 
	Questi comandi git sono simili al controllo del codice in un altro sistema di controllo del codice sorgente, ad esempio TFS. Quando si esegue `git push`, il nuovo commit attiva un push di codice automatico in Azure, che ricompila quindi l'applicazione in modo da rendere effettiva la modifica nell'ambiente di sviluppo.

4.	Per verificare che questo push del codice nell'ambiente di sviluppo sia stato eseguito, andare al pannello dell'app Web dell'ambiente di sviluppo ed esaminare la sezione **Distribuzione**, dove dovrebbe essere visibile l'ultimo messaggio di commit.

	![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.	Fare clic su **Sfoglia** per visualizzare la nuova modifica nell'applicazione live in Azure.

	![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

	Si tratta di una modifica secondaria all'applicazione. Spesso tuttavia le nuove modifiche apportate a un'applicazione Web complessa presentano effetti collaterali imprevisti e indesiderati. La possibilità di testare facilmente ogni commit nelle compilazioni live consente di individuare questi problemi prima dei clienti.

Per ora è sufficiente sapere che gli sviluppatori del progetto **NewUpdate** potranno creare facilmente un ambiente di sviluppo per l'utente, quindi compilare ogni commit e testare ogni compilazione.

## Unire il codice nell'ambiente di test ##

Quando si è pronti per effettuare il push del codice dal ramo Dev fino al ramo NewUpdate, si esegue il processo Git standard:

1.	Unire i nuovi commit per NewUpdate nel ramo Dev in GitHub, ad esempio i commit creati da altri sviluppatori. Qualsiasi nuovo commit in GitHub attiverà un push del codice e una compilazione nell'ambiente di sviluppo. È quindi possibile verificare che il codice nel ramo Dev funzioni ancora con i bit più recenti del ramo NewUpdate.

2.	Unire tutti i nuovi commit del ramo Dev nel ramo NewUpdate in GitHub. Questa azione attiva un push del codice e una compilazione nell'ambiente di test.

Si noti ancora una volta che, poiché la distribuzione continua è già configurata con questi rami git, non è necessario eseguire nessun'altra azione, ad esempio l'esecuzione di compilazioni di integrazione. È sufficiente eseguire le procedure di controllo del codice sorgente standard usando git e Azure eseguirà automaticamente tutti i processi di compilazione.

Ora si effettuerà il push del codice nel ramo **NewUpdate**. In Git Shell eseguire i comandi seguenti:

	git checkout NewUpdate
	git pull origin NewUpdate
	git merge Dev
	git push origin NewUpdate

La procedura è terminata.

Passare al pannello dell'app Web per l'ambiente di test per visualizzare il nuovo commit (unito nel ramo NewUpdate) di cui è stato appena effettuato il push nell'ambiente di test. Fare quindi clic su **Esplora** per osservare che la modifica apportata allo stile ora è in esecuzione in Azure.

## Distribuire l'aggiornamento nell'ambiente di produzione ##

Il push del codice nell'ambiente di gestione temporanea e di produzione non dovrebbe risultare diverso dal push del codice nell'ambiente di test. È davvero molto semplice.

In Git Shell eseguire i comandi seguenti:

	git checkout master
	git pull origin master
	git merge NewUpdate
	git push origin master

Tenere presente che, in base alla configurazione dell'ambiente di gestione temporanea e di produzione in ProdandStage.json, il push del nuovo codice viene effettuato nello slot **Staging**, dove viene eseguito. Quindi, se si passa all'URL dello slot di gestione temporanea, verrà visualizzato il nuovo codice in esecuzione. A questo scopo, eseguire il cmdlet `Show-AzureWebsite` in Git Shell.

	Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
A questo punto, dopo avere verificato l'aggiornamento nello slot di gestione temporanea, rimane solo da scambiarlo nell'ambiente di produzione. In Git Shell eseguire i comandi seguenti:

	cd <repository_root>\ARMTemplates
	.\swap.ps1 -Name ToDoApp<unique_string>master

Congratulazioni. È stato pubblicato un nuovo aggiornamento dell'applicazione Web di produzione. Ancora più importante, per eseguire questa operazione è stato sufficiente creare facilmente gli ambienti di sviluppo e di test e compilare e testare ogni commit. Questi sono gli elementi essenziali per Agile Software Development.

<a name="delete"></a>
## Eliminare gli ambienti di sviluppo e di test ##

Poiché gli ambienti di sviluppo e di test sono stati volutamente concepiti come gruppi di risorse autonomi, è molto facile eliminarli. Per eliminare quelli creati in questa esercitazione, sia i rami GitHub che gli elementi di Azure, eseguire i comandi seguenti in Git Shell:

	git branch -d Dev
	git push origin :Dev
	git branch -d NewUpdate
	git push origin :NewUpdate
	Switch-AzureMode AzureResourceManager
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## Riepilogo ##

Agile Software Development è uno strumento indispensabile per molte aziende che desiderano adottare Azure come piattaforma dell'applicazione. In questa esercitazione si è appreso a creare ed eliminare repliche esatte o molto simili dell'ambiente di produzione con facilità, anche per applicazioni complesse. Si è appreso anche a sfruttare questa possibilità per creare un processo di sviluppo in grado di compilare e testare ogni singolo commit in Azure. Questa esercitazione dovrebbe avere illustrato come usare nel modo migliore il servizio app di Azure e Gestione risorse di Azure App integrandoli per creare una soluzione di sviluppo che fornisce le metodologie Agile.

## Altre risorse ##

-	[Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
-	[Sviluppo Agile in pratica: suggerimenti e consigli per un ciclo di sviluppo rinnovato](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-	[Strategie di distribuzione avanzate per le app Web di Azure che usano modelli di Gestione risorse](http://channel9.msdn.com/Events/Build/2015/2-620)
-	[Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md)
-	[JSONLint - Il validator JSON](http://jsonlint.com/)
-	[ARMClient - Configurare la pubblicazione GitHub nel sito](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-	[Diramazione Git - Diramazione e unione di base](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Blog di David Ebbo](http://blog.davidebbo.com/)
-	[Azure PowerShell](powershell-install-configure.md)
-	[Strumenti della riga di comando multipiattaforma di Azure](xplat-cli-install.md)
-	[Creare o modificare utenti in Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-	[Wiki del progetto Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=Oct15_HO1-->