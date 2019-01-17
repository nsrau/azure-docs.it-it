---
title: Panoramica dei log di diagnostica di Azure
description: Informazioni sui log di diagnostica di Azure e su come usarli per comprendere gli eventi che si verificano all'interno di una risorsa di Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 88cd6bac85a8f255c33c1834485b8f90a04b8ee4
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305384"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Raccogliere e usare i dati dei log dalle risorse di Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Quali sono i log di diagnostica di Monitoraggio di Azure

**I log di diagnostica di Monitoraggio di Azure** sono log generati da un servizio di Azure che fornisce dati completi e frequenti sul funzionamento di tale servizio. Monitoraggio di Azure rende disponibili due tipi di log di diagnostica:
* **I log del tenant**. Questi log provengono dai servizi a livello di tenant che esistono di fuori di una sottoscrizione ad Azure, ad esempio i log di Azure Active Directory.
* **I log di risorsa**. Questi log provengono dai servizi di Azure che distribuiscono le risorse all'interno di una sottoscrizione ad Azure, ad esempio i gruppi di sicurezza di rete o gli account di archiviazione.

    ![Log di diagnostica di risorsa e altri tipi di log ](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Il contenuto di questi log varia in base al servizio di Azure e al tipo di risorsa. I contatori delle regole di gruppo di sicurezza di rete e i controlli di insieme di credenziali delle chiavi, ad esempio, sono due tipi di log di diagnostica.

Questi log differiscono dal [log attività](activity-logs-overview.md). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione tramite Resource Manager, ad esempio, la creazione di una macchina virtuale o l'eliminazione di un'app per la logica. Il log attività è un log a livello di sottoscrizione. I log di diagnostica a livello di risorsa includono informazioni sulle operazioni eseguite all'interno della risorsa stessa, ad esempio, il recupero di un segreto da un insieme di credenziali delle chiavi.

Questi log differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest vengono compilati da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa, mentre i log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

Non tutti i servizi supportano il log di diagnostica descritto di seguito. [Questo articolo contiene una sezione che elenca i servizi supportati dai log di diagnostica](./../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Che cosa si può fare con i log di diagnostica
Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

![Posizione logica dei log di diagnostica](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Salvarli in un [**account di archiviazione**](../../azure-monitor/platform/archive-diagnostic-logs.md) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni tramite le **impostazioni di diagnostica di risorsa**.
* [Trasmetterli a **Hub eventi**](diagnostic-logs-stream-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con [Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md), qualora i dati vengano scritti subito in Log Analytics e non sia necessario scriverli prima nella risorsa di archiviazione.  

È possibile usare un account di archiviazione o uno spazio dei nomi di Hub eventi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!NOTE]
>  Non è al momento possibile archiviare i log del flusso di rete in un account di archiviazione che risiede dietro una rete virtuale protetta.

> [!WARNING]
> Il formato dei dati di log nell'account di archiviazione verrà modificato in JSON Lines dal 1° novembre 2018. [Vedere questo articolo per una descrizione dell'impatto e per informazioni su come aggiornare gli strumenti per gestire il nuovo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica

I log di diagnostica di risorsa sono configurati usando le impostazioni di diagnostica delle risorse. I log di diagnostica del tenant sono configurati usando un'impostazione di diagnostica del tenant. **Le impostazioni di diagnostica** per un servizio controllano:

* Destinazione dei log di diagnostica di risorsa e delle metriche, ad esempio un account di archiviazione, un Hub eventi e/o Log Analytics.
* Categorie di log e metriche da inviare.
* Periodo di tempo in cui ogni log di categoria deve essere mantenuto nell'account di archiviazione
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o Log Analytics, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.

È possibile configurare facilmente queste impostazioni usando le impostazioni di diagnostica nel portale, i comandi di Azure PowerShell e dell'interfaccia della riga di comando di Azure oppure tramite l'[API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Come abilitare la raccolta dei log di diagnostica

La raccolta dei log di diagnostica può essere abilitata [durante la creazione di una risorsa in un modello di Gestione risorse](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) o successivamente usando la pagina della risorsa nel portale. È possibile abilitare la raccolta anche in qualsiasi momento usando i comandi di Azure PowerShell o l'interfaccia della riga di comando oppure l'API REST di Monitoraggio di Azure.

> [!TIP]
> Le istruzioni riportate di seguito potrebbero non essere direttamente applicabili a tutte le risorse. Vedere i collegamenti dello schema nella parte inferiore della pagina per comprendere i possibili passaggi speciali per determinati tipi di risorse.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Abilitare la raccolta dei log di diagnostica nel portale

È possibile abilitare la raccolta dei log di diagnostica di risorsa nel portale di Azure dopo avere creato una risorsa passando a una risorsa specifica o usando Monitoraggio di Azure. Per abilitare questa raccolta tramite Monitoraggio di Azure:

1. Nel [portale di Azure](https://portal.azure.com) passare a Monitoraggio di Azure e fare clic su **Impostazioni di diagnostica**

    ![Sezione relativa al monitoraggio di Monitoraggio di Azure](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Facoltativamente filtrare l'elenco in base al tipo di risorsa o al gruppo di risorse, quindi fare clic sulla risorsa per cui si vuole specificare un'impostazione di diagnostica.

3. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   Se esistono già impostazioni sulla risorsa, verrò visualizzato un elenco di impostazioni già configurate per questa risorsa. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Assegnare un nome all'impostazione, selezionare le caselle per ogni destinazione a cui si vuole inviare i dati e configurare la risorsa che verrà usata per ogni destinazione. Facoltativamente, impostare un numero di giorni per la conservazione di questi log usando i dispositivi di scorrimento **Conservazione (giorni)** (applicabile solo alla destinazione dell'account di archiviazione). Se il valore di conservazione è zero giorni, i log vengono conservati all'infinito.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log di diagnostica vengono inviati alle destinazioni specifiche non appena vengono generati nuovi dati di eventi.

Impostazioni di diagnostica del tenant possono essere configurate solo nel pannello del portale per il servizio tenant. Queste impostazioni non vengono visualizzate nel pannello impostazioni di diagnostica del Monitoraggio di Azure. Ad esempio, i log di controllo di Azure Active Directory sono configurati facendo clic sui **Esporta impostazioni dati** nel pannello log di controllo.

![Impostazioni di diagnostica di Azure Active Directory](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

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

Al momento non è possibile configurare le impostazioni di diagnostica di tenant usando Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'interfaccia della riga di comando di Azure

Per abilitare la raccolta dei log di diagnostica delle risorse tramite l'interfaccia della riga di comando di Azure, usare il comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

L'argomento `--resource-group` è obbligatorio solo se `--storage-account` non è un ID oggetto.

Per abilitare lo streaming dei log di diagnostica a un Hub eventi:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

L'ID regola è una stringa nel formato seguente: `{Service Bus resource ID}/authorizationrules/{key name}`.

Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

L'argomento `--resource-group` è obbligatorio solo se `--workspace` non è un ID oggetto

Con qualsiasi comando, è possibile aggiungere altre categorie al log di diagnostica aggiungendo dizionari alla matrice JSON passata come parametro `--logs`. È possibile combinare i parametri `--storage-account`, `--event-hub` e `--workspace` per abilitare più opzioni di output.

Al momento non è possibile configurare le impostazioni di diagnostica di tenant usando Interfaccia della riga di comando.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Abilitare la raccolta dei log di diagnostica di risorsa con l'API REST

Per modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure, vedere [questo documento](https://docs.microsoft.com/rest/api/monitor/).

Al momento non è possibile configurare le impostazioni di diagnostica di tenant usando API REST di Monitoraggio di Azure.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gestire le impostazioni di diagnostica di risorsa nel portale

Verificare che tutte le risorse siano configurate con le impostazioni di diagnostica. Passare a **Monitoraggio** nel portale e aprire **Impostazioni di diagnostica**.

![Pannello Log di diagnostica nel portale](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Potrebbe essere necessario fare clic su "Tutti i servizi" per individuare la sezione Monitoraggio.

Qui è possibile visualizzare e filtrare tutte le risorse che supportano le impostazioni di diagnostica per verificare se la diagnostica è abilitata. È possibile anche eseguire il drill-down per verificare se sono definite più impostazioni per una risorsa e controllare in quale account di archiviazione, spazio dei nomi di Hub eventi e/o area di lavoro di Log Analytics vengono trasmessi i dati.

![Risultati di Log di diagnostica nel portale](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

Aggiungendo un'impostazione di diagnostica verrà visualizzata la vista Impostazioni di diagnostica in cui è possibile abilitare, disabilitare o modificare le impostazioni di diagnostica per la risorsa selezionata.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Servizi, categorie e schemi supportati per i log di diagnostica

[Vedere questo articolo](../../azure-monitor/platform/tutorial-dashboards.md) per un elenco completo di servizi, categorie di log e schemi supportati usati da questi servizi.

## <a name="next-steps"></a>Passaggi successivi

* [Trasmettere log di diagnostica di Azure a **Hub eventi**](diagnostic-logs-stream-event-hubs.md)
* [Modificare le impostazioni di diagnostica di risorsa usando l'API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
