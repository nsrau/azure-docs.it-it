---
title: Configurare ambienti di staging per le app Web nel Servizio app di Azure | Documentazione Microsoft
description: Informazioni su come utilizzare la pubblicazione per fasi per le app Web in Azure App Service."
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 2fabf0d61ffd2f526fab49816eab36a86497a358
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

Quando si distribuiscono l'app Web, il back-end per dispositivi mobili, l'app Web su Linux e l'app per le API nel [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), è possibile eseguire questa operazione in uno slot di distribuzione distinto anziché in uno slot di produzione predefinito se il piano usato del servizio app è **Standard** o **Premium**. Gli slot di distribuzione sono in realtà app dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzata tramite la configurazione di [scambio automatico](#Auto-Swap) quando non è necessario spazio di pre-swapping convalida.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ogni piano del servizio app supporta un numero diverso di slot di distribuzione. Per conoscere il numero di slot supportati dal piano dell'app, vedere [Limiti relativi a Servizio app](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Per ridimensionare l'app passando a un livello diverso, è necessario che il livello di destinazione supporti il numero di slot già usato dall'app. Se l'app ha più di 5 slot, ad esempio, non è possibile passare un piano inferiore scegliendo **Standard**, perché il livello **Standard** supporta solo 5 slot di distribuzione.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Aggiungere uno slot di distribuzione
Per poter abilitare più slot di distribuzione, l'app deve essere in esecuzione con il piano **Standard** o **Premium**.

1. Nel [Portale di Azure](https://portal.azure.com/) aprire il pannello della [risorsa](../azure-resource-manager/resource-group-portal.md#manage-resources) dell'app.
2. Scegliere l'opzione **Slot di distribuzione**, quindi fare clic su **Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Se l'app non è già in esecuzione nel piano **Standard** o **Premium**, si riceverà un messaggio di errore che indica i piani supportati per l'abilitazione della pubblicazione di gestione temporanea. A questo punto è possibile selezionare **Aggiorna** e spostarsi alla scheda **Scalabilità** dell'app prima di continuare.
   > 
   > 
3. Nel pannello **Aggiungi uno slot** assegnare un nome allo slot e selezionare se clonare la configurazione dell'app da uno slot di distribuzione esistente. Fare clic sul segno di spunta per continuare.
   
    ![Origine della configurazione][ConfigurationSource1]
   
    La prima volta che si aggiunge uno slot, sono disponibili solo due opzioni: clonare la configurazione dello slot predefinito in produzione oppure no.
    Dopo aver creato vari slot, sarà possibile clonare la configurazione da uno slot diverso da quello in produzione:
   
    ![Origini della configurazione][MultipleConfigurationSources]
4. Nel pannello delle risorse dell'app fare clic su **Slot di distribuzione**, quindi fare clic su uno slot di distribuzione per aprire il pannello delle risorse dello slot, con un set di metriche e una configurazione come qualsiasi altra app. Il nome dello slot è indicato in alto nel pannello per ricordare all'utente che sta visualizzando lo slot di distribuzione.
   
    ![Titolo slot di distribuzione][StagingTitle]
5. Fare clic sull'URL dell'app nel pannello dello slot. Tenere presente che lo slot di distribuzione dispone di un nome host specifico ed è inoltre un'app attiva. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Non è presente alcun contenuto dopo la creazione dello slot di distribuzione. È possibile distribuire lo slot da un'area diversa dell'archivio o da un altro archivio. È anche possibile modificare la configurazione dello slot. Usare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot di distribuzione per gli aggiornamenti dei contenuti.  È ad esempio possibile [pubblicare in questo slot con git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Impostazioni incluse nello scambio
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Inoltre, alcuni elementi della configurazione seguiranno il contenuto nello scambio (non specifici dello slot) mentre altri elementi della configurazione resteranno nello stesso slot dopo uno scambio (specifici dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

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

Per configurare un'impostazione app o una stringa di connessione in modo da adattarla a uno (non scambiata), accedere al pannello **Impostazioni applicazione** di uno slot specifico, quindi selezionare la casella **Impostazione slot** per gli elementi di configurazione che devono adattarsi allo slot. Contrassegnando un elemento della configurazione come specifico dello slot si stabilisce che non può essere scambiato tra tutti gli slot di distribuzione associati all'app.

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

> [!NOTE]
> Lo scambio con anteprima non è supportato nelle applicazioni Web in Linux.

Quando si usa l'opzione **Scambio con anteprima** (vedere [Swap degli slot di distribuzione](#Swap)), il servizio app effettua le seguenti operazioni:

- Mantiene invariato lo slot di destinazione in modo da evitare un impatto sul carico di lavoro esistente nello slot, ad esempio di produzione.
- Applica gli elementi di configurazione dello slot di destinazione allo slot di origine, incluse le impostazioni delle app e le stringhe di connessione specifiche dello slot.
- Riavvia i processi di lavoro nello slot di origine usando questi elementi di configurazione menzionati.
- Al completamento dello scambio: lo slot di origine pre-riscaldato viene spostato nello slot di destinazione. Lo slot di destinazione viene spostato nello slot di origine come in uno scambio manuale.
- In caso di annullamento dello scambio: gli elementi di configurazione dello slot di origine vengono riapplicati allo slot di origine.

È possibile visualizzare in anteprima il funzionamento esatto dell'app con la configurazione dello slot di destinazione. Al termine della convalida, completare lo scambio in un passaggio separato. Questo passaggio offre anche il vantaggio di evitare tempi di inattività per i client perché lo slot di origine è già stato riscaldato con la configurazione desiderata.  

Degli esempi per i cmdlet PowerShell di Azure disponibili per lo swap multifase sono inclusi nei cmdlet PowerShell di Azure per la sezione relativa agli slot di distribuzione.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurare lo scambio automatico
Lo scambio automatico semplifica gli scenari DevOps nei quali si vuole distribuire continuamente l'app senza avvio a freddo e senza tempi di inattività per i clienti finali dell'app. Quando uno slot di distribuzione viene configurato per lo scambio automatico in produzione, ogni volta che si esegue il push dell'aggiornamento del codice in tale slot, il servizio app eseguirà automaticamente lo scambio dell'app in produzione dopo che è stato eseguito il riscaldamento nello slot.

> [!IMPORTANT]
> AZURE.IMPORTANTE Quando si abilita lo scambio automatico per uno slot, assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione dello slot desiderata per lo slot di destinazione (in genere lo slot di produzione).
> 
> 

> [!NOTE]
> Lo scambio automatico non è supportato nelle app Web in Linux.

La configurazione dello scambio automatico per uno slot è semplice. A tale scopo, seguire questa procedura:

1. In **Slot di distribuzione** selezionare uno slot non di produzione e scegliere **Impostazioni applicazione** nel pannello delle risorse di questo slot.  
   
    ![][Autoswap1]
2. Selezionare **On** per **Scambio automatico**, scegliere lo slot di destinazione desiderato in **Slot scambio automatico**, quindi fare clic su **Salva** nella barra dei comandi. Assicurarsi che la configurazione dello slot corrisponda esattamente alla configurazione desiderata per lo slot di destinazione.
   
    Al termine dell'operazione, nella scheda **Notifiche** verrà visualizzato il messaggio lampeggiante in verde **OPERAZIONE RIUSCITA**.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Per verificare lo scambio automatico per l'app, è possibile selezionare prima uno slot di destinazione non di produzione in **Scambia automaticamente slot** per acquisire familiarità con la funzionalità.  
   > 
   > 
3. Eseguire un push del codice in tale slot di distribuzione. Lo scambio automatico viene eseguito dopo un breve intervallo di tempo e l'aggiornamento si riflette nell'URL dello slot di destinazione.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Eseguire il rollback di un'app di produzione dopo lo scambio
Se vengono identificati errori nel sito di produzione dopo lo scambio di uno slot, ripristinare i due slot allo stato precedente scambiandoli immediatamente.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Riscaldamento personalizzato prima dello scambio
Alcune app potrebbero richiedere azioni di riscaldamento personalizzate. L'elemento di configurazione `applicationInitialization` in web.config consente di specificare le azioni di inizializzazione personalizzate da eseguire prima che venga ricevuta una richiesta. L'operazione di scambio attende il completamento del riscaldamento personalizzato. Di seguito è riportato un frammento web.config di esempio.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

## <a name="monitor-swap-progress"></a>Monitorare lo stato di avanzamento dello scambio

Il completamento dell'operazione di scambio talvolta richiede tempo, ad esempio quando l'app scambiata presenta un lungo tempo di riscaldamento. È possibile visualizzare altre informazioni sulle operazioni di scambio nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) nel [portale di Azure](https://portal.azure.com).

Nella pagina dell'app nel portale selezionare **Log attività** nel riquadro di spostamento a sinistra.

Un'operazione di scambio è riportata nella query di log come `Slotsswap`. È possibile espandere la voce e selezionare una sotto-operazione o un errore per visualizzare i dettagli.

![Log attività per lo scambio di slot](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Eliminare uno slot di distribuzione
Nel pannello di uno slot di distribuzione aprire il pannello dello slot di distribuzione, fare clic su **Panoramica** (la pagina predefinita), quindi fare clic su **Elimina** nella barra dei comandi.  

![Per eliminare uno slot di distribuzione][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatizzare le operazioni con Azure PowerShell

Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione in Servizio app di Azure.

* Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Creare un'app Web
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Creare uno slot di distribuzione
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Avviare uno scambio con anteprima (swap multifase) e applicare la configurazione dello slot di destinazione allo slot di origine
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Annullare uno scambio (scambio con anteprima) in sospeso e ripristinare la configurazione dello slot di origine
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitore gli eventi di scambio nel log attività
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Eliminare slot di distribuzione
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatizzare le operazioni con l'interfaccia della riga di comando di Azure

Per i comandi dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Passaggi successivi
[Blocco dell'accesso Web agli slot di distribuzione non di produzione nell'app Web del servizio app di Azure](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Introduzione al servizio app in Linux](../app-service/containers/app-service-linux-intro.md)  
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

