---
title: Visualizzare gli eventi del log attività di Azure in Monitoraggio di AzureView Azure Activity log events in Azure Monitor
description: Visualizzare il log attività di Azure in Monitoraggio di Azure e recuperare con PowerShell, l'interfaccia della riga di comando e l'API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397314"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visualizzare e recuperare gli eventi del log attività di AzureView and retrieve Azure Activity log events

Il log attività di Azure fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione che si sono verificati in Azure.The [Azure Activity Log](platform-logs-overview.md) provides insight into subscription-level events that have occurred in Azure. In questo articolo vengono fornite informazioni dettagliate sui diversi metodi per la visualizzazione e il recupero degli eventi del log attività.

## <a name="azure-portal"></a>Portale di Azure
Visualizzare il log attività per tutte le risorse dal menu Monitoraggio nel portale di Azure.View the Activity Log for all resources from the **Monitor** menu in the Azure portal. Visualizzare il log attività per una determinata risorsa dall'opzione **Log attività** nel menu della risorsa.

![Visualizza registro attività](./media/activity-logs-overview/view-activity-log.png)

È possibile filtrare gli eventi del Registro attività in base ai seguenti campi:

* **Timespan**: L'ora di inizio e di fine per gli eventi.
* **Categoria**: La categoria di eventi come descritto in [Categorie nel Registro attività](activity-log-view.md#categories-in-the-activity-log).
* **Sottoscrizione:** uno o più nomi di sottoscrizione di AzureSubscription : One or more Azure subscription names.
* Gruppo di **risorse:** uno o più gruppi di risorse all'interno delle sottoscrizioni selezionate.
* **Risorsa (nome)**: - Il nome di una risorsa specifica.
* **Tipo di risorsa:** il tipo di risorsa, ad esempio _Microsoft.Compute/virtualmachines_.
* **Nome dell'operazione:** nome di un'operazione di Azure Resource Manager, ad esempio _Microsoft.SQL/servers/Write_.
* **Gravità**: Il livello di gravità dell'evento. I valori disponibili sono _Informativo_, _Avviso_, _Errore_, _Critico_.
* **Evento avviato da**: l'utente che ha eseguito l'operazione.
* **Apri ricerca**: Apri casella di ricerca di testo che cerca la stringa in tutti i campi in tutti gli eventi.

## <a name="categories-in-the-activity-log"></a>Categorie nel registro attività
Ogni evento nel Log attività ha una particolare categoria descritta nella tabella seguente. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](activity-log-schema.md). 

| Category | Descrizione |
|:---|:---|
| Administrative | Contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Esempi di eventi amministrativi includono _la creazione_ di macchina virtuale ed _l'eliminazione_del gruppo di sicurezza di rete.<br><br>Ogni azione eseguita da un utente o da un'applicazione tramite Resource Manager viene modellata come un'operazione su un particolare tipo di risorsa. Se il tipo di operazione è _Write_, _Delete_o _Action_, i record di avvio e di esito positivo o negativo di tale operazione vengono registrati nella categoria amministrativa . Gli eventi amministrativi includono anche le modifiche al controllo degli accessi in base al ruolo in una sottoscrizione. |
| Integrità del servizio | Contiene il record di tutti gli eventi imprevisti di integrità del servizio che si sono verificati in Azure. Un esempio di evento di integrità del servizio _SQL Azure negli Stati Uniti orientali sta riscontrando tempi di inattività._ <br><br>Gli eventi di salute del servizio sono disponibili in sei varietà: _Azione richiesta_, _Recupero Assistito_, _Incidente_, _Manutenzione_, _Informazioni_o _Sicurezza_. Questi eventi vengono creati solo se si dispone di una risorsa nella sottoscrizione che potrebbe essere interessata dall'evento.
| Integrità delle risorse | Contiene il record di tutti gli eventi di integrità delle risorse che si sono verificati per le risorse di Azure. Un esempio di evento Integrità risorse è che lo stato di integrità della macchina virtuale è stato modificato in _non disponibile._<br><br>Gli eventi Integrità risorse possono rappresentare uno dei quattro stati _di integrità: Disponibile_, _Non disponibile_, _Degradato_e _Sconosciuto_. Inoltre, gli eventi di integrità delle risorse possono essere classificati come _avviati_ dalla piattaforma o _avviati dall'utente_. |
| Avviso | Contiene il record delle attivazioni per gli avvisi di Azure. Un esempio di evento Alert è _che la percentuale di CPU su myVM è stata superiore a 80 negli ultimi 5 minuti._|
| Autoscale | Contiene il record di tutti gli eventi correlati al funzionamento del motore di scalabilità automatica in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio di evento di scalabilità automatica è _l'azione scalabilità verticale automatica non riuscita._ |
| Recommendation | Contiene eventi di raccomandazione da Azure Advisor. |
| Security | Contiene il record di tutti gli avvisi generati dal Centro sicurezza di Azure. Un esempio di evento di protezione è _il file di doppia estensione sospetto eseguito._ |
| Policy | Contiene i record di tutte le operazioni di effetto eseguite da Criteri di Azure.Contains records of all effect action operations performed by Azure Policy. Esempi di eventi Policy includono _Audit_ e _Deny_. Ogni azione eseguita da Criteri viene modellata come operazione su una risorsa. |

## <a name="view-change-history"></a>Visualizzare la cronologia modifiche

Quando si esamina il registro attività, può aiutare a vedere quali modifiche si sono verificate durante l'evento. È possibile visualizzare queste informazioni con **Cronologia modifiche**. Selezionare un evento dal Registro attività in cui si desidera esaminare più a fondo. Selezionare la scheda **Cronologia modifiche (anteprima)** per visualizzare le modifiche associate all'evento.

![Elenco della cronologia delle modifiche per un evento](media/activity-logs-overview/change-history-event.png)

Se all'evento sono presenti modifiche associate, verrà visualizzato un elenco di modifiche che è possibile selezionare. Si apre la pagina **Cronologia modifiche (anteprima).** In questa pagina vengono visualizzate le modifiche apportate alla risorsa. Come si può vedere dall'esempio seguente, siamo in grado di vedere non solo che la macchina virtuale ha cambiato dimensioni, ma quali dimensioni della macchina virtuale precedente erano prima della modifica e a cosa è stata modificata.

![Pagina Cronologia modifiche che mostra le differenze](media/activity-logs-overview/change-history-event-details.png)

Per ulteriori informazioni sulla cronologia delle modifiche, vedere [Ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Utilizzare il cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) per recuperare il log attività da PowerShell. Di seguito sono riportati alcuni esempi comuni.

> [!NOTE]
> `Get-AzLog` fornisce solo 15 giorni di cronologia. Utilizzare il parametro **-MaxEvents** per eseguire una query sugli ultimi N eventi oltre i 15 giorni. Per accedere agli eventi anteriori a 15 giorni, usare l'API REST o l'SDK. Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.


Ottenere le voci di registro create dopo una determinata data e ora:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di registro tra un intervallo di data e ora:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Ottenere voci di log da un provider di risorse specifico tra un intervallo di data e ora:Get log entries from a specific resource provider between a date time range:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di registro con un chiamante specifico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Ottieni gli ultimi 1000 eventi:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Utilizzare [az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription) per recuperare il log attività dall'interfaccia della riga di comando. Di seguito sono riportati alcuni esempi comuni.


Visualizza tutte le opzioni disponibili.

```azurecli
az monitor activity-log list -h
```

Ottenere le voci di log da un gruppo di risorse specifico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Ottenere le voci di registro con un chiamante specifico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Ottenere i log dal chiamante in un tipo di risorsa, all'interno di un intervallo di date:Get logs by caller on a resource type, within a date range:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Usare [l'API REST](https://docs.microsoft.com/rest/api/monitor/) di Monitoraggio di Azure per recuperare il log attività da un client REST. Di seguito sono riportati alcuni esempi comuni.

Ottenere i registri attività con il filtro:Get Activity Logs with filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Ottenere i registri attività con il filtro e selezionare:Get Activity Logs with filter and select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottieni registri attività con selezione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottenere i registri attività senza filtro o selezionare:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Passaggi successivi

* [Leggi una panoramica dei log della piattaforma](platform-logs-overview.md)
* [Creare l'impostazione di diagnostica per inviare i log attività ad altre destinazioniCreate diagnostic setting to send Activity logs to other destinations](diagnostic-settings.md)
