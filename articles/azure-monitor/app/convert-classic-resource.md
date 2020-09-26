---
title: Eseguire la migrazione di un monitoraggio di Azure Application Insights risorsa classica a una risorsa basata sull'area di lavoro | Microsoft Docs
description: Informazioni sui passaggi necessari per aggiornare il monitoraggio di Azure Application Insights risorsa classica al nuovo modello basato sull'area di lavoro.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aab2d1ec5a6c3e046840e736ced0993e560c4661
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333342"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Eseguire la migrazione a risorse Application Insights basate sull'area di lavoro

Questa guida illustra in modo dettagliato il processo di migrazione di una risorsa di Application Insights classica a una risorsa basata sull'area di lavoro. Le risorse basate sull'area di lavoro supportano l'integrazione completa tra Application Insights e Log Analytics. Le risorse basate sull'area di lavoro inviano Application Insights dati di telemetria a un'area di lavoro Log Analytics comune, che consente di accedere [alle funzionalità più recenti di monitoraggio di Azure mantenendo i log delle](#new-capabilities) applicazioni, dell'infrastruttura e della piattaforma in un unico percorso consolidato.

Le risorse basate sull'area di lavoro consentono il controllo degli accessi in base al ruolo comune tra le risorse e Elimina la necessità di eseguire query tra app o aree di lavoro.

**Le risorse basate sull'area di lavoro sono attualmente disponibili in tutte le aree commerciali e nel governo degli Stati Uniti di Azure**

## <a name="new-capabilities"></a>Nuove funzionalità

Application Insights basata sull'area di lavoro consente di sfruttare tutte le funzionalità più recenti di monitoraggio di Azure e Log Analytics, tra cui:

* [Chiavi gestite dal cliente (CMK)](../platform/customer-managed-keys.md) fornisce la crittografia dei dati inattivi per i dati con chiavi di crittografia solo a cui si ha accesso.
* [Collegamento privato di Azure](../platform/private-link-security.md) consente di collegare in modo sicuro i servizi PaaS di Azure alla rete virtuale usando endpoint privati.
* [Bring your own Storage (BYOS) per Profiler e snapshot debugger](./profiler-bring-your-own-storage.md) offre il controllo completo sui criteri di crittografia dei dati inattivi, sui criteri di gestione della durata e sull'accesso alla rete per tutti i dati associati a Application Insights Profiler e snapshot debugger. 
* I [livelli di prenotazione della capacità](../platform/manage-cost-storage.md#pricing-model) consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. 
* Inserimento più rapido dei dati tramite Log Analytics inserimento di flussi.

## <a name="migration-process"></a>Processo di migrazione

Quando si esegue la migrazione a una risorsa basata sull'area di lavoro, nessun dato viene trasferito dall'archiviazione della risorsa classica alla nuova archiviazione basata sull'area di lavoro. Se si sceglie di eseguire la migrazione, viene invece modificato il percorso in cui vengono scritti i nuovi dati in un'area di lavoro Log Analytics mantenendo l'accesso ai dati della risorsa classica. 

I dati della risorsa classica verranno mantenuti e saranno soggetti alle impostazioni di conservazione della risorsa di Application Insights classica. Tutti i nuovi dati inseriti dopo la migrazione saranno soggetti alle [impostazioni di conservazione](../platform/manage-cost-storage.md#change-the-data-retention-period) dell'area di lavoro log Analytics associata, che supporta anche [impostazioni di conservazione diverse in base al tipo di dati](../platform/manage-cost-storage.md#retention-by-data-type).
Il processo di migrazione è **permanente e non può essere annullato**. Quando si esegue la migrazione di una risorsa a Application Insights basata sull'area di lavoro, sarà sempre una risorsa basata sull'area di lavoro. Tuttavia, una volta eseguita la migrazione, è possibile modificare l'area di lavoro di destinazione con la frequenza necessaria. 

> [!NOTE]
> L'inserimento e la conservazione dei dati per le risorse Application Insights basate sull'area di lavoro vengono [fatturate tramite l'area di lavoro log Analytics in](../platform/manage-cost-storage.md) cui si trovano i dati. Se è stata selezionata la conservazione dei dati superiore a 90 giorni per i dati inseriti nella risorsa Application Insights classica prima della migrazione, la conservazione dei dati continuerà a essere fatturata attraverso la risorsa Application Insights. [Altre informazioni]( ./pricing.md#workspace-based-application-insights) sulla fatturazione per le risorse di Application Insights basate sull'area di lavoro.

Se non è necessario eseguire la migrazione di una risorsa esistente e si vuole creare una nuova risorsa Application Insights basata sull'area di lavoro, usare la [Guida alla creazione di risorse basata sull'area di lavoro](create-workspace-resource.md).

## <a name="pre-requisites"></a>Prerequisiti 

- Un'area di lavoro Log Analytics con la modalità di controllo di accesso impostata sull' **`use resource or workspace permissions`** impostazione. 

    - Le risorse Application Insights basate sull'area di lavoro non sono compatibili con le aree di lavoro impostate sull' **`workspace based permissions`** impostazione dedicata. Per ulteriori informazioni sul controllo di accesso dell'area di lavoro Log Analytics, consultare la [log Analytics configurare le linee guida per la modalità controllo di accesso](../platform/manage-access.md#configure-access-control-mode)

    - Se non si dispone di un'area di lavoro Log Analytics esistente, [consultare la documentazione relativa alla creazione dell'area di lavoro Log Analytics](../learn/quick-create-workspace.md).
    
- L'esportazione continua non è supportata per le risorse basate sull'area di lavoro e deve essere disabilitata.
Al termine della migrazione, è possibile usare [le impostazioni di diagnostica](../platform/diagnostic-settings.md) per configurare l'archiviazione dei dati in un account di archiviazione o in streaming nell'hub eventi di Azure.  

- Controllare le impostazioni di conservazione correnti in utilizzo **generale**  >  e conservazione dei dati**sui costi stimati**  >  **Data Retention** per l'area di lavoro log Analytics. Questa impostazione influirà sul tempo durante il quale i nuovi dati inseriti vengono archiviati dopo la migrazione della risorsa Application Insights. Se attualmente si archiviano i dati Application Insights per un periodo di tempo superiore a quello predefinito di 90 giorni e si desidera mantenere questo periodo di conservazione più ampio, potrebbe essere necessario modificare le impostazioni di conservazione dell'area di lavoro.

## <a name="migrate-your-resource"></a>Eseguire la migrazione della risorsa

In questa sezione viene illustrato il processo di migrazione di una risorsa di Application Insights classica al nuovo tipo di risorsa basata sull'area di lavoro.

1. Dalla risorsa Application Insights selezionare **Proprietà** nell'intestazione **Configura** sulla barra dei menu a sinistra.

    ![Proprietà evidenziate in una casella rossa](./media/convert-classic-resource/properties.png)

2. Selezionare **`Migrate to Workspace-based`**.
    
     ![Pulsante migrate Resource](./media/convert-classic-resource/migrate.png)

3. Scegliere l'area di lavoro Log Analytics in cui si desidera archiviare tutti i dati di telemetria Application Insights inseriti in futuro.

     ![Interfaccia utente della migrazione guidata con opzione per selezionare l'area di lavoro carica](./media/convert-classic-resource/migration.png)
    

Dopo la migrazione della risorsa, le informazioni sull'area di lavoro corrispondenti vengono visualizzate nel riquadro **Panoramica** :

![Nome dell'area di lavoro](./media/create-workspace-resource/workspace-name.png)

Facendo clic sul testo del collegamento in blu verrà visualizzata l'area di lavoro Log Analytics associata in cui è possibile sfruttare il nuovo ambiente di query unificato dell'area di lavoro.

## <a name="understanding-log-queries"></a>Informazioni sulle query di log

È ancora disponibile la completa compatibilità con le versioni precedenti per le query classiche sulle risorse di Application Insights, le cartelle di lavoro e gli avvisi basati su log all'interno dell'esperienza di Application Insights. 

Per scrivere query su un [nuovo schema o struttura di tabella basata sull'area di lavoro](apm-tables.md), è innanzitutto necessario passare all'area di lavoro log Analytics. 

Quando si esegue una query direttamente dall'interfaccia utente di Log Analytics all'interno dell'area di lavoro, vengono visualizzati solo i dati inseriti dopo la migrazione. Per visualizzare sia i dati di Application Insights classici che i nuovi dati inseriti dopo la migrazione in un'esperienza di query unificata, usare la visualizzazione query log (Analytics) dalla risorsa Application Insights migrata.

## <a name="programmatic-resource-migration"></a>Migrazione di risorse a livello di codice

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per accedere all'anteprima dell'interfaccia della riga di comando di Azure di Application Insights, è prima necessario eseguire:

```azurecli
 az extension add -n application-insights
```

Se non si esegue il comando `az extension add`, verrà visualizzato un messaggio di errore che indica: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

A questo punto è possibile eseguire il comando seguente per creare la risorsa di Application Insights:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Esempio

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Per informazioni dettagliate su questo comando, vedere la [documentazione dell'interfaccia della riga di comando di Azure](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

Il `Update-AzApplicationInsights` comando di PowerShell attualmente non supporta la migrazione di una risorsa di Application Insights classica a basata sull'area di lavoro. Per creare una risorsa basata sull'area di lavoro con PowerShell, è possibile usare i modelli di Azure Resource Manager riportati di seguito ed eseguire la distribuzione con PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

#### <a name="template-file"></a>File modello

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>File dei parametri

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modifica dell'area di lavoro associata

Dopo aver creato una risorsa di Application Insights basata sull'area di lavoro, è possibile modificare l'area di lavoro Log Analytics associata.

Dall'interno del riquadro Application Insights risorsa selezionare **Proprietà**  >  **modifica area**di lavoro  >  **log Analytics aree di lavoro**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="access-mode"></a>Modalità di accesso

**Messaggio di errore:** *l'area di lavoro selezionata è configurata con modalità di accesso basata sull'area di lavoro. Alcune funzionalità APM potrebbero essere interessate. Selezionare un'altra area di lavoro o consentire l'accesso basato sulle risorse nelle impostazioni dell'area di lavoro. È possibile eseguire l'override di questo errore usando l'interfaccia* della riga di comando. 

Per il corretto funzionamento della risorsa Application Insights basata sull'area di lavoro, è necessario modificare la modalità di controllo di accesso dell'area di lavoro Log Analytics di destinazione nell'impostazione delle autorizzazioni per la **risorsa o l'area di lavoro** . Questa impostazione si trova nell'interfaccia utente dell'area di **Properties**lavoro log Analytics in  >  **modalità controllo di accesso**proprietà. Per istruzioni dettagliate, vedere la [log Analytics configurare le linee guida per la modalità controllo di accesso](../platform/manage-access.md#configure-access-control-mode). Se la modalità di controllo di accesso è impostata sull'impostazione esclusiva **Richiedi autorizzazioni** per l'area di lavoro, la migrazione tramite l'esperienza di migrazione del portale rimarrà bloccata.

Se non è possibile modificare la modalità di controllo di accesso per motivi di sicurezza per l'area di lavoro di destinazione corrente, è consigliabile creare una nuova area di lavoro Log Analytics da usare per la migrazione. 

### <a name="continuous-export"></a>Esportazione continua

**Messaggio di errore:** *prima di continuare è necessario disabilitare l'esportazione continua. Dopo la migrazione, usare le impostazioni di diagnostica per l'esportazione.* 

La funzionalità di esportazione continua legacy non è supportata per risorse basate su area di lavoro. Prima della migrazione è necessario disabilitare l'esportazione continua.

1. Dalla visualizzazione risorse Application Insights, sotto l'intestazione **Configura** selezionare **esportazione continua**.

    ![Voce di menu esportazione continua](./media/convert-classic-resource/continuous-export.png)

2. Selezionare **Disabilita**.

    ![Pulsante Disabilita esportazione continua](./media/convert-classic-resource/disable.png)

- Dopo aver selezionato Disabilita, è possibile tornare all'interfaccia utente di migrazione. Se nella pagina modifica esportazione continua viene richiesto se le impostazioni non verranno salvate, è possibile selezionare OK per questa richiesta poiché non è pertinente alla disabilitazione o all'abilitazione dell'esportazione continua.

- Dopo aver eseguito la migrazione della risorsa di Application Insights in base all'area di lavoro, è possibile usare le impostazioni di diagnostica per sostituire le funzionalità usate da Export continuo per fornire. Selezionare **impostazioni di diagnostica**  >  **Aggiungi impostazione di diagnostica** dall'interno della risorsa Application Insights. È possibile selezionare tutte le tabelle o un subset di tabelle da archiviare in un account di archiviazione o di cui eseguire lo streaming in Hub eventi di Azure. Per istruzioni dettagliate sulle impostazioni di diagnostica, vedere le [indicazioni sulle impostazioni di diagnostica di monitoraggio di Azure](../platform/diagnostic-settings.md).

### <a name="retention-settings"></a>Impostazioni di conservazione

**Messaggio di avviso:** le *impostazioni di conservazione Application Insights personalizzate non verranno applicate ai dati inviati all'area di lavoro. Sarà necessario riconfigurare separatamente questa operazione.*

Non è necessario apportare modifiche prima di eseguire la migrazione, ma questo messaggio consente di avvisare che le impostazioni di conservazione dei Application Insights correnti non sono impostate sul periodo di conservazione predefinito di 90 giorni. Questo messaggio di avviso indica che è possibile modificare le impostazioni di conservazione per l'area di lavoro di Log Analytics prima di eseguire la migrazione e iniziare a inserire nuovi dati. 

È possibile controllare le impostazioni di conservazione correnti per log Analytics in utilizzo **generale**  >  e la conservazione dei dati**sui costi stimati**  >  **Data Retention** dall'interfaccia utente di log Analytics. Questa impostazione influirà sul tempo durante il quale i nuovi dati inseriti vengono archiviati dopo la migrazione della risorsa Application Insights.

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare le metriche](../platform/metrics-charts.md)
* [Scrivere query di Analisi](../log-query/log-query-overview.md)
