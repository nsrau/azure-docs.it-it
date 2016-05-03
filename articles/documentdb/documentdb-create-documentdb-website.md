<properties 
	pageTitle="Distribuire DocumentDB e app Web del servizio app di Azure mediante un modello di Gestione risorse di Azure | Microsoft Azure" 
	description="Informazioni su come distribuire un account DocumentDB, app Web del servizio app di Azure e un'applicazione Web di esempio usando un modello di Gestione risorse di Azure." 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="stbaro"/>

# Distribuire DocumentDB e app Web del servizio app di Azure mediante un modello di Gestione risorse di Azure

Questa esercitazione illustra come usare un modello di Gestione risorse di Azure per distribuire e integrare [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), un'app Web del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e un'applicazione Web di esempio.

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:

-	Come è possibile usare un modello di Gestione risorse di Azure per distribuire e integrare un account DocumentDB e un'app Web nel servizio app di Azure?
-	Come è possibile usare un modello di Gestione risorse di Azure per distribuire e integrare un account DocumentDB, un'app Web nelle app Web del servizio app e un'applicazione WebDeploy?

<a id="Prerequisites"></a>
## Prerequisiti
> [AZURE.TIP] Sebbene questa esercitazione non presupponga esperienza nell'uso dei modelli di Gestione risorse di Azure, di JSON o di Azure PowerShell, qualora si intendano modificare i modelli o le opzioni di distribuzione cui viene fatto riferimento saranno necessarie le opportune nozioni per ciascuna di tali aree.

Prima di seguire le istruzioni di questa esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/), [Offerte per i membri](https://azure.microsoft.com/pricing/member-offers/) oppure [Versione di prova gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Un account di archiviazione di Azure Per istruzioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).
- Una workstation con Azure PowerShell 0.9.8. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Questa esercitazione non è ancora stata aggiornata per l'anteprima di Azure PowerShell 1.0. 

##<a id="CreateDB"></a>Passaggio 1: Scaricare ed estrarre i file di esempio ##
Per iniziare, scaricare i file di esempio che verranno usati in questa esercitazione.

1. Scaricare l'[esempio per creare un account DocumentDB, app Web e distribuire un'applicazione demo](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) in una cartella locale (ad esempio, C:\\DocumentDBTemplates), quindi estrarre i file. Questo esempio distribuirà un account DocumentDB, un'app Web del servizio app e un'applicazione Web. L'applicazione Web verrà anche configurata automaticamente per connettersi all'account DocumentDB.

2. Scaricare l'[esempio per creare un account DocumentDB e un'app Web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) in una cartella locale (ad esempio, C:\\DocumentDBTemplates), quindi estrarre i file. Questo esempio distribuirà un account DocumentDB, un'app Web del servizio app e modificherà la configurazione dell'app Web per rilevare in modo semplice le informazioni relative alla connessione di DocumentDB. Non è tuttavia inclusa alcuna applicazione Web.

> [AZURE.TIP] Si noti che, a seconda delle impostazioni di sicurezza del computer, potrebbe essere necessario sbloccare i file estratti facendo clic con il pulsante destro del mouse, scegliendo **Proprietà**, quindi facendo clic su **Sblocca**.

![Schermata della finestra Proprietà con pulsante Sblocca evidenziato](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Passaggio 2: Distribuire l'esempio relativo ad account DocumentDB, app Web del servizio app e applicazione demo

Si procederà a questo punto alla distribuzione del primo modello.

> [AZURE.TIP] Il modello non verifica che il nome dell'app Web e il nome dell'account DocumentDB siano validi e/o disponibili. È consigliabile verificare la disponibilità dei nomi che si vogliono usare prima di eseguire lo script di distribuzione di PowerShell.

1. Aprire Microsoft Azure PowerShell e passare alla cartella in cui è stato scaricato ed estratto l'[esempio per creare un account DocumentDB, un'app Web del servizio app e distribuire un'applicazione demo](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (ad esempio, C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo).


2. Verrà eseguito lo script di PowerShell CreateDocDBWebsiteTodo.ps1. Lo script accetta i parametri obbligatori seguenti:
	- WebsiteName: specifica il nome dell'app Web del servizio app e viene usato per creare l'URL che verrà usato per accedere all'app Web. Ad esempio, se si specifica "mydemodocdbwebapp", l'URL usato per accedere all'app Web sarà mydemodocdbwebapp.azurewebsites.net.

	- ResourceGroupName: specifica il nome del gruppo di risorse di Azure da distribuire. Se il gruppo di risorse specificato non esiste, verrà creato.

	- docDBAccountName: specifica il nome dell'account DocumentDB da creare.

	- location: specifica la località di Azure in cui creare le risorse di DocumentDB e dell'app Web. I valori validi sono Asia orientale, Asia sudorientale, Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Europa occidentale (si noti che per il valore del percorso fornito viene fatta distinzione tra lettere maiuscole e minuscole).


3. Di seguito è riportato un comando di esempio per eseguire lo script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Si noti che verrà richiesto di immettere il nome utente e la password dell'account Azure come parte dell'esecuzione dello script. Il completamento della distribuzione richiederà tra 10 e 15 minuti.

4. Di seguito è riportato un esempio di output risultante:

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Prima di esaminare l'applicazione di esempio, si cercherà di comprendere quali obiettivi sono stati raggiunti con il completamento della distribuzione:

	- È stata creata un'app Web del servizio app.

	- È stato creato un account DocumentDB.

	- Un pacchetto di distribuzione Web è stato distribuito nell'app Web del servizio app.

	- La configurazione dell'app Web è stata modificata in modo da rilevare l'endpoint di DocumentDB e la chiave master primaria come impostazioni dell'applicazione.

	- È stata creata una serie di regole di monitoraggio predefinite.

	
6. Per usare l'applicazione, passare semplicemente all'URL dell'app Web (nell'esempio precedente, tale URL sarebbe http://mydemodocdbwebapp.azurewebsites.net). Verrà visualizzata l'applicazione Web seguente:

	![Applicazione di esempio](./media/documentdb-create-documentdb-website/image2.png)

7. Creare un paio di attività e quindi aprire il [portale di Microsoft Azure](https://portal.azure.com).

8. Sfogliare i gruppi di risorse, quindi selezionare il gruppo di risorse creato durante la distribuzione (nell'esempio precedente myDemoResourceGroup).

	![Schermata del portale di Azure classico con myDemoResourceGroup evidenziato](./media/documentdb-create-documentdb-website/image3.png)
9.  Notare che la mappa delle risorse nel filtro di riepilogo mostra tutte le risorse correlate (account DocumentDB, app Web del servizio app, monitoraggio).

	![Schermata del filtro di riepilogo](./media/documentdb-create-documentdb-website/image4.png)
10.  Fare clic sull'account DocumentDB e avviare Esplora Query (nella parte inferiore del pannello Account).

	![Schermata dei pannelli Gruppo di risorse e Account con il riquadro Esplora query evidenziato](./media/documentdb-create-documentdb-website/image8.png)

11. Eseguire la query predefinita, "SELECT * FROM c" ed esaminarne i risultati. Si noti che la query ha recuperato la rappresentazione JSON delle attività create nel passaggio 7 precedente. È possibile sperimentare con le query. Ad esempio, provare a eseguire SELECT * FROM c WHERE c.isComplete = true per restituire tutti gli elementi ToDo che sono stati contrassegnati come completati.


	![Schermata dei pannelli Esplora query e Risultati con i risultati delle query](./media/documentdb-create-documentdb-website/image5.png)
12. È possibile verificare la funzionalità del portale di DocumentDB o modificare l'applicazione ToDo di esempio. A questo punto si è pronti per distribuire un altro modello.
	
<a id="Build"></a>
## Passaggio 3: Distribuire l'esempio relativo ad account DocumentDB e app Web

Si procederà ora alla distribuzione del secondo modello.

> [AZURE.TIP] Il modello non verifica che il nome dell'app Web e il nome dell'account DocumentDB siano validi e/o disponibili. È consigliabile verificare la disponibilità dei nomi che si vogliono usare prima di eseguire lo script di distribuzione di PowerShell.

1. Aprire Microsoft Azure PowerShell e passare alla cartella in cui è stato scaricato ed estratto l'[esempio per creare un account DocumentDB e un'app Web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (ad esempio, C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Verrà eseguito lo script di PowerShell CreateDocDBWebsite.ps1. Lo script accetta gli stessi parametri del primo modello che distribuito, vale a dire:
	- WebsiteName: specifica il nome dell'app Web del servizio app e viene usato per creare l'URL che verrà usato per accedere all'app Web. Ad esempio, se si specifica "myotherdocumentdbwebapp", l'URL usato per accedere all'app Web sarà myotherdocumentdbwebapp.azurewebsites.net.

	- ResourceGroupName: specifica il nome del gruppo di risorse di Azure da distribuire. Se il gruppo di risorse specificato non esiste, verrà creato.

	- docDBAccountName: specifica il nome dell'account DocumentDB da creare.

	- 	location: specifica la località di Azure in cui creare le risorse di DocumentDB e dell'app Web. I valori validi sono Asia orientale, Asia sudorientale, Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Europa occidentale (si noti che per il valore del percorso fornito viene fatta distinzione tra lettere maiuscole e minuscole).

3. Di seguito è riportato un comando di esempio per eseguire lo script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Si noti che verrà richiesto di immettere il nome utente e la password dell'account Azure come parte dell'esecuzione dello script. Il completamento della distribuzione richiederà tra 10 e 15 minuti.

4. L'output della distribuzione sarà molto simile a quello del primo esempio di modello.
5. Prima di aprire il portale di Azure, si cercheranno di individuare gli obiettivi raggiunti con il completamento della distribuzione:

	- È stata creata un'app Web del servizio app.

	- È stato creato un account DocumentDB.

	- 	La configurazione dell'app Web è stata modificata in modo da rilevare l'endpoint di Azure DocumentDB e la chiave master secondaria come impostazioni dell'applicazione.

	- 	È stata creata una serie di regole di monitoraggio predefinite.

6. Aprire il [portale di Azure](https://portal.azure.com), sfogliare i gruppi di risorse, quindi selezionare il gruppo di risorse creato durante la distribuzione (nell'esempio precedente myOtherDemoResourceGroup).
7. Nel filtro di riepilogo, fare clic sull'app Web che è stata appena distribuita.

	![Schermata del filtro di riepilogo con l'app Web myotherdocumentdbwebapp evidenziata](./media/documentdb-create-documentdb-website/image6.png)
8. Nel pannello dell'app Web fare clic su **Tutte le impostazioni**, quindi su **Impostazioni applicazione** e notare la presenza di impostazioni dell'applicazione per l'endpoint e per ognuna delle chiavi master di DocumentDB.

	![Schermata dei pannelli dell'app Web , Impostazioni e delle impostazioni dell'applicazione](./media/documentdb-create-documentdb-website/image7.png)
9. È possibile continuare a esplorare il portale di Azure o seguire uno degli [esempi](http://go.microsoft.com/fwlink/?LinkID=402386) di DocumentDB per creare la propria applicazione DocumentDB.

	
	
<a name="NextSteps"></a>
## Passaggi successivi

Congratulazioni. Si è completata la distribuzione di DocumentDB, di un'app Web del servizio app e di un'applicazione Web di esempio usando i modelli di Gestione risorse di Azure.

- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
- Per altre informazioni sulle app Web del servizio app di Azure, vedere [qui](http://go.microsoft.com/fwlink/?LinkId=325362).
- Per altre informazioni sui modelli di Gestione risorse di Azure, vedere [qui](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale vedere: [Informazioni di riferimento per l'esplorazione del portale di Azure classico](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
 

<!---HONumber=AcomDC_0420_2016-->