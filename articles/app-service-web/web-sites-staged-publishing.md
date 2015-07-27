<properties
	pageTitle="Configurare ambienti di gestione temporanea per le app Web nel servizio app di Azure"
	description="Informazioni su come usare la pubblicazione per fasi per le app Web nel servizio app di Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="cephalin"/>

# Configurare ambienti di gestione temporanea per le app Web nel servizio app di Azure
<a name="Overview"></a>

Quando si distribuisce l'app Web al [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), è possibile eseguire la distribuzione a uno slot di distribuzione distinto, invece di quello predefinito, se la modalità dei piani del servizio app è **Standard** o **Premium**. Gli slot di distribuzione sono in realtà app Web dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app Web e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

- È possibile convalidare le modifiche alle app Web in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.

- La distribuzione preliminare di un'app Web in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app Web. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzata tramite la configurazione di [scambio automatico](#configure-auto-swap-for-your-web-app) quando non è necessario spazio di pre-swapping convalida.

- Dopo uno scambio, lo slot con l'app Web gestita temporaneamente includerà l'app Web di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ciascuna modalità di piano del servizio app supporta un numero diverso di slot di distribuzione. Per scoprire il numero di slot supportati dalla modalità della propria app Web, vedere [Tariffe del servizio app](/pricing/details/app-service/).

- Se l'app Web dispone di più slot, non è possibile cambiare la modalità.

- Gli slot non di produzione non sono scalabili.

- La gestione delle risorse collegate non è supportata per gli slot non di produzione.

	> [AZURE.NOTE]Solo nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), è possibile evitare questo impatto potenziale su uno slot di produzione spostando temporaneamente lo slot non di produzione in una modalità di piano del servizio app differente. Si noti che lo slot non di produzione deve ancora una volta condividere la stessa modalità dello slot di produzione per potere eseguire lo scambio tra i due slot.

<a name="Add"></a>
## Aggiungere uno slot di distribuzione a un'app Web ##

Per abilitare più slot di distribuzione, l'app Web deve essere in esecuzione in modalità **Standard** o **Premium**.

1. Nel [portale di anteprima di Azure](https://portal.azure.com/), aprire il pannello dell'app Web.
2. Fare clic su **Slot di distribuzione**. Quindi, nel pannello **Slot di distribuzione**, fare clic su **Aggiungi slot**.

	![Aggiungi nuovo slot di distribuzione][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]Se l'app Web non è già in modalità **Standard** o **Premium**, si riceverà un messaggio di errore che indica le modalità supportate per l'abilitazione della pubblicazione per fasi. A questo punto, è possibile selezionare **Aggiorna** e spostarsi alla scheda **Scala** dell'app Web prima di continuare.

2. Nel pannello **Aggiungi uno slot**, dare un nome allo slot e selezionare se clonare la configurazione dell'app Web da uno slot di distribuzione esistente. Fare clic sul segno di spunta per continuare.

	![Origine della configurazione][ConfigurationSource1]

	La prima volta che si aggiunge uno slot, sono disponibili solo due opzioni: clonare la configurazione dallo slot predefinito in produzione oppure no.

	Dopo aver creato vari slot, sarà possibile clonare la configurazione da uno slot diverso da quello in produzione:

	![Origini della configurazione][MultipleConfigurationSources]

5. Nel pannello **Slot di distribuzione** fare clic sullo slot di distribuzione per aprire un pannello per lo slot, con un set di metriche e configurazione come qualsiasi altra app Web. <strong>La stringa <i>nome dell'app Web</i>-<i>nome slot di distribuzione</i></strong> verrà visualizzata nella parte superiore del pannello per ricordare all'utente che è visualizzato lo slot di distribuzione.

	![Titolo slot di distribuzione][StagingTitle]

5. Fare clic sull'URL dell'app nel pannello dello slot. Si noti che lo slot di distribuzione dispone di un nome host specifico ed è inoltre un'app dal vivo. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Non è presente alcun contenuto dopo la creazione dello slot di distribuzione. È possibile distribuire lo slot da un'area diversa dell'archivio o da un altro archivio. È anche possibile modificare la configurazione dello slot. Usare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot di distribuzione per gli aggiornamenti dei contenuti. È ad esempio possibile [pubblicare in questo slot con git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configurazione per gli slot di distribuzione ##
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Inoltre, alcuni elementi della configurazione seguiranno il contenuto nello scambio (non specifici dello slot) mentre altri elementi della configurazione resteranno nello stesso slot dopo uno scambio (specifici dello slot). Negli elenchi seguenti sono riportati gli elementi di configurazione che verranno modificati al momento dello scambio degli slot.

**Impostazioni che vengono scambiate**:

- Impostazioni generali, quali la versione del framework, 32/64 bit, i socket Web
- Impostazioni app (possono essere configurate per adattarsi a uno slot)
- Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
- Mapping dei gestori
- Impostazioni di monitoraggio e diagnostica
- Contenuto WebJobs

**Impostazioni che non vengono scambiate**:

- Endpoint di pubblicazione
- Nomi di dominio personalizzati
- Certificati e associazioni SSL
- Impostazioni di scalabilità
- Utilità di pianificazione WebJobs

Per configurare un'impostazione app o una stringa di connessione in modo da adattarla a uno (non scambiata), accedere al pannello **Impostazioni applicazione** di uno slot specifico, quindi selezionare la casella **Impostazione slot** per gli elementi di configurazione che devono adattarsi allo slot. Si noti che contrassegnando un elemento della configurazione come specifico dello slot, si determina che non può essere scambiato tra tutti gli slot di distribuzione associati all'app Web.

![Impostazioni di slot][SlotSettings]

<a name="Swap"></a>
## Per scambiare gli slot di distribuzione ##

>[AZURE.IMPORTANT]Prima di scambiare un'app Web da uno slot di distribuzione alla produzione, accertarsi che tutte le impostazioni non specifiche dello slot siano configurate esattamente nel modo in cui si desidera nello slot di destinazione.

1. Per scambiare gli slot di distribuzione, fare clic sul pulsante **Scambia** nella barra dei comandi dell'app Web o di uno slot di distribuzione. Assicurarsi che l'origine e la destinazione dello scambio siano impostati correttamente. In genere, la destinazione dello scambio è rappresentata dallo slot di produzione.  

	![Pulsante Swap][SwapButtonBar]

3. Fare clic su **OK** per completare l'operazione. Una volta terminata l'operazione, gli slot di distribuzione sono stati scambiati.

## Configurare lo scambio automatico per l'app Web ##

Lo scambio automatico semplifica gli scenari DevOps nei quali si desidera distribuire continuamente l'app Web senza avvio a freddo e senza tempi di inattività per i clienti finali dell'app Web. Quando uno slot di distribuzione viene configurato per lo scambio automatico in produzione, ogni volta che si esegue il push dell'aggiornamento del codice in tale slot, il servizio app eseguirà automaticamente lo scambio dell'app Web in produzione dopo che è stato eseguito nello slot.

>[AZURE.IMPORTANT]Quando si abilita lo scambio automatico per uno slot, assicurarsi che la configurazione dello slot corrisponda esattamente a quella desiderata per lo slot di destinazione (in genere lo slot di produzione).

La configurazione dello scambio automatico per uno slot è semplice. Attenersi ai passaggi indicati di seguito:

1. Nel pannello **Slot di distribuzione**, selezionare uno slot non di produzione, fare clic su **Tutte le impostazioni** per il pannello di tale slot.  

	![][Autoswap1]

2. Fare clic su **Impostazioni applicazione**. Selezionare **On** per **Scambio automatico**, scegliere lo slot di destinazione desiderato in **Slot scambio automatico**, quindi fare clic su **Salva** nella barra dei comandi. Assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione desiderata per lo slot di destinazione.

	Dopo il completamento dell'operazione, nella scheda **Notifiche** verrà visualizzata la scritta verde lampeggiante **OPERAZIONE RIUSCITA**.

	![][Autoswap2]

	>[AZURE.NOTE]Per verificare lo scambio automatico per l'app Web, è possibile selezionare dapprima uno slot di destinazione non di produzione in **Slot scambio automatico** per acquisire familiarità con la funzionalità.

3. Eseguire un push del codice in tale slot di distribuzione. Lo scambio automatico verrà eseguito poco dopo e l'aggiornamento verrà riflesso nell'URL dello slot di destinazione.

<a name="Rollback"></a>
## Per eseguire il rollback di un'app di produzione dopo lo scambio ##
Se vengono identificati errori nel sito di produzione dopo lo scambio di uno slot, ripristinare i due slot allo stato precedente scambiandoli immediatamente.

<a name="Delete"></a>
## Per eliminare uno slot di distribuzione##

Nel pannello di uno slot di distribuzione, fare clic su **Elimina** nella barra dei comandi.

![Eliminare uno slot di distribuzione][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlet Azure PowerShell per gli slot di distribuzione

Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione delle app Web nel servizio app di Azure.

- Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Windows Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](../install-configure-powershell.md).  

- Per un elenco dei cmdlet disponibili per il servizio app di Azure in PowerShell, chiamare `help AzureWebsite`.

----------

### Get-AzureWebsite
Il cmdlet **Get-AzureWebsite** presenta informazioni sulle app Web di Azure per la sottoscrizione corrente, come nell'esempio seguente.

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
È possibile creare uno slot di distribuzione mediante il cmdlet **New-AzureWebsite**, specificando i nomi dell'app Web e dello slot. Indicare, inoltre, la stessa area dell'app Web per la creazione dello slot di distribuzione, come nell'esempio seguente.

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
È possibile utilizzare il cmdlet **Publish-AzureWebsiteProject** per la distribuzione dei contenuti, come nell'esempio seguente.

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
Dopo aver applicato gli aggiornamenti dei contenuti e della configurazione al nuovo slot, è possibile convalidarli passando allo slot che utilizza il cmdlet **Show-AzureWebsite**, come nell'esempio seguente.

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
Il cmdlet **Switch-AzureWebsiteSlot** può eseguire un'operazione di scambio per applicare lo slot di distribuzione aggiornato al sito di produzione, come nell'esempio seguente. L'app di produzione non sarà caratterizzata da tempi di inattività, né subirà un avvio a freddo.

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
Se uno slot di distribuzione non è più necessario, è possibile eliminarlo utilizzando il cmdlet **Remove-AzureWebsite**, come nell'esempio seguente.

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## Comandi dell'interfaccia della riga di comando di Azure per gli slot di distribuzione

L'interfaccia della riga di comando di Azure fornisce comandi multipiattaforma che è possibile usare con Azure, incluso il supporto per la gestione degli slot di distribuzione delle app Web.

- Per istruzioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure, incluse le informazioni su come collegarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli.md).

-  Per l'elenco dei comandi disponibili per il servizio app di Azure, chiamare `azure site -h`.

----------
### azure site list
Per informazioni sulle app Web nell'attuale sottoscrizione, chiamare **azure site list**, come nell'esempio seguente.

`azure site list webappslotstest`

----------
### azure site create
Per creare uno slot di distribuzione, chiamare **azure site create** e specificare il nome di un'app Web esistente e il nome dello slot da creare, come nell'esempio seguente.

`azure site create webappslotstest --slot staging`

Per abilitare il controllo del codice sorgente per il nuovo slot, utilizzare l'opzione **- git**, come nell'esempio seguente.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Per applicare lo slot di distribuzione aggiornato al sito di produzione, utilizzare il comando **azure site swap** per eseguire un'operazione di scambio, come nell'esempio seguente. L'app di produzione non sarà caratterizzata da tempi di inattività, né subirà un avvio a freddo.

`azure site swap webappslotstest`

----------
### azure site delete
Per eliminare uno slot di distribuzione non più necessario, usare il comando **azure site delete**, come nell'esempio seguente.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Passaggi successivi ##
[Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=July15_HO3-->