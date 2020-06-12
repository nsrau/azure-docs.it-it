---
title: Visualizzare gli eventi del log attività in Monitoraggio di Azure
description: Visualizzare il log attività di Azure in Monitoraggio di Azure e recuperare gli eventi con PowerShell, l'interfaccia della riga di comando e l'API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834612"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visualizzare e recuperare gli eventi del log attività di Azure

Il [log attività di Azure](platform-logs-overview.md) fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Questo articolo fornisce informazioni dettagliate sui diversi metodi per la visualizzazione e il recupero degli eventi del log attività.

## <a name="azure-portal"></a>Portale di Azure
Visualizzare il log attività per tutte le risorse dal menu **Monitoraggio** nel portale di Azure. Visualizzare il log attività per una determinata risorsa dall'opzione **Log attività** del menu della risorsa.

![Visualizzare il log attività](./media/activity-logs-overview/view-activity-log.png)

È possibile filtrare gli eventi del log attività in base ai campi seguenti:

* **Intervallo di tempo**: ora di inizio e di fine degli eventi.
* **Categoria**: categoria dell'evento, come descritto in [Categorie nel log attività](activity-log-view.md#categories-in-the-activity-log).
* **Sottoscrizione** uno o più nomi di sottoscrizione di Azure.
* **Gruppo di risorse**: uno o più gruppi di risorse nelle sottoscrizioni selezionate.
* **Risorsa (nome)** : nome di una risorsa specifica.
* **Tipo di risorsa**: tipo di risorsa, ad esempio _Microsoft.Compute/virtualmachines_.
* **Nome operazione**: nome di un'operazione di Azure Resource Manager, ad esempio _Microsoft.SQL/servers/Write_.
* **Gravità**: Il livello di gravità dell'evento. I valori disponibili sono _Informativo_, _Avviso_, _Errore_, _Errore critico_.
* **Evento avviato da**: l'utente che ha eseguito l'operazione.
* **Apri ricerca**: apre la casella di ricerca di testo che cerca tale stringa in tutti i campi di tutti gli eventi.

## <a name="categories-in-the-activity-log"></a>Categorie nel log attività
Ogni evento nel log attività dispone di una categoria specifica descritta nella tabella seguente. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](activity-log-schema.md). 

| Category | Descrizione |
|:---|:---|
| Administrative | Contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Esempi di eventi amministrativi includono la _creazione di una macchina virtuale_ e l'_eliminazione di un gruppo di sicurezza di rete_.<br><br>Ogni azione eseguita da un utente o da un'applicazione che usa Resource Manager viene modellata come operazione su un particolare tipo di risorsa. Se l'operazione è di tipo _scrittura_, _eliminazione_ o _azione_, i record di avvio e riuscita o di non riuscita di tale operazione vengono registrati nella categoria Amministrativo. Gli eventi di tipo Amministrativo includono anche eventuali modifiche al controllo degli accessi in base al ruolo in una sottoscrizione. |
| Integrità del servizio | Contiene il record degli eventi imprevisti di integrità dei servizi che si sono verificati in Azure. Un esempio di evento di tipo Integrità dei servizi è _Tempo di inattività registrato in SQL Azure negli Stati Uniti orientali_. <br><br>Gli eventi di tipo Integrità dei servizi sono di cinque tipi: _Intervento necessario_, _Recupero assistito_, _Evento imprevisto_, _Manutenzione_, _Informazioni_ o _Sicurezza_. Questi eventi vengono creati solo se si dispone di una risorsa nella sottoscrizione che potrebbe essere interessata dall'evento.
| Integrità delle risorse | Contiene il record degli eventi di integrità delle risorse che si sono verificati nelle risorse di Azure. Un esempio di evento di tipo Integrità delle risorse è _Lo stato di integrità della macchina virtuale è cambiato in non disponibile_.<br><br>Gli eventi di tipo Integrità delle risorse possono rappresentare uno dei quattro stati di integrità: _Disponibile_, _Non disponibile_, _Danneggiato_ e _Sconosciuto_. Inoltre, gli eventi di tipo Integrità delle risorse possono essere classificati come _avviati dalla piattaforma_ o _avviati dall'utente_. |
| Avviso | Contiene il record delle attivazioni per gli avvisi di Azure. Un esempio di evento di tipo Avvisi è _% della CPU in myVM superiore a 80 negli ultimi 5 minuti_.|
| Autoscale | Contiene il record degli eventi correlati all'operazione del motore di ridimensionamento automatico in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio di un evento di tipo Scalabilità automatica è _Scalabilità automatica dell'azione di scalabilità verticale non riuscita_. |
| Recommendation | Contiene gli eventi di raccomandazione di Azure Advisor. |
| Sicurezza | Contiene il record degli avvisi generati dal Centro sicurezza di Azure. Un esempio di evento di tipo Sicurezza è _Esecuzione sospetta di un file a doppia estensione_. |
| Policy | Include i record di tutte le operazioni relative ad azioni effetto eseguite da Criteri di Azure. Esempi di eventi di tipo Criteri includono _Controlla_ e _Nega_. Ogni azione eseguita da Criteri viene modellata come operazione su una risorsa. |

## <a name="view-change-history"></a>Visualizzare la cronologia modifiche

Quando si esamina il log attività, può essere utile vedere quali modifiche sono state apportate durante l'intervallo di tempo dell'evento. È possibile visualizzare queste informazioni con **Cronologia modifiche**. Selezionare un evento nel log attività di cui si desidera visualizzare maggiori dettagli. Selezionare la scheda **Cronologia modifiche (anteprima)** per visualizzare eventuali modifiche associate a tale evento.

![Elenco di cronologia modifiche per un evento](media/activity-logs-overview/change-history-event.png)

Se sono presenti modifiche associate all'evento, viene visualizzato un elenco di modifiche che è possibile selezionare. Viene visualizzata la pagina **Cronologia modifiche (anteprima)** . In questa pagina sono elencate le modifiche apportate alla risorsa. Come si può notare dall'esempio seguente, è possibile vedere non solo che le dimensioni della macchina virtuale sono cambiate, ma anche la dimensione precedente alla modifica e la nuova dimensione.

![Pagina della cronologia delle modifiche che mostra le differenze](media/activity-logs-overview/change-history-event-details.png)

Per altre informazioni su Cronologia modifiche, vedere [Ottenere le modifiche delle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Usare il cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) per recuperare il log attività da PowerShell. Di seguito sono riportati alcuni esempi comuni.

> [!NOTE]
> `Get-AzLog` fornisce solo 15 giorni di cronologia. Usare il parametro **-MaxRecord** per eseguire una query sugli ultimi N eventi, oltre i 15 giorni. Per accedere agli eventi precedenti ai 15 giorni, usare l'API REST o l'SDK. Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.


Ottenere le voci di log create dopo una determinata data/ora:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di log in un intervallo di date e ore:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Ottenere le voci di log da un provider di risorse specifico in un intervallo di date e ore:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log con un chiamante specifico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Ottenere gli ultimi 1000 eventi:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
Usare [az monitor activity-log](../samples/cli-samples.md#view-activity-log-for-a-subscription) per recuperare il log attività dall'interfaccia della riga di comando. Di seguito sono riportati alcuni esempi comuni.


Visualizzare tutte le opzioni disponibili.

```azurecli
az monitor activity-log list -h
```

Ottenere le voci di log da un gruppo di risorse specifico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Ottenere le voci di log con un chiamante specifico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Ottenere i log in base al chiamante in un tipo di risorsa, in un intervallo di date:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Usare l'[API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/) per recuperare il log attività da un client REST. Di seguito sono riportati alcuni esempi comuni.

Ottenere i log attività con filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Ottenere i log attività con filtro e selezione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottenere i log attività con selezione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottenere i log attività senza filtro o selezione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Passaggi successivi

* Leggere una [panoramica dei log della piattaforma di Azure](platform-logs-overview.md)
* [Creare un'impostazione di diagnostica per inviare i log attività ad altre destinazioni](diagnostic-settings.md)
