<properties urlDisplayName="Cloud Service" pageTitle="Guida introduttiva di Node.js - Esercitazione di Azure" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="Esercitazione end-to-end che illustra come sviluppare una semplice applicazione Web Node.js e distribuirla in Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure

Dopo aver completato questa guida, si disporrà di una semplice applicazione Node.js in esecuzione in un servizio cloud di Azure. Servizi cloud è costituito da blocchi predefiniti di applicazioni cloud scalabili in Azure. Consente la separazione e la gestione indipendente e la scalabilità dei componenti front-end e back-end dell'applicazione.  Servizi cloud offre una potente macchina virtuale dedicata per ospitare ogni ruolo in modo affidabile.

Per altre informazioni su Servizi cloud e sulle differenze rispetto ai servizi Siti Web e Macchine virtuali di Azure, vedere [Confronto tra siti Web, servizi cloud e macchine virtuali](http://azure.microsoft.com/it-it/documentation/articles/choose-web-site-cloud-service-vm/).

<p />

<div class="dev-callout"><strong>Come creare un semplice sito Web</strong>
<p>Se si intende creare un semplice sito Web front-end, è possibile <a href="/it-it/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usare un sito Web di Azure semplificato.</a> È possibile procedere all'aggiornamento a un servizio cloud con facilità, in base alla crescita del sito Web e all'insorgere di nuove esigenze.</p>
</div>
<br />

Questa esercitazione consente di creare una semplice applicazione Web ospitata in un ruolo Web. Si utilizzerà l'emulatore di calcolo per eseguire in locale il test dell'applicazione che verrà quindi distribuita con gli strumenti della riga di comando di PowerShell.

Di seguito è riportata una schermata dell'applicazione completata:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Finestra del browser in cui è visualizzata la pagina Hello World. L'URL indica che la pagina è ora ospitata in Azure.">
</p>



## Creazione di una nuova applicazione Node

Per creare un nuovo progetto di Servizio cloud di Azure oltre allo scaffolding di Node.js, eseguire le operazioni seguenti:

1. Nel menu **Start** o nella schermata **Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Creare una nuova directory **node** nell'unità C e passare alla directory c:\\node:
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Immettere il cmdlet seguente per creare una nuova soluzione:

        PS C:\node> New-AzureServiceProject helloworld

    	Verrà visualizzata la risposta seguente:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Il cmdlet **New-AzureServiceProject** genera una struttura di base per la creazione di una nuova applicazione Node per Azure che verrà pubblicata su un servizio cloud. Contiene i file di configurazione necessari per la pubblicazione in Azure. Il cmdlet modifica inoltre la directory di lavoro nella directory per il servizio.

	I file creati dal cmdlet **New-AzureServiceProject** sono:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef** sono
        file specifici di Azure, necessari per la pubblicazione 
        dell'applicazione.
		
	Per ulteriori informazioni su questi file, vedere
        [Panoramica della creazione di un servizio ospitato per Azure][].

	-   **deploymentSettings.json** archivia le impostazioni locali utilizzate dai
        cmdlet di distribuzione di Azure PowerShell.

4.  Immettere il comando seguente per aggiungere un nuovo ruolo Web usando il cmdlet **Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Verrà visualizzata la risposta seguente:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Il cmdlet **Add-AzureNodeWebRole** crea una nuova directory per l'applicazione e genera lo scaffolding per un'applicazione Node.js di base. Modifica inoltre i file **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** e **ServiceDefinition.csdef** creati nel passaggio precedente per aggiungere voci di configurazione per il nuovo ruolo.

	<div class="dev-callout">
	<b>Nota</b>
	<p>Per impostazione predefinita, se non verrà specificato un nome del ruolo, ne verrà creato uno. È possibile specificare un nome come primo parametro di <b>Add-AzureNodeWebRole</b>. Ad esempio, <code>Add-AzureNodeWebRole MyRole</code></p>
	</div>

5.  Usare i comandi seguenti per passare alla directory **WebRole1** e quindi aprire il file **server.js** in Blocco note. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Il file **server.js** creato dal cmdlet **Add-AzureNodeWebRole** contiene il codice di avvio seguente. Questo codice è simile all'esempio "Hello World" disponibile nel sito Web [nodejs.org][] ad eccezione di alcune differenze:

   	-   La porta è stata modificata per consentire all'applicazione di trovare la porta corretta assegnata dall'ambiente cloud.
   	-   La registrazione della console è stata rimossa.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Esecuzione dell'applicazione in locale nell'emulatore

Uno degli strumenti installati da Azure SDK è l'emulatore di calcolo di Azure, che consente di eseguire il test dell'applicazione in locale. L'emulatore di calcolo simula l'ambiente nel quale l'applicazione verrà eseguita quando verrà distribuita nel cloud. Per eseguire il test dell'applicazione nell'emulatore, attenersi alla procedura seguente:

1.  Chiudere Blocco note e tornare alla finestra di Windows PowerShell.
  	Immettere il cmdlet seguente per eseguire il servizio nell'emulatore:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Il parametro **-Launch** specifica che gli strumenti devono aprire automaticamente una finestra del browser e visualizzare l'applicazione in esecuzione nell'emulatore. Nel browser verrà visualizzato "Hello World" come illustrato nella schermata riportata di seguito. Ciò indica che il servizio è in esecuzione nell'emulatore di calcolo e che funziona correttamente.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Per arrestare l'emulatore di calcolo, usare il comando **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Distribuzione dell'applicazione in Azure

	[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Download delle impostazioni di pubblicazione di Azure

Per distribuire l'applicazione in Azure, è necessario innanzitutto scaricare le impostazioni di pubblicazione per la sottoscrizione di Azure. Attenersi alla procedura seguente:

1.  Dalla finestra di Windows PowerShell, avviare la pagina di download eseguendo il cmdlet seguente:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Il browser verrà così usato per passare alla pagina di download delle impostazioni di pubblicazione. È possibile che venga richiesto di effettuare l'accesso con un account Microsoft. In tal caso, usare l'account associato alla sottoscrizione di Azure.

	Salvare il profilo scaricato in un percorso di file facilmente accessibile.

2.  Nella finestra di Azure PowerShell, usare il cmdlet seguente per configurare Windows PowerShell e consentire ai cmdlet di Node.js di usare il profilo di pubblicazione di Azure che è stato scaricato:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	<div class="dev-callout">
	<b>Nota</b>
	<p>Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file .publishSettings, in quanto contiene informazioni che possono essere usate da altri utenti per accedere all'account.</p>
	</div>
    

### Pubblicazione dell'applicazione

1.  Pubblicare l'applicazione usando il cmdlet **Publish-AzureServiceProject**, come illustrato di seguito.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Il parametro **servicename** specifica il nome usato per questa distribuzione. Deve trattarsi di un nome univoco, in caso contrario il processo di pubblicazione avrà esito negativo.

	- Il parametro **location** specifica il data center nel quale l'applicazione verrà ospitata. Per visualizzare un elenco dei data center disponibili, usare il cmdlet **Get-AzureLocation**.

	- Il parametro **launch** consente di avviare il browser e passare al servizio ospitato al termine della distribuzione.

	Al termine della pubblicazione, verrà visualizzata una risposta simile alla seguente:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	Il cmdlet **Publish-AzureServiceProject** esegue le operazioni seguenti:

1.  Crea un pacchetto che verrà distribuito in Azure. Il pacchetto contiene tutti i file nella cartella dell'applicazione Node.js.

2.  Crea un nuovo **account di archiviazione** nel caso non ne esista uno. L'account di archiviazione di Azure viene usato per archiviare il pacchetto dell'applicazione durante la distribuzione. L'account di archiviazione può essere eliminato dopo il completamento della distribuzione.

3.  Crea un nuovo **servizio cloud** nel caso in cui non ne esista già uno. Un **servizio cloud** è il contenitore nel quale l'applicazione è ospitata quando viene distribuita in Azure. Per altre informazioni, vedere [Panoramica della creazione di un servizio ospitato per Azure][].

4.  Pubblica il pacchetto di distribuzione in Azure.


	> [WACOM.NOTE]
	> Possono essere necessari da 5 a 7 minuti per la distribuzione dell'applicazione e prima che questa sia disponibile dopo la prima pubblicazione.

	Al termine della distribuzione, verrà visualizzata una finestra del browser che consente di passare al servizio cloud.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	L'applicazione è in esecuzione su Azure.


## Arresto ed eliminazione dell'applicazione

Dopo aver distribuito l'applicazione, è possibile disabilitarla per evitare costi aggiuntivi. Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita usa tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato.

1.  Nella finestra di Windows PowerShell arrestare la distribuzione del servizio creato nella sezione precedente con il cmdlet seguente:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	L'arresto del servizio può richiedere diversi minuti. Dopo l'arresto del servizio, viene visualizzato un messaggio di conferma dell'arresto.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Per eliminare il servizio, chiamare il cmdlet seguente:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Quando richiesto, immettere **S** per eliminare il servizio.

	L'eliminazione del servizio può richiedere diversi minuti. Al termine dell'eliminazione del servizio, verrà visualizzato un messaggio di conferma dell'eliminazione.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	<div class="dev-callout">
	<strong>Nota</strong>
	<p>L'eliminazione del servizio non comporta l'eliminazione dell'account di archiviazione creato quando il servizio è stato pubblicato e lo spazio di archiviazione usato continuerà a essere addebitato. Per altre informazioni
sull'eliminazione di un account di archiviazione, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/hh531562.aspx">Come eliminare un account di archiviazione da una sottoscrizione di Azure</a>.</p>
</div>


[Menu Start di Windows con la voce Node.js di Azure SDK espansa]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Elenco di directory della cartella helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Panoramica della creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj155995.aspx
[Elenco di directory della cartella WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Menu visualizzato facendo clic con il pulsante destro del mouse sull'emulatore di Azure dalla barra delle applicazioni.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Finestra del browser in cui è visualizzata la pagina http://www.windowsazure.com/ con il collegamento Versione di valutazione gratuita evidenziato]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Finestra del browser in cui è visualizzata la pagina di accesso a liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[In Internet Explorer viene visualizzata la finestra di dialogo salva con nome per il file publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Output completo dello stato del comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Come eliminare un account di archiviazione da una sottoscrizione di Azure]: https://www.windowsazure.com/it-it/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=35.2-->
