<properties 
	pageTitle="Distribuzione di Node.js con Cloud9 - Esercitazione di Azure" 
	description="Informazioni su come usare l'IDE Cloud9 per sviluppare, creare e distribuire un'applicazione di tipo Node.js in Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Distribuzione di un'applicazione di Azure da Cloud9

Questa esercitazione illustra come usare l'IDE Cloud9 per sviluppare, creare e
distribuire un'applicazione di tipo Node.js in Azure.

In questa esercitazione si apprenderà come:

-   Creare un progetto dell'IDE Cloud9
-   Distribuire il progetto in Azure
-   Aggiornare una distribuzione di Azure esistente
-   Spostare progetti tra distribuzioni di gestione temporanea e produzione

L'[IDE Cloud9][] offre un ambiente di sviluppo basato su browser e idoneo a più
piattaforme. Una delle funzionalità supportate da Cloud9 per i progetti Node.js è
che è possibile distribuire direttamente in Azure dall'IDE.
Cloud9 si integra inoltre con i servizi di repository GitHub e BitBucket
che consentono di condividere facilmente il progetto con altri utenti.

Con Cloud9, è possibile sviluppare e distribuire un'applicazione in Azure
da molti browser e sistemi operativi moderni, senza dover
installare SDK o strumenti di sviluppo aggiuntivi in locale. Le seguenti procedure
vengono eseguite in Google Chrome su Mac.

## Iscriversi

Per usare Cloud9, è innanzitutto necessario accedere al relativo sito Web e [registrarsi per una
sottoscrizione ][IDE Cloud9]. È possibile accedere con un account esistente
di GitHub o BitBucket oppure creare un account Cloud9. È disponibile
un'offerta di sottoscrizione gratuita, oltre a un'offerta a pagamento,
che include più funzionalità. Per altre informazioni, vedere [IDE Cloud9][].

## Creare un progetto di Node.js

1.  Accedere a Cloud9, fare clic sul segno più (**+**) accanto a **My Projects**
    e quindi selezionare **Create a new project**.

	![create new Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  Nella finestra di dialogo **Create a new project** immettere un nome di progetto,
    l'accesso e il tipo di progetto. Fare clic su **Create** per creare il progetto.

	![create new project dialog Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE] Per alcune opzioni è necessario un piano di Cloud9 a pagamento.
	   
	> [AZURE.NOTE] Il nome del progetto di Cloud9 non viene usato durante la distribuzione in Azure.

3.  Al termine della creazione del progetto, fare clic su **Start Editing**. Se si sta usando l'IDE Cloud9 per la prima volta, sarà possibile visualizzare una presentazione del servizio. Se si desidera visualizzare la presentazione in un secondo momento, selezionare **Just the editor, please**.

	![start editing the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Per creare una nuova applicazione Node, selezionare **File** e quindi **New
    File**.

	![create new file in the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Verrà visualizzata una nuova scheda denominata **Untitled1**. Immettere il
    seguente codice nella scheda **Untitled1** per creare
    l'applicazione Node:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE] L'uso di process.env.PORT consente di assicurare che l'applicazione scelga la porta corretta in caso di esecuzione nel debugger di Cloud9 o durante la distribuzione in Azure.

6.  Per salvare il codice, selezionare **File** e quindi **Save as**. Nella finestra di dialogo
    **Save As** immettere **server.js** come nome file e quindi
    fare clic su **Save**.


	> [AZURE.NOTE] È possibile che venga visualizzato un simbolo di avviso, che indica il mancato uso della variabile req. Tale avviso può essere ignorato.

	![save the server.js file](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## Eseguire l'applicazione

> [AZURE.NOTE] Benché la procedura disponibile in questa sezione sia sufficiente per un'applicazione di tipo Hello World, è possibile che per le applicazioni che usano moduli esterni sia necessario selezionare una versione specifica di Node.js per l'ambiente di debug. A tale scopo, selezionare **Configure** nell'elenco a discesa di debug e quindi selezionare la versione specifica di Node.js. Ad esempio, possono verificarsi errori di autenticazione quando si usa il modulo 'azure' se Node.js 0.6.x non è selezionato.


1.  Fare clic su **Debug** per eseguire l'applicazione nel debugger di Cloud9.
	
	![run in the debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Verrà visualizzata una finestra di output. Fare clic sull'URL visualizzato per
    accedere all'applicazione tramite una finestra del browser.

	![output window](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	L'applicazione risultante avrà un aspetto analogo al seguente:

	![application running in browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Per interrompere il debug dell'applicazione, fare clic su **stop**.

## Creare un account Azure

Per distribuire l'applicazione in Azure, è necessario disporre di un account. Se non
si dispone già di un account Azure, è possibile iscriversi gratuitamente
per ottenere una versione di prova seguendo questa procedura:

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## Creare una distribuzione

1.  Per creare una nuova distribuzione, selezionare **Deploy** e quindi fare clic sul segno più (**+**) per creare un server di distribuzione.

    ![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

2.  Nella finestra di dialogo **Add a deploy target** immettere un nome per la distribuzione e quindi selezionare **Azure** nell'elenco **Choose type**. Il nome specificato per la distribuzione verrà usato per identificare la distribuzione in Cloud9, ma non corrisponderà al nome della distribuzione in Azure.

3.  Se si sta creando per la prima volta una distribuzione di Cloud9 che usa Azure, sarà necessario configurare le impostazioni di pubblicazione di Azure. Seguire questa procedura per scaricare e installare queste impostazioni in Cloud9:

    1.  Fare clic su **Download Azure Settings**.

        ![download publish settings](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Verrà aperto il portale di gestione di Azure e verrà richiesto di scaricare le impostazioni di pubblicazione di Azure. Prima di iniziare, sarà necessario accedere all'account Azure.

    2.  Salvare il file delle impostazioni di pubblicazione nell'unità locale.

    3.  Nella finestra di dialogo **Add a deploy target** selezionare **Choose File**
        e quindi scegliere il file scaricato nel passaggio precedente.

    4.  Dopo aver selezionato il file fare clic su **Upload**.

4.  Fare clic su **+ Create new** per creare un nuovo servizio ospitato. Un  *hosted service* è il contenitore in cui l'applicazione viene ospitata quando viene distribuita in Azure. Per altre informazioni, vedere l'argomento relativo alle [informazioni generali sulla creazione di un servizio ospitato per Azure][].

	![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  Verrà richiesto di specificare il nome del nuovo servizio ospitato e le opzioni di configurazione quali il numero di istanze, il sistema operativo di hosting e il data center. Il nome specificato per la distribuzione verrà usato come nome del servizio ospitato in Azure. Il nome deve essere univoco nel sistema di Azure.
	
	![create a new hosted service](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE] Nella finestra di dialogo **Add a deploy target** tutti i servizi ospitati di Azure verranno elencati nella sezione **Choose existing deployment**. Se si seleziona un servizio ospitato esistente, il progetto sarà distribuito in tale servizio.

	> [AZURE.NOTE] Se si seleziona **Enable RDP** e si specifica un nome utente e una password, Desktop remoto sarà abilitato per la distribuzione.


## Distribuire nell'ambiente di produzione di Azure

1.  Selezionare la distribuzione creata nel passaggio precedente. Verrà visualizzata
    una finestra di dialogo che fornisce informazioni su questa distribuzione e
    l'URL di produzione che verrà usato dopo la distribuzione in Microsoft
    Azure.

	![select a deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Selezionare **Deploy to Production environment**.

3.  Fare clic su **Deploy** per iniziare la distribuzione.

4.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No web.config found'**. Selezionare **Yes** per creare il file. Un file  'Web.cloud.config' verrà aggiunto al progetto.
	
	![no web.config file found message](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No  'csdef' file present'**. Selezionare **Yes** per creare il file con estensione csdef. Un file 'ServiceDefinition.csdef' verrà aggiunto al progetto.    ServiceDefinition.csdef è un file specifico di Azure, necessario per la pubblicazione dell'applicazione. Per altre informazioni, vedere l'argomento relativo alle [informazioni generali sulla creazione di un servizio ospitato per Azure][].

6.  Verrà richiesta la selezione delle dimensioni delle istanze per l'applicazione. Selezionare **Small** e quindi fare clic su **Create**. Per informazioni dettagliate sulle dimensioni delle macchine virtuali di Azure, vedere l'argomento relativo alla [configurazione delle dimensioni della macchina virtuale][].

	![specify csdef file values](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  Lo stato del processo di distribuzione sarà visualizzato nella voce relativa alla distribuzione. Al termine, la distribuzione sarà visualizzata come **Active**.

	![deployment status](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE] Ai progetti distribuiti tramite l'IDE Cloud 9 viene assegnato un GUID come nome per la distribuzione in Azure.

8.  Nella finestra di dialogo relativa alla distribuzione è disponibile un collegamento all'URL di produzione. Al termine della distribuzione, fare clic sull'URL per passare all'applicazione in esecuzione in Azure.

	![Azure production URL link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## Aggiornare l'applicazione

Quando si apportano modifiche all'applicazione, è possibile usare Cloud9 per distribuire l'applicazione aggiornata nello stesso servizio ospitato di Azure.

1.  Nel file server.js aggiornare il codice, in modo che sullo schermo venga visualizzato il testo "hello azure v2". È possibile sostituire il codice esistente con il seguente codice aggiornato:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Per salvare il codice, selezionare **File** e quindi **Save**.

## Distribuire l'aggiornamento nell'ambiente di gestione temporanea di Azure

1.  Selezionare **Deploy to Staging**.

2.  Fare clic su **Deploy** per iniziare la distribuzione.

	Ogni servizio ospitato di Azure supporta due ambienti, ovvero l'ambiente di gestione temporanea e di distribuzione. L'ambiente di gestione temporanea è identico all'ambiente di produzione, ma è possibile accedere all'applicazione di gestione temporanea solo tramite un URL offuscato basato su GUID generato da Azure. È possibile usare l'ambiente di gestione temporanea per testare l'applicazione e, dopo avere verificato le modifiche, sarà possibile passare in produzione la versione di gestione temporanea eseguendo uno scambio di indirizzi IP virtuali (VIP, Virtual IP), come illustrato più avanti in questa esercitazione.

3.  Quando l'applicazione è distribuita nell'ambiente di gestione temporanea, l'URL di gestione temporanea basato su GUID viene visualizzato nell'output della console, come mostrato nella schermata riportata di seguito. Fare clic sull'URL per aprire l'applicazione di gestione temporanea in un browser.

	![console output showing staging URL](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## Spostare l'aggiornamento in produzione tramite lo scambio di indirizzi VIP

Quando un servizio viene distribuito in un ambiente di produzione o gestione temporanea,
al servizio viene assegnato un indirizzo IP virtuale
(VIP). Quando si desidera spostare un servizio dall'ambiente di gestione temporanea
a quello di produzione, è necessario
ridistribuirlo effettuando uno scambio di indirizzi VIP, che scambia le distribuzioni di gestione temporanea
e di produzione. Uno scambio di indirizzi VIP imposta l'applicazione testata e in gestione temporanea
in produzione senza tempi di inattività nell'ambiente di produzione. Per altre
informazioni dettagliate, vedere l'argomento relativo alle [informazioni generali sulla gestione delle distribuzioni in Azure.][]

1.  Nella finestra di distribuzione, fare clic sul collegamento **Open portal** per aprire il
    portale di gestione di Azure.

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Accedere al portale usando le proprie credenziali.

3.  A sinistra della pagina web, selezionare **Servizi ospitati, account di archiviazione
    e CDN** e quindi fare clic su **Servizi ospitati**.

	![Azure Management Portal][Azure Management Portal]

	Nel riquadro dei risultati viene mostrato il servizio ospitato con il nome specificato in Cloud9, oltre a due distribuzioni, il cui valore    **Ambiente** è impostato su **Gestione temporanea**, per la prima, e su **Produzione**, per la seconda.

4.  Per eseguire lo scambio di indirizzi VIP, selezionare il servizio ospitato e fare clic su **Scambia VIP** sulla barra multifunzione.

	![VIP SWAP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Fare clic su **OK** nella finestra di dialogo Scambia VIP che viene visualizzata.

6.  Passare all'applicazione di produzione. Come si può notare, la versione dell'applicazione precedentemente distribuita nell'ambiente di gestione temporanea si trova ora nell'ambiente di produzione.

	![Production application running on Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## Usare Desktop remoto

Se RDP è stato abilitato e un nome utente e una password sono stati specificati durante la creazione della distribuzione, sarà possibile usare Desktop remoto per la connessione al servizio ospitato, selezionando un'istanza specifica e quindi selezionando Connect sulla
barra multifunzione.

![Connect to an instance](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Quando si fa clic su Connetti, verrà richiesto di aprire o scaricare un file RDP. Tale file include le informazioni necessarie per la connessione alla sessione di Desktop remoto. Se si esegue questo file in un sistema Windows, verranno richiesti il nome utente e la password specificati durante la creazione della distribuzione e verrà quindi effettuata la connessione al desktop dell'istanza
selezionata.

> [AZURE.NOTE] Il file RDP per la connessione all'istanza ospitata dell'applicazione funzionerà solo con l'applicazione Desktop remoto in
Windows.

## Interrompere ed eliminare l'applicazione

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria e un'applicazione in fase di distribuzione usa tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato. Inoltre,
il tempo del server viene utilizzato dalle distribuzioni di produzione e di gestione temporanea.

Cloud9 si occupa principalmente di rendere disponibile un IDE e non offre alcun metodo diretto per l'interruzione o l'eliminazione di un'applicazione dopo la distribuzione in Azure. Per eliminare un'applicazione ospitata in Azure, seguire questa procedura:

1.  Nella finestra di dialogo di distribuzione fare clic sul collegamento **Open portal** per aprire il portale di gestione di Azure.

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Accedere al portale usando le proprie credenziali.

3.  Nella parte sinistra della pagina Web selezionare **Servizi ospitati, account di archiviazione e CDN** e quindi fare clic su **Servizi ospitati**.

4.  Selezionare l'ambiente di gestione temporanea, indicato dal valore **Ambiente**. Per eliminare l'applicazione, fare clic su **Elimina** sulla barra multifunzione.

	![delete the deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Selezionare la distribuzione di produzione e quindi fare clic su **Elimina** per eliminare anche tale applicazione.

## Risorse aggiuntive

-   [Documentazione di Cloud9][]


  [IDE Cloud9]: http://cloud9ide.com/ 
  [informazioni generali sulla creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
  [configurazione delle dimensioni della macchina virtuale]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
  [informazioni generali sulla gestione delle distribuzioni in Azure.]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Documentazione di Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=45--> 
