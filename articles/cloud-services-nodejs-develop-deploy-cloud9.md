<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Distribuzione di un'applicazione di Azure da Cloud9

In questa esercitazione viene illustrato come usare l'IDE Cloud9 per sviluppare, creare e distribuire un'applicazione di tipo Node.js in Azure.

In questa esercitazione si apprenderà come:

-   Creare un progetto dell'IDE Cloud9
-   Distribuire il progetto in Azure
-   Aggiornare una distribuzione di Azure esistente
-   Spostare progetti tra distribuzioni di gestione temporanea e produzione

L'[IDE Cloud9][IDE Cloud9] offre un ambiente di sviluppo basato su browser e idoneo a più piattaforme. Una delle funzionalità supportate da Cloud9 per i progetti Node.js consiste nella possibilità effettuare la distribuzione direttamente in Azure dall'IDE. Cloud9 è anche integrato con i servizi repository GitHub e BitBucket e ciò semplifica la condivisione dei progetti con altri utenti.

L'utilizzo di Cloud9 permette di sviluppare e distribuire un'applicazione in Azure da molti browser e sistemi operativi attuali, senza dovere installare localmente strumenti di sviluppo o SDK aggiuntivi. Le procedure seguenti vengono eseguite in Google Chrome su Mac.

## Iscrizione

Per usare Cloud9, è innanzitutto necessario visitare il sito Web corrispondente ed [effettuare la registrazione per una sottoscrizione][IDE Cloud9]. È possibile accedere usando un account GitHub o BitBucket esistente oppure creare un account Cloud9. È disponibile un'offerta di sottoscrizione gratuita, oltre a un'offerta a pagamento, che include più funzionalità. Per altre informazioni, vedere la pagina relativa all'[IDE Cloud9][IDE Cloud9].

## Creazione di un progetto di Node.js

1.  Effettuare l'accesso a Cloud9, fare clic sul simbolo **+** accanto a **My Projects**, quindi selezionare **Create a new project**.

    ![Creazione di un nuovo progetto di Cloud9][Creazione di un nuovo progetto di Cloud9]

2.  Nella finestra di dialogo **Create a new project** immettere un nome,    un accesso e un tipo per il progetto. Fare clic su **Create** per creare il progetto.

    ![Finestra di dialogo per la creazione di un nuovo progetto di Cloud9][Finestra di dialogo per la creazione di un nuovo progetto di Cloud9]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Per alcune opzioni &egrave; necessario un piano di Cloud9 a pagamento.</p>
</div>

    <div class="dev-callout">
<strong>Nota</strong>
<p>Il nome del progetto di Cloud9 non viene usato durante la distribuzione in Azure.</p>
</div>

3.  Al termine della creazione del progetto, fare clic su **Start Editing**. Se si sta usando l'IDE Cloud9 per la prima volta, sarà possibile visualizzare una presentazione del servizio. Se si desidera visualizzare la presentazione in un secondo momento, selezionare **Just the editor,please**.

    ![Inizio della modifica del progetto di Cloud9][Inizio della modifica del progetto di Cloud9]

4.  Per creare una nuova applicazione Node, selezionare **File** e quindi **New    File**.

    ![Creazione di un nuovo file nel progetto di Cloud9][Creazione di un nuovo file nel progetto di Cloud9]

5.  Verrà visualizzata una nuova scheda intitolata **Untitled1**. Immettere il    codice seguente nella scheda **Untitled1** per creare l'applicazione    Node:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Nota</strong>
<p>L'utilizzo di process.env.PORT consente di assicurare che l'applicazione scelga la porta corretta in caso di esecuzione nel debugger di Cloud9 o durante la distribuzione in Azure.</p>
</div>

6.  Per salvare il codice, selezionare **File** e quindi **Save as**. Nella finestra di dialogo    **Save As** immettere **server.js** come nome del file e quindi fare    clic su **Save**.

    <div class="dev-callout">
<strong>Nota</strong>
<p>&Egrave; possibile che venga visualizzato un simbolo di avviso, che indica il mancato utilizzo della variabile req. Tale avviso pu&ograve; essere ignorato.</p>
</div>

    ![Salvataggio del file server.js][Salvataggio del file server.js]

## Esecuzione dell'applicazione

<div class="dev-callout">
<strong>Nota</strong>
<p>Bench&eacute; la procedura disponibile in questa sezione sia sufficiente per un'applicazione di tipo Hello World, &egrave; possibile che per le applicazioni che utilizzano moduli esterni sia necessario selezionare una versione specifica di Node.js per l'ambiente di debug. A tale scopo, selezionare <strong>Configure...</strong> dall'elenco a discesa relativo al debug, quindi selezionare la versione specifica di Node.js. &Egrave; ad esempio possibile che vengano visualizzati errori di autenticazione quando si utilizza il modulo 'azure' senza avere selezionato Node.js 0.6.x.</p>
</div>

1.  Fare clic su **Debug** per eseguire l'applicazione nel debugger di Cloud9.

    ![Esecuzione nel debugger][Esecuzione nel debugger]

2.  Verrà visualizzata una finestra di output. Fare clic sull'URL elencato per    accedere all'applicazione tramite una finestra del browser.

    ![Finestra di output][Finestra di output]

    L'applicazione risultante avrà un aspetto analogo al seguente:

    ![Applicazione in esecuzione nel browser][Applicazione in esecuzione nel browser]

3.  Per interrompere il debug dell'applicazione, fare clic su **stop**.

## Creare un account Azure

Per distribuire l'applicazione in Azure, è necessario disporre di un account. Se non si dispone di un account Azure, è possibile effettuare l'iscrizione per ottenere una versione di prova gratuita attenendosi alla procedura seguente:

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

## Creare una distribuzione

1.  Per creare una nuova distribuzione, selezionare **Deploy** e quindi fare clic su **+** per creare un server di distribuzione.

    [create a new deployment][create a new deployment]

2.  Nella finestra di dialogo **Add a deploy target** immettere un nome per la distribuzione e quindi selezionare **Azure** nell'elenco **Choose type**. Il nome specificato per la distribuzione verrà usato per identificare la distribuzione in Cloud9, ma non corrisponderà al nome della distribuzione in Azure.

3.  Se si sta creando per la prima volta una distribuzione di Cloud9 che utilizza Azure, sarà necessario configurare le impostazioni di pubblicazione di Azure. Eseguire la procedura seguente per scaricare e installare queste impostazioni in Cloud9:

    1.  Fare clic su **Download Azure Settings**.

        ![Download delle impostazioni di pubblicazione][Download delle impostazioni di pubblicazione]

        Verrà aperto il portale di gestione di Azure e verrà richiesto di scaricare le impostazioni di pubblicazione di Azure. Prima di iniziare, sarà necessario accedere all'account Azure.

    2.  Salvare il file delle impostazioni di pubblicazione nell'unità locale.

    3.  Nella finestra di dialogo **Aggiungi una destinazione di distribuzione** selezionare **Scegli file**,        e quindi selezionare il file scaricato nel passaggio precedente.

    4.  Dopo la selezione del file, fare clic su **Upload**.

4.  Fare clic su **+ Create new** per creare un nuovo servizio ospitato. Un *servizio ospitato* è il contenitore nel quale l'applicazione è ospitata quando viene distribuita in Azure. Per altre informazioni, vedere [Creazione di un servizio ospitato per Azure][Creazione di un servizio ospitato per Azure].

    ![Creazione di una nuova distribuzione][Creazione di una nuova distribuzione]

5.  Verrà richiesto di specificare il nome del nuovo servizio ospitato e le opzioni di configurazione quali il numero di istanze, il sistema operativo di hosting e il data center. Il nome specificato per la distribuzione verrà usato come nome del servizio ospitato in Azure. Il nome deve essere univoco nel sistema di Azure.

    ![Creazione di un nuovo servizio ospitato][Creazione di un nuovo servizio ospitato]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Nella finestra di dialogo <strong>Add a deploy target</strong> tutti i servizi ospitati di Azure verranno elencati nella sezione <strong>Choose existing deployment</strong>. Se si seleziona un servizio ospitato esistente, il progetto sar&agrave; distribuito in tale servizio.</p>
</div>

    <div class="dev-callout">
<strong>Nota</strong>
<p>Se si seleziona <strong>Enable RDP</strong> e si specifica un nome utente e una password, Desktop remoto sar&agrave; abilitato per la distribuzione.</p>
</div>

## Distribuzione nell'ambiente di produzione di Azure

1.  Selezionare la distribuzione creata nel passaggio precedente. Verrà visualizzata    una finestra di dialogo che include informazioni su tale distribuzione, oltre    all'URL di produzione che verrà usato dopo la distribuzione in Microsoft    Azure.

    ![Selezione di una distribuzione][Selezione di una distribuzione]

2.  Selezionare **Deploy to Production environment**.

3.  Fare clic su **Deploy** per iniziare la distribuzione.

4.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No web.config found'**. Selezionare **Yes** per creare il file. Un file 'Web.cloud.config' verrà aggiunto al progetto.

    ![Messaggio relativo al file web.config non trovato][Messaggio relativo al file web.config non trovato]

5.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No 'csdef' file present'**. Selezionare **Yes** per creare il file con estensione csdef. Un file 'ServiceDefinition.csdef' verrà aggiunto al progetto. ServiceDefinition.csdef è un file specifico di Azure, necessario per la pubblicazione dell'applicazione. Per altre informazioni, vedere [Creazione di un servizio ospitato per Azure][Creazione di un servizio ospitato per Azure].

6.  Verrà richiesta la selezione delle dimensioni delle istanze per l'applicazione. Selezionare **Small** e quindi fare clic su **Create**. Per informazioni dettagliate sulle dimensioni delle macchine virtuali di Azure, vedere [Come configurare le dimensioni della macchina virtuale][Come configurare le dimensioni della macchina virtuale].

    ![Definizione dei valori del file con estensione csdef][Definizione dei valori del file con estensione csdef]

7.  Lo stato del processo di distribuzione sarà visualizzato nella voce relativa alla distribuzione. Al termine, la distribuzione sarà visualizzata come **Active**.

    ![Stato della distribuzione][Stato della distribuzione]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Ai progetti distribuiti tramite l'IDE Cloud 9 viene assegnato un GUID come nome per la distribuzione in Azure.</p>
</div>

8.  Nella finestra di dialogo relativa alla distribuzione è disponibile un collegamento all'URL di produzione. Al termine della distribuzione, fare clic sull'URL per passare all'applicazione in esecuzione in Azure.

    ![Collegamento relativo all'URL di produzione di Azure][Collegamento relativo all'URL di produzione di Azure]

## Aggiornamento dell'applicazione

Quando si apportano modifiche all'applicazione, è possibile usare Cloud9 per distribuire l'applicazione aggiornata nello stesso servizio ospitato di Azure.

1.  Nel file server.js aggiornare il codice, in modo che sullo schermo venga visualizzato il testo "hello azure v2". È possibile sostituire il codice esistente con il codice aggiornato seguente:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Per salvare il codice, selezionare **File** e quindi **Save**.

## Distribuzione dell'aggiornamento nell'ambiente di gestione temporanea di Azure

1.  Selezionare **Deploy to Staging**.

2.  Fare clic su **Deploy** per iniziare la distribuzione.

    Ogni servizio ospitato di Azure supporta due ambienti, ovvero l'ambiente di gestione temporanea e di distribuzione. L'ambiente di gestione temporanea è identico all'ambiente di produzione, ma è possibile accedere all'applicazione di gestione temporanea solo tramite un URL offuscato basato su GUID generato da Azure. È possibile usare l'ambiente di gestione temporanea per testare l'applicazione e, dopo avere verificato le modifiche, sarà possibile passare in produzione la versione di gestione temporanea eseguendo uno scambio VIP (Virtual IP, IP virtuale), come illustrato più avanti in questa esercitazione.

3.  Quando l'applicazione è distribuita nell'ambiente di gestione temporanea, l'URL di gestione temporanea basato su GUID viene visualizzato nell'output della console, come mostrato nella schermata riportata di seguito. Fare clic sull'URL per aprire l'applicazione di gestione temporanea in un browser.

    ![Output della console con URL di gestione temporanea][Output della console con URL di gestione temporanea]

## Spostamento dell'aggiornamento in produzione tramite lo scambio VIP

Quando un servizio viene distribuito in ambienti di produzione o di gestione temporanea,al servizio in questo ambiente viene assegnato un indirizzo IP virtuale (VIP). Se si vuole spostare un servizio dall'ambiente di gestione temporanea all'ambiente di produzione, è possibile eseguire tale operazione senza ripetere la distribuzione effettuando uno scambio VIP, che consente di scambiare le distribuzioni di gestione temporanea e d produzione. Uno scambio VIP consente di inserire in produzione l'applicazione di gestione temporanea testata, senza tempi di inattività nell'ambiente di produzione. Per altre informazioni, vedere [Panoramica della gestione delle distribuzioni in Azure.][Panoramica della gestione delle distribuzioni in Azure.]

1.  Nella finestra di dialogo di distribuzione fare clic sul collegamento **Apri il portale** per aprire il    portale di gestione di Azure.

    [Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  Accedere al portale usando le proprie credenziali.

3.  Nella parte sinistra della pagina Web selezionare **Servizi ospitati, Account di    archiviazione e CDN** e quindi fare clic su **Servizi ospitati**.

    [Azure Management Portal][Azure Management Portal]

    Nel riquadro dei risultati viene mostrato il servizio ospitato con il nome specificato in Cloud9, oltre a due distribuzioni, il cui valore **Environment** è impostato su **Staging** per la prima e su **Production** per la seconda.

4.  Per eseguire lo scambio VIP, selezionare il servizio ospitato e quindi fare clic su **Swap VIP** sulla barra multifunzione.

    ![Scambio VIP][Scambio VIP]

5.  Fare clic su **OK** nella finestra di dialogo Swap VIPs visualizzata.

6.  Passare all'applicazione di produzione. Come si può notare, la versione dell'applicazione precedentemente distribuita nell'ambiente di gestione temporanea si trova ora nell'ambiente di produzione.

    ![Applicazione di produzione in esecuzione in Azure][Applicazione di produzione in esecuzione in Azure]

## Utilizzo di Desktop remoto

Se RDP è stato abilitato e un nome utente e una password sono stati specificati durante la creazione della distribuzione, sarà possibile usare Desktop remoto per la connessione al servizio ospitato, selezionando un'istanza specifica e quindi selezionando Connetti
sulla barra multifunzione.

![Connessione a un'istanza][Connessione a un'istanza]

Quando si fa clic su Connect, verrà richiesto di aprire o scaricare un file RDP. Tale file include le informazioni necessarie per la connessione alla sessione di Desktop remoto. Se si esegue questo file in un sistema Windows, verranno richiesti il nome utente e la password specificati durante la creazione della distribuzione e verrà quindi effettuata la connessione al desktop dell'istanza
selezionata.

<div class="dev-callout">
<strong>Nota</strong>
<p>Il file RDP per la connessione all'istanza ospitata dell'applicazione funzioner&agrave; solo con l'applicazione Desktop remoto in
Windows.</p>
</div>

## Interruzione ed eliminazione dell'applicazione

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria e un'applicazione in fase di distribuzione utilizza tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato. Il tempo
del server viene inoltre consumato dalle distribuzioni di produzione e di gestione temporanea.

Cloud9 si occupa principalmente di rendere disponibile un IDE e non offre alcun metodo diretto per l'interruzione o l'eliminazione di un'applicazione dopo la distribuzione in Azure. Per eliminare un'applicazione ospitata in Azure, eseguire la procedura seguente:

1.  Nella finestra di dialogo di distribuzione fare clic sul collegamento **Open portal** per aprire il portale di gestione di Azure.

    ![Collegamento dalla finestra di dialogo di distribuzione per il portale di gestione di Azure][Collegamento dalla finestra di dialogo di distribuzione per il portale di gestione di Azure]

2.  Accedere al portale usando le proprie credenziali.

3.  Nella parte sinistra della pagina Web selezionare **Hosted Services, Storage Accounts & CDN** e quindi fare clic su **Hosted Services**.

4.  Selezionare l'ambiente di gestione temporanea, indicato dal valore **Environment**. Per eliminare l'applicazione, fare clic su **Delete** sulla barra multifunzione.

    ![Eliminazione della distribuzione][Eliminazione della distribuzione]

5.  Selezionare la distribuzione di produzione e quindi fare clic su **Delete** per eliminare anche tale applicazione.

## Risorse aggiuntive

-   [Documentazione relativa a Cloud9][Documentazione relativa a Cloud9]

  [IDE Cloud9]: http://cloud9ide.com/
  [Creazione di un nuovo progetto di Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [Finestra di dialogo per la creazione di un nuovo progetto di Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [Inizio della modifica del progetto di Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [Creazione di un nuovo file nel progetto di Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [Salvataggio del file server.js]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [Esecuzione nel debugger]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [Finestra di output]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [Applicazione in esecuzione nel browser]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [Download delle impostazioni di pubblicazione]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [Creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj155995.aspx
  [Creazione di una nuova distribuzione]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [Creazione di un nuovo servizio ospitato]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [Selezione di una distribuzione]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [Messaggio relativo al file web.config non trovato]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [Come configurare le dimensioni della macchina virtuale]: http://msdn.microsoft.com/it-it/library/windowsazure/ee814754.aspx
  [Definizione dei valori del file con estensione csdef]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [Stato della distribuzione]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [Collegamento relativo all'URL di produzione di Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [Output della console con URL di gestione temporanea]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [Panoramica della gestione delle distribuzioni in Azure.]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433027.aspx
  [Scambio VIP]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Applicazione di produzione in esecuzione in Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [Connessione a un'istanza]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [Collegamento dalla finestra di dialogo di distribuzione per il portale di gestione di Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [Eliminazione della distribuzione]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Documentazione relativa a Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
