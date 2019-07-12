---
title: Log di diagnostica Azure Stream all'area di lavoro di Log Analitica in Monitoraggio di Azure
description: Informazioni su come trasmettere log di diagnostica di Azure a un'area di lavoro di Log Analitica in Monitoraggio di Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612835"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Log di diagnostica Azure Stream all'area di lavoro di Log Analitica in Monitoraggio di Azure

**[Log di diagnostica di Azure](diagnostic-logs-overview.md)**  possono essere trasmessi quasi in tempo reale per un'area di lavoro di Log Analitica in Monitoraggio di Azure usando il portale, i cmdlet di PowerShell o CLI di Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Operazioni eseguibili con diagnostica di log in un'area di lavoro di Log Analitica

Monitoraggio di Azure fornisce uno strumento di Progettazione query e analitica log flessibile che consente di approfondire i dati di log non elaborati generati dalle risorse di Azure. Le funzionalità includono:

* **Query di log** -scrittura di query avanzate su dati del registro, correlare i log da diverse origini e generano grafici che possono essere aggiunti al dashboard di Azure.
* **Avvisi** -rilevare quando uno o più eventi corrispondano a una particolare query e vengono notificati con una chiamata di posta elettronica o webhook tramite avvisi di monitoraggio di Azure.
* **Analisi avanzata**: applicare algoritmi di machine learning e criteri di ricerca per identificare i possibili problemi risultanti dai log.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Abilitare lo streaming dei log di diagnostica all'area di lavoro di Log Analitica

È possibile abilitare la trasmissione dei log di diagnostica a livello di codice tramite il portale o tramite le [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). In entrambi i casi, si crea un'impostazione di diagnostica in cui specificare un'area di lavoro Log Analytics, le categorie di log e le metriche da inviare all'area di lavoro. Una **categoria di log** di diagnostica è un tipo di log che una risorsa può fornire.

L'area di lavoro Log Analytics non deve trovarsi nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Eseguire lo streaming dei log di diagnostica usando il portale
1. Nel portale, passare a monitoraggio di Azure e fare clic su **le impostazioni di diagnostica** nel **impostazioni** menu.


2. Facoltativamente filtrare l'elenco in base al tipo di risorsa o al gruppo di risorse, quindi fare clic sulla risorsa per cui si vuole specificare un'impostazione di diagnostica.

3. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Se esistono già impostazioni sulla risorsa, verrò visualizzato un elenco di impostazioni già configurate per questa risorsa. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Assegnare all'impostazione un nome e selezionare la casella **Invia a Log Analytics**, quindi selezionare un'area di lavoro Log Analytics.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e vengono trasmessi i log di diagnostica a tale area di lavoro non appena vengono generati nuovi dati di eventi. Potrebbero essere presenti fino a 15 minuti tra quando viene generato un evento e la sua apparizione in Log Analitica.

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per abilitare la trasmissione tramite i [cmdlet di Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), è possibile usare il cmdlet `Set-AzDiagnosticSetting` con i parametri seguenti:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Si noti che la proprietà ID dell'area di lavoro assume l'intero ID della risorsa Azure dell'area di lavoro e non l'ID/chiave dell'area di lavoro visualizzata nel portale Log Analytics.

### <a name="via-azure-cli"></a>Tramite l'interfaccia della riga di comando di Azure

Per abilitare lo streaming tramite l'[interfaccia della riga di comando di Azure](../../azure-monitor/platform/cli-samples.md), è possibile usare il comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

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

È possibile aggiungere altre categorie al log di diagnostica aggiungendo dizionari alla matrice JSON passata come parametro `--logs`.

L'argomento `--resource-group` è obbligatorio solo se `--workspace` non è un ID oggetto.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Come si eseguono query i dati da un'area di lavoro di Log Analitica?

Nel pannello del log nel portale di monitoraggio di Azure, è possibile eseguire una query dei log di diagnostica come parte della soluzione gestione dei Log sotto la tabella AzureDiagnostics. Esistono inoltre [diverse soluzioni di monitoraggio per risorse di Azure](../../azure-monitor/insights/solutions.md) è possibile installare per ottenere informazioni dettagliate immediate sui dati di log inviati in Monitoraggio di Azure.

### <a name="examples"></a>Esempi

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Confronto tra Azure Diagnostics e specifici della risorsa  
Una volta che una destinazione di Log Analitica è abilitata in una configurazione di diagnostica di Azure, esistono due modi distinti che i dati verranno visualizzati nell'area di lavoro:  
- **Diagnostica di Azure** -si tratta del metodo legacy usato oggi per la maggior parte dei servizi di Azure. In questa modalità, tutti i dati da qualsiasi impostazione di diagnostica a cui un'area di lavoro finirà nel _AzureDiagnostics_ tabella. 
<br><br>Poiché molte risorse di inviare dati alla stessa tabella (_AzureDiagnostics_), lo schema della tabella è il set degli schemi di tutti i tipi di dati diversi raccolti con privilegi avanzati. Ad esempio, se è stato creato le impostazioni di diagnostica per la raccolta di tipi di dati seguenti, tutti inviati alla stessa area di lavoro:
    - Controllare i log di 1 risorsa (con un schema costituito da colonne A, B e C)  
    - Log degli errori di 2 risorse (con un schema costituito da colonne D, E e F)  
    - Log dei flussi di dati di 3 di risorsa (con un schema costituito da colonne G, H e ho)  

    La tabella AzureDiagnostics apparirà come segue, con alcuni dati di esempio:  

    | ResourceProvider | Categoria | Una | b | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | Log di controllo | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | Log di controllo | x5 | y5 | z5 |
    | ... |

- **Risorse specifiche** -In questa modalità, le singole tabelle nell'area di lavoro selezionato vengono create per ogni categoria selezionata nella configurazione di impostazioni di diagnostica. Questo metodo più recente rende molto più semplice trovare esattamente da trovare tramite esplicita separazione dei compiti: una tabella per ogni categoria. Inoltre, offre vantaggi nel supporto per tipi dinamici. Questa modalità per selezionare i tipi di risorse di Azure, si può già osservare ad esempio [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) oppure [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) i log. Infine, si prevede che ogni tipo di dati per eseguire la migrazione alla modalità di specifici della risorsa. 

    Nell'esempio precedente, ciò comporterebbe tre tabelle create: 
    - Nella tabella _AuditLogs_ come indicato di seguito:

        | ResourceProvider | Categoria | Una | b | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | Log di controllo | x1 | y1 | z1 |
        | Microsoft.Resource1 | Log di controllo | x5 | y5 | z5 |
        | ... |

    - Nella tabella _ErrorLogs_ come indicato di seguito:  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - Nella tabella _DataFlowLogs_ come indicato di seguito:  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Altri vantaggi dell'uso della modalità specifiche delle risorse includono prestazioni migliori tra latenza di inserimento e tempi di query, una migliore individuabilità di schemi e la loro struttura, la possibilità di concedere autorizzazioni RBAC a una tabella specifica e altro ancora.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Selezione di diagnostica di Azure modalità specifiche delle risorse di Visual Studio
Per le risorse di Azure più, non si avrà una scelta se si desidera utilizzare la modalità di diagnostica di Azure o specifici della risorsa; i dati verranno automaticamente instradato attraverso il metodo che la risorsa ha scelto di usare. Vedere la documentazione fornita con la risorsa che è stato abilitato per inviare dati a Log Analitica per informazioni dettagliate in cui viene utilizzata la modalità. 

Come indicato nella sezione precedente, in definitiva è l'obiettivo del monitoraggio di Azure per avere tutti i servizi in Azure, usare la modalità di specifici della risorsa. Per agevolare questa transizione e assicurarsi che nessun dato venga perso come parte di esso, alcuni servizi di Azure durante il caricamento nel Log Analitica fornirà all'utente una selezione della modalità:  
   ![Selettore di modalità impostazioni diagnostica](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Abbiamo **fortemente** consigliabile che, per evitare le migrazioni potenzialmente difficile scegliessimo, qualsiasi appena creati impostazioni di diagnostica utilizzano la modalità incentrato sugli elementi di risorsa.  

Le impostazioni di diagnostica esistenti, una volta abilitato per una particolare risorsa di Azure, sarà in grado di passare in modo retroattivo da diagnostica di Azure per la modalità di specifici della risorsa. I dati inseriti in precedenza continueranno a essere disponibile nel _AzureDiagnostics_ tabella fino a quando non è stato rimosso come configurato nell'impostazione memorizzazione area di lavoro, ma tutti i nuovi dati verranno inviati alla tabella dedicata. Ciò significa che per qualsiasi query che sia necessario eseguire sia i dati vecchi e nuovi (fino a quando non ormai completamente i vecchi dati), un [unione](https://docs.microsoft.com/azure/kusto/query/unionoperator) operatore nella query sarà necessario combinare i due set di dati.

Controllare per i log di supporti nella modalità specifiche delle risorse dei servizi di notizie sul nuovo Azure sul [gli aggiornamenti di Azure](https://azure.microsoft.com/updates/) blog.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Limitazioni note: limite di colonna in AzureDiagnostics
È previsto un limite esplicito di ogni singola tabella di Log di Azure non utilizzare le colonne contenenti più di 500. Una volta raggiunto, verranno eliminate tutte le righe contenenti dati con una colonna all'esterno i primi 500 in fase di inserimento. La tabella AzureDiagnostics è particolarmente sensibili per essere interessate questo limite. In genere ciò accade in quanto un'ampia gamma di origini dati vengono inviati alla stessa area di lavoro, o più origini dati dettagliati inviate alla stessa area di lavoro. 

#### <a name="azure-data-factory"></a>Data factory di Azure  
Azure Data Factory, a causa di un set molto dettagliato dei log, è una risorsa che è noto per essere particolarmente interessate da questo limite. In particolare, per tutte le impostazioni di diagnostica configurati prima le risorse specifiche modalità è stato abilitato o in modo esplicito sceglie di usare la modalità di risorse specifiche per motivi di compatibilità inverso:  
- *Parametri dell'utente definiti nei confronti di qualsiasi attività nella pipeline*: esisterà una nuova colonna per ogni parametro denominato in modo univoco utenti su qualsiasi attività creata. 
- *Attività input e output*: queste variano in un'attività a altra e generare un numero elevato di colonne a causa delle loro natura dettagliato. 
 
Come con le proposte di soluzione più ampia riportato di seguito, è consigliabile eseguire la migrazione dei registri per usare la modalità di specifici della risorsa appena possibile. Se è in grado di farlo immediatamente, provvisoria alternativa consiste nell'isolare i log di Azure Data Factory nella propria area di lavoro per ridurre al minimo le probabilità di questi log alcun impatto sugli altri tipi di log vengono raccolti in aree di lavoro. 
 
#### <a name="workarounds"></a>Soluzioni alternative
A breve termine, fino a quando tutti i servizi di Azure sono abilitati nella modalità specifiche delle risorse, per tutti i servizi non è ancora che supportano la modalità di risorse specifiche, è consigliabile separare i tipi di dati dettagliati pubblicati da questi servizi in aree di lavoro separati per ridurre il possibilità di raggiungere il limite.  
 
Durata, diagnostica di Azure sarà lo spostamento verso tutti i servizi di Azure che supportano la modalità di specifici della risorsa. È consigliabile lo spostamento di questa modalità appena possibile per ridurre le possibilità di essere interessati da questo limite di 500 colonne.  


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log di diagnostica di Azure](diagnostic-logs-overview.md)

