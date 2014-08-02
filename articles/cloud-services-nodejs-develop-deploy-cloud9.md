<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

Distribuzione di un'applicazione di Azure da Cloud9
===================================================

In questa esercitazione viene illustrato come utilizzare l'IDE Cloud9 per sviluppare, creare e distribuire un'applicazione di tipo Node.js in Azure.

In questa esercitazione si apprenderà come:

-   Creare un progetto dell'IDE Cloud9
-   Distribuire il progetto in Azure
-   Aggiornare una distribuzione di Azure esistente
-   Spostare progetti tra distribuzioni di gestione temporanea e produzione

L'[IDE Cloud9](http://cloud9ide.com/) offre un ambiente di sviluppo basato su browser e idoneo a più piattaforme. Una delle funzionalità supportate da Cloud9 per i progetti Node.js consiste nella possibilità di distribuire direttamente in Azure dall'IDE. Cloud9 consente inoltre l'integrazione con i servizi archivio GitHub e BitBucket, semplificando la condivisione del progetto con altri utenti.

L'utilizzo di Cloud9 consente di sviluppare e distribuire un'applicazione in Azure da molti browser e sistemi operativi attuali, senza dovere installare localmente strumenti di sviluppo o SDK aggiuntivi. Le procedure seguenti vengono eseguite in Google Chrome su Mac.

Iscrizione
----------

Per utilizzare Cloud9, è innanzitutto necessario visitare il sito Web corrispondente ed [effettuare la registrazione per una sottoscrizione](http://cloud9ide.com/). È possibile accedere utilizzando un account GitHub o BitBucket esistente oppure creare un account Cloud9. È disponibile un'offerta di sottoscrizione gratuita, oltre a un'offerta a pagamento, che include più funzionalità. Per ulteriori informazioni, vedere la pagina relativa all'[IDE Cloud9](http://cloud9ide.com/).

Creazione di un progetto di Node.js
-----------------------------------

1.  Effettuare l'accesso a Cloud9, fare clic sul simbolo **+** accanto a **My Projects**, quindi selezionare **Create a new project**.

    ![Creazione di un nuovo progetto di Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  Nella finestra di dialogo **Create a new project** immettere un nome, un accesso e un tipo per il progetto. Fare clic su **Create** per creare il progetto.

    ![Finestra di dialogo per la creazione di un nuovo progetto di Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

    **Nota**

    Per alcune opzioni è necessario un piano di Cloud9 a pagamento.

    **Nota**

    Il nome del progetto di Cloud9 non viene utilizzato durante la distribuzione in Azure.

3.  Al termine della creazione del progetto, fare clic su **Start Editing**. Se si sta utilizzando l'IDE Cloud9 per la prima volta, sarà possibile visualizzare una presentazione del servizio. Se si desidera visualizzare la presentazione in un secondo momento, selezionare **Just the editor,please**.

    ![Inizio della modifica del progetto di Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Per creare una nuova applicazione Node, selezionare **File** e quindi **New File**.

    ![Creazione di un nuovo file nel progetto di Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Verrà visualizzata una nuova scheda intitolata **Untitled1**. Immettere il codice seguente nella scheda **Untitled1** per creare l'applicazione Node:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    **Nota**

    L'utilizzo di process.env.PORT consente di assicurare che l'applicazione scelga la porta corretta in caso di esecuzione nel debugger di Cloud9 o durante la distribuzione in Azure.

6.  Per salvare il codice, selezionare **File** e quindi **Save as**. Nella finestra di dialogo **Save As** immettere **server.js** come nome del file e quindi fare clic su **Save**.

    **Nota**

    È possibile che venga visualizzato un simbolo di avviso, che indica il mancato utilizzo della variabile req. Tale avviso può essere ignorato.

    ![Salvataggio del file server.js](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

Esecuzione dell'applicazione
----------------------------

**Nota**

Benché la procedura disponibile in questa sezione sia sufficiente per un'applicazione di tipo Hello World, è possibile che per le applicazioni che utilizzano moduli esterni sia necessario selezionare una versione specifica di Node.js per l'ambiente di debug. A tale scopo, selezionare **Configure...** dall'elenco a discesa relativo al debug, quindi selezionare la versione specifica di Node.js. È ad esempio possibile che vengano visualizzati errori di autenticazione quando si utilizza il modulo 'azure' senza avere selezionato Node.js 0.6.x.

1.  Fare clic su **Debug** per eseguire l'applicazione nel debugger di Cloud9.

    ![Esecuzione nel debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Verrà visualizzata una finestra di output. Fare clic sull'URL elencato per accedere all'applicazione tramite una finestra del browser.

    ![Finestra di output](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

    L'applicazione risultante avrà un aspetto analogo al seguente:

    ![Applicazione in esecuzione nel browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Per interrompere il debug dell'applicazione, fare clic su **stop**.

Creazione di un account Azure
-----------------------------

Per distribuire l'applicazione in Azure, è necessario disporre di un account. Se non si dispone di un account Azure, è possibile effettuare l'iscrizione per ottenere una versione di prova gratuita attenendosi alla procedura seguente:

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

Creazione di una distribuzione
------------------------------

1.  Per creare una nuova distribuzione, selezionare **Deploy** e quindi fare clic su **+** per creare un server di distribuzione.

    ![create a new deployment][create a new deployment]

2.  Nella finestra di dialogo **Add a deploy target** immettere un nome per la distribuzione e quindi selezionare **Azure** nell'elenco **Choose type**. Il nome specificato per la distribuzione verrà utilizzato per identificare la distribuzione in Cloud9, ma non corrisponderà al nome della distribuzione in Azure.

3.  Se si sta creando per la prima volta una distribuzione di Cloud9 che utilizza Azure, sarà necessario configurare le impostazioni di pubblicazione di Azure. Eseguire la procedura seguente per scaricare e installare queste impostazioni in Cloud9:

    1.  Fare clic su **Download Azure Settings**.

        ![Download delle impostazioni di pubblicazione](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Verrà aperto il portale di gestione di Azure e verrà richiesto di scaricare le impostazioni di pubblicazione di Azure. Prima di iniziare, sarà necessario accedere all'account Azure.

    2.  Salvare il file delle impostazioni di pubblicazione nell'unità locale.

    3.  Nella finestra di dialogo **Add a deploy target** selezionare **Choose File** e quindi selezionare il file scaricato nel passaggio precedente.

    4.  Dopo la selezione del file, fare clic su **Upload**.

4.  Fare clic su **+ Create new** per creare un nuovo servizio ospitato. Un *servizio ospitato* è il contenitore nel quale l'applicazione è ospitata quando viene distribuita in Azure. Per ulteriori informazioni, vedere [Creazione di un servizio ospitato per Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx).

    ![Creazione di una nuova distribuzione](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  Verrà richiesto di specificare il nome del nuovo servizio ospitato e le opzioni di configurazione quali il numero di istanze, il sistema operativo di hosting e il data center. Il nome specificato per la distribuzione verrà utilizzato come nome del servizio ospitato in Azure. Il nome deve essere univoco nel sistema di Azure.

    ![Creazione di un nuovo servizio ospitato](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

    **Nota**

    Nella finestra di dialogo **Add a deploy target** tutti i servizi ospitati di Azure verranno elencati nella sezione **Choose existing deployment**. Se si seleziona un servizio ospitato esistente, il progetto sarà distribuito in tale servizio.

    **Nota**

    Se si seleziona **Enable RDP** e si specifica un nome utente e una password, Desktop remoto sarà abilitato per la distribuzione.

Distribuzione nell'ambiente di produzione di Azure
--------------------------------------------------

1.  Selezionare la distribuzione creata nel passaggio precedente. Verrà visualizzata una finestra di dialogo che include informazioni su tale distribuzione, oltre all'URL di produzione che verrà utilizzato dopo la distribuzione in Windows Azure.

    ![Selezione di una distribuzione](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Selezionare **Deploy to Production environment**.

3.  Fare clic su **Deploy** per iniziare la distribuzione.

4.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No web.config found'**. Selezionare **Yes** per creare il file. Un file 'Web.cloud.config' verrà aggiunto al progetto.

    ![Messaggio relativo al file web.config non trovato](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Se questa è la prima distribuzione del progetto in Azure, verrà visualizzato l'errore **'No 'csdef' file present'**. Selezionare **Yes** per creare il file con estensione csdef. Un file 'ServiceDefinition.csdef' verrà aggiunto al progetto. ServiceDefinition.csdef è un file specifico di Azure, necessario per la pubblicazione dell'applicazione. Per ulteriori informazioni, vedere [Creazione di un servizio ospitato per Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj155995.aspx).

6.  Verrà richiesta la selezione delle dimensioni delle istanze per l'applicazione. Selezionare **Small** e quindi fare clic su **Create**. Per informazioni dettagliate sulle dimensioni delle macchine virtuali di Azure, vedere [Come configurare le dimensioni della macchina virtuale](http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx).

    ![Definizione dei valori del file con estensione csdef](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  Lo stato del processo di distribuzione sarà visualizzato nella voce relativa alla distribuzione. Al termine, la distribuzione sarà visualizzata come **Active**.

    ![Stato della distribuzione](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

    **Nota**

    Ai progetti distribuiti tramite l'IDE Cloud 9 viene assegnato un GUID come nome per la distribuzione in Azure.

8.  Nella finestra di dialogo relativa alla distribuzione è disponibile un collegamento all'URL di produzione. Al termine della distribuzione, fare clic sull'URL per passare all'applicazione in esecuzione in Azure.

    ![Collegamento relativo all'URL di produzione di Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

Aggiornamento dell'applicazione
-------------------------------

Quando si apportano modifiche all'applicazione, è possibile utilizzare Cloud9 per distribuire l'applicazione aggiornata nello stesso servizio ospitato di Azure.

1.  Nel file server.js aggiornare il codice, in modo che sullo schermo venga visualizzato il testo "hello azure v2". È possibile sostituire il codice esistente con il codice aggiornato seguente:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Per salvare il codice, selezionare **File** e quindi **Save**.

Distribuzione dell'aggiornamento nell'ambiente di gestione temporanea di Azure
------------------------------------------------------------------------------

1.  Selezionare **Deploy to Staging**.

2.  Fare clic su **Deploy** per iniziare la distribuzione.

    Ogni servizio ospitato di Azure supporta due ambienti, ovvero l'ambiente di gestione temporanea e di distribuzione. L'ambiente di gestione temporanea è identico all'ambiente di produzione, ma è possibile accedere all'applicazione di gestione temporanea solo tramite un URL offuscato basato su GUID generato da Azure. È possibile utilizzare l'ambiente di gestione temporanea per testare l'applicazione e, dopo avere verificato le modifiche, sarà possibile passare in produzione la versione di gestione temporanea eseguendo uno scambio VIP (Virtual IP, IP virtuale), come illustrato più avanti in questa esercitazione.

3.  Quando l'applicazione è distribuita nell'ambiente di gestione temporanea, l'URL di gestione temporanea basato su GUID viene visualizzato nell'output della console, come mostrato nella schermata riportata di seguito. Fare clic sull'URL per aprire l'applicazione di gestione temporanea in un browser.

    ![Output della console con URL di gestione temporanea](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

Spostamento dell'aggiornamento in produzione tramite lo scambio VIP
-------------------------------------------------------------------

Quando un servizio viene distribuito in ambienti di produzione o di gestione temporanea, al servizio in questo ambiente viene assegnato un indirizzo IP virtuale (VIP). Se si desidera spostare un servizio dall'ambiente di gestione temporanea all'ambiente di produzione, è possibile eseguire tale operazione senza ripetere la distribuzione effettuando uno scambio VIP, che consente di scambiare le distribuzioni di gestione temporanea e di produzione. Uno scambio VIP consente di inserire in produzione l'applicazione di gestione temporanea testata, senza tempi di inattività nell'ambiente di produzione. Per ulteriori informazioni, vedere [Panoramica della gestione delle distribuzioni in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433027.aspx).

1.  Nella finestra di dialogo di distribuzione fare clic sul collegamento **Open portal** per aprire il portale di gestione di Azure.

    ![Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  Accedere al portale utilizzando le proprie credenziali.

3.  Nella parte sinistra della pagina Web selezionare **Hosted Services, Storage Accounts & CDN** e quindi fare clic su **Hosted Services**.

    ![Azure Management Portal][Azure Management Portal]

    Nel riquadro dei risultati viene mostrato il servizio ospitato con il nome specificato in Cloud9, oltre a due distribuzioni, il cui valore **Environment** è impostato su **Staging** per la prima e su **Production** per la seconda.

4.  Per eseguire lo scambio VIP, selezionare il servizio ospitato e quindi fare clic su **Swap VIP** sulla barra multifunzione.

    ![Scambio VIP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Fare clic su **OK** nella finestra di dialogo Swap VIPs visualizzata.

6.  Passare all'applicazione di produzione. Come si può notare, la versione dell'applicazione precedentemente distribuita nell'ambiente di gestione temporanea si trova ora nell'ambiente di produzione.

    ![Applicazione di produzione in esecuzione in Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

Utilizzo di Desktop remoto
--------------------------

Se RDP è stato abilitato e un nome utente e una password sono stati specificati durante la creazione della distribuzione, sarà possibile utilizzare Desktop remoto per la connessione al servizio ospitato, selezionando un'istanza specifica e quindi selezionando Connect sulla barra multifunzione.

![Connessione a un'istanza](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Quando si fa clic su Connect, verrà richiesto di aprire o scaricare un file RDP. Tale file include le informazioni necessarie per la connessione alla sessione di Desktop remoto. Se si esegue questo file in un sistema Windows, verranno richiesti il nome utente e la password specificati durante la creazione della distribuzione e verrà quindi effettuata la connessione al desktop dell'istanza selezionata.

**Nota**

Il file RDP per la connessione all'istanza ospitata dell'applicazione funzionerà solo con l'applicazione Desktop remoto in Windows.

Interruzione ed eliminazione dell'applicazione
----------------------------------------------

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria e un'applicazione in fase di distribuzione utilizza tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato. Il tempo del server viene inoltre consumato dalle distribuzioni di produzione e di gestione temporanea.

Cloud9 si occupa principalmente di rendere disponibile un IDE e non offre alcun metodo diretto per l'interruzione o l'eliminazione di un'applicazione dopo la distribuzione in Azure. Per eliminare un'applicazione ospitata in Azure, eseguire la procedura seguente:

1.  Nella finestra di dialogo di distribuzione fare clic sul collegamento **Open portal** per aprire il portale di gestione di Azure.

    ![Collegamento dalla finestra di dialogo di distribuzione per il portale di gestione di Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Accedere al portale utilizzando le proprie credenziali.

3.  Nella parte sinistra della pagina Web selezionare **Hosted Services, Storage Accounts & CDN** e quindi fare clic su **Hosted Services**.

4.  Selezionare l'ambiente di gestione temporanea, indicato dal valore **Environment**. Per eliminare l'applicazione, fare clic su **Delete** sulla barra multifunzione.

    ![Eliminazione della distribuzione](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Selezionare la distribuzione di produzione e quindi fare clic su **Delete** per eliminare anche tale applicazione.

Risorse aggiuntive
------------------

-   [Documentazione relativa a Cloud9](http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409)

