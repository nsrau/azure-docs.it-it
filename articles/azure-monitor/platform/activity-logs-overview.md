---
title: Panoramica del log attività di Azure
description: Informazioni sul log attività di Azure e su come usarlo per comprendere gli eventi che si verificano all'interno della sottoscrizione di Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 7f454cb0979fb2379490d4b275b14afee8706f0f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715376"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorare l'attività di sottoscrizione con il log attività di Azure

Il **log attività di Azure** è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Il log attività era noto in precedenza come "log di controllo" o "log operativo", perché la categoria amministrativa segnala eventi del piano di controllo per le sottoscrizioni. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include le operazioni di lettura (GET) o quelle per le risorse che usano il modello classico/"RDFE".

![Log attività o altri tipi di log ](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

Figura 1: Log attività o altri tipi di log

Il log attività è diverso dal [log di diagnostica](diagnostic-logs-overview.md). I log attività contengono dati relativi alle operazioni su una risorsa esterna (il "piano di controllo"). I log di diagnostica vengono generati da una risorsa e contengono informazioni sul funzionamento di tale risorsa (il "piano dati").

> [!WARNING]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. È possibile esaminare il log delle operazioni in una sezione distinta del portale.
>
>

Per recuperare eventi dal log attività è possibile usare il portale di Azure, l'interfaccia della riga di comando, i cmdlet di PowerShell e l'API REST di Monitoraggio di Azure.

> [!NOTE]
> [I nuovi avvisi](../../azure-monitor/platform/alerts-overview.md) offrono un'esperienza ottimizzata per la creazione e la gestione delle regole degli avvisi del log attività.  [Altre informazioni](../../azure-monitor/platform/alerts-activity-log.md)


## <a name="categories-in-the-activity-log"></a>Categorie nel log attività
Il log attività contiene diverse categorie di dati. Per informazioni dettagliate sugli schemi di queste categorie, [vedere questo articolo](../../azure-monitor/platform/activity-log-schema.md). incluse le seguenti:
* **Amministrativa**: questa categoria contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Tra gli esempi dei tipi di eventi visualizzati in questa categoria sono inclusi "create virtual machine" e "delete network security group". Ogni azione eseguita da un utente o da un'applicazione usando Resource Manager viene modellata come operazione in un determinato tipo di risorsa. Se l'operazione è di tipo scrittura, eliminazione o azione, i record di avvio e riuscita o di non riuscita di tale operazione vengono registrati nella categoria amministrativa. La categoria amministrativa include anche eventuali modifiche al controllo degli accessi in base al ruolo in una sottoscrizione.
* **Integrità del servizio**: questa categoria contiene il record degli eventi imprevisti di integrità del servizio che si sono verificati in Azure. Un esempio del tipo di evento visualizzato in questa categoria è "SQL Azure in East US is experiencing downtime". Gli eventi di integrità dei servizi sono di cinque tipi: Azione necessaria, Recupero assistito, Evento imprevisto, Manutenzione, Informazioni e Sicurezza. Vengono visualizzati solo se una risorsa della sottoscrizione è interessata dall'evento.
* **Integrità risorse**: questa categoria contiene il record degli eventi di integrità delle risorse che si sono verificati nelle risorse di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "Stato di integrità della macchina virtuale modificato su non disponibile". Gli eventi di integrità delle risorse possono rappresentare uno dei quattro stati di integrità: Disponibile, Non disponibile, Danneggiato e Sconosciuto. Inoltre, gli eventi di integrità delle risorse possono essere classificati come avviati dalla piattaforma o avviati dall'utente.
* **Avviso**: questa categoria contiene il record di tutte le attivazioni degli avvisi di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "CPU % on myVM has been over 80 for the past 5 minutes". In diversi sistemi Azure il concetto di avviso prevede la possibilità di definire una regola di qualche tipo e di ricevere una notifica quando le condizioni corrispondono a tale regola. Ogni volta che un tipo di avviso di Azure supportato viene "attivato" o vengono soddisfatte le condizioni per generare una notifica, viene anche eseguito il push di un record dell'attivazione in questa categoria del log attività.
* **Ridimensionamento automatico**: questa categoria contiene il record degli eventi correlati all'operazione del motore di ridimensionamento automatico in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio del tipo di evento visualizzato in questa categoria è "Autoscale scale up action failed". Con il ridimensionamento automatico è possibile aumentare o ridurre automaticamente il numero di istanze in un tipo di risorsa supportato in base all'ora del giorno e/o ai dati di caricamento (metrica) usando un'impostazione di ridimensionamento automatico. Quando vengono soddisfatte le condizioni per aumentare o ridurre le prestazioni, gli eventi di avvio riusciti o quelli non riusciti vengono registrati in questa categoria.
* **Raccomandazione**: questa categoria contiene gli eventi di raccomandazione di Azure Advisor.
* **Sicurezza**: questa categoria contiene il record degli avvisi generati dal Centro sicurezza di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "Suspicious double extension file executed".
* **Criteri**: questa categoria non contiene eventi, è riservata per utilizzi futuri. 

## <a name="event-schema-per-category"></a>Schema di eventi per categoria
[Vedere questo articolo per comprendere lo schema di eventi del log attività per categoria.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Che cosa si può fare con il log attività
Ecco alcune delle attività che è possibile eseguire con il log attività:

![Log attività di Azure](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Eseguire query e visualizzarlo nel **portale di Azure**.
* [Creare un avviso per un evento del log attività](../../azure-monitor/platform/activity-log-alerts.md)
* [Trasmetterlo a un **hub eventi**](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
* Analizzarlo in Power BI usando il [**pacchetto di contenuto di Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Salvarlo in un **account di archiviazione** per fini di archiviazione o di ispezione manuale](../../azure-monitor/platform/archive-activity-log.md). È possibile specificare il tempo di conservazione in giorni usando il **profilo di log**.
* Eseguire query tramite l'API REST, i cmdlet di PowerShell o l'interfaccia della riga di comando.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Eseguire query sul log attività nel portale di Azure
Nel portale di Azure è possibile visualizzare il log attività in diverse posizioni:
* Il **log attività**, a cui è possibile accedere cercando Log attività in **Tutti i servizi** nel riquadro di spostamento sinistro.
* **Monitoraggio** viene visualizzato per impostazione predefinita nel riquadro di spostamento sinistro. Il log attività è una sezione di Monitoraggio di Azure.
* La maggior parte delle **risorse**, ad esempio il pannello di configurazione di una macchina virtuale. Il log attività è una delle sezioni nella maggior parte di questi pannelli delle risorse e, facendovi clic, vengono automaticamente filtrati gli eventi correlati alla risorsa specifica.

Nel portale di Azure è possibile filtrare il log attività in base a questi campi:
* Intervallo di tempo: ora di inizio e di fine degli eventi.
* Categoria: la categoria di eventi, come descritto sopra.
* Sottoscrizione: uno o più nomi di sottoscrizione di Azure.
* Gruppo di risorse: uno o più gruppi di risorse in tali sottoscrizioni.
* Risorsa (nome): nome di una risorsa specifica.
* Tipo di risorsa: tipo di risorsa, ad esempio Microsoft.Compute/virtualmachines.
* Nome operazione: nome di un'operazione di Azure Resource Manager, ad esempio Microsoft.SQL/servers/Write.
* Gravità: livello di gravità dell'evento (informativo, avviso, errore, critico).
* Evento avviato da: "chiamante" o utente che ha eseguito l'operazione.
* Apri ricerca: casella di ricerca di testo aperta che cerca tale stringa in tutti i campi di tutti gli eventi.

Dopo aver definito un set di filtri è anche possibile aggiungere una query al dashboard di Azure per avere sempre sotto controllo eventi specifici.

Per eseguire operazioni più avanzate è possibile fare clic sull'icona **Log**, che visualizza i dati del Log attività nella [soluzione Analisi log attività di Log Analytics](../../azure-monitor/platform/collect-activity-logs.md). Il pannello Log attività offre un'esperienza di filtro/esplorazione di base con i log, ma Log Analytics consente di trasformare tramite Pivot, eseguire query e visualizzare i dati in modo più dettagliato.

## <a name="export-the-activity-log-with-a-log-profile"></a>Esportare il log attività con un profilo di log
Un **profilo di log** controlla la modalità di esportazione del log attività. Un profilo di log permette di configurare quanto segue:

* Destinazione del log attività, ad esempio un account di archiviazione o un hub eventi.
* Categorie di eventi da inviare (scrittura, eliminazione o azione). *Il significato di "categoria" nei profili del log è diverso da quello negli eventi del log attività. Nel profilo del log, la "Categoria" rappresenta il tipo di operazione (scrittura, eliminazione, azione). In un evento del log attività, la proprietà "categoria" rappresenta l'origine o il tipo di evento (ad esempio, amministrazione, ServiceHealth, avviso e altro).*
* Aree o località da esportare. Assicurarsi di includere "global", perché molti eventi del log attività sono eventi globali.
* Per quanto tempo il log attività deve essere mantenuto nell'account di archiviazione.
    - Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
    - Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o Log Analytics, i criteri di conservazione non hanno alcun effetto.
    - I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.

È possibile usare un account di archiviazione o un hub eventi dello spazio dei nomi che non si trovi nella stessa sottoscrizione di quello che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!NOTE]
>  Non è al momento possibile archiviare i dati in un account di archiviazione che risiede dietro una rete virtuale protetta.

> [!WARNING]
> Il formato dei dati di log nell'account di archiviazione è stato modificato in righe JSON dal 1° novembre 2018. [Vedere questo articolo per una descrizione dell'impatto e per informazioni su come aggiornare gli strumenti per gestire il nuovo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Queste impostazioni possono essere configurate tramite l'opzione "Esporta" nel pannello Log attività nel portale oppure a livello di codice tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o l'interfaccia della riga di comando. Una sottoscrizione può avere un solo profilo di log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurare i profili di log tramite il portale di Azure
È possibile trasmettere il Log attività a un hub eventi o memorizzarlo in un account di archiviazione usando l'opzione "Export to Event Hub" (Esporta in Hub eventi) nel portale di Azure.

1. Passare al **log attività** usando il menu sul lato sinistro del portale.

    ![Passare al log attività nel portale](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Fare clic sul pulsante **Export to Event Hub** (Esporta in Hub eventi) nella parte superiore del pannello.

    ![Pulsante Esporta nel portale](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. Nel pannello visualizzato è possibile selezionare:  
  * aree per cui esportare gli eventi
  * account di archiviazione in cui salvare gli eventi
  * numero di giorni di conservazione degli eventi nella risorsa di archiviazione (se si impostano 0 giorni, i log vengono conservati all'infinito)
  * spazio dei nomi del bus di servizio in cui creare un hub eventi per la trasmissione di questi eventi.

     ![Pannello Esporta log di controllo](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurare i profili di log tramite i cmdlet di Azure PowerShell

#### <a name="get-existing-log-profile"></a>Ottenere un profilo di log esistente

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| NOME |Yes |Nome del profilo di log. |
| StorageAccountId |No  |ID risorsa dell'account di archiviazione in cui salvare il log attività. |
| serviceBusRuleId |No  |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato seguente: `{service bus resource ID}/authorizationrules/{key name}`. |
| Località |Yes |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
| RetentionInDays |Yes |Numero di giorni per cui gli eventi devono essere mantenuti, compreso tra 1 e 2147483647. Se il valore è zero, i log vengono mantenuti per un periodo illimitato. |
| Categoria |No  |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |

#### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Configurare i profili di log tramite l'interfaccia della riga di comando di Azure

#### <a name="get-existing-log-profile"></a>Ottenere un profilo di log esistente

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

La proprietà `name` deve essere il nome del profilo di log.

#### <a name="add-a-log-profile"></a>Aggiungere un profilo di log

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Per la documentazione completa relativa alla creazione di un profilo di monitoraggio mediante l'interfaccia della riga di comando, vedere la documentazione di [riferimento ai comandi dell'interfaccia della riga di comando](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività (in precedenza, log di controllo)](../../azure-resource-manager/resource-group-audit.md)
* [Trasmettere il log attività di Azure a Hub eventi](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
