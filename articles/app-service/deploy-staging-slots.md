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
ms.topic: article
ms.date: 09/19/2019
ms.author: cephalin
ms.openlocfilehash: 436ab0a561349185de58c3783f334ea1dce9001d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720124"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurare gli ambienti di gestione temporanea nel Servizio app di Azure
<a name="Overview"></a>

Quando si distribuisce l'app Web, l'app Web in Linux, il back-end per dispositivi mobili o l'app per le API nel [servizio app Azure](https://go.microsoft.com/fwlink/?LinkId=529714), è possibile usare uno slot di distribuzione separato invece dello slot di produzione predefinito quando si esegue in modalità **standard**, **Premium**o **isolated** Livello del piano di servizio app. Gli slot di distribuzione sono app Live con nomi host propri. È possibile scambiare il contenuto dell'app e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. 

La distribuzione dell'applicazione in uno slot non di produzione presenta i seguenti vantaggi:

* È possibile convalidare le modifiche alle app in uno slot di distribuzione temporaneo prima di scambiarlo con quello di produzione.
* La distribuzione preliminare di un'app in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. È possibile automatizzare l'intero flusso di lavoro configurando lo [scambio automatico](#Auto-Swap) quando non è necessaria la convalida pre-swap.
* Dopo uno scambio, lo slot con l'app gestita temporaneamente include l'app di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultimo sito con i dati corretti.

Ogni piano del servizio app supporta un numero diverso di slot di distribuzione. Non sono previsti costi aggiuntivi per l'uso degli slot di distribuzione. Per conoscere il numero di slot supportati dal livello dell'app, vedere [limiti del servizio app](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Per ridimensionare l'app a un livello diverso, verificare che il livello di destinazione supporti il numero di slot già utilizzati dall'app. Ad esempio, se l'app ha più di cinque slot, non è possibile ridurla al livello **standard** , perché il livello **standard** supporta solo cinque slot di distribuzione. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Aggiungere uno slot
Per poter abilitare più slot di distribuzione, l'app deve essere in esecuzione con il piano **Standard**, **Premium** o **Isolato**.

1. Nel [portale di Azure](https://portal.azure.com/) aprire la [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) dell'app.

2. Nel riquadro sinistro selezionare **slot** > di distribuzione**Aggiungi slot**.
   
    ![Aggiungi nuovo slot di distribuzione](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se l'app non è già nel livello **standard**, **Premium**o **isolato** , viene visualizzato un messaggio che indica i livelli supportati per l'abilitazione della pubblicazione di gestione temporanea. A questo punto, è possibile selezionare **Aggiorna** e passare alla scheda **scalabilità** dell'app prima di continuare.
   > 

3. Nella finestra di dialogo **Aggiungi uno slot** assegnare un nome allo slot e selezionare se clonare una configurazione dell'app da un altro slot di distribuzione. Selezionare **Aggiungi** per continuare.
   
    ![Origine configurazione](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    È possibile clonare una configurazione da qualsiasi slot esistente. Le impostazioni che possono essere clonate includono le impostazioni dell'app, le stringhe di connessione, le versioni di framework del linguaggio, i Web Socket, la versione HTTP e il numero di bit della piattaforma.

4. Dopo l'aggiunta dello slot, selezionare **Chiudi** per chiudere la finestra di dialogo. Il nuovo slot viene ora visualizzato nella pagina **slot di distribuzione** . Per impostazione predefinita, il **traffico%** è impostato su 0 per il nuovo slot e il traffico del cliente viene indirizzato allo slot di produzione.

5. Selezionare il nuovo slot di distribuzione per aprire la pagina delle risorse dello slot.
   
    ![Titolo dello slot di distribuzione](./media/web-sites-staged-publishing/StagingTitle.png)

    Lo slot di staging ha una pagina di gestione come qualsiasi altra app del servizio app. È possibile modificare la configurazione dello slot. Il nome dello slot è indicato all'inizio della pagina per ricordare all'utente che sta visualizzando lo slot di distribuzione.

6. Selezionare l'URL dell'app nella pagina delle risorse dello slot. Lo slot di distribuzione ha un proprio nome host ed è anche un'app Live. Per limitare l'accesso pubblico allo slot di distribuzione, vedere [restrizioni IP del servizio app Azure](app-service-ip-restrictions.md).

Il nuovo slot di distribuzione non ha contenuto, anche se si clonano le impostazioni da un altro slot. Ad esempio, è possibile [pubblicare in questo slot con git](app-service-deploy-local-git.md). È possibile distribuire lo slot da un ramo diverso del repository o da un repository diverso. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Cosa accade durante uno scambio

### <a name="swap-operation-steps"></a>Passaggi dell'operazione di scambio

Quando si scambiano due slot (in genere da uno slot di gestione temporanea nello slot di produzione), il servizio app esegue le operazioni seguenti per assicurarsi che lo slot di destinazione non riscontri un tempo di inattività:

1. Applicare le impostazioni seguenti dallo slot di destinazione (ad esempio, lo slot di produzione) a tutte le istanze dello slot di origine: 
    - Impostazioni dell'app [specifiche dello slot](#which-settings-are-swapped) e stringhe di connessione, se applicabile.
    - Impostazioni di [distribuzione continua](deploy-continuous-deployment.md) , se abilitate.
    - Impostazioni di [autenticazione del servizio app](overview-authentication-authorization.md) , se abilitato.
    
    Uno di questi casi attiva tutte le istanze dello slot di origine per il riavvio. Durante lo [scambio con anteprima](#Multi-Phase), viene contrassegnata la fine della prima fase. L'operazione swap è stata sospesa ed è possibile verificare che lo slot di origine funzioni correttamente con le impostazioni dello slot di destinazione.

1. Attendere che ogni istanza nello slot di origine completi il riavvio. Se un'istanza non viene riavviata, l'operazione di scambio Ripristina tutte le modifiche apportate allo slot di origine e interrompe l'operazione.

1. Se la [cache locale](overview-local-cache.md) è abilitata, attivare l'inizializzazione della cache locale effettuando una richiesta HTTP alla radice dell'applicazione ("/") in ogni istanza dello slot di origine. Attendere fino a quando ogni istanza non restituisce alcuna risposta HTTP. L'inizializzazione della cache locale causa un altro riavvio in ogni istanza.

1. Se lo [scambio automatico](#Auto-Swap) è abilitato con [Riscaldamento personalizzato](#Warm-up), attivare l' [avvio dell'applicazione](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) eseguendo una richiesta HTTP alla radice dell'applicazione ("/") in ogni istanza dello slot di origine.

    Se `applicationInitialization` non è specificato, attivare una richiesta HTTP alla radice dell'applicazione dello slot di origine in ogni istanza. 
    
    Se un'istanza restituisce una risposta HTTP, viene considerata come riscaldata.

1. Se tutte le istanze dello slot di origine vengono completate correttamente, scambiare i due slot cambiando le regole di routing per i due slot. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) dispone dell'app precedentemente riscaldata nello slot di origine.

1. Ora che lo slot di origine ha l'app pre-swap nello slot di destinazione, eseguire la stessa operazione applicando tutte le impostazioni e riavviando le istanze.

In qualsiasi momento dell'operazione di scambio, tutte le operazioni di inizializzazione delle app scambiate avvengono nello slot di origine. Lo slot di destinazione rimane online mentre è in corso la preparazione e il riscaldamento dello slot di origine, indipendentemente dalla posizione in cui lo scambio ha esito positivo o negativo. Per scambiare uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione sia sempre lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sull'app di produzione.

### <a name="which-settings-are-swapped"></a>Impostazioni incluse nello scambio

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Per configurare un'impostazione o una stringa di connessione dell'app in modo che si trovino in uno slot specifico (non invertito), passare alla pagina di **configurazione** per lo slot. Aggiungere o modificare un'impostazione, quindi selezionare **impostazione dello slot di distribuzione**. Selezionando questa casella di controllo si indica al servizio app che l'impostazione non è scambiabile. 

![Impostazione slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Scambiare due slot 
È possibile scambiare gli slot di distribuzione nella pagina degli **slot di distribuzione** dell'app e nella pagina **Panoramica** . Per informazioni tecniche sullo scambio di slot, vedere [cosa accade durante lo scambio](#AboutConfiguration).

> [!IMPORTANT]
> Prima di scambiare un'app da uno slot di distribuzione nell'ambiente di produzione, assicurarsi che la produzione sia lo slot di destinazione e che tutte le impostazioni nello slot di origine siano configurate esattamente come si desiderano nell'ambiente di produzione.
> 
> 

Per scambiare gli slot di distribuzione:

1. Passare alla pagina degli **slot di distribuzione** dell'app e selezionare **swap**.
   
    ![Pulsante Scambia](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Nella finestra di dialogo di **scambio** vengono visualizzate le impostazioni degli slot di origine e di destinazione selezionati che verranno modificate.

2. Selezionare gli slot desiderati in **Origine** e **Destinazione**. In genere, la destinazione è lo slot di produzione. Selezionare anche le schede **modifiche di origine** e **modifiche di destinazione** e verificare che siano previste le modifiche alla configurazione. Al termine, è possibile scambiare immediatamente gli slot selezionando **Scambia**.

    ![Scambio completo](./media/web-sites-staged-publishing/SwapImmediately.png)

    Per vedere come viene eseguito lo slot di destinazione con le nuove impostazioni prima che lo scambio avvenga effettivamente, non selezionare **swap**, ma seguire le istruzioni in [scambiare con l'anteprima](#Multi-Phase).

3. Al termine, chiudere la finestra di dialogo selezionando **Chiudi**.

In caso di problemi, vedere risolvere i problemi di [swap](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Scambio con anteprima (swap multifase)

Prima di scambiare in produzione come slot di destinazione, verificare che l'app venga eseguita con le impostazioni scambiate. Lo slot di origine viene anche scaldato prima del completamento dello scambio, che è consigliabile per le applicazioni cruciali.

Quando si esegue uno scambio con anteprima, il servizio app esegue la stessa [operazione di scambio](#AboutConfiguration) ma viene sospeso dopo il primo passaggio. È quindi possibile verificare il risultato sullo slot di staging prima di completare lo scambio. 

Se si annulla lo scambio, il servizio app riapplica gli elementi di configurazione allo slot di origine.

Per scambiare con l'anteprima:

1. Seguire i passaggi in [scambiare gli slot di distribuzione](#Swap) , ma selezionare **Esegui swapping con anteprima**.

    ![Scambio con anteprima](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La finestra di dialogo Mostra come la configurazione nello slot di origine cambia nella fase 1 e come lo slot di origine e di destinazione cambiano nella fase 2.

2. Quando si è pronti per avviare lo scambio, selezionare **Avvia scambio**.

    Al termine della fase 1, viene inviata una notifica nella finestra di dialogo. Visualizzare in anteprima lo swap nello slot di origine passando `https://<app_name>-<source-slot-name>.azurewebsites.net`a. 

3. Quando si è pronti per completare lo scambio in sospeso, selezionare **completa scambio** in **azione di scambio** e selezionare **completa scambio**.

    Per annullare uno scambio in sospeso, selezionare **Annulla scambio** .

4. Al termine, chiudere la finestra di dialogo selezionando **Chiudi**.

In caso di problemi, vedere risolvere i problemi di [swap](#troubleshoot-swaps).

Per automatizzare uno scambio in più fasi, vedere [Automatizzare con PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio
Se si verificano errori nello slot di destinazione (ad esempio, lo slot di produzione) dopo uno scambio di slot, ripristinare gli slot allo stato precedente scambiandoli immediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configura scambio automatico

> [!NOTE]
> Lo scambio automatico non è supportato nelle app Web in Linux.

Lo scambio automatico semplifica gli scenari DevOps di Azure in cui si vuole distribuire l'app in modo continuo con zero avvio a freddo e senza tempi di inattività per i clienti dell'app. Quando lo scambio automatico è abilitato da uno slot in produzione, ogni volta che si esegue il push delle modifiche al codice in tale slot, il servizio app [scambia automaticamente l'app in produzione](#swap-operation-steps) dopo che è stato scaldato nello slot di origine.

   > [!NOTE]
   > Prima di configurare lo scambio automatico per lo slot di produzione, provare a testare lo scambio automatico in uno slot di destinazione non di produzione.
   > 

Per configurare lo scambio automatico:

1. Passare alla pagina delle risorse dell'app. Selezionare slot > di **distribuzione** >  >  *\<slot di origine desiderata >* configurazione**Impostazioni generali**.
   
2. Per **scambio automatico abilitato**selezionare attivato. Selezionare quindi lo slot di destinazione desiderato per lo **slot di distribuzione di scambio automatico**e selezionare **Salva** sulla barra dei comandi. 
   
    ![Selezioni per la configurazione dello scambio automatico](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Eseguire un push del codice allo slot di origine. Lo scambio automatico si verifica dopo un breve periodo di tempo e l'aggiornamento viene riflesso nell'URL dello slot di destinazione.

In caso di problemi, vedere risolvere i problemi di [swap](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Specificare un riscaldamento personalizzato

Alcune app potrebbero richiedere azioni di riscaldamento personalizzate prima dello scambio. L' `applicationInitialization` elemento di configurazione in Web. config consente di specificare azioni di inizializzazione personalizzate. L' [operazione di scambio](#AboutConfiguration) attende il completamento di questo riscaldamento personalizzato prima di eseguire lo scambio con lo slot di destinazione. Ecco un frammento di esempio di Web. config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Per altre informazioni sulla personalizzazione dell' `applicationInitialization` elemento, vedere la [maggior parte degli errori di swap degli slot di distribuzione più comuni e come risolverli](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

È anche possibile personalizzare il comportamento di riscaldamento con una o entrambe le [impostazioni dell'app](configure-common.md)seguenti:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Il percorso di ping per scaldare il sito. Aggiungere questa impostazione dell'app specificando un percorso personalizzato che inizi con una barra come valore. Un esempio è `/statuscheck`. Il valore predefinito è `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: codici di risposta HTTP validi per l'operazione di riscaldamento. Aggiungere questa impostazione dell'app con un elenco di codici HTTP separati da virgole. Un esempio è `200,202` . Se il codice di stato restituito non è presente nell'elenco, le operazioni di riscaldamento e scambio vengono interrotte. Per impostazione predefinita, sono validi tutti i codici di risposta.

> [!NOTE]
> `<applicationInitialization>`fa parte di ogni avvio di app, in cui le due impostazioni dell'app si applicano solo agli scambi di slot.

In caso di problemi, vedere risolvere i problemi di [swap](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorare uno scambio

Se il completamento dell' [operazione di scambio](#AboutConfiguration) richiede molto tempo, è possibile ottenere informazioni sull'operazione di scambio nel [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Nel riquadro sinistro della pagina delle risorse dell'app nel portale selezionare **log attività**.

Un'operazione di scambio è riportata nella query di log come `Swap Web App Slots`. È possibile espandere la voce e selezionare una sotto-operazione o un errore per visualizzare i dettagli.

## <a name="route-traffic"></a>Indirizzare il traffico

Per impostazione predefinita, tutte le richieste client all'URL di produzione dell'app (`http://<app_name>.azurewebsites.net`) vengono indirizzate allo slot di produzione. È possibile indirizzare una parte del traffico a un altro slot. Questa funzionalità è utile se occorre il feedback degli utenti per un nuovo aggiornamento, ma non si è pronti per rilasciarlo nell'ambiente di produzione.

### <a name="route-production-traffic-automatically"></a>Indirizzare automaticamente il traffico di produzione

Per instradare automaticamente il traffico di produzione:

1. Passare alla pagina delle risorse dell'app e selezionare **slot di distribuzione**.

2. Nella colonna **% traffico** dello slot di destinazione dell'indirizzamento, specificare una percentuale (compresa tra 0 e 100) per rappresentare la quantità di traffico totale da indirizzare. Selezionare **Salva**.

    ![Impostazione di una percentuale di traffico](./media/web-sites-staged-publishing/RouteTraffic.png)

Dopo che l'impostazione è stata salvata, la percentuale di client specificata viene indirizzata in modo casuale allo slot non di produzione. 

Dopo che un client viene indirizzato automaticamente a uno slot specifico, viene aggiunto a tale slot per la durata della sessione client. Nel browser client è possibile visualizzare lo slot a cui è associata la sessione esaminando il cookie `x-ms-routing-name` nelle intestazioni HTTP. Per una richiesta indirizzata allo slot di "staging" il cookie è `x-ms-routing-name=staging`. Per una richiesta indirizzata allo slot di produzione il cookie è `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Indirizzare manualmente il traffico di produzione

Oltre al routing automatico del traffico, il Servizio app può indirizzare le richieste a uno slot specifico. Questa operazione è utile quando si vuole che gli utenti siano in grado di acconsentire esplicitamente o rifiutare esplicitamente l'app beta. Per indirizzare manualmente il traffico di produzione, si usa il parametro di query `x-ms-routing-name`.

Per consentire agli utenti di rifiutare esplicitamente l'app beta, ad esempio, è possibile inserire questo collegamento nella pagina Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La stringa `x-ms-routing-name=self` specifica lo slot di produzione. Quando il browser client accede al collegamento, viene reindirizzato allo slot di produzione. Ogni richiesta successiva ha il `x-ms-routing-name=self` cookie che blocca la sessione allo slot di produzione.

Per consentire agli utenti di acconsentire esplicitamente all'app beta, impostare lo stesso parametro di query sul nome dello slot non di produzione. Di seguito è riportato un esempio:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Per impostazione predefinita, ai nuovi slot viene assegnata una `0%`regola di routing, visualizzata in grigio. Quando si imposta in modo esplicito questo `0%` valore su (visualizzato in testo nero), gli utenti possono accedere allo slot di staging manualmente `x-ms-routing-name` tramite il parametro di query. Ma non verranno indirizzati automaticamente allo slot perché la percentuale di routing è impostata su 0. Si tratta di uno scenario avanzato in cui è possibile "nascondere" lo slot di staging dal pubblico, consentendo ai team interni di testare le modifiche nello slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Eliminare uno slot

Passare alla pagina delle risorse dell'app. Selezionare > slot di distribuzione > *slot per eliminare > Panoramica.\<* Selezionare **Elimina** sulla barra dei comandi.  

![Eliminare uno slot di distribuzione](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizzare con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell è un modulo che fornisce i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione in Servizio app di Azure.

Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Microsoft Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Crea un'app Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Creare uno slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Avviare uno scambio con un'anteprima (swap multifase) e applicare la configurazione dello slot di destinazione allo slot di origine
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Annulla uno scambio in sospeso (scambia con revisione) e ripristina la configurazione dello slot di origine
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Scambia slot di distribuzione
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorare gli eventi di scambio nel log attività
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Eliminare uno slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-arm-templates"></a>Automatizzare i modelli ARM

I [modelli ARM](https://docs.microsoft.com/en-us/azure/azure-resource-manager/template-deployment-overview) sono file JSON dichiarativi usati per automatizzare la distribuzione e la configurazione delle risorse di Azure. Per scambiare gli slot con i modelli ARM, è necessario impostare due proprietà nelle risorse *Microsoft. Web/sites/Slots* e *Microsoft. Web/sites* :

- `buildVersion`: proprietà stringa che rappresenta la versione corrente dell'app distribuita nello slot. Ad esempio: "V1", "1.0.0.1" o "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: questa è una proprietà di stringa che specifica il `buildVersion` dello slot. Se targetBuildVersion non è uguale al `buildVersion` corrente, verrà attivata l'operazione di scambio individuando lo slot con l'`buildVersion` specificato.

### <a name="example-arm-template"></a>Modello ARM di esempio

Il modello ARM seguente aggiornerà il `buildVersion` dello slot di staging e imposterà il `targetBuildVersion` sullo slot di produzione. Questa operazione comporterà lo scambio dei due slot. Il modello presuppone che sia già stato creato un webapp con uno slot denominato "Staging".

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

Questo modello ARM è idempotente, vale a dire che può essere eseguito ripetutamente e produrre lo stesso stato degli slot. Dopo la prima esecuzione, `targetBuildVersion` corrisponderà al `buildVersion` corrente, quindi non verrà attivato uno scambio.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizzare con l'interfaccia della riga di comando

Per i comandi dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli) relativi agli slot di distribuzione, vedere [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Risoluzione dei problemi di swap

Se si verifica un errore durante uno [scambio di slot](#AboutConfiguration), questo viene registrato in *D:\home\LogFiles\eventlog.XML*. Viene inoltre registrato nel log degli errori specifico dell'applicazione.

Di seguito sono riportati alcuni errori di scambio comuni:

- Una richiesta HTTP alla radice dell'applicazione è scaduta. L'operazione swap resta in attesa di 90 secondi per ogni richiesta HTTP e ritenta fino a 5 volte. Se si è verificato il timeout di tutti i tentativi, l'operazione di scambio viene arrestata.

- L'inizializzazione della cache locale potrebbe non riuscire quando il contenuto dell'app supera la quota del disco locale specificata per la cache locale. Per altre informazioni, vedere [Panoramica della cache locale](overview-local-cache.md).

- Durante il [Riscaldamento personalizzato](#Warm-up), le richieste HTTP vengono effettuate internamente (senza passare attraverso l'URL esterno). Possono avere esito negativo con determinate regole di riscrittura URL in *Web. config*. Ad esempio, le regole per il reindirizzamento dei nomi di dominio o l'applicazione di HTTPS possono impedire che le richieste di riscaldamento raggiungano il codice dell'app. Per risolvere questo problema, modificare le regole di riscrittura aggiungendo le due condizioni seguenti:

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
- Alcune [regole di restrizione IP](app-service-ip-restrictions.md) potrebbero impedire all'operazione di scambio di inviare richieste HTTP all'app. Gli intervalli di indirizzi IPv4 che `10.` iniziano `100.` con e sono interni alla distribuzione. È necessario consentire la connessione all'app.

- Dopo lo scambio di slot, l'app può riscontrare riavvii imprevisti. Questo è dovuto al fatto che dopo uno scambio la configurazione dell'associazione del nome host non viene sincronizzata, che non provoca il riavvio. Tuttavia, alcuni eventi di archiviazione sottostanti, ad esempio i failover del volume di archiviazione, possono rilevare tali discrepanze e forzare il riavvio di tutti i processi di lavoro. Per ridurre al minimo questi tipi di riavvii, impostare l' [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` impostazione dell'app](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) su tutti gli *slot*. Tuttavia, questa impostazione dell'app *non* funziona con le app Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Passaggi successivi
[Bloccare l'accesso a slot non di produzione](app-service-ip-restrictions.md)
