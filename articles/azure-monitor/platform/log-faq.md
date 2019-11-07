---
title: Domande frequenti su Log Analytics | Documentazione Microsoft
description: Risposte alle domande frequenti sul servizio di analisi dei log di monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579317"
---
# <a name="log-analytics-faq"></a>Domande frequenti su Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Queste domande frequenti su Microsoft sono un elenco di domande frequenti sull'area di lavoro Log Analytics di monitoraggio di Azure. Per altre domande su Log Analytics, visitare il [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.


## <a name="new-logs-experience"></a>Nuova esperienza Log

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>D: Qual è la differenza tra la nuova esperienza Log e Log Analytics?

R: Sono la stessa cosa. [Log Analytics è una funzionalità integrata in Monitoraggio di Azure](../../azure-monitor/azure-monitor-rebrand.md) per offrire un'esperienza di monitoraggio più uniforme. La nuova esperienza Log di Monitoraggio di Azure corrisponde esattamente alle query di Log Analytics già usate da molti clienti.

### <a name="q-can-i-still-use-log-search"></a>D: È ancora possibile usare Ricerca Log? 

R: Ricerca log attualmente è ancora disponibile nel portale di OMS e nel portale di Azure con il nome **Log (versione classica)** . Il portale di OMS verrà ritirato ufficialmente il 15 gennaio 2019. L'esperienza Log classica nel portale di Azure classico sarà ritirata gradualmente e sostituita con la nuova esperienza Log. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>D: È ancora possibile utilizzare il portale di Analisi avanzata? 
La nuova esperienza Log nel portale di Azure si basa sul portale di analisi avanzato, ma è comunque ancora accessibile dall'esterno del portale di Azure. La roadmap per il ritiro di questo portale esterno sarà annunciata presto.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>D: Perché non è possibile vedere i pulsanti Esplora query e Salva nella nuova esperienza Log?

I pulsanti **Esplora query**, **Salva** e **Imposta avviso** non sono disponibili durante l'esplorazione dei log nel contesto di una risorsa specifica. Per creare avvisi, salvare o caricare una query, Log deve avere come ambito un'area di lavoro. Per aprire Log nel contesto dell'area di lavoro, selezionare **tutti i servizi** > **Monitor** > **Log**. L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. Per altre informazioni, vedere [Visualizzazione e analisi dei dati in Log Analytics](../log-query/portals.md).

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>D: Come è possibile estrarre Campi personalizzati nella nuova esperienza Log? 

R: L'estrazione di Campi personalizzati è attualmente supportata nell'esperienza Log classica. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>D: Dove si trova la visualizzazione elenco nella nuova esperienza Log? 

R: La visualizzazione elenco non è disponibile nella nuova esperienza Log. È presente una freccia a sinistra di ogni record nella tabella dei risultati. Fare clic su tale freccia per visualizzare i dettagli per un record specifico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>D: Dopo aver eseguito una query, è disponibile un elenco di filtri suggeriti. Come è possibile visualizzare i filtri? 

R: fare clic su' filtri ' nel riquadro a sinistra per visualizzare un'anteprima della nuova implementazione dei filtri. Ora i filtri si basano sul set di risultati completo anziché essere vincolati dal limite di 10.000 record dell'interfaccia utente. Al momento questo è un elenco dei filtri più popolari e dei 10 valori più comuni per ogni filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>D: Perché viene visualizzato l'errore "Registrare il provider di risorse 'Microsoft.Insights' per questa sottoscrizione per abilitare questa query" in Log, dopo l'eplorazione dalla macchina virtuale? 

R: Per impostazione predefinita, molti provider di risorse sono registrati automaticamente. Tuttavia, potrebbe essere necessario registrare alcuni provider di risorse manualmente. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per altre informazioni, vedere [Provider e tipi di risorse](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>D: Perché viene visualizzato un messaggio di errore di accesso quando si accede a Log da una pagina della macchina virtuale? 

R: Per visualizzare i log della macchina virtuale, è necessario disporre dell'autorizzazione di lettura per le aree di lavoro in cui sono archiviati i log della macchina virtuale. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>D: Perché è possibile accedere alla propria area di lavoro nel portale di OMS, ma viene visualizzato un errore di accesso nel portale di Azure?  

R: Per accedere a un'area di lavoro in Azure, è necessario disporre delle autorizzazioni di Azure. In alcuni casi è possibile che non si disponga delle autorizzazioni di accesso appropriate. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure. Per altre informazioni, vedere [Passaggio del portale di OMS in Azure](oms-portal-transition.md).

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>D: Perché non è presente la voce Progettazione viste in Log?

R: Progettazione viste è disponibile in Log solo per gli utenti che dispongono di autorizzazioni di collaboratore o superiori.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>D: È possibile usare il portale di Log Analytics all'esterno di Azure?

R. Sì, la pagina Log in Azure e il portale di analisi avanzato si basano sullo stesso codice. È in corso l'integrazione di Log Analytics come funzionalità di Monitoraggio di Azure per offrire un'esperienza di monitoraggio unificata. È comunque possibile accedere al portale di Analytics usando l'URL: https:\/\/Portal. loganalytics. io/subscriptions/{subscriptionId}/ResourceGroups/{resourceGroupName}/Workspaces/{WorkspaceName}.



## <a name="general"></a>Generale

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>D: Come è possibile visualizzare le proprie viste e soluzioni nel portale di Azure? 

R: L'elenco delle viste e delle soluzioni installate è disponibile nel portale di Azure. Fare clic su **Tutti i servizi**. Nell'elenco delle risorse selezionare **Monitor**, quindi fare clic su **...Altro**. L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>D: Perché non è possibile creare aree di lavoro nella regione Stati Uniti centro-occidentali? 

R: Questa regione è al limite della capacità temporanea. Questo limite è pianificato per essere risolto entro la fine di settembre 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>D: Log Analytics usa lo stesso agente del Centro sicurezza di Azure?

R: All'inizio di giugno 2017 il Centro sicurezza di Azure ha iniziato a usare Microsoft Monitoring Agent per la raccolta e l'archiviazione dei dati. Per altre informazioni, vedere [Domande frequenti sulla migrazione della piattaforma del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>D: Quali controlli vengono eseguiti dalle soluzioni di valutazione SQL e AD?

R: La query seguente illustra una descrizione di tutti i controlli attualmente eseguiti:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

I risultati possono quindi essere esportati in Excel per analizzarli più attentamente.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>D: Perché non viene visualizzato OMS nella console di System Center Operations Manager?

R: A seconda dell'aggiornamento cumulativo di Operations Manager in uso, viene visualizzato un nodo relativo a *System Center Advisor*, *Operational Insights* o *Log Analytics*.

L'aggiornamento della stringa di testo a *OMS* è incluso in un Management Pack, che deve essere importato manualmente. Per visualizzare il testo e le funzionalità correnti, seguire le istruzioni nell'ultimo articolo della KB sull'aggiornamento cumulativo di System Center Operations Manager e aggiornare la console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>D: Esiste una versione locale di Log Analytics?

R: No. Log Analytics è un servizio cloud scalabile che elabora e archivia grandi quantità di dati. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>D: Come inviare una notifica all'utente quando la raccolta dati si interrompe?

R: Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in [Creare un nuovo avviso del log](../../azure-monitor/platform/alerts-metric.md).

Quando si crea l'avviso per l'interruzione della raccolta dati, applicare le seguenti impostazioni:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - **Nome segnale** selezionare **Ricerca log personalizzata**.
   - **Query di ricerca** su `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *30* minuti e **Frequenza di avviso** ogni *10* minuti
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data collection stopped* (Raccolta dati interrotta)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](../../azure-monitor/platform/action-groups.md) esistente o crearne uno nuovo, in modo che se l'avviso del log corrisponde ai criteri e un heartbeat manca per più di 15 minuti si riceve una notifica.

## <a name="configuration"></a>Configurazione

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>D: È possibile modificare il nome del contenitore di tabelle/BLOB usato per leggere da Diagnostica di Azure?

R. No, non è attualmente possibile leggere da tabelle o contenitori arbitrari in Archiviazione di Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>D: Quali indirizzi IP usa il servizio Log Analytics? Come assicurarsi che il firewall consenta solo il traffico ai servizi di Log Analytics?

R. Il servizio Log Analytics è basato su Azure. Gli indirizzi IP di Log Analytics si trovano in [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli di indirizzi IP dei data center di Microsoft Azure).

Quando vengono eseguite distribuzioni di servizi, gli indirizzi IP effettivi del servizio Log Analytics vengono modificati. I nomi DNS da consentire attraverso il firewall sono indicati nei [requisiti di rete](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>D: Si usa ExpressRoute per connettersi ad Azure. Il traffico di Log Analytics userà la connessione ExpressRoute?

R. I diversi tipi di traffico di ExpressRoute vengono descritti nella [Documentazione di ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Il traffico verso Log Analytics usa il circuito ExpressRoute di peer pubblico.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>D: Esiste un modo semplice per spostare un'area di lavoro Log Analytics esistente in un'altra area di lavoro Log Analytics/della sottoscrizione di Azure?

R. Il cmdlet `Move-AzResource` consente di spostare un'area di lavoro Log Analytics e anche un account di Automazione da una sottoscrizione di Azure a un'altra. Per ulteriori informazioni, vedere [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Questa modifica può essere apportata anche nel portale di Azure.

Non è possibile spostare dati da un'area di lavoro Log Analytics a un'altra o cambiare l'area in cui sono archiviati i dati di Log Analytics.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>D: Come aggiungere Log Analytics a System Center Operations Manager?

R: Il passaggio all'aggiornamento cumulativo più recente e l'importazione dei Management Pack consente di connettere Operations Manager a Log Analytics.

>[!NOTE]
>La connessione di Operations Manager a Log Analytics è disponibile solo per System Center Operations Manager 2012 SP1 e versioni successive.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>D: Come è possibile verificare che un agente può comunicare con Log Analytics?

R: per assicurarsi che l'agente sia in grado di comunicare con l'area di lavoro Log Analytics, passare a: Pannello di controllo, impostazioni di sicurezza &, **Microsoft Monitoring Agent**.

Nella scheda **Analisi dei log di Azure (OMS)** cercare un segno di spunta verde. Un'icona con un segno di spunta verde conferma che l'agente può comunicare con il servizio di Azure.

Un'icona di avviso gialla indica problemi di comunicazione tra l'agente e Log Analytics. Un motivo comune è che il servizio Microsoft Monitoring Agent è stato arrestato. Usare Gestione controllo servizi per riavviare il servizio.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>D: Come si arresta la comunicazione tra un agente e Log Analytics?

R: in System Center Operations Manager rimuovere il computer dall'elenco Log Analytics computer gestiti. Operations Manager aggiorna la configurazione dell'agente affinché non invii altri report a Log Analytics. È possibile impedire la comunicazione degli agenti connessi direttamente a Log Analytics tramite il Pannello di controllo, Sicurezza e impostazioni, **Microsoft Monitoring Agent**.
In **Analisi dei log di Azure (OMS)** rimuovere tutte le aree di lavoro elencate.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>D: Perché viene visualizzato un errore quando si tenta di spostare l'area di lavoro da una sottoscrizione di Azure a un'altra?

R: Per spostare un'area di lavoro in un'altra sottoscrizione o un altro gruppo di lavoro, occorre prima scollegare l'account di Automazione nell'area di lavoro. Per scollegare un account di Automazione è necessaria la rimozione di queste soluzioni, se sono installate nell'area di lavoro: Gestione aggiornamenti, Rilevamento modifiche o Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Dopo la rimozione di queste soluzioni, scollegare l'account di Automazione selezionando **Linked workspaces** (Aree di lavoro collegate) nel riquadro sinistro nella risorsa dell'account di Automazione e facendo clic su **Scollega area di lavoro** nella barra multifunzione.
 > Dopo lo spostamento, le soluzioni rimosse devono essere reinstallate nell'area di lavoro e il collegamento di Automazione all'area di lavoro deve essere reimpostato.

Verificare di avere l'autorizzazione in entrambe le sottoscrizioni di Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>D: Perché viene generato un errore quando si cerca di aggiornare una risorsa SavedSearch?

R: È necessario aggiungere "etag" nel corpo dell'API oppure le proprietà del modello di Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dati dell'agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>D: Quanti dati è possibile inviare tramite l'agente a Log Analytics? È prevista una quantità massima di dati per ogni cliente?
R. Non esiste alcun limite alla quantità di dati caricati. si basa sull'opzione relativa ai prezzi selezionata, ovvero sulla prenotazione della capacità o sul pagamento in base al consumo. Un'area di lavoro Log Analytics è progettata per la scalabilità verticale automatica per gestire il volume proveniente da un cliente, anche se si tratta di terabyte al giorno. Per ulteriori informazioni, vedere la pagina relativa ai [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/monitor/).

L'agente di Log Analytics è stato progettato in modo da avere un impatto minimo. Il volume dei dati varia a seconda delle soluzioni attivate. È possibile trovare informazioni dettagliate sul volume di dati e visualizzare la suddivisione per soluzioni nella pagina [Uso](../../azure-monitor/platform/data-usage.md).

Per ulteriori informazioni, è possibile leggere il [Blog](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) di un cliente mostrandone i risultati dopo aver valutato l'utilizzo delle risorse (footprint) dell'agente log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>D: Quanta larghezza di banda di rete viene usata da Microsoft Management Agent (MMA) quando si inviano dati a Log Analytics?

R. La larghezza di banda è una funzione della quantità di dati inviati. I dati vengono compressi quando vengono inviati in rete.

### <a name="q-how-much-data-is-sent-per-agent"></a>D: Quanti dati vengono inviati per ogni agente?

R. La quantità di dati inviati per ciascun agente dipende da:

* le soluzioni abilitate
* il numero di log e di contatori delle prestazioni che vengono raccolti
* il volume di dati nei log

L'utilizzo complessivo viene visualizzato nella pagina [Utilizzo](../../azure-monitor/platform/data-usage.md) .

Per i computer che possono eseguire l'agente WireData, usare la query seguente per visualizzare la quantità di dati inviati:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passaggi successivi

[Inizia a usare monitoraggio di Azure](../../azure-monitor/overview.md) per scoprire di più su log Analytics e iniziare a usare in pochi minuti.
