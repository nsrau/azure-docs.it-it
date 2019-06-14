---
title: Configurare ambienti di staging per le app Web nel Servizio app di Azure | Documentazione Microsoft
description: Informazioni su come utilizzare la pubblicazione per fasi per le app Web in Azure App Service."
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 1e09eec89c683d36df49110227488a6413ed371c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65955826"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

> [!NOTE]
> Questa guida pratica illustra come gestire gli slot usando una nuova pagina di gestione di anteprima. I clienti abituati a usare la pagina di gestione degli slot esistente possono continuare a farlo. 
>

Quando si distribuiscono l'app Web, l'app Web su Linux, il back-end per dispositivi mobili e l'app per le API nel [Servizio app](https://go.microsoft.com/fwlink/?LinkId=529714), è possibile eseguire questa operazione in uno slot di distribuzione distinto anziché in uno slot di produzione predefinito se il piano usato del Servizio app è **Standard**, **Premium** o **Isolato**. Gli slot di distribuzione sono in realtà app dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot non di produzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzato tramite la configurazione dello [scambio automatico](#Auto-Swap) quando non è necessaria la convalida preliminare.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ogni piano del servizio app supporta un numero diverso di slot di distribuzione. Per conoscere il numero di slot supportati dal piano dell'app, vedere [Limiti relativi a Servizio app](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Per ridimensionare l'app passando a un livello diverso, è necessario che il livello di destinazione supporti il numero di slot già usato dall'app. Se l'app ha più di cinque slot, ad esempio, non è possibile passare a un piano inferiore scegliendo **Standard**, perché il livello **Standard** supporta solo cinque slot di distribuzione.

<a name="Add"></a>

## <a name="add-slot"></a>Aggiungere slot
Per poter abilitare più slot di distribuzione, l'app deve essere in esecuzione con il piano **Standard**, **Premium** o **Isolato**.

1. Nel [portale di Azure](https://portal.azure.com/) aprire la [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) dell'app.

2. Nel riquadro di spostamento a sinistra, scegliere l'opzione **Slot di distribuzione (anteprima)** e quindi fare clic su **Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se l'app non è già in esecuzione nel piano **Standard**, **Premium** o **Isolato**, si riceverà un messaggio di errore che indica i piani supportati per l'abilitazione della pubblicazione di gestione temporanea. A questo punto è possibile selezionare **Aggiorna** e spostarsi alla scheda **Scalabilità** dell'app prima di continuare.
   > 

3. Nella finestra di dialogo **Aggiungi uno slot** assegnare un nome allo slot e selezionare se clonare la configurazione dell'app da uno slot di distribuzione esistente. Fare clic su **Aggiungi** per continuare.
   
    ![Origine della configurazione](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    È possibile clonare la configurazione da uno slot esistente. Le impostazioni che possono essere clonate includono le impostazioni dell'app, le stringhe di connessione, le versioni di framework del linguaggio, i Web Socket, la versione HTTP e il numero di bit della piattaforma.

4. Dopo aver aggiunto lo slot, fare clic su **Chiudi** per chiudere la finestra di dialogo. Il nuovo slot è ora visualizzato nella pagina **Slot di distribuzione (anteprima)** . Per impostazione predefinita, l'opzione **% traffico** è impostata su 0 per il nuovo slot, con tutto il traffico dei clienti instradato verso lo slot di produzione.

5. Fare clic sul nuovo slot di distribuzione per aprire la pagina delle risorse di tale slot.
   
    ![Titolo slot di distribuzione](./media/web-sites-staged-publishing/StagingTitle.png)

    Lo slot di staging ha una pagina di gestione come qualsiasi altra app del servizio app. È possibile modificare la configurazione dello slot. Il nome dello slot è indicato all'inizio della pagina per ricordare all'utente che sta visualizzando lo slot di distribuzione.

6. Fare clic sull'URL dell'app nella pagina delle risorse dello slot. Lo slot di distribuzione ha un nome host specifico ed è inoltre un'app attiva. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md).

Il nuovo slot di distribuzione non ha contenuto, anche se si clonano le impostazioni da un altro slot. È ad esempio possibile [pubblicare in questo slot con git](app-service-deploy-local-git.md). È possibile distribuire lo slot da un ramo diverso del repository o da un repository diverso. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Impostazioni incluse nello scambio
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Inoltre, alcuni elementi della configurazione seguono il contenuto nello scambio (non specifici dello slot) mentre altri elementi della configurazione restano nello stesso slot dopo uno scambio (specifici dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, quali la versione del framework, 32/64 bit, i socket Web
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Impostazioni di monitoraggio e diagnostica
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Integrazione rete virtuale *
* Endpoint di servizio *
* Rete CDN di Azure *

Funzionalità contrassegnate con un * è pianificata per essere reso permanente nello slot. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati privati e associazioni SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Le impostazioni del protocollo (HTTP**S**, versione di TLS, certificati client)
* Impostazioni di log di diagnostica
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Per configurare un'impostazione app o una stringa di connessione in modo che rimanga associata a uno slot specifico (non inclusa nello scambio), passare alla pagina **Impostazioni applicazione** di uno slot specifico e quindi selezionare la casella **Impostazione slot** per gli elementi di configurazione che devono rimanere associati allo slot. Contrassegnando un elemento di configurazione come specifico dello slot si indica al Servizio app che non può essere incluso nello scambio. 

![Impostazione slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Scambiare due slot 
È possibile scambiare gli slot di distribuzione nella pagina **Slot di distribuzione (anteprima)** dell'app. 

È anche possibile scambiare gli slot dalle pagine **Panoramica** e **Slot di distribuzione**, ma attualmente si ottiene l'esperienza precedente. Questa guida mostra come usare la nuova interfaccia utente nella pagina **Slot di distribuzione (anteprima)** .

> [!IMPORTANT]
> Prima di scambiare un'app da uno slot di distribuzione alla produzione, accertarsi che tutte le impostazioni siano configurate esattamente nel modo desiderato nello slot di destinazione.
> 
> 

Per scambiare gli slot di distribuzione, seguire questa procedura:

1. Passare alla pagina **Slot di distribuzione (anteprima)** e fare clic su **Scambia**.
   
    ![Pulsante Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

    La finestra di dialogo **Scambia** mostra le impostazioni negli slot di origine e di destinazione selezionati che verranno modificati.

2. Selezionare gli slot desiderati in **Origine** e **Destinazione**. In genere, la destinazione è lo slot di produzione. Inoltre, fare clic sulle schede **Modifiche dell'origine** e **Modifiche della destinazione** e verificare che le modifiche della configurazione siano quelle previste. Al termine, è possibile effettuare immediatamente lo scambio degli slot facendo clic su **Scambia**.

    ![Scambio completo](./media/web-sites-staged-publishing/SwapImmediately.png)

    Per verificare il funzionamento dello slot di destinazione con le nuove impostazioni prima di eseguire effettivamente lo scambio, non fare clic su **Scambia**, ma seguire le istruzioni in [Scambio con anteprima](#Multi-Phase).

3. Al termine, chiudere la finestra di dialogo facendo clic su **Chiudi**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Scambio con anteprima (swap multifase)

> [!NOTE]
> Lo scambio con anteprima non è supportato nelle app Web in Linux.

Prima dello scambio in produzione come slot di destinazione, verificare che l'app funzioni con le impostazioni diverse prima che si verifichi lo scambio. Lo slot di origine viene anche riscaldato prima del completamento dello scambio, operazione consigliabile anche per le applicazioni mission-critical.

Quando si esegue uno scambio con anteprima, il Servizio app esegue le operazioni seguenti quando si avvia lo scambio:

- Mantiene invariato lo slot di destinazione in modo da evitare un impatto sul carico di lavoro esistente nello slot, ad esempio di produzione.
- Applica gli elementi di configurazione dello slot di destinazione allo slot di origine, incluse le impostazioni delle app e le stringhe di connessione specifiche dello slot.
- Riavvia i processi di lavoro nello slot di origine usando questi elementi di configurazione. È possibile esplorare lo slot di origine e visualizzare l'app in esecuzione con le modifiche di configurazione.

Se si completa lo scambio in un passaggio separato, il Servizio app sposta lo slot di origine riscaldato nello slot di destinazione e lo slot di destinazione nello slot di origine. Se si annulla lo scambio, il Servizio app riapplica gli elementi di configurazione dello slot di origine allo slot di origine.

Per eseguire lo scambio con anteprima, seguire questa procedura.

1. Seguire i passaggi descritti in [Scambiare gli slot di distribuzione](#Swap), ma selezionare **Esegui scambio con anteprima**.

    ![Scambio con anteprima](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La finestra di dialogo mostra come cambia la configurazione nello slot di origine nella fase 1 e come cambiano gli slot di origine e di destinazione nella fase 2.

2. Quando si è pronti per avviare lo scambio, fare clic su **Avvia scambio**.

    Al termine della fase 1 viene visualizzata una notifica nella finestra di dialogo. Visualizzare in anteprima lo scambio nello slot di origine passando a `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando si è pronti per completare lo scambio in sospeso, selezionare **Completa scambio** in **Azione di scambio** e fare clic su **Completa scambio**.

    Per annullare uno scambio in sospeso, selezionare invece **Annulla scambio** e fare clic su **Annulla scambio**.

4. Al termine, chiudere la finestra di dialogo facendo clic su **Chiudi**.

Per automatizzare uno scambio in più fasi, vedere Automatizzare con PowerShell.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Eseguire il rollback dello scambio
Se si verificano errori nello slot di destinazione (ad esempio, lo slot di produzione) dopo uno scambio di slot, ripristinare gli slot allo stato precedente scambiandoli immediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurare lo scambio automatico

> [!NOTE]
> Lo scambio automatico non è supportato nelle app Web in Linux.

Lo scambio automatico semplifica gli scenari DevOps nei quali si vuole distribuire l'app in modo continuativo senza avvio a freddo e senza tempi di inattività per i clienti finali dell'app. Con lo scambio automatico di uno slot in produzione ogni volta che si esegue il push delle modifiche di codice in tale slot, il Servizio app scambia automaticamente l'app in produzione dopo il riscaldamento nello slot di origine.

   > [!NOTE]
   > Prima di configurare lo scambio automatico per lo slot di produzione, valutare la possibilità di testare prima di tutto lo scambio automatico su uno slot di destinazione non di produzione.
   > 

Per configurare lo scambio automatico, seguire questa procedura:

1. Passare alla pagina delle risorse dell'app. Selezionare **Slot di distribuzione (anteprima)**  >  *\<slot di origine desiderato>*  > **Impostazioni applicazione**.
   
2. In **Scambio automatico** selezionare **Sì** e quindi selezionare lo slot di destinazione desiderato in **Slot scambio automatico**, quindi fare clic su **Salva** nella barra dei comandi. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Eseguire un push del codice allo slot di origine. Lo scambio automatico viene eseguito dopo un breve intervallo di tempo e l'aggiornamento si riflette nell'URL dello slot di destinazione.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Riscaldamento personalizzato
Quando si usa [Scambio automatico](#Auto-Swap), alcune app potrebbero richiedere azioni di riscaldamento personalizzate prima dello scambio. L'elemento di configurazione `applicationInitialization` in web.config consente di specificare le azioni di inizializzazione personalizzate da eseguire. L'operazione di scambio attende il completamento del riscaldamento personalizzato prima dello scambio con lo slot di destinazione. Di seguito è riportato un frammento web.config di esempio.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Per altre informazioni sulla personalizzazione il `applicationInitialization` elemento, vedere [più comuni errori scambio dello slot di distribuzione e come risolverli](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

È anche possibile personalizzare il comportamento di riscaldamento con una o più [impostazioni dell'app](configure-common.md) seguenti:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: percorso di cui effettuare il ping per il riscaldamento del sito. Aggiungere questa impostazione dell'app specificando un percorso personalizzato che inizi con una barra come valore. Ad esempio: `/statuscheck`. Il valore predefinito è `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: codici di risposta HTTP validi per l'operazione di riscaldamento. Aggiungere questa impostazione dell'app con un elenco di codici HTTP separati da virgole. Ad esempio: `200,202`. Se il codice di stato restituito non è presente nell'elenco, le operazioni di riscaldamento e scambio vengono arrestate. Per impostazione predefinita, sono validi tutti i codici di risposta.

## <a name="monitor-swap"></a>Monitoraggio dello scambio

Se l'operazione di scambio richiede molto tempo per il completamento, è possibile ottenere informazioni sull'operazione di scambio nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Nella pagina delle risorse dell'app nel portale selezionare **Log attività** nel riquadro di spostamento a sinistra.

Un'operazione di scambio è riportata nella query di log come `Swap Web App Slots`. È possibile espandere la voce e selezionare una sotto-operazione o un errore per visualizzare i dettagli.

## <a name="route-traffic"></a>Indirizzare il traffico

Per impostazione predefinita, tutte le richieste client all'URL di produzione dell'app (`http://<app_name>.azurewebsites.net`) vengono indirizzate allo slot di produzione. È possibile indirizzare una parte del traffico a un altro slot. Questa funzionalità è utile se occorre il feedback degli utenti per un nuovo aggiornamento, ma non si è pronti per rilasciarlo nell'ambiente di produzione.

### <a name="route-production-traffic-automatically"></a>Indirizzare automaticamente il traffico di produzione

Per indirizzare automaticamente il traffico di produzione, seguire questa procedura:

1. Passare alla pagina delle risorse dell'app e selezionare **Slot di distribuzione (anteprima)** .

2. Nella colonna **% traffico** dello slot di destinazione dell'indirizzamento, specificare una percentuale (compresa tra 0 e 100) per rappresentare la quantità di traffico totale da indirizzare. Fare clic su **Save**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

Dopo aver salvato l'impostazione, la percentuale specificata di client viene indirizzata in modo casuale allo slot non di produzione. 

Una volta indirizzato automaticamente a uno slot specifico, il client viene associato a tale slot per tutta la durata della sessione client. Nel browser client è possibile visualizzare lo slot a cui è associata la sessione esaminando il cookie `x-ms-routing-name` nelle intestazioni HTTP. Per una richiesta indirizzata allo slot di "staging" il cookie è `x-ms-routing-name=staging`. Per una richiesta indirizzata allo slot di produzione il cookie è `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Indirizzare manualmente il traffico di produzione

Oltre al routing automatico del traffico, il Servizio app può indirizzare le richieste a uno slot specifico. Ciò risulta utile se si vuole consentire agli utenti di acconsentire esplicitamente o rifiutare esplicitamente l'app beta. Per indirizzare manualmente il traffico di produzione, si usa il parametro di query `x-ms-routing-name`.

Per permettere agli utenti di rifiutare esplicitamente l'app beta, ad esempio, è possibile inserire questo collegamento nella pagina Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La stringa `x-ms-routing-name=self` specifica lo slot di produzione. Quando il browser client accede al collegamento, non solo viene reindirizzato allo slot di produzione, ma ogni richiesta successiva conterrà il cookie `x-ms-routing-name=self` che associa la sessione allo slot di produzione.

Per permettere agli utenti di acconsentire esplicitamente all'app beta, impostare lo stesso parametro di query sul nome dello slot non di produzione, ad esempio:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Per impostazione predefinita, gli slot di nuovo vengono assegnati a una regola di routing di `0%`, visualizzati in grigio. Impostando questo valore in modo esplicito su `0%` (mostrati in testo di colore nero), gli utenti possono accedere manualmente lo slot di staging usando il `x-ms-routing-name` parametro di query, ma non verranno indirizzati allo slot automaticamente perché la percentuale di routing è impostata su 0. Si tratta di uno scenario avanzato in cui è possibile "nascondere" lo slot di staging al pubblico, consentendo ai team interni testare le modifiche nello slot.

<a name="Delete"></a>

## <a name="delete-slot"></a>Eliminare uno slot

Passare alla pagina delle risorse dell'app. Selezionare **Slot di distribuzione (anteprima)**  >  *\<slot da eliminare >*  > **Panoramica**. Fare clic su **Elimina** sulla barra dei comandi.  

![Per eliminare uno slot di distribuzione](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizzare con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione in Servizio app di Azure.

Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Crea app Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Creare uno slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Avviare uno scambio con anteprima (scambio in più fasi) e applicare la configurazione dello slot di destinazione allo slot di origine
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Annullare lo scambio (scambio con anteprima) in sospeso e ripristinare la configurazione dello slot di origine
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorare gli eventi di scambio nel log attività
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Eliminare uno slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatizzare con l'interfaccia della riga di comando

Per i comandi dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Passaggi successivi
[Bloccare l'accesso a slot non di produzione](app-service-ip-restrictions.md)
