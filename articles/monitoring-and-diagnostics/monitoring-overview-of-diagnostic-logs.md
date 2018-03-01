---
title: Panoramica dei log di diagnostica di Azure | Microsoft Docs
description: Informazioni sui log di diagnostica di Azure e su come usarli per comprendere gli eventi che si verificano all'interno di una risorsa di Azure.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: df20e174abb9960ad378221008ac7261fd0582f1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Raccogliere e usare i dati dei log dalle risorse di Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Definizione di log di diagnostica di risorse di Azure
I **log di diagnostica a livello di risorsa di Azure** sono log generati da una risorsa che presentano dati completi e frequenti sull'attività di tale risorsa. Il contenuto di questi log varia in base al tipo di risorsa. I contatori delle regole di gruppo di sicurezza di rete e i controlli di insieme di credenziali delle chiavi, ad esempio, sono due categorie di log di risorsa.

I log di diagnostica a livello di risorsa differiscono dal [Log attività](monitoring-overview-activity-logs.md). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione tramite Resource Manager, ad esempio, la creazione di una macchina virtuale o l'eliminazione di un'app per la logica. Il log attività è un log a livello di sottoscrizione. I log di diagnostica a livello di risorsa includono informazioni sulle operazioni eseguite all'interno della risorsa stessa, ad esempio, il recupero di un segreto da un insieme di credenziali delle chiavi.

I log di diagnostica a livello di risorsa differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest vengono compilati da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa, mentre i log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

Non tutte le risorse supportano il nuovo tipo di log di diagnostica di risorsa descritto di seguito. Questo articolo contiene una sezione che elenca i tipi di risorsa che supportano i nuovi log di diagnostica a livello di risorsa.

![Log di diagnostica di risorsa e altri tipi di log ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Possibili operazioni con i log di diagnostica a livello di risorsa
Ecco alcune delle attività che è possibile eseguire con i log di diagnostica di risorsa:

![Posizionamento logico dei log di diagnostica di risorsa](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Salvarli in un [**account di archiviazione**](monitoring-archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni tramite le **impostazioni di diagnostica di risorsa**.
* [Trasmetterli a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

È possibile usare un account di archiviazione o uno spazio dei nomi di Hub eventi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="resource-diagnostic-settings"></a>Impostazioni di diagnostica di risorsa
I log di diagnostica per le non di calcolo vengono configurati tramite le impostazioni di diagnostica di risorsa. Le **impostazioni di diagnostica di risorsa** permettono di controllare quanto segue:

* Destinazione dei log di diagnostica di risorsa e delle metriche, ad esempio un account di archiviazione, un Hub eventi e/o OMS Log Analytics.
* Categorie di log e metriche da inviare.
* Periodo di tempo in cui ogni log di categoria deve essere mantenuto nell'account di archiviazione
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o OMS, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente.

È possibile configurare facilmente queste impostazioni usando le impostazioni diagnostica di risorsa nel portale di Azure, i comandi di Azure PowerShell e dell'interfaccia della riga di comando di Azure oppure l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Le metriche e i log di diagnostica per il livello di sistema operativo guest delle risorse di calcolo, ad esempio le macchine virtuali o Service Fabric, usano un [meccanismo distinto per la configurazione e la selezione degli output](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Come abilitare la raccolta dei log di diagnostica di risorsa
La raccolta dei log di diagnostica di risorsa può essere abilitata [durante la creazione di una risorsa in un modello di Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) o successivamente usando la pagina della risorsa nel portale. È possibile abilitare la raccolta anche in qualsiasi momento usando i comandi di Azure PowerShell o l'interfaccia della riga di comando oppure l'API REST di Monitoraggio di Azure.

> [!TIP]
> Le istruzioni riportate di seguito potrebbero non essere direttamente applicabili a tutte le risorse. Vedere i collegamenti dello schema nella parte inferiore della pagina per comprendere i possibili passaggi speciali per determinati tipi di risorse.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Abilitare la raccolta dei log di diagnostica di risorsa nel portale
È possibile abilitare la raccolta dei log di diagnostica di risorsa nel portale di Azure dopo avere creato una risorsa passando a una risorsa specifica o usando Monitoraggio di Azure. Per abilitare questa raccolta tramite Monitoraggio di Azure:

1. Nel [portale di Azure](http://portal.azure.com) passare a Monitoraggio di Azure e fare clic su **Impostazioni di diagnostica**

    ![Sezione relativa al monitoraggio di Monitoraggio di Azure](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Facoltativamente filtrare l'elenco in base al tipo di risorsa o al gruppo di risorse, quindi fare clic sulla risorsa per cui si vuole specificare un'impostazione di diagnostica.

3. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Se esistono già impostazioni sulla risorsa, verrò visualizzato un elenco di impostazioni già configurate per questa risorsa. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Assegnare un nome all'impostazione, selezionare le caselle per ogni destinazione a cui si vuole inviare i dati e configurare la risorsa che verrà usata per ogni destinazione. Facoltativamente, impostare un numero di giorni per la conservazione di questi log usando i dispositivi di scorrimento **Conservazione (giorni)** (applicabile solo alla destinazione dell'account di archiviazione). Se il valore di conservazione è zero giorni, i log vengono conservati all'infinito.
   
   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log di diagnostica vengono inviati alle destinazioni specifiche non appena vengono generati nuovi dati di eventi.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Abilitare la raccolta dei log di diagnostica di risorsa con PowerShell
Per abilitare la raccolta dei log di diagnostica di risorsa tramite Azure PowerShell, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare lo streaming dei log di diagnostica a un Hub eventi, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{Service Bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'interfaccia della riga di comando
Per abilitare la raccolta dei log di diagnostica di risorsa tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

Per abilitare lo streaming dei log di diagnostica a un Hub eventi, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L'ID regola del bus di servizio è una stringa nel formato seguente: `{Service Bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'API REST
Per modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure, vedere [questo documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gestire le impostazioni di diagnostica di risorsa nel portale
Verificare che tutte le risorse siano configurate con le impostazioni di diagnostica. Passare a **Monitoraggio** nel portale e aprire **Impostazioni di diagnostica**.

![Pannello Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Potrebbe essere necessario fare clic su "Tutti i servizi" per individuare la sezione Monitoraggio.

Qui è possibile visualizzare e filtrare tutte le risorse che supportano le impostazioni di diagnostica per verificare se la diagnostica è abilitata. È possibile anche eseguire il drill-down per verificare se sono definite più impostazioni per una risorsa e controllare in quale account di archiviazione, spazio dei nomi di Hub eventi e/o area di lavoro di Log Analytics vengono trasmessi i dati.

![Risultati di Log di diagnostica nel portale](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Aggiungendo un'impostazione di diagnostica verrà visualizzata la vista Impostazioni di diagnostica in cui è possibile abilitare, disabilitare o modificare le impostazioni di diagnostica per la risorsa selezionata.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Servizi, categorie e schemi supportati per i log di diagnostica di risorsa
[Vedere questo articolo](monitoring-diagnostic-logs-schema.md) per un elenco completo di servizi, categorie di log e schemi supportati usati da questi servizi.

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
