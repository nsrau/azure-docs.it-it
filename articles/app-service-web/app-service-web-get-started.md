<properties 
	pageTitle="Distribuire la prima app Web in Azure in 5 minuti" 
	description="Informazioni facilità di esecuzione delle app Web nel servizio app mediante la distribuzione di un'app di esempio con pochi passaggi. In 5 minuti sarà possibile eseguire vere e proprie attività di sviluppo con risultati immediati." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="05/12/2016" 
	ms.author="cephalin"
/>
	
# Distribuire la prima app Web in Azure in 5 minuti

[AZURE.INCLUDE [schede](../../includes/app-service-web-get-started-nav-tabs.md)]

Questa esercitazione illustra come distribuire la prima app Web nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Il servizio app consente di creare app Web, [back-end dell'app per dispositivi mobili](/documentation/learning-paths/appservice-mobileapps/) e [app per le api](../app-service-api/app-service-api-apps-why-best-platform.md).

Con poche operazioni sarà possibile:

- Distribuire un'app Web di esempio (scegliere tra ASP.NET, PHP, Node.js, Java o Python).
- Visualizzare l'app eseguita dinamicamente in pochi secondi.
- Aggiornare l'app Web nello stesso modo in cui si effettua il [push dei commit Git](https://git-scm.com/docs/git-push).

Verrà anche fornita una panoramica del [portale di Azure](https://portal.azure.com) e si esamineranno le funzionalità disponibili nel portale.

## Prerequisiti

- [Installare Git](http://www.git-scm.com/downloads). 
- [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). 
- Ottenere un account Microsoft Azure. Se non è disponibile un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Verificare il funzionamento di un'app Web. È possibile [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) immediatamente e creare un'app iniziale temporanea, senza dovere specificare una carta di credito e senza impegni.

## Distribuire un'app Web

Verrà ora distribuita un'app Web in Servizio app di Azure.

1. Aprire un nuovo prompt dei comandi di Windows, una finestra di PowerShell, una shell di Linux o un terminale di OS X. Eseguire `git --version` e `azure --version` per verificare che Git e l'interfaccia della riga di comando di Azure siano installati nel computer. 

    ![Installazione di prova degli strumenti dell'interfaccia della riga di comando per la prima app Web in Azure](./media/app-service-web-get-started/1-test-tools.png)

    Se gli strumenti non sono stati installati, vedere i [Prerequisiti](#Prerequisites) per i collegamenti di download.

1. Eseguire il comando `CD` in una directory di lavoro e clonare l'app di esempio, come illustrato di seguito:

        git clone <github_sample_url>

    ![Clonare il codice dell'app di esempio per la prima app Web in Azure](./media/app-service-web-get-started/2-clone-sample.png)

    Per *&lt;github\_sample\_url>* usare uno degli URL seguenti, a seconda del framework preferito:

    - HTML+CSS+JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. Eseguire il comando `CD` nel repository dell'app di esempio. Ad esempio,

        cd app-service-web-html-get-started

3. Accedere ad Azure in questo modo:

        azure login
    
    Seguire le istruzioni del messaggio della Guida per continuare il processo di accesso.
    
    ![Accedere ad Azure per creare la prima app Web](./media/app-service-web-get-started/3-azure-login.png)

4. Creare la risorsa dell'app del servizio app in Azure con un nome app univoco con il comando successivo. Quando richiesto, specificare il numero dell'area desiderata.

        azure site create --git <app_name>
    
    ![Creare la risorsa di Azure per la prima app Web in Azure](./media/app-service-web-get-started/4-create-site.png)
    
    >[AZURE.NOTE] Se non sono mai state configurate credenziali di distribuzione per la sottoscrizione di Azure, verrà chiesto di crearle. Queste credenziali, e non le credenziali dell'account Azure, vengono usate dal servizio app solo per le distribuzioni Git e gli accessi FTP.
    
    Ora l'app viene creata in Azure. La directory corrente viene inizializzata da Git e connessa alla nuova app del servizio app come Git remoto. È possibile passare all'URL dell'app, http://&lt;app_name>.azurewebsites.net, per visualizzare la pagina HTML predefinita. A questo punto, inserire il proprio codice.

4. Distribuire il codice di esempio nella nuova app del servizio app nello stesso modo in cui si effettua il push del codice con Git:

        git push azure master 

    ![Eseguire il push di codice nella prima app Web in Azure](./media/app-service-web-get-started/5-push-code.png)
    
    Se è stato usato uno dei framework di linguaggio, verrà visualizzato un output diverso da quello riportato in precedenza. Ciò dipende dal fatto che `git push` non solo inserisce il codice in Azure, ma attiva anche le attività di distribuzione nel motore di distribuzione. Se nella radice del progetto (repository) è presente un file package.json (Node.js) o requirements.txt (Python) o se nel progetto ASP.NET è presente un file packages.config, gli script di distribuzione ripristinano automaticamente i pacchetti necessari. È anche possibile [abilitare l'estensione Composer](web-sites-php-mysql-deploy-use-git.md#composer) per elaborare automaticamente i file composer.json nell'app PHP.

La distribuzione dell'app in Servizio app di Azure è stata completata.

## Visualizzare l'app eseguita dinamicamente

Per visualizzare l'app eseguita dinamicamente in Azure, eseguire questo comando da qualsiasi directory del repository:

    azure site browse

## Eseguire aggiornamenti dell'app

Ora è possibile usare Git per effettuare il push dalla radice del progetto (repository) in qualsiasi momento per poter eseguire un aggiornamento del sito attivo. La procedura è uguale alla prima distribuzione dell'app in Azure. Ad esempio, quando si vuole effettuare il push di una nuova modifica testata in locale, è sufficiente eseguire i comandi seguenti dalla radice del progetto (repository):
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Visualizzare l'app nel portale di Azure

Ora si accederà al portale di Azure per visualizzare ciò che è stato creato:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account Microsoft a cui è associata la sottoscrizione di Azure.

2. Nella barra a sinistra fare clic su **Servizi app**.

3. Fare clic sull'app appena creata per aprire la relativa pagina, definita [pannello](../azure-portal-overview.md), nel portale. Per praticità anche il pannello **Impostazioni** viene aperto per impostazione predefinita.

    ![Visualizzazione del portale della prima app Web in Azure](./media/app-service-web-get-started/portal-view.png)

Il pannello del portale dell'app del servizio app include un set completo di impostazioni e strumenti per la configurazione, il monitoraggio, la protezione e la risoluzione dei problemi dell'app. Per familiarizzare con questa interfaccia, eseguire alcune semplici attività. Il numero dell'attività corrisponde al numero nella schermata:

1. Arrestare l'app
2. Riavviare l'app
3. Fare clic sul collegamento **Gruppo di risorse** per visualizzare tutte le risorse distribuite nel gruppo di risorse
4. Fare clic su **Impostazioni** > **Proprietà** per visualizzare altre informazioni sull'app
5. Fare clic su **Strumenti** per accedere a strumenti utili per il monitoraggio e la risoluzione dei problemi  

## Passaggi successivi

- Ottimizzare l'app di Azure. Proteggerla con l'autenticazione. Ridimensionarla in base alla richiesta. Configurare alcuni avvisi sulle prestazioni. Tutto con pochi clic. Vedere [Aggiungere funzionalità alla prima app Web](app-service-web-get-started-2.md).
- Oltre a Git e all'interfaccia della riga di comando di Azure, è possibile distribuire in altri modi le app Web in Azure. Vedere [Distribuire l'app nel servizio app di Azure](../app-service-web/web-sites-deploy.md). Individuare le procedure di sviluppo e distribuzione per il framework di linguaggio selezionando il framework nella parte superiore dell'articolo.

<!---HONumber=AcomDC_0518_2016-->