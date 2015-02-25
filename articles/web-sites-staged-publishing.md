<properties 
	pageTitle="Distribuzione temporanea su Siti Web di Microsoft Azure" 
	description="Informazioni su come usare la pubblicazione di gestione temporanea in Siti Web di Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Distribuzione temporanea su Siti Web di Microsoft Azure#
Quando si distribuisce un'applicazione su Siti Web di Azure, è possibile eseguire questa operazione su uno slot di distribuzione separato anziché sullo slot di produzione predefinito. Tali slot sono in realtà siti attivi con i rispettivi nomi host. Questa opzione è disponibile nel piano di hosting Web **Standard**. È inoltre possibile scambiare i siti e le configurazioni di sito tra due slot di distribuzione o produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

- È possibile convalidare le modifiche ai siti Web in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.

- Dopo uno scambio, lo slot con il sito gestito temporaneamente includerà il sito di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti. 
 
- La distribuzione preliminare di un sito in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione del sito. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. 

Per ogni sito Web nel piano **Standard** sono supportati quattro slot di distribuzione oltre a quello di produzione. 

## Sommario ##
- [Per aggiungere uno slot di distribuzione a un sito Web](#Add)
- [Informazioni sulla configurazione degli slot di distribuzione](#AboutConfiguration)
- [Per scambiare gli slot di distribuzione](#Swap)
- [Per eseguire il rollback di un sito di produzione alla gestione temporanea](#Rollback)
- [Per eliminare uno slot di sito](#Delete)
- [Cmdlet di Azure PowerShell per gli slot di sito](#PowerShell)
- [Comandi dell'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) per gli slot di sito](#CLI)

<a name="Add"></a>
##Per aggiungere uno slot di distribuzione a un sito Web##

Per abilitare l'uso di più slot di distribuzione è necessario che il sito Web sia in esecuzione nel piano di hosting **Standard**. 

1. Nella pagina Avvio rapido o nella sezione Riepilogo rapido della pagina Dashboard del proprio sito Web fare clic su **Aggiungi nuovo slot di distribuzione**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> Se il sito Web non è già in modalità **Standard** si riceverà un messaggio simile al seguente: **Per abilitare la pubblicazione di gestione temporanea, è necessario essere in modalità Standard**. A questo punto è possibile selezionare **Aggiorna** e passare alla scheda **Scala** del sito Web prima di continuare.
	
2. Nella finestra di dialogo **Aggiungi nuovo slot di distribuzione** assegnare un nome allo slot e specificare se si desidera clonare la configurazione del sito Web da un altro slot di distribuzione esistente. Fare clic sul segno di spunta per continuare. 
	
	![Configuration Source][ConfigurationSource1]
	
	La prima volta che si crea uno slot, sono disponibili due opzioni: clonare o non clonare la configurazione dallo slot predefinito in produzione 
	
	Dopo aver creato vari slot, sarà possibile clonare la configurazione da uno slot diverso da quello in produzione:
	
	![Configuration sources][MultipleConfigurationSources]

5. Nell'elenco dei siti Web espandere il segno a sinistra del nome del proprio sito Web per visualizzare lo slot di distribuzione, con il nome del sito seguito da quello dello slot. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. Quando si farà clic sul nome dello slot del sito di distribuzione, si aprirà una pagina con un gruppo di schede, proprio come in qualsiasi altro sito Web. Nella parte superiore della pagina del portale sarà visualizzata la dicitura <strong><i>your-website-name</i>(<i>deployment-slot-name</i>)</strong> per ricordare all'utente che sta visualizzando lo slot del sito di distribuzione.
	
	![Deployment Slot Title][StagingTitle]
	
5. Fare clic sull'URL del sito nella visualizzazione Dashboard. Si noti che lo slot di distribuzione ha un proprio nome host ed è anche un sito attivo. Per limitare l'accesso pubblico allo slot di distribuzione, vedere il post relativo al [blocco dell'accesso Web agli slot di distribuzione non di produzione in Siti Web di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

	-	 

Non sono inclusi i contenuti. È possibile distribuire lo slot da un'area diversa dell'archivio o da un altro archivio. È anche possibile modificare la configurazione dello slot. Usare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot di distribuzione per gli aggiornamenti dei contenuti.  Ad esempio è possibile [pubblicare in questo slot con git](http://azure.microsoft.com/it-it/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
##Informazioni sulla configurazione degli slot di distribuzione##
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Nei seguenti elenchi sono riportati gli elementi di configurazione che verranno modificati al momento dello scambio degli slot.

**Elementi di configurazione che verranno modificati allo scambio degli slot**:

- Impostazioni generali
- Stringhe di connessione
- Mapping dei gestori
- Impostazioni di monitoraggio e diagnostica

**Elementi di configurazione che non verranno modificati allo scambio degli slot**:

- Endpoint di pubblicazione
- Nomi di dominio personalizzati
- Certificati e associazioni SSL
- Impostazioni di scalabilità

**Note**:

- Sono disponibili più slot di distribuzione solo per i siti nel piano di hosting Web **Standard**.

- Quando il sito ha più slot, non è possibile cambiare il piano di hosting.

- Se si intende applicare uno slot al sito di produzione è necessario configurarlo esattamente come dovrebbe essere nell'ambiente di produzione.

- Per impostazione predefinita, uno slot di distribuzione punterà allo stesso database del sito di produzione. Tuttavia, è possibile configurare lo slot di distribuzione in modo che punti a un database alternativo modificando la stringa o le stringhe di connessione al database per lo slot di distribuzione. Sarà quindi possibile ripristinare la stringa o le stringhe di connessione al database originali sullo slot di distribuzione poco prima di applicarlo al sito di produzione.


<a name="Swap"></a>
##Per scambiare gli slot di distribuzione##

1. Per scambiare gli slot di distribuzione, selezionare lo slot di distribuzione desiderato nell'elenco dei siti Web e fare clic sul pulsante **Scambia** nella barra dei comandi. 
	
	![Swap Button][SwapButtonBar]
	
2. Viene visualizzata la finestra di dialogo Scambia distribuzioni. La finestra di dialogo consente di scegliere quale slot di sito dovrebbe essere l'origine e quale sito dovrebbe essere la destinazione.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Fare clic sul segno di spunta per completare l'operazione. Al termine dell'operazione, lo scambio dei siti sarà stato eseguito correttamente.


<a name="Rollback"></a>
##Per eseguire il rollback di un sito di produzione alla gestione temporanea##
Se vengono identificati errori nel sito di produzione dopo lo scambio di uno slot, ripristinare i due slot allo stato precedente scambiandoli immediatamente. 

<a name="Delete"></a>
##Per eliminare uno slot di sito##

Fare clic su **Elimina** nella barra dei comandi nella parte inferiore della pagina del portale di Siti Web di Azure. Sarà possibile scegliere di eliminare il sito Web e tutti gli slot di distribuzione oppure di eliminare solo lo slot di distribuzione. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Note**:

- Gli slot non di produzione non sono scalabili. Questa funzione è disponibile esclusivamente per gli slot di produzione.

- La gestione delle risorse collegate non è supportata per gli slot non di produzione. 

- Se lo si desidera, è comunque possibile pubblicare direttamente sullo slot di produzione.

- Per impostazione predefinita, gli slot (siti) di distribuzione condividono le stesse risorse degli slot (siti) di produzione e vengono eseguiti sulle stesse macchine virtuali. Se si esegue il test di stress su uno slot di gestione temporanea, l'ambiente di produzione subirà un carico di stress analogo. 
	
	> [AZURE.NOTE] Solo nel [portale di anteprima di Azure](https://portal.azure.com) è possibile evitare questo impatto potenziale su uno slot di produzione spostando temporaneamente lo slot non di produzione in un piano di hosting Web differente. Si noti che lo slot di test e quello di produzione devono condividere lo stesso piano di hosting Web affinché sia possibile eseguire lo scambio tra i due slot.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Cmdlet di Azure PowerShell per gli slot di sito 

Azure PowerShell è un modulo che fornisce cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione di Siti Web di Azure. 

- Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con la sottoscrizione Microsoft Azure, vedere [Come installare e configurare Azure PowerShell](http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell).  

- Per elencare i cmdlet disponibili per Siti Web di Azure in PowerShell, chiamare `help AzureWebsite`. 

----------

###Get-AzureWebsite
Il cmdlet **Get-AzureWebsite** presenta informazioni su Siti Web di Azure per l'attuale sottoscrizione, come illustrato nel seguente esempio. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
È possibile creare un slot di sito per qualsiasi sito Web in modalità Standard usando il cmdlet **New-AzureWebsite** e specificando i nomi del sito e dello slot. Indicare inoltre la stessa area del sito per la creazione dello slot di distribuzione, come nel seguente esempio. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
È possibile usare il cmdlet **Publish-AzureWebsiteProject** per la distribuzione dei contenuti, come nel seguente esempio. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
Dopo aver applicato gli aggiornamenti dei contenuti e della configurazione al nuovo slot, è possibile convalidarli passando allo slot che usa il cmdlet **Show-AzureWebsite**, come nel seguente esempio.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
Il cmdlet **Switch-AzureWebsiteSlot** può eseguire un'operazione di scambio per applicare lo slot di distribuzione aggiornato al sito di produzione, come nel seguente esempio. Il sito di produzione non sarà caratterizzato da tempi di inattività né subirà un avvio a freddo. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
Se uno slot di distribuzione non è più necessario, è possibile eliminarlo usando il cmdlet **Remove-AzureWebsite**, come nel seguente esempio.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Comandi dell'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) per gli slot di sito

L'interfaccia della riga di comando multipiattaforma (xplat-cli) di Azure fornisce comandi multipiattaforma per l'uso con Azure, incluso il supporto per la gestione degli slot di distribuzione su Siti Web di Azure. 

- Per istruzioni sull'installazione e la configurazione dell'interfaccia xplat-cli, incluse le informazioni su come collegarla alla sottoscrizione Azure, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure ](http://www.windowsazure.com/it-it/documentation/articles/xplat-cli). 

-  Per elencare i comandi disponibili per Siti Web nell'interfaccia xplat-cli, chiamare `azure site -h`. 

----------
###azure site list
Per informazioni sui siti Web di Azure nell'attuale sottoscrizione, chiamare **azure site list**, come nel seguente esempio.
 
`azure site list siteslotstest`

----------
###azure site create
Per creare uno slot di sito per qualsiasi sito Web in modalità Standard, chiamare **azure site create** e specificare il nome di un sito esistente e il nome dello slot da creare, come nel seguente esempio.

`azure site create siteslotstest --slot staging`

Per abilitare il controllo del codice sorgente per il nuovo slot, usare l'opzione **--git**, come nel seguente esempio.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
Per applicare lo slot di distribuzione aggiornato al sito di produzione, usare il comando **azure site swap** per eseguire un'operazione di scambio, come nel seguente esempio. Il sito di produzione non sarà caratterizzato da tempi di inattività né subirà un avvio a freddo. 

`azure site swap siteslotstest`

----------
###azure site delete
Per eliminare uno slot di distribuzione non più necessario, usare il comando **azure site delete**, come nel seguente esempio.

`azure site delete siteslotstest --slot staging`

----------
## Passaggi successivi ##
[Blocco dell'accesso Web agli slot di distribuzione non di produzione in Siti Web di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versione di valutazione gratuita di Microsoft Azure](http://azure.microsoft.com/it-it/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png


<!--HONumber=42-->
