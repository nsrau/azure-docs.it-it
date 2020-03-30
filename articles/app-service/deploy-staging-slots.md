---
title: Configurare gli ambienti di gestione temporanea
description: Informazioni su come distribuire app in uno slot non di produzione e scambiare automaticamente nell'ambiente di produzione. Aumenta l'affidabilità ed elimina i tempi di inattività delle app dalle distribuzioni.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300861"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

Quando si distribuisce l'app Web, l'app Web in Linux, il back-end mobile o l'app API nel servizio app di [Azure,](https://go.microsoft.com/fwlink/?LinkId=529714)è possibile usare uno slot di distribuzione separato anziché lo slot di produzione predefinito quando si esegue nel piano del piano del servizio app **Standard,** **Premium**o **Isolato.** Gli slot di distribuzione sono app live con i propri nomi host. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. 

La distribuzione dell'applicazione in uno slot non di produzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. È possibile automatizzare l'intero flusso di lavoro configurando [lo scambio automatico](#Auto-Swap) quando la convalida pre-swap non è necessaria.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ogni piano del servizio app supporta un numero diverso di slot di distribuzione. Non sono previsti costi aggiuntivi per l'utilizzo degli slot di distribuzione. Per informazioni sul numero di slot supportati dal livello dell'app, vedere Limiti del [servizio app.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Per ridimensionare l'app a un livello diverso, assicurati che il livello di destinazione supporti il numero di slot già utilizzati dall'app. Ad esempio, se l'app ha più di cinque slot, non è possibile ridimensionarla al livello **Standard,** perché il livello **Standard** supporta solo cinque slot di distribuzione. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Aggiungi uno slot
L'app deve essere in esecuzione nel livello **Standard**, **Premium**o **Isolato** per poter abilitare più slot di distribuzione.


1. nel [portale](https://portal.azure.com/)di Azure , cercare e selezionare **Servizi app** e selezionare l'app. 
   
    ![Cercare i servizi dell'appSearch for App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. Nel riquadro sinistro selezionare **Slot di** > distribuzione**Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se l'app non è già nel livello **Standard**, **Premium**o **Isolato,** viene visualizzato un messaggio che indica i livelli supportati per l'abilitazione della pubblicazione in fasi. A questo punto, è possibile selezionare **Aggiorna** e passare alla scheda **Scala** dell'app prima di continuare.
   > 

3. Nella finestra di dialogo **Aggiungi uno slot** assegnare un nome allo slot e scegliere se clonare la configurazione di un'app da un altro slot di distribuzione. Selezionare **Aggiungi** per continuare.
   
    ![Origine configurazione](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    È possibile clonare una configurazione da qualsiasi slot esistente. Le impostazioni che possono essere clonate includono le impostazioni dell'app, le stringhe di connessione, le versioni di framework del linguaggio, i Web Socket, la versione HTTP e il numero di bit della piattaforma.

4. Dopo aver aggiunto lo slot, selezionare **Chiudi** per chiudere la finestra di dialogo. Il nuovo slot viene ora visualizzato nella pagina **Slot di distribuzione.** Per impostazione predefinita, **la % del traffico** è impostata su 0 per il nuovo slot, con tutto il traffico dei clienti instradato allo slot di produzione.

5. Selezionare il nuovo slot di distribuzione per aprire la pagina delle risorse dello slot.
   
    ![Titolo slot distribuzione](./media/web-sites-staged-publishing/StagingTitle.png)

    Lo slot di staging ha una pagina di gestione come qualsiasi altra app del servizio app. È possibile modificare la configurazione dello slot. Il nome dello slot è indicato all'inizio della pagina per ricordare all'utente che sta visualizzando lo slot di distribuzione.

6. Selezionare l'URL dell'app nella pagina delle risorse dello slot. Lo slot di distribuzione ha un proprio nome host ed è anche un'app live. Per limitare l'accesso pubblico allo slot di distribuzione, vedere Restrizioni IP del servizio app di Azure .To limit public access to the deployment slot, see [Azure App Service IP restrictions](app-service-ip-restrictions.md).

Il nuovo slot di distribuzione non ha contenuto, anche se si clonano le impostazioni da un altro slot. Ad esempio, è possibile [pubblicare](app-service-deploy-local-git.md)in questo slot con Git . È possibile distribuire lo slot da un ramo diverso del repository o da un repository diverso. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Cosa succede durante uno scambio

### <a name="swap-operation-steps"></a>Scambiare i passaggi dell'operazione

Quando si scambiano due slot (in genere da uno slot di gestione temporanea nello slot di produzione), il servizio app esegue le operazioni seguenti per garantire che lo slot di destinazione non si verifichi tempi di inattività:

1. Applicare le seguenti impostazioni dallo slot di destinazione (ad esempio, lo slot di produzione) a tutte le istanze dello slot di origine: 
    - [Impostazioni](#which-settings-are-swapped) dell'app specifiche dello slot e stringhe di connessione, se applicabile.
    - [Impostazioni di distribuzione continua,](deploy-continuous-deployment.md) se abilitate.
    - Impostazioni di autenticazione del [servizio app,](overview-authentication-authorization.md) se abilitate.
    
    Ognuno di questi casi attiva tutte le istanze nello slot di origine per il riavvio. Durante [lo scambio con l'anteprima,](#Multi-Phase)questo segna la fine della prima fase. L'operazione di scambio è sospesa ed è possibile verificare che lo slot di origine funzioni correttamente con le impostazioni dello slot di destinazione.

1. Attendere il completamento del riavvio di ogni istanza nello slot di origine. Se un'istanza non viene riavviata, l'operazione di scambio ripristina tutte le modifiche allo slot di origine e interrompe l'operazione.

1. Se la [cache locale](overview-local-cache.md) è abilitata, attivare l'inizializzazione della cache locale effettuando una richiesta HTTP alla radice dell'applicazione ("/") in ogni istanza dello slot di origine. Attendere che ogni istanza restituisca qualsiasi risposta HTTP. L'inizializzazione della cache locale causa un altro riavvio in ogni istanza.

1. Se [lo scambio automatico](#Auto-Swap) è abilitato con il [riscaldamento personalizzato,](#Warm-up)attivare [l'avvio dell'applicazione](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) effettuando una richiesta HTTP alla radice dell'applicazione ("/") in ogni istanza dello slot di origine.

    Se `applicationInitialization` non viene specificato, attivare una richiesta HTTP alla radice dell'applicazione dello slot di origine in ogni istanza. 
    
    Se un'istanza restituisce una risposta HTTP, viene considerata riscaldata.

1. Se tutte le istanze nello slot di origine sono riscaldate correttamente, scambiare i due slot cambiando le regole di instradamento per i due slot. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) ha l'app che in precedenza è riscaldata nello slot di origine.

1. Ora che lo slot di origine ha l'app di pre-scambio precedentemente nello slot di destinazione, eseguire la stessa operazione applicando tutte le impostazioni e riavviando le istanze.

In qualsiasi punto dell'operazione di scambio, tutto il lavoro di inizializzazione delle app scambiate avviene nello slot di origine. Lo slot di destinazione rimane online mentre lo slot di origine viene preparato e riscaldato, indipendentemente dal luogo in cui lo scambio ha esito positivo o negativo. Per scambiare uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione sia sempre lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sull'app di produzione.

### <a name="which-settings-are-swapped"></a>Impostazioni incluse nello scambio

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Per configurare un'impostazione dell'app o una stringa di connessione in modo che si attenga a uno slot specifico (non scambiato), passare alla pagina **Configurazione** per tale slot. Aggiungere o modificare un'impostazione e quindi selezionare **l'impostazione dello slot di distribuzione**. Se si seleziona questa casella di controllo, il servizio app indica che l'impostazione non è scambiabile. 

![Impostazione slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Scambiare due slot 
È possibile scambiare gli slot di distribuzione nella pagina Slot di distribuzione dell'app e nella pagina **Panoramica.You** can swap deployment slots on your app's **Deployment slots** page and the Overview page. Per informazioni tecniche sullo scambio di slot, vedere [Cosa succede durante lo scambio](#AboutConfiguration).

> [!IMPORTANT]
> Prima di scambiare un'app da uno slot di distribuzione nell'ambiente di produzione, assicurarsi che la produzione sia lo slot di destinazione e che tutte le impostazioni nello slot di origine siano configurate esattamente come si vuole in produzione.
> 
> 

Per scambiare gli slot di distribuzione:

1. Vai alla pagina **Slot di distribuzione** della tua app e seleziona **Scambia**.
   
    ![Pulsante Scambia](./media/web-sites-staged-publishing/SwapButtonBar.png)

    La finestra di dialogo **Scambia** mostra le impostazioni negli slot di origine e di destinazione selezionati che verranno modificati.

2. Selezionare gli slot desiderati in **Origine** e **Destinazione**. In genere, la destinazione è lo slot di produzione. Selezionare inoltre le schede **Modifiche di origine** e Modifiche di **destinazione** e verificare che le modifiche alla configurazione siano previste. Al termine, è possibile scambiare immediatamente gli slot selezionando **Scambia**.

    ![Scambio completo](./media/web-sites-staged-publishing/SwapImmediately.png)

    Per vedere come verrebbe eseguito lo slot di destinazione con le nuove impostazioni prima che lo scambio avvenga effettivamente, non selezionare **Scambia**, ma seguire le istruzioni in [Scambia con anteprima](#Multi-Phase).

3. Al termine, chiudere la finestra di dialogo selezionando **Chiudi**.

In caso di problemi, vedere [Risoluzione dei problemi relativi](#troubleshoot-swaps)agli swap .

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Scambio con anteprima (swap multifase)

Prima di passare alla produzione come slot di destinazione, verificare che l'app venga eseguita con le impostazioni scambiate. Lo slot di origine viene inoltre riscaldato prima del completamento dello scambio, che è auspicabile per le applicazioni mission-critical.

Quando si esegue uno scambio con l'anteprima, il servizio app esegue la stessa operazione di [scambio](#AboutConfiguration) ma viene sospeso dopo il primo passaggio. È quindi possibile verificare il risultato nello slot di gestione temporanea prima di completare lo scambio. 

Se si annulla lo scambio, il servizio app riapplica gli elementi di configurazione allo slot di origine.

Per sostituire l'anteprima:

1. Seguire i passaggi descritti in [Scambiare gli slot di distribuzione,](#Swap) ma selezionare **Esegui scambio con anteprima**.

    ![Scambio con anteprima](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La finestra di dialogo mostra come cambia la configurazione nello slot di origine nella fase 1 e come cambiano lo slot di origine e di destinazione nella fase 2.

2. Quando si è pronti per avviare lo scambio, selezionare **Avvia scambio**.

    Al termine della fase 1, si riceve una notifica nella finestra di dialogo. Visualizzare l'anteprima dello scambio `https://<app_name>-<source-slot-name>.azurewebsites.net`nello slot di origine accedendo a . 

3. Quando si è pronti per completare lo scambio in sospeso, selezionare **Completa swap** in **azione Di swap** e selezionare Completa **swap**.

    Per annullare uno scambio in sospeso, **selezionare Annulla scambio.**

4. Al termine, chiudere la finestra di dialogo selezionando **Chiudi**.

In caso di problemi, vedere [Risoluzione dei problemi relativi](#troubleshoot-swaps)agli swap .

Per automatizzare uno scambio in più fasi, vedere [Automatizzare con PowerShell.](#automate-with-powershell)

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio
Se si verificano errori nello slot di destinazione (ad esempio, lo slot di produzione) dopo uno scambio di slot, ripristinare gli slot allo stato precedente scambiandoli immediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurare lo scambio automatico

> [!NOTE]
> Lo scambio automatico non è supportato nelle app Web su Linux.

Lo scambio automatico semplifica gli scenari di Azure DevOps in cui si vuole distribuire l'app continuamente con avvii a freddo zero e nessun tempo di inattività per i clienti dell'app. Quando lo scambio automatico è abilitato da uno slot nell'ambiente di produzione, ogni volta che si esegue il push delle modifiche del codice a tale slot, il servizio app scambia automaticamente [l'app nell'ambiente di produzione](#swap-operation-steps) dopo che è stata riscaldata nello slot di origine.

   > [!NOTE]
   > Prima di configurare lo scambio automatico per lo slot di produzione, è consigliabile testare lo scambio automatico in uno slot di destinazione non di produzione.
   > 

Per configurare lo scambio automatico:

1. Vai alla pagina delle risorse della tua app. Selezionare **Slot di** > *\< *distribuzione slot di origine desiderato> > **Impostazioni generali**di **configurazione** > .
   
2. Per **Scambio automatico abilitato**, selezionare **Attivato**. Selezionare quindi lo slot di destinazione desiderato per **Slot di distribuzione scambio automatico**e selezionare **Salva** nella barra dei comandi. 
   
    ![Selezioni per la configurazione dello scambio automatico](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Eseguire un push del codice allo slot di origine. Lo scambio automatico avviene dopo un breve periodo di tempo e l'aggiornamento si riflette nell'URL dello slot di destinazione.

In caso di problemi, vedere [Risoluzione dei problemi relativi](#troubleshoot-swaps)agli swap .

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Specificare il riscaldamento personalizzato

Alcune app potrebbero richiedere azioni di riscaldamento personalizzate prima dello scambio. L'elemento `applicationInitialization` di configurazione in web.config consente di specificare azioni di inizializzazione personalizzate. L'operazione di [scambio](#AboutConfiguration) attende il completamento di questo riscaldamento personalizzato prima dello scambio con lo slot di destinazione. Di seguito è riportato un frammento web.config di esempio.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Per ulteriori informazioni sulla `applicationInitialization` personalizzazione dell'elemento, vedere Errori di scambio degli slot di [distribuzione più comuni e come correggerli](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

È inoltre possibile personalizzare il comportamento di riscaldamento con una o entrambe le seguenti [impostazioni dell'app:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: il percorso per eseguire il ping per riscaldare il sito. Aggiungere questa impostazione dell'app specificando un percorso personalizzato che inizi con una barra come valore. Un esempio è `/statuscheck`. Il valore predefinito è `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: codici di risposta HTTP validi per l'operazione di riscaldamento. Aggiungere questa impostazione dell'app con un elenco di codici HTTP separati da virgole. Un esempio `200,202` è . Se il codice di stato restituito non è presente nell'elenco, le operazioni di riscaldamento e scambio vengono interrotte. Per impostazione predefinita, sono validi tutti i codici di risposta.

> [!NOTE]
> L'elemento `<applicationInitialization>` di configurazione fa parte di ogni avvio dell'app, mentre le due impostazioni dell'app comportamento di riscaldamento si applicano solo agli scambio di slot.

In caso di problemi, vedere [Risoluzione dei problemi relativi](#troubleshoot-swaps)agli swap .

## <a name="monitor-a-swap"></a>Monitorare uno scambio

Se il completamento [dell'operazione](#AboutConfiguration) di scambio richiede molto tempo, è possibile ottenere informazioni sull'operazione di scambio nel [log attività.](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

Nella pagina delle risorse dell'app nel portale, nel riquadro sinistro, selezionare **Log attività.**

Un'operazione di scambio è riportata nella query di log come `Swap Web App Slots`. È possibile espandere la voce e selezionare una sotto-operazione o un errore per visualizzare i dettagli.

## <a name="route-traffic"></a>Indirizzare il traffico

Per impostazione predefinita, tutte le richieste client all'URL di produzione dell'app (`http://<app_name>.azurewebsites.net`) vengono indirizzate allo slot di produzione. È possibile indirizzare una parte del traffico a un altro slot. Questa funzionalità è utile se occorre il feedback degli utenti per un nuovo aggiornamento, ma non si è pronti per rilasciarlo nell'ambiente di produzione.

### <a name="route-production-traffic-automatically"></a>Indirizzare automaticamente il traffico di produzione

Per instradare automaticamente il traffico di produzione:

1. Vai alla pagina delle risorse della tua app e seleziona **Slot di distribuzione.**

2. Nella colonna **% traffico** dello slot di destinazione dell'indirizzamento, specificare una percentuale (compresa tra 0 e 100) per rappresentare la quantità di traffico totale da indirizzare. Selezionare **Salva**.

    ![Impostazione di una percentuale di traffico](./media/web-sites-staged-publishing/RouteTraffic.png)

Dopo il salvataggio dell'impostazione, la percentuale specificata di client viene instradata in modo casuale allo slot non di produzione. 

Dopo che un client viene instradato automaticamente a uno slot specifico, viene "bloccato" a tale slot per la durata della sessione client. Nel browser client è possibile visualizzare lo slot a cui è associata la sessione esaminando il cookie `x-ms-routing-name` nelle intestazioni HTTP. Per una richiesta indirizzata allo slot di "staging" il cookie è `x-ms-routing-name=staging`. Per una richiesta indirizzata allo slot di produzione il cookie è `x-ms-routing-name=self`.

   > [!NOTE]
   > Accanto al portale di Azure, [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) è anche possibile usare il comando nell'interfaccia della riga di comando di Azure per impostare le percentuali di routing da strumenti CI/CD come pipeline DevOps o altri sistemi di automazione.
   > 

### <a name="route-production-traffic-manually"></a>Indirizzare manualmente il traffico di produzione

Oltre al routing automatico del traffico, il Servizio app può indirizzare le richieste a uno slot specifico. Ciò è utile quando si desidera che gli utenti siano in grado di acconsentire esplicitamente o rifiutare esplicitamente l'app beta. Per indirizzare manualmente il traffico di produzione, si usa il parametro di query `x-ms-routing-name`.

Per consentire agli utenti di rifiutare esplicitamente l'app beta, ad esempio, puoi inserire questo link nella tua pagina web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La stringa `x-ms-routing-name=self` specifica lo slot di produzione. Dopo che il browser client accede al collegamento, viene reindirizzato allo slot di produzione. Ogni richiesta successiva `x-ms-routing-name=self` ha il cookie che blocca la sessione allo slot di produzione.

Per consentire agli utenti di acconsentire esplicitamente all'app beta, imposta lo stesso parametro di query sul nome dello slot non di produzione. Ad esempio:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Per impostazione predefinita, ai nuovi `0%`slot viene assegnata una regola di instradamento di , visualizzata in grigio. Quando si imposta in `0%` modo esplicito questo valore su (visualizzato in `x-ms-routing-name` testo nero), gli utenti possono accedere manualmente allo slot di gestione temporanea utilizzando il parametro query. Ma non verranno indirizzati allo slot automaticamente perché la percentuale di instradamento è impostata su 0. Si tratta di uno scenario avanzato in cui è possibile "nascondere" lo slot di staging dal pubblico consentendo ai team interni di testare le modifiche nello slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Eliminare uno slot

Cerca e seleziona la tua app. Selezionare **Slot slot slot per** > *\<la distribuzione per eliminare>*  >  **Panoramica**. Selezionare **Elimina** nella barra dei comandi.  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Avviare uno scambio con un'anteprima (scambio multifase) e applicare la configurazione dello slot di destinazione allo slot di origine
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Annullare uno scambio in sospeso (scambiare con la revisione) e ripristinare la configurazione dello slot di origine
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Swap degli slot di distribuzione
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorare gli eventi di scambio nel registro attivitàMonitor swap events in the activity log
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Eliminare uno slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizzare con i modelli di Resource ManagerAutomate with Resource Manager templates

I modelli di Azure Resource Manager sono file JSON dichiarativi usati per automatizzare la distribuzione e la configurazione delle risorse di [Azure.Azure Resource Manager templates](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) are declarative JSON files used to automate the deployment and configuration of Azure resources. Per scambiare gli slot utilizzando i modelli di Resource Manager, si imposteranno due proprietà nelle risorse *Microsoft.Web/sites/slots* e *Microsoft.Web/sites:*

- `buildVersion`: proprietà string che rappresenta la versione corrente dell'app distribuita nello slot. Ad esempio: "v1", "1.0.0.1" o "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: questa è una proprietà `buildVersion` stringa che specifica il valore dello slot. Se targetBuildVersion non è `buildVersion`uguale a corrente, verrà attivata l'operazione `buildVersion`di scambio individuando lo slot con l'oggetto specificato.

### <a name="example-resource-manager-template"></a>Modello di Resource Manager di esempioExample Resource Manager template

Il modello di Resource `buildVersion` Manager seguente aggiornerà `targetBuildVersion` lo slot di gestione temporanea e imposterà il nello slot di produzione. Questo cambierà i due slot. Il modello presuppone che tu abbia già creato una webapp con uno slot denominato "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Questo modello di Resource Manager è idempotente, ovvero può essere eseguito ripetutamente e produrre lo stesso stato degli slot. Dopo la prima `targetBuildVersion` esecuzione, `buildVersion`corrisponderà alla corrente , in modo che non verrà attivato uno scambio.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizza con l'interfaccia della riga di comando

Per i comandi dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Risolvere i problemi relativi agli swap

Se si verifica un errore durante uno [scambio](#AboutConfiguration)di slot , viene registrato in *D:* Viene inoltre registrato nel log degli errori specifico dell'applicazione.

Di seguito sono riportati alcuni errori di scambio comuni:Here are some common swap errors:

- Viene eseguita una richiesta HTTP alla radice dell'applicazione. L'operazione di scambio attende 90 secondi per ogni richiesta HTTP e riprova fino a 5 volte. Se tutti i tentativi sono stime, l'operazione di scambio viene interrotta.

- L'inizializzazione della cache locale potrebbe non riuscire quando il contenuto dell'app supera la quota disco locale specificata per la cache locale. Per ulteriori informazioni, consultate [Panoramica della cache locale.](overview-local-cache.md)

- Durante il [riscaldamento personalizzato,](#Warm-up)le richieste HTTP vengono effettuate internamente (senza passare attraverso l'URL esterno). Possono avere esito negativo con determinate regole di riscrittura URL in *Web.config*. Ad esempio, le regole per reindirizzare i nomi di dominio o applicare HTTPS possono impedire alle richieste di riscaldamento di raggiungere il codice dell'app. Per risolvere questo problema, modificare le regole di riscrittura aggiungendo le due condizioni seguenti:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Senza un riscaldamento personalizzato, le regole di riscrittura URL possono comunque bloccare le richieste HTTP. Per risolvere questo problema, modificare le regole di riscrittura aggiungendo la seguente condizione:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Alcune regole di [restrizione IP](app-service-ip-restrictions.md) potrebbero impedire all'operazione di scambio di inviare richieste HTTP all'app. Intervalli di indirizzi IPv4 che iniziano con `10.` e `100.` sono interni alla distribuzione. Devi consentire loro di connettersi alla tua app.

- Dopo lo scambio di slot, l'applicazione potrebbe verificarsi riavvii imprevisti. Questo perché dopo uno scambio, la configurazione dell'associazione del nome host non viene sincronizzata, che di per sé non causa il riavvio. Tuttavia, alcuni eventi di archiviazione sottostanti (ad esempio i failover del volume di archiviazione) possono rilevare queste discrepanze e forzare il riavvio di tutti i processi di lavoro. Per ridurre al minimo questi tipi di riavvii, impostare [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` l'impostazione dell'app](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) su *tutti gli slot.* Tuttavia, questa impostazione dell'app *non* funziona con le app di Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Passaggi successivi
[Bloccare l'accesso a slot non di produzione](app-service-ip-restrictions.md)
