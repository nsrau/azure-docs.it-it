---
title: Configurare ambienti di gestione temporanea per le app Web in Azure App Service
description: Informazioni su come utilizzare la pubblicazione per fasi per le app Web in Azure App Service.
;": ''
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: wpickett
editor: mollybos

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2016
ms.author: cephalin

---
# Configurare ambienti di gestione temporanea per le app Web in Azure App Service
<a name="Overview"></a>

Quando si distribuisce l'app Web ad [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), è possibile eseguire la distribuzione a uno slot di distribuzione distinto, invece di quello predefinito se la modalità dei piani App Service utilizzata è **Standard** o **Premium**. Gli slot di distribuzione sono in realtà app Web dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app Web e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app Web in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app Web in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app Web. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzata tramite la configurazione di [scambio automatico](#configure-auto-swap-for-your-web-app) quando non è necessario spazio di pre-swapping convalida.
* Dopo uno scambio, lo slot con l'app Web gestita temporaneamente includerà l'app Web di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ciascuna modalità di piano App Service supporta un numero diverso di slot di distribuzione. Per scoprire il numero di slot supportati dalla modalità della propria app Web, vedere [Tariffe di App Service](/pricing/details/app-service/).

* Se l'app Web dispone di più slot, non è possibile cambiare la modalità.
* Gli slot non di produzione non sono scalabili.
* La gestione delle risorse collegate non è supportata per gli slot non di produzione. Solo nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), è possibile evitare questo impatto potenziale su uno slot di produzione spostando temporaneamente lo slot non di produzione in una modalità di piano servizio app differente. Si noti che lo slot non di produzione deve ancora una volta condividere la stessa modalità dello slot di produzione per potere eseguire lo scambio tra i due slot.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>

## Aggiungere uno slot di distribuzione a un'app Web
Per abilitare più slot di distribuzione, l'app Web deve essere in esecuzione in modalità **Standard** o **Premium**.

1. Nel [portale di Azure](https://portal.azure.com/), aprire il pannello dell'app Web.
2. Fare clic su **Impostazioni** e quindi su **Slot di distribuzione**. Quindi, nel pannello **Slot di distribuzione**, fare clic su **Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Se l'app Web non è già in modalità **Standard** o **Premium**, si riceverà un messaggio di errore che indica le modalità supportate per l'abilitazione della pubblicazione per fasi. A questo punto, è possibile selezionare **Aggiorna** e spostarsi alla scheda **Scala** dell'app Web prima di continuare.
   > 
   > 
3. Nel pannello **Aggiungi uno slot**, dare un nome allo slot e selezionare se clonare la configurazione dell'app Web da uno slot di distribuzione esistente. Fare clic sul segno di spunta per continuare.
   
    ![Origine della configurazione][ConfigurationSource1]
   
    La prima volta che si aggiunge uno slot, sono disponibili solo due opzioni: clonare la configurazione dallo slot predefinito in produzione oppure no.
   
    Dopo aver creato vari slot, sarà possibile clonare la configurazione da uno slot diverso da quello in produzione:
   
    ![Origini della configurazione][MultipleConfigurationSources]
4. Nel pannello **Slot di distribuzione** fare clic sullo slot di distribuzione per aprire un pannello per lo slot, con un set di metriche e configurazione come qualsiasi altra app Web. La stringa **your-web-app-name-deployment-slot-name** verrà visualizzata nella parte superiore del pannello per ricordare all'utente che è visualizzato lo slot di distribuzione.
   
    ![Titolo slot di distribuzione][StagingTitle]
5. Fare clic sull'URL dell'app nel pannello dello slot. Tenere presente che lo slot di distribuzione dispone di un nome host specifico ed è inoltre un'app attiva. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Non è presente alcun contenuto dopo la creazione dello slot di distribuzione. È possibile distribuire lo slot da un'area diversa dell'archivio o da un altro archivio. È anche possibile modificare la configurazione dello slot. Usare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot di distribuzione per gli aggiornamenti dei contenuti. È ad esempio possibile [pubblicare in questo slot con git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## Configurazione per gli slot di distribuzione ##
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Inoltre, alcuni elementi della configurazione seguiranno il contenuto nello scambio (non specifici dello slot) mentre altri elementi della configurazione resteranno nello stesso slot dopo uno scambio (specifici dello slot). Negli elenchi seguenti sono riportati gli elementi di configurazione che verranno modificati al momento dello scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, quali la versione del framework, 32/64 bit, i socket Web
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Impostazioni di monitoraggio e diagnostica
* Contenuto WebJobs

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati e associazioni SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs

Per configurare un'impostazione app o una stringa di connessione in modo da adattarla a uno (non scambiata), accedere al pannello **Impostazioni applicazione** di uno slot specifico, quindi selezionare la casella **Impostazione slot** per gli elementi di configurazione che devono adattarsi allo slot. Si noti che contrassegnando un elemento della configurazione come specifico dello slot, si determina che non può essere scambiato tra tutti gli slot di distribuzione associati all'app Web.

![Impostazioni di slot][SlotSettings]

<a name="Swap"></a>

## Per scambiare gli slot di distribuzione
> [!IMPORTANT]
> Prima di scambiare un'app Web da uno slot di distribuzione alla produzione, accertarsi che tutte le impostazioni non specifiche dello slot siano configurate esattamente nel modo in cui si desidera nello slot di destinazione.
> 
> 

1. Per scambiare gli slot di distribuzione, fare clic sul pulsante **Scambia** nella barra dei comandi dell'app Web o di uno slot di distribuzione. Assicurarsi che l'origine e la destinazione dello scambio siano impostati correttamente. In genere, la destinazione dello scambio è rappresentata dallo slot di produzione.
   
    ![Pulsante Swap][SwapButtonBar]
2. Fare clic su **OK** per completare l'operazione. Una volta terminata l'operazione, gli slot di distribuzione sono stati scambiati.

## Configurare lo scambio automatico per l'app Web
Lo scambio automatico semplifica gli scenari DevOps nei quali si desidera distribuire continuamente l'app Web senza avvio a freddo e senza tempi di inattività per i clienti finali dell'app Web. Quando uno slot di distribuzione viene configurato per lo scambio automatico in produzione, ogni volta che si esegue il push dell'aggiornamento del codice in tale slot, App Service eseguirà automaticamente lo scambio dell'app Web in produzione dopo che è stato eseguito nello slot.

> [!IMPORTANT]
> AZURE.IMPORTANTE Quando si abilita lo scambio automatico per uno slot, assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione dello slot desiderata per lo slot di destinazione (in genere lo slot di produzione).
> 
> 

La configurazione dello scambio automatico per uno slot è semplice. Attenersi ai passaggi indicati di seguito:

1. Nel pannello **Slot di distribuzione**, selezionare uno slot non di produzione, fare clic su **Tutte le impostazioni** per il pannello di tale slot.
   
    ![][Autoswap1]
2. Fare clic su **Impostazioni applicazione**. Selezionare **On** per **Scambio automatico**, scegliere lo slot di destinazione desiderato in **Slot scambio automatico**, quindi fare clic su **Salva** nella barra dei comandi. Assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione desiderata per lo slot di destinazione.
   
    Dopo il completamento dell'operazione, nella scheda **Notifiche** verrà visualizzata la scritta verde lampeggiante **OPERAZIONE RIUSCITA**.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Per verificare lo scambio automatico per l'app Web, è possibile selezionare dapprima uno slot di destinazione non di produzione in **Slot scambio automatico** per acquisire familiarità con la funzionalità.
   > 
   > 
3. Eseguire un push del codice in tale slot di distribuzione. Lo scambio automatico verrà eseguito poco dopo e l'aggiornamento verrà riflesso nell'URL dello slot di destinazione.

<a name="Multi-Phase"></a>

## Utilizzare spazio di swap in più fasi per l'app web
Lo swap multifase è disponibile per semplificare la convalida nel contesto di elementi di configurazione progettati per mantenere uno slot come le stringhe di connessione. In questi casi può essere utile applicare tali elementi di configurazione dalla destinazione di scambio all'origine di scambio e convalidare prima che lo scambio diventi effettivo. Una volta che gli elementi di configurazione della destinazione di swap vengono applicati all'origine di swap le azioni disponibili sono lo swap di completamento o il ripristino della configurazione originale per l'origine di swap che ha anche l'effetto di annullare lo swap. Degli esempi per i cmdlet PowerShell di Azure disponibili per lo swap multifase sono inclusi nei cmdlet PowerShell di Azure per la sezione relativa agli slot di distribuzione.

<a name="Rollback"></a>

## Per eseguire il rollback di un'app di produzione dopo lo scambio
Se vengono identificati errori nel sito di produzione dopo lo scambio di uno slot, ripristinare i due slot allo stato precedente scambiandoli immediatamente.

<a name="Warm-up"></a>

## Riscaldamento personalizzato prima dello scambio
Alcune app potrebbero richiedere azioni di riscaldamento personalizzate. L'elemento di configurazione applicationInitialization in web.config consente di specificare le azioni di inizializzazione personalizzate da eseguire prima che venga ricevuta una richiesta. L'operazione di scambio attenderà il completamento del riscaldamento personalizzato. Di seguito è riportato un frammento web.config di esempio.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## Per eliminare uno slot di distribuzione
Nel pannello di uno slot di distribuzione, fare clic su **Elimina** nella barra dei comandi.

![Per eliminare uno slot di distribuzione][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## Cmdlet Azure PowerShell per gli slot di distribuzione
Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione delle app Web in Azure App Service.

* Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](../powershell-install-configure.md).

- - -
### Crea app Web
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### Creare uno slot di distribuzione per un'app web
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### Avviare uno swap multifase e applicare la configurazione dello slot di destinazione allo slot di origine
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### Ripristinare la prima fase di swap multifase e ripristinare la configurazione di slot di origine
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### Swap degli slot di distribuzione
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### Eliminare slot di distribuzione
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## Comandi dell'interfaccia della riga di comando di Azure per gli slot di distribuzione
L'interfaccia della riga di comando di Azure fornisce comandi multipiattaforma che è possibile usare con Azure, incluso il supporto per la gestione degli slot di distribuzione delle app Web.

* Per istruzioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure, incluse le informazioni su come collegarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
* Per l'elenco dei comandi disponibili per il servizio app di Azure, chiamare `azure site -h`.

- - -
### azure site list
Per informazioni sulle app Web nell'attuale sottoscrizione, chiamare **azure site list**, come nell'esempio seguente.

`azure site list webappslotstest`

- - -
### azure site create
Per creare uno slot di distribuzione, chiamare **azure site create** e specificare il nome di un'app Web esistente e il nome dello slot da creare, come nell'esempio seguente.

`azure site create webappslotstest --slot staging`

Per abilitare il controllo del codice sorgente per il nuovo slot, utilizzare l'opzione **- git**, come nell'esempio seguente.

`azure site create --git webappslotstest --slot staging`

- - -
### azure site swap
Per applicare lo slot di distribuzione aggiornato al sito di produzione, utilizzare il comando **azure site swap** per eseguire un'operazione di scambio, come nell'esempio seguente. L'app di produzione non sarà caratterizzata da tempi di inattività, né subirà un avvio a freddo.

`azure site swap webappslotstest`

- - -
### azure site delete
Per eliminare uno slot di distribuzione non più necessario, usare il comando **azure site delete**, come nell'esempio seguente.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## Passaggi successivi
[Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

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


<!---HONumber=AcomDC_0803_2016-->