<properties 
	pageTitle="Guida introduttiva di Node.js - Esercitazione di Azure" 
	description="Esercitazione end-to-end che illustra come sviluppare una semplice applicazione Web Node.js e distribuirla in Azure." 
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






# Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure

Dopo aver completato questa guida, si disporrà di una semplice applicazione Node.js in esecuzione 
in un servizio cloud di Azure. Servizi cloud è costituito da blocchi predefiniti di 
applicazioni cloud scalabili in Azure. Consente la separazione, la gestione
indipendente e la scalabilità dei componenti front-end e back-end dell'applicazione..  Servizi cloud 
offre una potente macchina virtuale dedicata per ospitare ogni ruolo in modo affidabile.

Per altre informazioni su Servizi cloud e sulle differenze rispetto ai servizi Siti Web e Macchine virtuali di Azure, vedere [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure](http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Come creare un semplice sito Web Se si intende creare un semplice sito Web front-end, è possibile <a href="/it-it/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usare un sito Web di Azure semplificato.</a> È possibile procedere all'aggiornamento a un servizio cloud con facilità, in base alla crescita del sito Web e all'insorgere di nuove esigenze.


Questa esercitazione consente di creare una semplice applicazione Web ospitata in un ruolo Web. Si
userà l'emulatore di calcolo per eseguire in locale il test dell'applicazione che verrà quindi distribuita
con gli strumenti della riga di comando di PowerShell.

Di seguito è riportata una schermata dell'applicazione completata:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.">
</p>



## Creare una nuova applicazione Node

Per creare un nuovo progetto di Servizio cloud di Azure oltre allo scaffolding di Node.js, eseguire queste operazioni:

1. Nel menu **Start** o nella schermata **Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Creare una nuova directory **node** nell'unità C e passare alla directory c:\\node:
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Immettere il seguente cmdlet per creare una nuova soluzione:

        PS C:\node> New-AzureServiceProject helloworld

    	Verrà visualizzata la seguente risposta:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Il cmdlet **New-AzureServiceProject** genera una struttura di base per la creazione di una nuova applicazione Node per Azure che verrà pubblicata su un servizio cloud. Contiene i file di configurazione necessari per la pubblicazione in Azure. Il cmdlet modifica inoltre la directory di lavoro nella directory per il servizio.

	File creati dal cmdlet **New-AzureServiceProject**:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef** sono
        file di Azure necessari per la pubblicazione
        dell'applicazione.
		
	Per altre informazioni su questi file, vedere l'argomento relativo alle
        [informazioni generali sulla creazione di un servizio ospitato di Azure][].

	-   **deploymentSettings.json** archivia le impostazioni locali usate dai
        cmdlet di distribuzione di Azure PowerShell.

4.  Immettere il seguente comando per aggiungere un nuovo ruolo Web usando il cmdlet
    **Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Verrà visualizzata la seguente risposta:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Il cmdlet **Add-AzureNodeWebRole** crea una nuova directory per l'applicazione e genera lo scaffolding per un'applicazione Node.js di base. Modifica inoltre i file **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** e **ServiceDefinition.csdef** creati nel passaggio precedente per aggiungere voci di configurazione per il nuovo ruolo.

	> [AZURE.NOTE] Per impostazione predefinita, se non verrà specificato un nome del ruolo, ne verrà creato uno. È possibile specificare un nome come primo parametro di **Add-AzureNodeWebRole**. Ad esempio, `Add-AzureNodeWebRole MyRole`.

5.  Usare i seguenti comandi per passare alla directory **WebRole1** e quindi aprire il file **server.js** in Blocco note. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Il file **server.js** creato dal cmdlet **Add-AzureNodeWebRole** contiene il seguente codice di avvio. Questo codice è simile all'esempio "Hello World" disponibile nel sito Web [nodejs.org][] ad eccezione di alcune differenze:

   	-   La porta è stata modificata per consentire all'applicazione di trovare la 
        porta corretta assegnata dall'ambiente cloud.
   	-   La registrazione della console è stata rimossa.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Eseguire l'applicazione in locale nell'emulatore

Uno degli strumenti installati da Azure SDK è
l'emulatore di calcolo di Azure, che consente di testare l'applicazione in locale. L'emulatore
di calcolo simula l'ambiente di esecuzione dell'applicazione
una volta distribuita nel cloud. Per eseguire il test dell'applicazione nell'emulatore, seguire questa procedura:

1.  Chiudere Blocco note e tornare alla finestra di Windows PowerShell.
  	Immettere il seguente cmdlet per eseguire il servizio nell'emulatore:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Il parametro **-Launch** specifica che gli strumenti devono aprire automaticamente una finestra del browser e visualizzare l'applicazione in esecuzione nell'emulatore. Nel browser verrà visualizzato "Hello World" come illustrato nella schermata riportata di seguito. Ciò indica che il servizio è in esecuzione nell'emulatore di calcolo e che funziona correttamente.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Per arrestare l'emulatore di calcolo, usare il comando **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Distribuire l'applicazione in Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Scaricare le impostazioni di pubblicazione di Azure

Per distribuire l'applicazione in Azure, è necessario innanzitutto scaricare le impostazioni di pubblicazione per la sottoscrizione di Azure. Seguire questa procedura:

1.  Dalla finestra di Windows PowerShell, avviare la pagina di download eseguendo il seguente cmdlet:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Il browser verrà così usato per passare alla pagina di download delle impostazioni di pubblicazione. È possibile che venga richiesto di effettuare l'accesso con un account Microsoft. In tal caso, usare l'account associato alla sottoscrizione di Azure.

	Salvare il profilo scaricato in un percorso di file facilmente accessibile.

2.  Nella finestra di Azure PowerShell, usare il seguente cmdlet per configurare Windows PowerShell e consentire ai cmdlet di Node.js di usare il profilo di pubblicazione di Azure che è stato scaricato:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file .publishSettings, in quanto contiene informazioni che possono essere usate da altri utenti per accedere all'account.
    

### Pubblicare l'applicazione

1.  Pubblicare l'applicazione usando il cmdlet **Publish-AzureServiceProject**,
    come illustrato di seguito.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Il parametro **servicename** specifica il nome usato per questa distribuzione. Deve trattarsi di un nome univoco, in caso contrario il processo di pubblicazione avrà esito negativo.

	- Il parametro **location** specifica il data center nel quale l'applicazione verrà ospitata. Per visualizzare un elenco dei data center disponibili, usare il cmdlet **Get-AzureLocation**.

	- Il parametro **launch** consente di avviare il browser e passare al servizio ospitato al termine della distribuzione.

	Al termine della pubblicazione, verrà visualizzata una risposta simile alla seguente:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	Il cmdlet **Publish-AzureServiceProject** esegue queste operazioni:

1.  Crea un pacchetto che verrà distribuito in Azure. Il pacchetto contiene tutti i file nella cartella dell'applicazione Node.js.

2.  Crea un nuovo **account di archiviazione** nel caso non ne esista uno. L'account di archiviazione di Azure viene usato per archiviare il pacchetto dell'applicazione durante la distribuzione. L'account di archiviazione può essere eliminato dopo il completamento della distribuzione.

3.  Crea un nuovo **servizio cloud** nel caso in cui non ne esista già uno. Un **servizio cloud** è il contenitore nel quale l'applicazione è ospitata quando viene distribuita in Azure. Per altre informazioni, vedere l'argomento relativo alle [informazioni generali sulla creazione di un servizio ospitato per Azure][].

4.  Pubblica il pacchetto di distribuzione in Azure.


	> [AZURE.NOTE]
	> Possono essere necessari da 5 a 7 minuti per la distribuzione dell'applicazione e prima che questa sia disponibile dopo la prima pubblicazione.

	Al termine della distribuzione, verrà visualizzata una finestra del browser che consente di passare al servizio cloud.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	L'applicazione è in esecuzione su Azure.


## Arrestare ed eliminare l'applicazione

Dopo aver distribuito l'applicazione, è possibile disabilitarla per evitare costi aggiuntivi. Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita usa tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato.

1.  Nella finestra di Windows PowerShell arrestare la distribuzione del servizio creato nella sezione precedente con il seguente cmdlet:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	L'arresto del servizio può richiedere diversi minuti. Dopo l'arresto del servizio, viene visualizzato un messaggio di conferma dell'arresto.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Per eliminare il servizio, chiamare il seguente cmdlet:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Quando richiesto, immettere **Y** per eliminare il servizio.

	L'eliminazione del servizio può richiedere diversi minuti. Al termine dell'eliminazione del servizio, verrà visualizzato un messaggio di conferma dell'eliminazione.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] L'eliminazione del servizio non comporta l'eliminazione dell'account di archiviazione creato quando il servizio è stato pubblicato e lo spazio di archiviazione usato continuerà a essere addebitato. Per altre informazioni sull'eliminazione di un account di archiviazione, vedere [Come eliminare un account di archiviazione da una sottoscrizione Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[informazioni generali sulla creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[informazioni generali sulla creazione di un servizio ospitato di Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Come eliminare un account di archiviazione da una sottoscrizione Azure]: https://www.windowsazure.com/it-it/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=45--> 
