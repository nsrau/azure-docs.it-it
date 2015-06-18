<properties 
	pageTitle="Distribuire DocumentDB e un sito Web di Azure usando un modello di Gestione risorse di Azure | Azure" 
	description="Informazioni su come distribuire un account DocumentDB, un sito Web di Azure e un'applicazione Web di esempio usando un modello di Gestione risorse di Azure." 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

# Distribuire DocumentDB e un sito Web di Azure usando un modello di Gestione risorse di Azure #

Questa esercitazione illustra come usare un modello di Gestione risorse di Azure per distribuire e integrare [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), un [sito Web di Azure](http://azure.microsoft.com/services/websites/) e un'applicazione Web di esempio.

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:  

-	Come è possibile usare un modello di Gestione risorse di Azure per distribuire e integrare un account DocumentDB e un sito Web di Azure?
-	Come è possibile usare un modello di Gestione risorse di Azure per distribuire e integrare un account DocumentDB, un sito Web di Azure e un'applicazione WebDeploy?

##<a id="Prerequisites"></a>Prerequisiti ##
> [AZURE.TIP] Sebbene questa esercitazione non presupponga esperienza nell'uso dei modelli di Gestione risorse di Azure, di JSON o di Azure PowerShell, qualora si intendano modificare i modelli o le opzioni di distribuzione cui viene fatto riferimento saranno necessarie le opportune nozioni per ciascuna di tali aree.

Prima di seguire le istruzioni di questa esercitazione, verificare che siano disponibili gli elementi seguenti:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione.  Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/), [Offerte per i membri](http://azure.microsoft.com/pricing/member-offers/) oppure [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
- Un account di archiviazione di Azure Per istruzioni, vedere [Informazioni sugli account di archiviazione di Azure](../storage-whatis-account.md).
- Workstation con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](../install-configure-powershell.md).

##<a id="CreateDB"></a>Passaggio 1: Scaricare ed estrarre i file di esempio ##
Per iniziare, scaricare i file di esempio che verranno usati in questa esercitazione.

1. Scaricare l'[esempio per creare un account DocumentDB, un sito Web e distribuire un'applicazione demo](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) in una cartella locale (ad esempio, C:\DocumentDBTemplates), quindi estrarre i file.  Con questo esempio si distribuirà un account DocumentDB, un sito Web di Azure e un'applicazione Web.  L'applicazione Web verrà anche configurata automaticamente per connettersi all'account DocumentDB.

2. Scaricare l'[esempio per creare un account DocumentDB e un sito Web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) in una cartella locale (ad esempio, C:\DocumentDBTemplates), quindi estrarre i file.  Questo esempio distribuirà un account DocumentDB, un sito Web di Azure e modificherà la configurazione del sito Web per rilevare in modo semplice le informazioni relative alla connessione di DocumentDB. Non è tuttavia inclusa alcuna applicazione Web.  

> [AZURE.TIP] Si noti che, a seconda delle impostazioni di sicurezza del computer, potrebbe essere necessario sbloccare i file estratti facendo clic con il pulsante destro del mouse, scegliendo **Proprietà**, quindi facendo clic su **Sblocca**.

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>Passaggio 2: Distribuire l'esempio di account DocumentDB, sito Web e applicazione demo ##

Si procederà a questo punto alla distribuzione del primo modello.

> [AZURE.TIP] Il modello non verifica che il nome del sito Web e il nome dell'account DocumentDB siano validi e/o disponibili.  È consigliabile verificare la disponibilità dei nomi che si vogliono usare prima di eseguire lo script di distribuzione di PowerShell.

1. Aprire Microsoft Azure PowerShell e passare alla cartella in cui è stato scaricato ed estratto l'[esempio per creare un account DocumentDB, un sito Web e distribuire un'applicazione demo](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (ad esempio, C:\DocumentDBTemplates\CreateDocDBWebsiteTodo).


2. Verrà eseguito lo script di PowerShell CreateDocDBWebsiteTodo.ps1.  Lo script accetta i parametri obbligatori seguenti:
	- WebsiteName: specifica il nome del sito Web e viene usato per costruire l'URL che si userà per accedere al sito Web (ad esempio, se si specifica "mydemodocdbwebsite", l'URL tramite cui si accederà al sito Web sarà mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName: specifica il nome del gruppo di risorse di Azure da distribuire. Se il gruppo di risorse specificato non esiste, verrà creato.

	- docDBAccountName: Specifica il nome dell'account DocumentDB da creare.

	- location: Specifica la località di Azure in cui creare le risorse di DocumentDB e del sito Web.  I valori validi sono Asia orientale, Asia sudorientale, Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Europa occidentale (si noti che per il valore del percorso fornito viene fatta distinzione tra lettere maiuscole e minuscole).


3. Di seguito è riportato un comando di esempio per eseguire lo script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Si noti che verrà richiesto di immettere il nome utente e la password dell'account Azure come parte dell'esecuzione dello script. Il completamento della distribuzione richiederà tra 10 e 15 minuti.  	

4. Di seguito è riportato un esempio di output risultante: 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Prima di esaminare l'applicazione di esempio, si cercherà di comprendere quali obiettivi sono stati raggiunti con il completamento della distribuzione:

	- È stato creato un sito Web di Azure.

	- È stato creato un account Azure DocumentDB.

	- Un pacchetto di distribuzione Web è stato distribuito nel sito Web di Azure

	- La configurazione del sito Web di Azure è stata modificata in modo da rilevare l'endpoint di Azure DocumentDB e la chiave master primaria come impostazioni dell'applicazione.

	- È stata creata una serie di regole di monitoraggio predefinite.

	
6. Per usare l'applicazione, passare semplicemente all'URL del sito Web (nell'esempio precedente, tale URL sarebbe http://mydemodocdbwebsite.azurewebsites.net).  Verrà visualizzata l'applicazione Web seguente:

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. Creare un paio di attività e quindi aprire il [portale di anteprima di Azure](https://portal.azure.com).

8. Sfogliare i gruppi di risorse, quindi selezionare il gruppo di risorse creato durante la distribuzione (nell'esempio precedente myDemoResourceGroup).

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  Notare che la mappa delle risorse nel filtro di riepilogo mostra tutte le risorse correlate (account DocumentDB, sito Web, monitoraggio).

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  Fare clic sull'account DocumentDB e avviare Esplora Query (nella parte inferiore del pannello Account).

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. Eseguire la query predefinita, "SELECT * FROM c" ed esaminarne i risultati.  Si noti che la query ha recuperato la rappresentazione JSON delle attività create nel passaggio 7 precedente.  È possibile sperimentare con le query. Ad esempio, provare a eseguire SELECT * FROM c WHERE c.isComplete = true per restituire tutti gli elementi ToDo che sono stati contrassegnati come completati.


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. È possibile verificare la funzionalità del portale di DocumentDB o modificare l'applicazione ToDo di esempio.  A questo punto si è pronti per distribuire un altro modello.
	 
##<a id="Build"></a>Passaggio 3: Distribuire l'account DocumentDB e il sito Web di esempio ##

Si procederà ora alla distribuzione del secondo modello.

> [AZURE.TIP] Il modello non verifica che il nome del sito Web e il nome dell'account DocumentDB siano validi e/o disponibili.  È consigliabile verificare la disponibilità dei nomi che si vogliono usare prima di eseguire lo script di distribuzione di PowerShell.

1. Aprire Microsoft Azure PowerShell e passare alla cartella in cui è stato scaricato ed estratto l'[esempio per creare un account DocumentDB e un sito Web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (ad esempio, C:\DocumentDBTemplates\CreateDocDBWebsite).


2. Verrà eseguito lo script di PowerShell CreateDocDBWebsite.ps1.  Lo script accetta gli stessi parametri del primo modello che distribuito, vale a dire:
	- WebsiteName: specifica il nome del sito Web e viene usato per costruire l'URL che si userà per accedere al sito Web (ad esempio, se si specifica "myotherdocumentdbwebsite", l'URL tramite cui si accederà al sito Web sarà myotherdocumentdbwebsite.azurewebsites.net).

	- ResourceGroupName: specifica il nome del gruppo di risorse di Azure da distribuire.  Se il gruppo di risorse specificato non esiste, verrà creato.

	- docDBAccountName: Specifica il nome dell'account DocumentDB da creare.

	- 	location: Specifica la località di Azure in cui creare le risorse di DocumentDB e del sito Web.  I valori validi sono Asia orientale, Asia sudorientale, Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Europa occidentale (si noti che per il valore del percorso fornito viene fatta distinzione tra lettere maiuscole e minuscole).

3. Di seguito è riportato un comando di esempio per eseguire lo script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Si noti che verrà richiesto di immettere il nome utente e la password dell'account Azure come parte dell'esecuzione dello script.  Il completamento della distribuzione richiederà tra 10 e 15 minuti.  	

4. L'output della distribuzione sarà molto simile a quello del primo esempio di modello. 
5. Prima di aprire il portale di Azure, si cercheranno di individuare gli obiettivi raggiunti con il completamento della distribuzione:

	- È stato creato un sito Web di Azure.

	- È stato creato un account Azure DocumentDB.

	- 	La configurazione del sito Web di Azure è stata modificata in modo da rilevare l'endpoint di Azure DocumentDB e la chiave master secondaria come impostazioni dell'applicazione.

	- 	È stata creata una serie di regole di monitoraggio predefinite.

6. Aprire il [portale di anteprima di Azure](https://portal.azure.com), sfogliare i gruppi di risorse, quindi selezionare il gruppo di risorse creato durante la distribuzione (nell'esempio precedente myOtherDemoResourceGroup).
7. Nel filtro di riepilogo, fare clic su sito Web di Azure che è stato appena distribuito.

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. Nel pannello del sito Web fare clic su **Tutte le impostazioni**, quindi su **Impostazioni applicazione** e notare la presenza di impostazioni dell'applicazione per l'endpoint e per ognuna delle chiavi master di DocumentDB.

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. È possibile continuare a esplorare il portale di Azure o seguire uno degli [esempi](http://go.microsoft.com/fwlink/?LinkID=402386) di DocumentDB per creare la propria applicazione DocumentDB.

	
	

##<a name="NextSteps"></a>Passaggi successivi

Congratulazioni. Si è completata la distribuzione di DocumentDB, di un sito Web di Azure e di un'applicazione Web di esempio usando i modelli di Gestione risorse di Azure.

- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
- Per altre informazioni su Siti Web di Azure, vedere [qui](http://go.microsoft.com/fwlink/?LinkId=325362).
- Per altre informazioni sui modelli di Gestione risorse di Azure, vedere [qui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

<!--HONumber=49--> 