<properties 
	pageTitle="Guida introduttiva di Node.js - Esercitazione di Azure" 
	description="Informazioni su come creare una semplice applicazione Web Node.js e distribuirla in un servizio cloud di Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Questa esercitazione illustra come creare una semplice applicazione Node.js in esecuzione in un servizio cloud di Azure. I Servizi cloud sono blocchi predefiniti di applicazioni cloud scalabili in Azure. Consente la separazione e la gestione indipendente e la scalabilità dei componenti front-end e back-end dell'applicazione.  Servizi cloud offre una potente macchina virtuale dedicata per ospitare ogni ruolo in modo affidabile.

Per altre informazioni su Servizi cloud e sulle differenze rispetto ai servizi Siti Web e Macchine virtuali di Azure, vedere [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Come creare un semplice sito Web Se si intende creare un semplice sito Web front-end, è possibile <a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usare un sito Web di Azure semplificato.</a> È possibile procedere all'aggiornamento a un servizio cloud con facilità, in base alla crescita del sito Web e all'insorgere di nuove esigenze.


Questa esercitazione consente di creare una semplice applicazione Web ospitata in un ruolo Web. Si utilizzerà l'emulatore di calcolo per testare in locale l'applicazione, che verrà quindi distribuita con gli strumenti della riga di comando di PowerShell.

L'applicazione è una semplice applicazione "hello world":

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Finestra del browser che mostra la pagina hello world. L'URL indica che la pagina è ora ospitata in Azure.">
</p>

## Prerequisiti

> [AZURE.NOTE] Questa esercitazione usa Azure PowerShell, che richiede Windows.

- Installare Azure SDK per Node.js: <a href="http://go.microsoft.com/fwlink/?LinkId=254279">Windows Installer</a> 

- Installare e configurare [Azure Powershell](install-configure-powershell.md).


## Creare un progetto di Servizi cloud di Azure

Per creare un nuovo progetto di Servizi cloud di Azure, oltre allo scaffolding di Node.js è necessario eseguire queste operazioni:


1. Eseguire **Azure PowerShell** come amministratore dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**.

2.  Immettere il seguente cmdlet di PowerShell per creare il progetto:

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Il cmdlet **New-AzureServiceProject** genera una struttura di base per la pubblicazione di un'applicazione Node.js in un servizio cloud. Contiene i file di configurazione necessari per la pubblicazione in Azure. Il cmdlet modifica inoltre la directory di lavoro nella directory per il servizio.

	Il cmdlet crea i seguenti file:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: 
        File specifici di Azure necessari per la pubblicazione dell'applicazione.
		Per altre informazioni, vedere
        [Overview of Creating a Hosted Service for Azure][].

	-   **deploymentSettings.json**: Stores local settings that are used by
        the Azure PowerShell deployment cmdlets.

4.  Immettere il seguente comando per aggiungere un nuovo ruolo Web:

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Il cmdlet **Add-AzureNodeWebRole** crea un'applicazione Node.js di base. Modifica inoltre i file con estensione **csfg** e **csdef** per aggiungere voci di configurazione per il nuovo ruolo.

	> [AZURE.NOTE] Se non si specifica un nome di ruolo, viene usato un nome predefinito. È possibile specificare un nome come primo parametro di cmdlet: `Add-AzureNodeWebRole MyRole`


L'app Node.js è definita nel file **server.js**, contenuto nella directory per il ruolo Web (**WebRole1** per impostazione predefinita). Il codice è il seguente:

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Questo codice è essenzialmente lo stesso dell'esempio "Hello World" nel sito Web [nodejs.org][], si differenzia solo perché usa il numero di porta assegnato dall'ambiente cloud.


## Eseguire l'applicazione localmente nell'emulatore

Uno degli strumenti installati da Azure SDK è
l'emulatore di calcolo di Azure, che consente di testare l'applicazione in locale. L'oggetto
di calcolo simula l'ambiente di esecuzione dell'applicazione
una volta distribuita nel cloud. 

1.  Immettere il seguente cmdlet di Azure PowerShell per eseguire il servizio nell'emulatore:

        Start-AzureEmulator -Launch

	Usare il parametro **-Launch** per aprire automaticamente una finestra del browser quando il ruolo Web è in esecuzione nell'emulatore. La finestra del browser dovrebbe visualizzare "Hello World", come mostrato nella seguente schermata. 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Per arrestare l'emulatore di calcolo, usare il cmdlet **Stop-AzureEmulator**:
	
		Stop-AzureEmulator

## Distribuire l'applicazione in Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Scaricare le impostazioni di pubblicazione di Azure

Per distribuire l'applicazione in Azure, è necessario innanzitutto scaricare le impostazioni di pubblicazione per la sottoscrizione Azure. 

1.  Eseguire questo cmdlet di Azure PowerShell:

        Get-AzurePublishSettingsFile

	Il browser verrà così usato per passare alla pagina di download delle impostazioni di pubblicazione. È possibile che venga richiesto di effettuare l'accesso con un account Microsoft. In tal caso, usare l'account associato alla sottoscrizione di Azure.

	Salvare il profilo scaricato in un percorso di file facilmente accessibile.

2.  Eseguire questo cmdlet per importare il profilo di pubblicazione che è stato scaricato:

        Import-AzurePublishSettingsFile [percorso file]


	> [AZURE.NOTE] Dopo aver importato le impostazioni di pubblicazione, è consigliabile eliminare il file con estensione publishsettings, perché contiene informazioni che potrebbero consentire l'accesso all'account.
    

### Pubblicare l'applicazione

Per pubblicare l'applicazione, eseguire il cmdlet **Publish-AzureServiceProject** come indicato di seguito:

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** specifica il nome per la distribuzione. Deve essere un nome univoco, in caso contrario il processo di pubblicazione avrà esito negativo.

- **-Location** specifica il data center in cui verrà ospitata l'applicazione. Per visualizzare un elenco dei data center disponibili, usare il cmdlet **Get-AzureLocation**.

- **-Launch** apre una finestra del browser e passa al servizio ospitato dopo che è stata completata la distribuzione.

Al termine della pubblicazione, verrà visualizzata una risposta simile alla seguente:

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> Possono essere necessari da 5 a 7 minuti per la distribuzione dell'applicazione e prima che questa sia disponibile dopo la prima pubblicazione.

Al termine della distribuzione, verrà visualizzata una finestra del browser che consente di passare al servizio cloud.


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

L'applicazione è in esecuzione su Azure.

Il cmdlet **Publish-AzureServiceProject** esegue queste operazioni:

1.  Crea un pacchetto da distribuire. Il pacchetto contiene tutti i file della cartella dell'applicazione.

2.  Crea un nuovo **account di archiviazione** nel caso non ne esista uno. L'account di archiviazione di Azure viene usato per archiviare il pacchetto dell'applicazione durante la distribuzione. L'account di archiviazione può essere eliminato dopo il completamento della distribuzione.

3.  Crea un nuovo **servizio cloud** nel caso in cui non ne esista già uno. Un **servizio cloud** è il contenitore nel quale l'applicazione è ospitata quando viene distribuita in Azure. Per altre informazioni, vedere la pagina relativa alle [informazioni generali sulla creazione di un servizio ospitato per Azure][].

4.  Pubblica il pacchetto di distribuzione in Azure.



## Arrestare ed eliminare l'applicazione

Dopo aver distribuito l'applicazione, è possibile disabilitarla per evitare costi aggiuntivi. Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita usa tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato.

1.  Nella finestra di Windows PowerShell arrestare la distribuzione del servizio creata nella sezione precedente con il seguente cmdlet:

        Stop-AzureService

	L'arresto del servizio può richiedere diversi minuti. Dopo l'arresto del servizio, viene visualizzato un messaggio di conferma dell'arresto.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Per eliminare il servizio, chiamare il seguente cmdlet:

        Remove-AzureService

	Quando richiesto, immettere **Y** per eliminare il servizio.

	L'eliminazione del servizio può richiedere diversi minuti. Al termine dell'eliminazione del servizio, verrà visualizzato un messaggio di conferma dell'eliminazione.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] L'eliminazione del servizio non comporta l'eliminazione dell'account di archiviazione creato quando il servizio è stato pubblicato e lo spazio di archiviazione usato continuerà a essere addebitato. Per altre informazioni sull'eliminazione di un account di archiviazione, vedere la pagina relativa a [come eliminare un account di archiviazione da una sottoscrizione Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[Menu Start di Windows con la voce Node.js di Azure SDK espansa]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Elenco di directory della cartella helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Informazioni generali sulla creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Elenco di directory della cartella WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[Menu visualizzato facendo clic con il pulsante destro del mouse sull'emulatore di Azure dalla barra delle applicazioni.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Finestra del browser in cui è visualizzata la pagina http://www.windowsazure.com/ con il collegamento Versione di valutazione gratuita evidenziato]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Finestra del browser in cui è visualizzata la pagina di accesso a liveID]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[In Internet Explorer viene visualizzata la finestra di dialogo salva con nome per il file publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[Output completo dello stato del comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Come eliminare un account di archiviazione da una sottoscrizione di Azure]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=52-->