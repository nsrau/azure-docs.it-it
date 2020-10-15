---
title: Estendere la sentinella di Azure tra le aree di lavoro e i tenant | Microsoft Docs
description: Come usare Azure Sentinel per eseguire query e analizzare i dati nelle aree di lavoro e nei tenant.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 502b93b4459fba4da04207d9186f8c7ce6b298c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578479"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Estendere Azure Sentinel tra più aree di lavoro e tenant

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>La necessità di usare più aree di lavoro di Sentinel di Azure

Azure Sentinel si basa su un'area di lavoro Log Analytics. Si noterà che il primo passaggio del caricamento di Azure Sentinel consiste nel selezionare l'area di lavoro Log Analytics che si desidera utilizzare a tale scopo.

Usando un'unica area di lavoro è possibile sfruttare tutti i vantaggi offerti dall'esperienza di Azure Sentinel. Anche in questo caso, è possibile che sia necessario disporre di più aree di lavoro. La tabella seguente elenca alcune di queste situazioni e, quando possibile, suggerisce come il requisito può essere soddisfatto con un'unica area di lavoro:

| Requisito | Descrizione | Modi per ridurre il numero di aree di lavoro |
|-------------|-------------|--------------------------------|
| Sovranità e conformità alle normative | Un'area di lavoro è associata a un'area specifica. Se i dati devono essere conservati in diverse aree [geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) per soddisfare i requisiti normativi, devono essere suddivisi in aree di lavoro separate. |  |
| Proprietà dei dati | I limiti della proprietà dei dati, ad esempio da filiali o società affiliate, sono meglio delineati con aree di lavoro separate. |  |
| Più tenant di Azure | Azure Sentinel supporta la raccolta di dati da Microsoft e risorse SaaS di Azure solo all'interno del proprio Azure Active Directory (Azure AD) limite del tenant. Ogni tenant di Azure AD richiede pertanto un'area di lavoro separata. |  |
| Controllo granulare dell'accesso ai dati | Un'organizzazione potrebbe avere la necessità di consentire a gruppi diversi, all'interno o all'esterno dell'organizzazione, di accedere ad alcuni dei dati raccolti da Sentinel di Azure. Ad esempio:<br><ul><li>Accesso dei proprietari delle risorse ai dati relativi alle risorse</li><li>SOC regionale o sussidiario "accesso ai dati relativi alle parti dell'organizzazione</li></ul> | USA [controllo](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) degli accessi in base al ruolo o [RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Impostazioni di conservazione granulari | In passato, le aree di lavoro erano l'unico modo per impostare periodi di conservazione diversi per tipi di dati diversi. Questa operazione non è più necessaria in molti casi, grazie all'introduzione delle impostazioni di conservazione a livello di tabella. | Usare [le impostazioni di conservazione a livello di tabella](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) o automatizzare l' [eliminazione dei dati](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Suddivisione della fatturazione | Inserendo le aree di lavoro in sottoscrizioni separate, è possibile fatturarle a diverse parti. | Report di utilizzo e addebito incrociato |
| Architettura legacy | L'uso di più aree di lavoro può derivare da una progettazione cronologica che prende in considerazione le limitazioni o le procedure consigliate che non contengono più il vero. Si potrebbe trattare anche di una scelta di progettazione arbitraria che può essere modificata per supportare meglio Azure Sentinel.<br><br>Tra gli esempi sono inclusi:<br><ul><li>Uso di un'area di lavoro predefinita per sottoscrizione quando si distribuisce il Centro sicurezza di Azure</li><li>La necessità di impostazioni di conservazione o controllo degli accessi granulari, le soluzioni per le quali sono relativamente nuove</li></ul> | Riprogettare le aree di lavoro |

### <a name="managed-security-service-provider-mssp"></a>Provider di servizi di sicurezza gestito (MSSP)

Un caso d'uso specifico che impone più aree di lavoro è un servizio MSSP di Azure Sentinel. In questo caso, molti se non tutti i requisiti precedenti si applicano, la procedura consigliata consiste nel creare più aree di lavoro tra i tenant. MSSP può usare [Azure Lighthouse](../lighthouse/overview.md) per estendere le funzionalità di Azure Sentinel tra più tenant.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Architettura con più aree di lavoro di Azure Sentinel

In base ai requisiti indicati in precedenza, esistono casi in cui più aree di lavoro di Sentinel di Azure, potenzialmente nei tenant Azure Active Directory (Azure AD), devono essere monitorate e gestite a livello centralizzato da un singolo SOC.

- Servizio Sentinel di Azure MSSP.

- Un SOC globale che funge da più filiali, ognuno dei quali dispone di un SOC locale.

- Un SOC che monitora più tenant Azure AD all'interno di un'organizzazione.

Per soddisfare questo requisito, Azure Sentinel offre funzionalità con più aree di lavoro che consentono il monitoraggio centrale, la configurazione e la gestione, fornendo un unico riquadro di vetro tra tutti gli elementi coperti dal SOC, come illustrato nel diagramma seguente.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Architettura tra aree di lavoro":::

Questo modello offre vantaggi significativi rispetto a un modello completamente centralizzato in cui tutti i dati vengono copiati in una singola area di lavoro:

- Assegnazione di ruolo flessibile al SOC globale e locale oppure ai clienti di MSSP.

- Meno problemi relativi alla proprietà dei dati, alla privacy dei dati e alla conformità alle normative.

- Latenza di rete e addebiti minimi.

- Onboarding semplificato e offboarding di nuovi clienti o filiali.

Nelle sezioni seguenti verrà illustrato come utilizzare questo modello e in particolare come:

- Monitora centralmente più aree di lavoro, potenzialmente tra i tenant, fornendo al SOC un unico riquadro di vetro.

- Configurare e gestire centralmente più aree di lavoro, potenzialmente tra i tenant, usando l'automazione.

## <a name="cross-workspace-monitoring"></a>Monitoraggio tra aree di lavoro

### <a name="manage-incidents-on-multiple-workspaces"></a>Gestire gli eventi imprevisti in più aree di lavoro

Azure Sentinel supporta una [visualizzazione degli eventi imprevisti a più aree di lavoro](./multiple-workspace-view.md) che facilita il monitoraggio e la gestione degli eventi imprevisti centrali tra più aree La visualizzazione degli eventi imprevisti centralizzati consente di gestire gli eventi imprevisti direttamente o eseguire il drill-down in modo trasparente nei dettagli dell'evento imprevisto nel contesto dell'area di lavoro di origine.

### <a name="cross-workspace-querying"></a>Esecuzione di query tra aree di lavoro

Azure Sentinel supporta l'esecuzione di query [su più aree di lavoro in una singola query](../azure-monitor/log-query/cross-workspace-query.md), consentendo la ricerca e la correlazione dei dati da più aree di lavoro in una singola query. 

- Utilizzare l' [espressione Workspace ()](../azure-monitor/log-query/workspace-expression.md) per fare riferimento a una tabella in un'area di lavoro diversa. 
- Utilizzare l' [operatore Union](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) insieme all'espressione Workspace () per applicare una query tra tabelle in più aree di lavoro.

È possibile usare le [funzioni](../azure-monitor/log-query/functions.md) salvate per semplificare le query tra aree di lavoro. Se, ad esempio, un riferimento a un'area di lavoro è lungo, potrebbe essere necessario salvare l'espressione `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` come funzione chiamata `SecurityEventCustomerA` . È quindi possibile scrivere query come `SecurityEventCustomerA | where ...` .

Una funzione può anche semplificare un'Unione di uso comune. Ad esempio, è possibile salvare l'espressione seguente come funzione denominata `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

È quindi possibile scrivere una query in entrambe le aree di lavoro iniziando da `unionSecurityEvent | where ...` .

#### <a name="scheduled-alerts"></a>Avvisi pianificati

È ora possibile includere le query tra aree di lavoro negli avvisi pianificati nelle regole di analisi, in base alle limitazioni seguenti:

- In una singola query è possibile includere fino a 10 aree di lavoro.
- Azure Sentinel deve essere distribuito in ogni area di lavoro a cui viene fatto riferimento nella query.

> [!NOTE] 
> L'esecuzione di query su più aree di lavoro nella stessa query può influire sulle prestazioni ed è quindi consigliabile solo quando la logica richiede questa funzionalità.

### <a name="using-cross-workspace-workbooks"></a>Uso di cartelle di lavoro tra aree di lavoro

Le [cartelle di lavoro](./overview.md#workbooks) di forniscono dashboard e app a Sentinel di Azure. Quando si lavora con più aree di lavoro, vengono forniti monitoraggio e azioni tra le aree di lavoro.

Le cartelle di lavoro possono fornire query tra aree di lavoro in uno dei tre metodi, ognuno dei quali soddisfa i diversi livelli di esperienza degli utenti finali:

| Metodo  | Descrizione | Quando è consigliabile usare? |
|---------|-------------|--------------------|
| Scrivere query tra aree di lavoro | Il creatore della cartella di lavoro può scrivere query tra aree di lavoro (descritte in precedenza) nella cartella di lavoro. | Questa opzione consente agli autori di cartelle di lavoro di schermare completamente l'utente dalla struttura dell'area di lavoro. |
| Aggiungere un selettore area di lavoro alla cartella di lavoro | L'autore della cartella di lavoro può implementare un selettore dell'area di lavoro come parte della cartella di lavoro, come descritto [qui](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Questa opzione consente all'utente di controllare le aree di lavoro mostrate dalla cartella di lavoro, mediante una casella a discesa di facile utilizzo. |
| Modificare la cartella di lavoro in modo interattivo | Un utente avanzato che modifica una cartella di lavoro esistente può modificarvi le query, selezionando le aree di lavoro di destinazione usando il selettore dell'area di lavoro nell'editor. | Questa opzione consente a un utente Power di modificare facilmente le cartelle di lavoro esistenti in modo da usare più aree di lavoro. |
|

### <a name="cross-workspace-hunting"></a>Caccia tra aree di lavoro

Azure Sentinel fornisce esempi di query precaricati progettati per iniziare e acquisire familiarità con le tabelle e il linguaggio di query. Queste query di caccia predefinite vengono sviluppate dai ricercatori della sicurezza Microsoft su base continuativa, sia l'aggiunta di nuove query che l'ottimizzazione delle query esistenti, per fornire un punto di ingresso per cercare nuovi rilevamenti e identificare i segnali di intrusione che potrebbero non essere stati rilevati dagli strumenti di sicurezza.  

Le funzionalità di ricerca tra aree di lavoro consentono ai cacciatori di minacce di creare nuove query di ricerca o di adattare quelle esistenti per coprire più aree di lavoro, usando l'operatore Union e l'espressione Workspace (), come illustrato in precedenza.

## <a name="cross-workspace-management-using-automation"></a>Gestione tra aree di lavoro tramite automazione

Per configurare e gestire più aree di lavoro di Sentinel di Azure, sarà necessario automatizzare l'uso dell'API di gestione di Sentinel di Azure. Per altre informazioni su come automatizzare la distribuzione delle risorse di Azure Sentinel, incluse le regole di avviso, la ricerca di query, cartelle di lavoro e PlayBook, vedere [estensione di Azure Sentinel: API, integrazione e automazione di gestione](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Vedere anche [distribuzione e gestione di Azure Sentinel come codice](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) e [combinazione di Azure Lighthouse con le funzionalità di DevOps di Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) per una metodologia consolidata, fornita dalla community per la gestione di Azure Sentinel come codice e per la distribuzione e la configurazione delle risorse da un repository GitHub privato. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Gestione delle aree di lavoro tra tenant con Azure Lighthouse

Come indicato in precedenza, in molti scenari le diverse aree di lavoro di Azure Sentinel possono trovarsi in tenant di Azure AD diversi. È possibile usare [Azure Lighthouse](../lighthouse/overview.md) per estendere tutte le attività tra aree di lavoro tra i limiti dei tenant, consentendo agli utenti nel tenant di gestione di lavorare su aree di lavoro di Sentinel di Azure in tutti i tenant. Una volta caricato Azure [Lighthouse, usare](../lighthouse/how-to/onboard-customer.md)il [selettore directory + sottoscrizioni](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) nella portale di Azure per selezionare tutte le sottoscrizioni contenenti le aree di lavoro che si vuole gestire, per assicurarsi che siano tutte disponibili nei diversi selettori di area di lavoro nel portale.

Quando si usa Azure Lighthouse, è consigliabile creare un gruppo per ogni ruolo di Azure Sentinel e delegare le autorizzazioni da ogni tenant a tali gruppi.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come è possibile estendere le funzionalità di Azure Sentinel tra più aree di lavoro e tenant. Per informazioni pratiche sull'implementazione dell'architettura tra più aree di lavoro di Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come usare [più tenant](./multiple-tenants-service-providers.md) in Sentinel di Azure con Azure Lighthouse.
- Informazioni su come [visualizzare e gestire gli eventi imprevisti in più aree di lavoro in](./multiple-workspace-view.md) modo uniforme.