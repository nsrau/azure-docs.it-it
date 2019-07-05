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
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445606"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

Quando si distribuisce l'app web, app web in Linux, back-end per dispositivi mobili o app per le API [servizio App di Azure](https://go.microsoft.com/fwlink/?LinkId=529714), è possibile usare uno slot di distribuzione distinto anziché lo slot di produzione predefinito quando si esegue nel **Standard**, **Premium**, o **Isolated** livello del piano di servizio App. Gli slot di distribuzione sono App dinamiche con i propri nomi host. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. 

La distribuzione dell'applicazione in uno slot non di produzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. È possibile automatizzare l'intero flusso di lavoro configurando [scambio automatico](#Auto-Swap) quando non è necessario pre-swapping convalida.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ogni piano del servizio app supporta un numero diverso di slot di distribuzione. Non sono previsti addebiti aggiuntivi per l'uso degli slot di distribuzione. Per scoprire il numero di slot supportati dal piano dell'app, vedere [limiti del servizio App](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Per ridimensionare l'app a un livello diverso, assicurarsi che il livello di destinazione supporta il numero di slot per che l'app Usa già. È ad esempio, se l'app ha più di cinque slot, non è possibile ridimensionare verso il basso per il **Standard** tier, perché le **Standard** livello supporta solo cinque gli slot di distribuzione. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Aggiungere uno slot
Per poter abilitare più slot di distribuzione, l'app deve essere in esecuzione con il piano **Standard**, **Premium** o **Isolato**.

1. Nel [portale di Azure](https://portal.azure.com/) aprire la [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) dell'app.

2. Nel riquadro sinistro, selezionare **slot di distribuzione** > **Aggiungi Slot**.
   
    ![Aggiungi nuovo slot di distribuzione](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se l'app non è già nel **Standard**, **Premium**, o **Isolated** livello, viene visualizzato un messaggio che indica i piani supportati per l'abilitazione della pubblicazione di gestione temporanea. A questo punto, è possibile selezionare **aggiornare** e passare alle **scalabilità** scheda dell'app prima di continuare.
   > 

3. Nel **aggiungere uno slot** finestra di dialogo, assegnare un nome lo slot e selezionare se clonare una configurazione di app da uno slot di distribuzione. Selezionare **Add** per continuare.
   
    ![Origine configurazione](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    È possibile clonare una configurazione da eventuali slot esistente. Le impostazioni che possono essere clonate includono le impostazioni dell'app, le stringhe di connessione, le versioni di framework del linguaggio, i Web Socket, la versione HTTP e il numero di bit della piattaforma.

4. Dopo aver aggiunto lo slot, selezionare **chiudere** per chiudere la finestra di dialogo. Il nuovo slot sono ora visualizzate nel **slot di distribuzione** pagina. Per impostazione predefinita **traffico %** è impostato su 0 per il nuovo slot, con tutto il traffico dei clienti instradato allo slot di produzione.

5. Selezionare il nuovo slot di distribuzione per aprire una pagina delle risorse di questo slot.
   
    ![Titolo slot di distribuzione](./media/web-sites-staged-publishing/StagingTitle.png)

    Lo slot di staging ha una pagina di gestione come qualsiasi altra app del servizio app. È possibile modificare la configurazione dello slot. Il nome dello slot è indicato all'inizio della pagina per ricordare all'utente che sta visualizzando lo slot di distribuzione.

6. Selezionare l'URL dell'app nella pagina delle risorse dello slot. Lo slot di distribuzione ha il proprio nome host ed è anche un'app in tempo reale. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [restrizioni IP del servizio App di Azure](app-service-ip-restrictions.md).

Il nuovo slot di distribuzione non ha contenuto, anche se si clonano le impostazioni da un altro slot. Ad esempio, è possibile [pubblicare in questo slot con Git](app-service-deploy-local-git.md). È possibile distribuire lo slot da un ramo diverso del repository o da un repository diverso. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Che cosa avviene durante uno scambio

### <a name="swap-operation-steps"></a>Passaggi dell'operazione di scambio

Durante lo scambio di slot di due (in genere da uno slot di staging nello slot di produzione), servizio App esegue il comando seguente per verificare che lo slot di destinazione non subire tempi di inattività:

1. Applicare le impostazioni seguenti dallo slot di destinazione (ad esempio, lo slot di produzione) a tutte le istanze dello slot di origine: 
    - [Specifici dello slot](#which-settings-are-swapped) le impostazioni dell'app e le stringhe di connessione, se applicabile.
    - [Distribuzione continua](deploy-continuous-deployment.md) impostazioni, se abilitato.
    - [Autenticazione del servizio app](overview-authentication-authorization.md) impostazioni, se abilitato.
    
    Ognuno di questi casi attivare tutte le istanze nello slot di origine riavviare. Durante [scambio con anteprima](#Multi-Phase), ciò contrassegna la fine della prima fase. L'operazione di scambio è in pausa, ed è possibile convalidare che lo slot di origine funzioni correttamente con le impostazioni degli slot di destinazione.

1. Attesa per ogni istanza nello slot di origine per completare il riavvio. Se qualsiasi istanza di riavvio non riesce, l'operazione di scambio Ripristina tutte le modifiche allo slot di origine e arresta l'operazione.

1. Se [cache locale](overview-local-cache.md) è abilitata, attivare l'inizializzazione della cache locale eseguendo una richiesta HTTP per la radice dell'applicazione ("/") in ogni istanza di slot di origine. Attendere che ogni istanza restituisce una risposta HTTP. Inizializzazione della cache locale fa in modo che un altro riavvio in ogni istanza.

1. Se [scambio automatico](#Auto-Swap) è abilitata con [riscaldamento personalizzato](#Warm-up), trigger [avvio applicazione](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) eseguendo una richiesta HTTP per la radice dell'applicazione ("/") in ogni istanza dell'origine slot.

    Se `applicationInitialization` non è specificato, attivare una richiesta HTTP per la radice dell'applicazione dello slot di origine in ogni istanza. 
    
    Se un'istanza restituisce una risposta HTTP, è considerata come essere riscaldato.

1. Se tutte le istanze nello slot di origine siano pronte correttamente, è possibile scambiare i due slot passando le regole di routing per i due slot. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) include l'app che in precedenza viene eseguito il riscaldamento nello slot di origine.

1. Ora che lo slot di origine ha l'app pre-swapping in precedenza nello slot di destinazione, eseguire la stessa operazione applicando tutte le impostazioni e riavviare le istanze.

In qualsiasi punto dell'operazione di sostituzione, tutte le operazioni di inizializzazione le app invertite avviene nello slot di origine. Lo slot di destinazione rimane online mentre lo slot di origine viene preparato e riscaldato, indipendentemente da dove lo scambio ha esito positivo o non riesce. Per lo scambio di uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione è sempre lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sulle app di produzione.

### <a name="which-settings-are-swapped"></a>Impostazioni incluse nello scambio
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Alcuni elementi di configurazione seguono il contenuto nello scambio (non specifici dello slot), mentre altri elementi di configurazione rimangono nello stesso slot dopo uno scambio (specifici dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, ad esempio la versione di framework, 32 o 64 bit, web socket
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Impostazioni di monitoraggio e diagnostica
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Integrazione della rete virtuale *
* Endpoint di servizio *
* Rete CDN di Azure *

Le funzionalità contrassegnate con un asterisco (*) sono state pianificate per essere reso permanente nello slot. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati privati e associazioni SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Le impostazioni del protocollo (HTTPS, la versione di TLS, certificati client)
* Impostazioni di log di diagnostica
* Cross-origin resource sharing (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

Per configurare una stringa di connessione o impostazione di app per adattarsi a uno slot specifico (non scambiato), vedere la **configurazione** pagina per tale slot. Aggiungere o modificare un'impostazione e quindi selezionare **impostazione slot di distribuzione**. Se si seleziona questa casella di controllo indica che l'impostazione non sostituibile servizio App. 

![Impostazione slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Scambiare due slot 
È possibile scambiare gli slot di distribuzione nella tua app **slot di distribuzione** pagina e il **Panoramica** pagina. Per informazioni tecniche sullo scambio di slot, vedere [cosa accade durante lo scambio](#AboutConfiguration).

> [!IMPORTANT]
> Prima di scambiare un'app da uno slot di distribuzione nell'ambiente di produzione, assicurarsi che l'ambiente di produzione sia lo slot di destinazione e che tutte le impostazioni nello slot di origine siano configurate esattamente come si desidera che ne sia nell'ambiente di produzione.
> 
> 

Scambiare gli slot di distribuzione:

1. Passare all'app **slot di distribuzione** pagina e selezionare **Swap**.
   
    ![Pulsante swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Il **scambiare** nella finestra di dialogo Mostra le impostazioni negli slot di origine e di destinazione selezionati che verranno modificati.

2. Selezionare gli slot desiderati in **Origine** e **Destinazione**. In genere, la destinazione è lo slot di produzione. Selezionare anche il **modifiche apportate all'origine** e **di destinazione viene modificata** schede e verificare che le modifiche alla configurazione sono previsti. Quando hai finito, è possibile scambiare immediatamente gli slot selezionando **scambio**.

    ![Scambio completo](./media/web-sites-staged-publishing/SwapImmediately.png)

    Per vedere come slot di destinazione verrà eseguita con le nuove impostazioni prima dello scambio si verifica effettivamente, non selezionare **scambio**, ma seguire le istruzioni [lo scambio con anteprima](#Multi-Phase).

3. Al termine, chiudere la finestra di dialogo selezionando **chiudere**.

Se si verificano problemi, vedere [risolvere i problemi di scambi](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Scambio con anteprima (swap multifase)

> [!NOTE]
> Lo scambio con anteprima non è supportato nelle app Web in Linux.

Prima di scambiare in produzione come lo slot di destinazione, verificare che l'app viene eseguita con le impostazioni invertite. Lo slot di origine viene anche preparato prima del completamento di scambio, che è consigliabile che le applicazioni mission-critical.

Quando si esegue uno scambio con anteprima, servizio App esegue gli stessi [operazione di scambio](#AboutConfiguration) ma viene sospeso dopo il primo passaggio. È quindi possibile verificare il risultato nello slot di staging prima di completare lo scambio. 

Se si annulla lo scambio, servizio App consente di riapplicare gli elementi di configurazione allo slot di origine.

Per lo scambio con anteprima:

1. Seguire i passaggi descritti in [scambiare gli slot di distribuzione](#Swap) ma selezionare **Esegui scambio con anteprima**.

    ![Scambio con anteprima](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La finestra di dialogo viene illustrato come la configurazione nello slot di origine viene modificata nella fase 1 e su come cambiare di slot di origine e di destinazione nella fase 2.

2. Quando si è pronti per avviare lo scambio, selezionare **scambio avviare**.

    Al termine della fase 1, si riceverà una notifica nella finestra di dialogo. Visualizzare in anteprima lo scambio di slot di origine, passare a `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando è pronti per completare lo scambio in sospeso, selezionare **completa scambio** nelle **azione di scambio** e selezionare **completa scambio**.

    Per annullare uno scambio in sospeso, selezionare **Annulla scambio** invece.

4. Al termine, chiudere la finestra di dialogo selezionando **chiudere**.

Se si verificano problemi, vedere [risolvere i problemi di scambi](#troubleshoot-swaps).

Per automatizzare uno scambio in più fasi, vedere [Automatizzare con PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio
Se si verificano errori nello slot di destinazione (ad esempio, lo slot di produzione) dopo uno scambio di slot, ripristinare gli slot allo stato precedente scambiandoli immediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurare lo scambio automatico

> [!NOTE]
> Lo scambio automatico non è supportato nelle App web in Linux.

Lo scambio automatico semplifica gli scenari DevOps di Azure in cui si desidera distribuire l'app in modo continuo con zero avvii a freddo e senza tempi di inattività per i clienti dell'app. Quando lo scambio automatico è abilitato da uno slot nell'ambiente di produzione, ogni volta che si attiva automaticamente le modifiche al codice in tale slot, servizio App [Scambia l'app nell'ambiente di produzione](#swap-operation-steps) dopo che viene eseguito il riscaldamento nello slot di origine.

   > [!NOTE]
   > Prima di configurare lo scambio automatico per lo slot di produzione, è consigliabile testare lo scambio automatico in uno slot di destinazione non di produzione.
   > 

Per configurare lo scambio automatico:

1. Passare alla pagina delle risorse dell'app. Selezionare **slot di distribuzione** >  *\<slot di origine desiderato >*  > **configurazione**  >  **Impostazioni generali**.
   
2. Per la **scambio automatico abilitato**, selezionare **su**. Quindi selezionare lo slot di destinazione desiderata per **slot di distribuzione di scambio automatico**e selezionare **salvare** sulla barra dei comandi. 
   
    ![Selezioni per la configurazione lo scambio automatico](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Eseguire un push del codice allo slot di origine. Lo scambio automatico avviene dopo un breve periodo di tempo e l'aggiornamento viene inviato all'URL dello slot di destinazione.

Se si verificano problemi, vedere [risolvere i problemi di scambi](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Specificare riscaldamento personalizzato
Quando si usa [scambio automatico](#Auto-Swap), alcune App potrebbero richiedere azioni di riscaldamento personalizzato prima dello scambio. Il `applicationInitialization` elemento di configurazione nel file Web. config consente di specificare le azioni di inizializzazione personalizzati. Il [operazione di scambio](#AboutConfiguration) attende il completamento prima dello scambio con lo slot di destinazione del riscaldamento personalizzato. Ecco un frammento Web. config di esempio.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Per altre informazioni sulla personalizzazione il `applicationInitialization` elemento, vedere [più comuni errori scambio dello slot di distribuzione e come risolverli](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

È anche possibile personalizzare il comportamento di riscaldamento con uno o entrambi gli elementi seguenti [le impostazioni dell'app](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Il percorso per eseguire il ping per preparare il sito. Aggiungere questa impostazione dell'app specificando un percorso personalizzato che inizi con una barra come valore. Un esempio è `/statuscheck`. Il valore predefinito è `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: codici di risposta HTTP validi per l'operazione di riscaldamento. Aggiungere questa impostazione dell'app con un elenco di codici HTTP separati da virgole. Un esempio è `200,202` . Se il codice di stato restituito non è nell'elenco, le operazioni di riscaldamento e swap vengono arrestate. Per impostazione predefinita, sono validi tutti i codici di risposta.

Se si verificano problemi, vedere [risolvere i problemi di scambi](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorare uno scambio

Se il [operazione di scambio](#AboutConfiguration) richiede molto tempo per il completamento, è possibile ottenere informazioni sull'operazione di scambio nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Nella pagina delle risorse dell'app nel portale, nel riquadro sinistro, selezionare **log attività**.

Un'operazione di scambio è riportata nella query di log come `Swap Web App Slots`. È possibile espandere la voce e selezionare una sotto-operazione o un errore per visualizzare i dettagli.

## <a name="route-traffic"></a>Indirizzare il traffico

Per impostazione predefinita, tutte le richieste client all'URL di produzione dell'app (`http://<app_name>.azurewebsites.net`) vengono indirizzate allo slot di produzione. È possibile indirizzare una parte del traffico a un altro slot. Questa funzionalità è utile se occorre il feedback degli utenti per un nuovo aggiornamento, ma non si è pronti per rilasciarlo nell'ambiente di produzione.

### <a name="route-production-traffic-automatically"></a>Indirizzare automaticamente il traffico di produzione

Per instradare il traffico di produzione automaticamente:

1. Passare alla pagina delle risorse dell'app e selezionare **slot di distribuzione**.

2. Nella colonna **% traffico** dello slot di destinazione dell'indirizzamento, specificare una percentuale (compresa tra 0 e 100) per rappresentare la quantità di traffico totale da indirizzare. Selezionare **Salva**.

    ![Impostazione di una percentuale di traffico](./media/web-sites-staged-publishing/RouteTraffic.png)

Dopo l'impostazione viene salvata, la percentuale specificata di client in modo casuale viene instradata allo slot non di produzione. 

Dopo che un client viene automaticamente indirizzato a uno slot specifico, è "bloccato" in tale slot per tutta la durata della sessione client. Nel browser client è possibile visualizzare lo slot a cui è associata la sessione esaminando il cookie `x-ms-routing-name` nelle intestazioni HTTP. Per una richiesta indirizzata allo slot di "staging" il cookie è `x-ms-routing-name=staging`. Per una richiesta indirizzata allo slot di produzione il cookie è `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Indirizzare manualmente il traffico di produzione

Oltre al routing automatico del traffico, il Servizio app può indirizzare le richieste a uno slot specifico. Ciò è utile quando si desidera che gli utenti siano in grado di fornire il consenso esplicito per o rifiutare esplicitamente l'app beta. Per indirizzare manualmente il traffico di produzione, si usa il parametro di query `x-ms-routing-name`.

Per consentire agli utenti di rifiutare esplicitamente l'app beta, ad esempio, è possibile inserire questo collegamento nella pagina Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La stringa `x-ms-routing-name=self` specifica lo slot di produzione. Dopo che il browser client accede al collegamento, viene reindirizzato allo slot di produzione. Ogni richiesta successiva ha il `x-ms-routing-name=self` cookie che aggiunge la sessione allo slot di produzione.

Per consentire agli utenti di acconsentire esplicitamente all'App beta, impostare lo stesso parametro di query per il nome dello slot non di produzione. Ad esempio:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Per impostazione predefinita, gli slot di nuovo vengono assegnati a una regola di routing di `0%`, visualizzati in grigio. Quando si imposta in modo esplicito questo valore su `0%` (mostrati in testo di colore nero), gli utenti possono accedere manualmente lo slot di staging usando il `x-ms-routing-name` parametro di query. Ma non sarà instradate allo slot automaticamente perché la percentuale di routing è impostata su 0. Si tratta di uno scenario avanzato in cui è possibile "nascondere" lo slot di staging al pubblico, consentendo ai team interni testare le modifiche nello slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Eliminare uno slot

Passare alla pagina delle risorse dell'app. Selezionare **slot di distribuzione** >  *\<slot eliminare >*  > **Panoramica**. Selezionare **eliminare** sulla barra dei comandi.  

![Eliminare uno slot di distribuzione](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizzare con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione in Servizio app di Azure.

Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Creare un'app Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Creare uno slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Avviare uno scambio con anteprima (swap multifase) e applicare la configurazione dello slot di destinazione allo slot di origine
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Annullare uno scambio (scambio con anteprima) in sospeso e ripristinare la configurazione dello slot di origine
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Scambio di monitorare gli eventi nel log attività
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Eliminare uno slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizzare con l'interfaccia della riga di comando

Per i comandi dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Risolvere i problemi di swap

Se si verifica un errore durante una [scambio di slot](#AboutConfiguration), viene registrato *D:\home\LogFiles\eventlog.xml*. Viene anche registrato nel log degli errori specifici dell'applicazione.

Ecco alcuni errori comuni di swapping:

- Una richiesta HTTP alla radice dell'applicazione è scaduta. L'operazione di scambio attende 90 secondi per ogni richiesta HTTP e i tentativi fino a 5 volte. Se si verifica il timeout tutti i tentativi, viene arrestato l'operazione di scambio.

- Inizializzazione della cache locale potrebbe non riuscire quando il contenuto dell'app supera la quota disco locale specificata per la cache locale. Per altre informazioni, vedere [Panoramica della cache locale](overview-local-cache.md).

- Durante [riscaldamento personalizzato](#Warm-up), le richieste HTTP vengono eseguite internamente (senza passare attraverso l'URL esterno). Si può non riuscire con alcune regole di riscrittura URL nelle *Web. config*. Ad esempio, le regole per il reindirizzamento di nomi di dominio o l'applicazione HTTPS possono impedire le richieste di riscaldamento di raggiungere il codice dell'app. Per risolvere questo problema, modificare le regole di riscrittura aggiungendo le due condizioni seguenti:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Senza un riscaldamento personalizzato, le regole di riscrittura URL possono comunque bloccare le richieste HTTP. Per risolvere questo problema, modificare le regole di riscrittura aggiungendo la condizione seguente:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Alcuni [regole di restrizioni IP](app-service-ip-restrictions.md) potrebbero impedire l'operazione di scambio di inviare richieste HTTP all'app. Che iniziano con gli intervalli di indirizzi IPv4 `10.` e `100.` interne alla distribuzione. È consigliabile consentire loro di connettersi all'app.

## <a name="next-steps"></a>Passaggi successivi
[Bloccare l'accesso a slot non di produzione](app-service-ip-restrictions.md)
