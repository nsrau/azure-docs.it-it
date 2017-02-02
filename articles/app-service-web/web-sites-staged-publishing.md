---
title: Configurare ambienti di staging per le app Web nel Servizio app di Azure | Documentazione Microsoft
description: Informazioni su come utilizzare la pubblicazione per fasi per le app Web in Azure App Service.&quot;
services: app-service
documentationcenter: 
author: cephalin
writer: cephalin
manager: wpickett
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: b7890150da382e4ef08323654c076ff8236e3e79


---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

Quando si distribuisce l'app Web, il back-end per dispositivi mobili e l'App per le API al [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), è possibile eseguire la distribuzione in uno slot di distribuzione distinto, invece che in quello predefinito, se la modalità del piano di servizio app usata è **Standard** o **Premium**. Gli slot di distribuzione sono in realtà app dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzata tramite la configurazione di [scambio automatico](#configure-auto-swap-for-your-web-app) quando non è necessario spazio di pre-swapping convalida.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ciascuna modalità di piano App Service supporta un numero diverso di slot di distribuzione. Per conoscere il numero di slot supportati dalla modalità della propria app, vedere [Tariffe del servizio app](https://azure.microsoft.com/pricing/details/app-service/).

* Se l'app dispone di più slot, non è possibile cambiare la modalità.
* Gli slot non di produzione non sono scalabili.
* La gestione delle risorse collegate non è supportata per gli slot non di produzione. Solo nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) , è possibile evitare questo impatto potenziale su uno slot di produzione spostando temporaneamente lo slot non di produzione in una modalità di piano servizio app differente. Si noti che lo slot non di produzione deve ancora una volta condividere la stessa modalità dello slot di produzione per potere eseguire lo scambio tra i due slot.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Aggiungere uno slot di distribuzione
Per abilitare più slot di distribuzione, l'app deve essere in esecuzione in modalità **Standard** o **Premium**.

1. Nel [Portale di Azure](https://portal.azure.com/) aprire il pannello della [risorsa](../azure-resource-manager/resource-group-portal.md#manage-resources) dell'app.
2. Scegliere l'opzione **Slot di distribuzione**, quindi fare clic su **Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Se l'app non è già in modalità **Standard** o **Premium**, si riceverà un messaggio di errore che indica le modalità supportate per l'abilitazione della pubblicazione per fasi. A questo punto è possibile selezionare **Aggiorna** e spostarsi alla scheda **Scalabilità** dell'app prima di continuare.
   > 
   > 
3. Nel pannello **Aggiungi uno slot** assegnare un nome allo slot e selezionare se clonare la configurazione dell'app da uno slot di distribuzione esistente. Fare clic sul segno di spunta per continuare.
   
    ![Origine della configurazione][ConfigurationSource1]
   
    La prima volta che si aggiunge uno slot, sono disponibili solo due opzioni: clonare la configurazione dallo slot predefinito in produzione oppure no.
    Dopo aver creato vari slot, sarà possibile clonare la configurazione da uno slot diverso da quello in produzione:
   
    ![Origini della configurazione][MultipleConfigurationSources]
4. Nel pannello delle risorse dell'app fare clic su **Slot di distribuzione**, quindi su uno slot di distribuzione per aprire il pannello delle risorse dello slot, con un set di metriche e una configurazione come qualsiasi altra app. Il nome dello slot è indicato in alto nel pannello per ricordare all'utente che sta visualizzando lo slot di distribuzione.
   
    ![Titolo slot di distribuzione][StagingTitle]
5. Fare clic sull'URL dell'app nel pannello dello slot. Tenere presente che lo slot di distribuzione dispone di un nome host specifico ed è inoltre un'app attiva. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Non è presente alcun contenuto dopo la creazione dello slot di distribuzione. È possibile distribuire lo slot da un'area diversa dell'archivio o da un altro archivio. È anche possibile modificare la configurazione dello slot. Usare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot di distribuzione per gli aggiornamenti dei contenuti.  È ad esempio possibile [pubblicare in questo slot con git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Configurazione per gli slot di distribuzione ##
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

Per configurare un'impostazione app o una stringa di connessione in modo da adattarla a uno (non scambiata), accedere al pannello **Impostazioni applicazione** di uno slot specifico, quindi selezionare la casella **Impostazione slot** per gli elementi di configurazione che devono adattarsi allo slot. Si noti che contrassegnando un elemento della configurazione come specifico dello slot, si determina che non può essere scambiato tra tutti gli slot di distribuzione associati all'app.

![Impostazioni di slot][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione 
È possibile scambiare gli slot di distribuzione nella visualizzazione **Panoramica** o **Slot di distribuzione** del pannello delle risorse dell'app.

> [!IMPORTANT]
> Prima di scambiare un'app da uno slot di distribuzione alla produzione, accertarsi che tutte le impostazioni non specifiche dello slot siano configurate esattamente nel modo desiderato nello slot di destinazione.
> 
> 

1. Per scambiare gli slot di distribuzione, fare clic sul pulsante **Scambia** nella barra dei comandi dell'app o di uno slot di distribuzione.
   
    ![Pulsante Swap][SwapButtonBar]

2. Assicurarsi che l'origine e la destinazione dello scambio siano impostati correttamente. In genere, la destinazione dello scambio è lo slot di produzione. Fare clic su **OK** per completare l'operazione. Una volta terminata l'operazione, gli slot di distribuzione sono stati scambiati.

    ![Scambio completo](./media/web-sites-staged-publishing/SwapImmediately.png)

    Per il tipo **Scambio con anteprima** vedere [Scambio con anteprima (swap multifase)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Scambio con anteprima (swap multifase)

Lo scambio con anteprima o swap multifase semplifica la convalida degli elementi di configurazione di uno slot specifico, ad esempio le stringhe di connessione.
Per i carichi di lavoro mission-critical, è possibile convalidare il comportamento previsto dell'app quando viene applicata la configurazione dello slot di produzione ed è necessario eseguire questa convalida *prima* dello scambio dell'app in produzione. Lo scambio con anteprima è ciò che serve.

Quando si usa l'opzione **Scambio con anteprima** (vedere [Swap degli slot di distribuzione](#Swap)), il servizio app effettua le seguenti operazioni:

- Mantiene invariato lo slot di destinazione in modo che non interessi il carico di lavoro esistente in questo slot, ad esempio, la produzione.
- Applica gli elementi di configurazione dello slot di destinazione allo slot di origine, incluse le impostazioni delle app e le stringhe di connessione specifiche dello slot.
- Riavvia i processi di lavoro nello slot di origine usando questi elementi di configurazione menzionati.
- Al completamento dello scambio: lo slot di origine pre-riscaldato viene spostato nello slot di destinazione. Lo slot di destinazione viene spostato nello slot di origine come in uno scambio manuale.
- In caso di annullamento dello scambio: gli elementi di configurazione dello slot di origine vengono riapplicati allo slot di origine.

È possibile visualizzare in anteprima il funzionamento esatto dell'app con la configurazione dello slot di destinazione. Al termine della convalida completare lo scambio in un passaggio separato. Questo passaggio ha anche il vantaggio che lo slot di origine è già riscaldato con la configurazione desiderata e i client non subiranno tempi di inattività.  

Degli esempi per i cmdlet PowerShell di Azure disponibili per lo swap multifase sono inclusi nei cmdlet PowerShell di Azure per la sezione relativa agli slot di distribuzione.

## <a name="configure-auto-swap"></a>Configurare lo scambio automatico
Lo scambio automatico semplifica gli scenari DevOps nei quali si vuole distribuire continuamente l'app senza avvio a freddo e senza tempi di inattività per i clienti finali dell'app. Quando uno slot di distribuzione viene configurato per lo scambio automatico in produzione, ogni volta che si esegue il push dell'aggiornamento del codice in tale slot, il servizio app eseguirà automaticamente lo scambio dell'app in produzione dopo che è stato eseguito il riscaldamento nello slot.

> [!IMPORTANT]
> AZURE.IMPORTANTE Quando si abilita lo scambio automatico per uno slot, assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione dello slot desiderata per lo slot di destinazione (in genere lo slot di produzione).
> 
> 

La configurazione dello scambio automatico per uno slot è semplice. Attenersi ai passaggi indicati di seguito:

1. In **Slot di distribuzione** selezionare uno slot non di produzione e scegliere **Impostazioni applicazione** nel pannello delle risorse di questo slot.  
   
    ![][Autoswap1]
2. Selezionare **On** per **Scambio automatico**, scegliere lo slot di destinazione desiderato in **Slot scambio automatico**, quindi fare clic su **Salva** nella barra dei comandi. Assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione desiderata per lo slot di destinazione.
   
    Dopo il completamento dell'operazione, nella scheda **Notifiche** verrà visualizzata la scritta verde lampeggiante **OPERAZIONE RIUSCITA**.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Per verificare lo scambio automatico per l'app, è possibile selezionare prima uno slot di destinazione non di produzione in **Scambia automaticamente slot** per acquisire familiarità con la funzionalità.  
   > 
   > 
3. Eseguire un push del codice in tale slot di distribuzione. Lo scambio automatico verrà eseguito poco dopo e l'aggiornamento verrà riflesso nell'URL dello slot di destinazione.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Per eseguire il rollback di un'app di produzione dopo lo scambio
Se vengono identificati errori nel sito di produzione dopo lo scambio di uno slot, ripristinare i due slot allo stato precedente scambiandoli immediatamente.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Riscaldamento personalizzato prima dello scambio
Alcune app potrebbero richiedere azioni di riscaldamento personalizzate. L'elemento di configurazione `applicationInitialization` in web.config consente di specificare le azioni di inizializzazione personalizzate da eseguire prima che venga ricevuta una richiesta. L'operazione di scambio attenderà il completamento del riscaldamento personalizzato. Di seguito è riportato un frammento web.config di esempio.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Per eliminare uno slot di distribuzione##
Nel pannello di uno slot di distribuzione aprire il pannello dello slot di distribuzione, fare clic su **Panoramica** (la pagina predefinita), quindi fare clic su **Elimina** nella barra dei comandi.  

![Per eliminare uno slot di distribuzione][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Cmdlet Azure PowerShell per gli slot di distribuzione
Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione in Servizio app di Azure.

* Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](/powershell/azureps-cmdlets-docs).  

- - -
### <a name="create-a-web-app"></a>Creare un'app Web
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Creare uno slot di distribuzione
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-review-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Avviare uno scambio con anteprima (swap multifase) e applicare la configurazione dello slot di destinazione allo slot di origine
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Annullare uno scambio (scambio con anteprima) in sospeso e ripristinare la configurazione dello slot di origine
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Eliminare slot di distribuzione
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Comandi dell'interfaccia della riga di comando di Azure per gli slot di distribuzione
L'interfaccia della riga di comando di Azure fornisce comandi multipiattaforma che è possibile usare con Azure, incluso il supporto per la gestione degli slot di distribuzione del servizio app.

* Per istruzioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure, incluse le informazioni su come collegarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
* Per l'elenco dei comandi disponibili per il servizio app di Azure, chiamare `azure site -h`.

> [!NOTE] 
> Per i comandi dell'[interfaccia della riga di comando di Azure 2.0 (anteprima)](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az appservice web deployment slot](/cli/azure/appservice/web/deployment/slot).

- - -
### <a name="azure-site-list"></a>azure site list
Per informazioni sulle app nell'attuale sottoscrizione, chiamare **azure site list**, come nell'esempio seguente.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>azure site create
Per creare uno slot di distribuzione, chiamare **azure site create** e specificare il nome di un'app esistente e il nome dello slot da creare, come nell'esempio seguente.

`azure site create webappslotstest --slot staging`

Per abilitare il controllo del codice sorgente per il nuovo slot, utilizzare l'opzione **- git** , come nell'esempio seguente.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>azure site swap
Per applicare lo slot di distribuzione aggiornato al sito di produzione, utilizzare il comando **azure site swap** per eseguire un'operazione di scambio, come nell'esempio seguente. L'app di produzione non sarà caratterizzata da tempi di inattività, né subirà un avvio a freddo.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>azure site delete
Per eliminare uno slot di distribuzione non più necessario, usare il comando **azure site delete** , come nell'esempio seguente.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Verificare il funzionamento di un'app Web. [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) immediatamente e creare un'app iniziale temporanea, senza necessità di fornire una carta di credito e senza impegni.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
[Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

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
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png




<!--HONumber=Dec16_HO3-->


