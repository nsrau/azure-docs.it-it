---
title: Visualizzare gli eventi del log attività di Azure in monitoraggio di Azure
description: Visualizzare il log attività di Azure in monitoraggio di Azure e recuperare con PowerShell, l'interfaccia della riga di comando e l'API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 4ea29888d4dcf589e3e5d4dfe594f5f4bff2287e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559968"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visualizzare e recuperare gli eventi del log attività di Azure

Il [log attività di Azure](platform-logs-overview.md) fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Questo articolo fornisce informazioni dettagliate sui diversi metodi per la visualizzazione e il recupero degli eventi del log attività.

## <a name="azure-portal"></a>Portale di Azure
Visualizzare il log attività per tutte le risorse dal menu **monitoraggio** nel portale di Azure. Visualizzare il log attività per una determinata risorsa dall'opzione **log attività** nel menu di tale risorsa.

![Visualizzare il log attività](./media/activity-logs-overview/view-activity-log.png)

È possibile filtrare gli eventi del log attività in base ai campi seguenti:

* **TimeSpan**: ora di inizio e di fine per gli eventi.
* **Category**: la categoria di eventi, come descritto in [categorie nel log attività](activity-log-view.md#categories-in-the-activity-log).
* **Sottoscrizione**: uno o più nomi di sottoscrizione di Azure.
* **Gruppo di risorse**: uno o più gruppi di risorse all'interno delle sottoscrizioni selezionate.
* **Resource (Name)**: nome di una risorsa specifica.
* **Tipo di risorsa**: tipo di risorsa, ad esempio _Microsoft. Compute/VirtualMachines_.
* **Nome operazione** : nome di un'operazione di Azure Resource Manager, ad esempio _Microsoft. SQL/Servers/Write_.
* **Gravità**: livello di gravità dell'evento. I valori disponibili sono _informazioni_, _avviso_, _errore_, _critico_.
* **Evento avviato da**: l'utente che ha eseguito l'operazione.
* **Apri ricerca**: apre la casella di ricerca di testo che cerca la stringa in tutti i campi in tutti gli eventi.

## <a name="categories-in-the-activity-log"></a>Categorie nel log attività
Ogni evento nel log attività dispone di una categoria specifica descritta nella tabella seguente. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](activity-log-schema.md). 

| Categoria | Descrizione |
|:---|:---|
| Administrative | Contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Gestione risorse. Esempi di eventi amministrativi includono _creare una macchina virtuale_ ed _eliminare un gruppo di sicurezza di rete_.<br><br>Ogni azione eseguita da un utente o da un'applicazione che usa Gestione risorse viene modellata come operazione su un particolare tipo di risorsa. Se il tipo di operazione è _Write_, _Delete_o _Action_, i record di avvio e di esito positivo o negativo di tale operazione vengono registrati nella categoria amministrativa. Gli eventi amministrativi includono anche eventuali modifiche al controllo degli accessi in base al ruolo in una sottoscrizione. |
| Integrità del servizio | Contiene il record degli eventi imprevisti di integrità del servizio che si sono verificati in Azure. Un esempio di evento di integrità del servizio _SQL Azure negli Stati Uniti orientali sta riscontrando tempi di inattività_. <br><br>Gli eventi di integrità del servizio sono disponibili in sei varietà: _azione richiesta_, _ripristino assistito_, eventi _imprevisti_, _manutenzione_, _informazioni_o _sicurezza_. Questi eventi vengono creati solo se si dispone di una risorsa nella sottoscrizione che potrebbe essere interessata dall'evento.
| Integrità delle risorse | Contiene il record degli eventi di integrità delle risorse che si sono verificati nelle risorse di Azure. Un esempio di evento Integrità risorse è _lo stato di integrità della macchina virtuale modificato in non disponibile_.<br><br>Gli eventi Integrità risorse possono rappresentare uno dei quattro stati di integrità seguenti: _disponibile_, non _disponibile_, _danneggiato_e _sconosciuto_. Inoltre, gli eventi Integrità risorse possono essere categorizzati come _avviati dalla piattaforma_ o _avviati dall'utente_. |
| Avviso | Contiene il record delle attivazioni per gli avvisi di Azure. Un esempio di evento di avviso è _la percentuale della CPU su myVM è stata superata 80 per gli ultimi 5 minuti_.|
| Autoscale | Contiene il record degli eventi correlati all'operazione del motore di ridimensionamento automatico in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio di evento di scalabilità automatica è l'azione di scalabilità _orizzontale automatica non riuscita_. |
| Recommendation | Contiene gli eventi di raccomandazione da Azure Advisor. |
| Sicurezza | Contiene il record degli avvisi generati dal centro sicurezza di Azure. Un esempio di evento di sicurezza è un _file di estensione doppio sospetto eseguito_. |
| Criteri di | Contiene i record di tutte le operazioni di azione effetto eseguite da criteri di Azure. Esempi di eventi dei criteri includono _Audit_ e _Deny_. Ogni azione eseguita da Criteri viene modellata come operazione su una risorsa. |

## <a name="view-change-history"></a>Visualizzare la cronologia modifiche

Quando si esamina il log attività, può essere utile vedere quali modifiche sono state apportate durante l'intervallo di tempo dell'evento. È possibile visualizzare queste informazioni con la **cronologia delle modifiche**. Selezionare un evento nel log attività in cui si desidera eseguire una ricerca più approfondita. Selezionare la scheda **cronologia modifiche (anteprima)** per visualizzare eventuali modifiche associate a tale evento.

![Elenco cronologia modifiche per un evento](media/activity-logs-overview/change-history-event.png)

Se sono presenti modifiche associate all'evento, verrà visualizzato un elenco di modifiche che è possibile selezionare. Verrà visualizzata la pagina **cronologia modifiche (anteprima)** . In questa pagina vengono visualizzate le modifiche apportate alla risorsa. Come si può notare dall'esempio seguente, è possibile vedere non solo che le dimensioni della macchina virtuale sono cambiate, ma la dimensione precedente della macchina virtuale è precedente alla modifica e a cosa è stato modificato.

![Pagina della cronologia delle modifiche che mostra le differenze](media/activity-logs-overview/change-history-event-details.png)

Per altre informazioni sulla cronologia delle modifiche, vedere [ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Usare il cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) per recuperare il log attività da PowerShell. Di seguito sono riportati alcuni esempi comuni.

> [!NOTE]
> `Get-AzLog` fornisce solo 15 giorni di cronologia. Usare il parametro **-MaxRecord** per eseguire una query sugli ultimi N eventi oltre i 15 giorni. Per accedere agli eventi più vecchi di 15 giorni, usare l'API REST o l'SDK. Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.


Ottenere le voci di log create dopo una determinata data/ora:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di log tra un intervallo di data e ora:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Ottenere le voci di log da un provider di risorse specifico tra un intervallo di tempo di data:

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
Usare [AZ monitor Activity-log](cli-samples.md#view-activity-log-for-a-subscription) per recuperare il log attività dall'interfaccia della riga di comando. Di seguito sono riportati alcuni esempi comuni.


Visualizza tutte le opzioni disponibili.

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

Ottenere i log in base al chiamante in un tipo di risorsa, entro un intervallo di date:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Usare l' [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/) per recuperare il log attività da un client REST. Di seguito sono riportati alcuni esempi comuni.

Ottenere i log attività con Filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Ottenere i log attività con Filter e selezionare:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottenere i log attività con SELECT:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Ottenere i log attività senza filtro oppure selezionare:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Passaggi successivi

* [Leggi una panoramica dei log della piattaforma](platform-logs-overview.md)
* [Creare un'impostazione di diagnostica per inviare i log attività ad altre destinazioni](diagnostic-settings.md)
