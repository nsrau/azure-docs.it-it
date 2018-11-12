---
title: Domande frequenti su Log Analytics | Documentazione Microsoft
description: Risposte alle domande frequenti sul servizio Log Analytics di Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c79a44422944fd2049c47a6729a86d063dbcd15e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232342"
---
# <a name="log-analytics-faq"></a>Domande frequenti su Log Analytics
Le Domande frequenti Microsoft sono un elenco di domande frequenti su Log Analytics in Microsoft Azure. Per altre domande su Log Analytics, visitare il [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.


## <a name="new-logs-experience"></a>Nuova esperienza Log

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>D: Qual è la differenza tra la nuova esperienza Log e Log Analytics?

R: Sono la stessa cosa. [Log Analytics è una funzionalità integrata in Monitoraggio di Azure](../azure-monitor/azure-monitor-rebrand.md) per offrire un'esperienza di monitoraggio più uniforme. La nuova esperienza Log di Monitoraggio di Azure corrisponde esattamente alle query di Log Analytics già usate da molti clienti.

### <a name="q-can-i-still-use-log-search"></a>D: È ancora possibile usare Ricerca Log? 

R: Ricerca log attualmente è ancora disponibile nel portale di OMS e nel portale di Azure con il nome **Log (versione classica)**. Il portale di OMS verrà ritirato ufficialmente il 15 gennaio 2019. L'esperienza Log classica nel portale di Azure classico sarà ritirata gradualmente e sostituita con la nuova esperienza Log. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>D: È ancora possibile utilizzare il portale di Analisi avanzata? 
La nuova esperienza Log nel portale di Azure si basa sul [portale di Analisi avanzata](https://portal.loganalytics.io/), che è comunque ancora accessibile dall'esterno del portale di Azure. La roadmap per il ritiro di questo portale esterno sarà annunciata presto.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>D: Perché non è possibile vedere i pulsanti Esplora query e Salva nella nuova esperienza Log?

I pulsanti **Esplora query**, **Salva** e **Imposta avviso** non sono disponibili durante l'esplorazione dei log nel contesto di una risorsa specifica. Per creare avvisi, salvare o caricare una query, Log deve avere come ambito un'area di lavoro. Per aprire Log nel contesto dell'area di lavoro, selezionare **tutti i servizi** > **Monitor** > **Log**. L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. Per altre informazioni, vedere [Visualizzazione e analisi dei dati in Log Analytics](../log-analytics/log-analytics-log-search-portals.md).

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>D: Come è possibile estrarre Campi personalizzati nella nuova esperienza Log? 

R: L'estrazione di Campi personalizzati è attualmente supportata nell'esperienza Log classica. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>D: Dove si trova la visualizzazione elenco nella nuova esperienza Log? 

R: La visualizzazione elenco non è disponibile nella nuova esperienza Log. È presente una freccia a sinistra di ogni record nella tabella dei risultati. Fare clic su tale freccia per visualizzare i dettagli per un record specifico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-shows-up-but-it-doesnt-include-all-filters-how-can-i-see-the-rest"></a>D: Dopo aver eseguito una query, viene visualizzato un elenco di filtri suggeriti che non include tutti i filtri. Come è possibile visualizzare i filtri restanti? 

R: Ciò che è attualmente visualizzato è un'anteprima dell'implementazione dei nuovi filtri. Ora i filtri si basano sul set di risultati completo anziché essere vincolati dal limite di 10.000 record dell'interfaccia utente. Al momento questo è un elenco dei filtri più popolari e dei 10 valori più comuni per ogni filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>D: Perché viene visualizzato l'errore "Registrare il provider di risorse 'Microsoft.Insights' per questa sottoscrizione per abilitare questa query" in Log, dopo l'eplorazione dalla macchina virtuale? 

R: Per impostazione predefinita, molti provider di risorse sono registrati automaticamente. Tuttavia, potrebbe essere necessario registrare alcuni provider di risorse manualmente. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md#portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>D: Perché viene visualizzato un messaggio di errore di accesso quando si accede a Log da una pagina della macchina virtuale? 

R: Per visualizzare i log della macchina virtuale, è necessario disporre dell'autorizzazione di lettura per le aree di lavoro in cui sono archiviati i log della macchina virtuale. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>D: Perché è possibile accedere alla propria area di lavoro nel portale di OMS, ma viene visualizzato un errore di accesso nel portale di Azure?  

R: Per accedere a un'area di lavoro in Azure, è necessario disporre delle autorizzazioni di Azure. In alcuni casi è possibile che non si disponga delle autorizzazioni di accesso appropriate. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure. Per altre informazioni, vedere [Passaggio del portale di OMS in Azure](../log-analytics/log-analytics-oms-portal-transition.md).

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>D: Perché non è presente la voce Progettazione viste in Log? 
R: Progettazione viste è disponibile in Log solo per gli utenti che dispongono di autorizzazioni di collaboratore o superiori.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>D: È possibile usare il portale di Log Analytics all'esterno di Azure?
R. Sì, la pagina Log in Azure e il [portale di Advanced Analytics](https://portal.loganalytics.io) si basano sullo stesso codice. È in corso l'integrazione di Log Analytics come funzionalità di Monitoraggio di Azure per offrire un'esperienza di monitoraggio unificata. È comunque possibile accedere al portale di Log Analytics tramite l'URL https://portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>Generale

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>D: Come è possibile visualizzare le proprie viste e soluzioni nel portale di Azure? 

R: L'elenco delle viste e delle soluzioni installate è disponibile nel portale di Azure. Fare clic su **Tutti i servizi**. Nell'elenco delle risorse selezionare **Monitor**, quindi fare clic su **...Altro**. L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>D: Perché non è possibile creare aree di lavoro nella regione Stati Uniti centro-occidentali? 

R: Questa regione è al limite della capacità temporanea. Il problema sarà affrontato nella prima metà del 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>D: Log Analytics usa lo stesso agente del Centro sicurezza di Azure?

R: All'inizio di giugno 2017 il Centro sicurezza di Azure ha iniziato a usare Microsoft Monitoring Agent per la raccolta e l'archiviazione dei dati. Per altre informazioni, vedere [Domande frequenti sulla migrazione della piattaforma del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md).

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

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>D: Come si verifica se Log Analytics non sta più raccogliendo dati?

R: Per una sottoscrizione e un'area di lavoro create prima del 2 aprile 2018, ovvero con il piano tariffario *Gratuito*, se vengono inviati più di 500 MB di dati in un giorno, la raccolta dati viene arrestata per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  

Log Analytics crea un evento di tipo *Heartbeat* e può essere usato per determinare se la raccolta dei dati si arresta. 

Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti:`Heartbeat | summarize max(TimeGenerated)`

Per controllare un computer specifico, eseguire la query seguente: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Quando la raccolta dei dati si arresta, a seconda dell'intervallo di tempo selezionato, non verranno visualizzati record restituiti.   

La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:

| Motivi di interruzione della raccolta dati                       | Per riprendere la raccolta dati |
| -------------------------------------------------- | ----------------  |
| Limite di dati gratuiti raggiunto<sup>1</sup>       | Attendere fino al mese successivo; la raccolta riprenderà automaticamente oppure<br> passare a un piano tariffario a pagamento |
| La sottoscrizione di Azure è in sospeso perché: <br> la versione di prova gratuita è terminata <br> Azure Pass è scaduto <br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio                          | Passare a una sottoscrizione a pagamento <br> Passare a una sottoscrizione a pagamento <br> Rimuovere il limite oppure attendere fino ripristino del limite |

<sup>1</sup> Se l'area di lavoro ha un piano tariffario *Gratuito*, è possibile inviare al servizio solo 500 MB di dati al giorno. Quando si raggiunge il limite giornaliero, si interrompe la raccolta dei dati fino al giorno successivo. I dati inviati quando la raccolta dati è sospesa non vengono indicizzati e non sono disponibili per la ricerca. Quando la raccolta dei dati riprende, l'elaborazione avviene solo per i nuovi dati inviati. 

Log Analytics usa l'ora UTC e ogni giorno inizia a mezzanotte UTC. Se l'area di lavoro raggiunge il limite giornaliero, l'elaborazione riprenderà la prima ora del giorno UTC successivo.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>D: Come inviare una notifica all'utente quando la raccolta dati si interrompe?

R: Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in [Creare un nuovo avviso del log](../monitoring-and-diagnostics/alert-metric.md).

Quando si crea l'avviso per l'interruzione della raccolta dati, applicare le seguenti impostazioni:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro di Log Analytics come destinazione della risorsa.
- **Criteri di avviso** consente di specificare quanto segue:
   - **Nome segnale** selezionare **Ricerca log personalizzata**.
   - **Query di ricerca** su `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *30* minuti e **Frequenza di avviso** ogni *10* minuti
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data collection stopped* (Raccolta dati interrotta)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) esistente o crearne uno nuovo, in modo che se l'avviso del log corrisponde ai criteri e un heartbeat manca per più di 15 minuti si riceve una notifica.

## <a name="configuration"></a>Configurazione
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>D: È possibile modificare il nome del contenitore di tabelle/BLOB usato per leggere da Diagnostica di Azure?

R. No, non è attualmente possibile leggere da tabelle o contenitori arbitrari in Archiviazione di Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>D: Quali indirizzi IP usa il servizio Log Analytics? Come assicurarsi che il firewall consenta solo il traffico ai servizi di Log Analytics?

R. Il servizio Log Analytics è basato su Azure. Gli indirizzi IP di Log Analytics si trovano in [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli di indirizzi IP dei data center di Microsoft Azure).

Quando vengono eseguite distribuzioni di servizi, gli indirizzi IP effettivi del servizio Log Analytics vengono modificati. I nomi DNS da consentire attraverso il firewall sono indicati nei [requisiti di rete](log-analytics-concept-hybrid.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>D: Si usa ExpressRoute per connettersi ad Azure. Il traffico di Log Analytics userà la connessione ExpressRoute?

R. I diversi tipi di traffico di ExpressRoute vengono descritti nella [Documentazione di ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Il traffico verso Log Analytics usa il circuito ExpressRoute di peer pubblico.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>D: Esiste un modo semplice per spostare un'area di lavoro di Log Analytics esistente in un'altra area di lavoro di Log Analytics/della sottoscrizione di Azure?

R. Il cmdlet `Move-AzureRmResource` consente di spostare un'area di lavoro di Log Analytics e anche un account di Automazione da una sottoscrizione di Azure a un'altra. Per altre informazioni, vedere [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

Questa modifica può essere apportata anche nel portale di Azure.

Non è possibile spostare dati da un'area di lavoro di Log Analytics a un'altra o cambiare l'area in cui sono archiviati i dati di Log Analytics.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>D: Come aggiungere Log Analytics a System Center Operations Manager?

R: Il passaggio all'aggiornamento cumulativo più recente e l'importazione dei Management Pack consente di connettere Operations Manager a Log Analytics.

>[!NOTE]
>La connessione di Operations Manager a Log Analytics è disponibile solo per System Center Operations Manager 2012 SP1 e versioni successive.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>D: Come è possibile verificare che un agente può comunicare con Log Analytics?

R: Per assicurarsi che l'agente possa comunicare con OMS, andare a: Pannello di controllo, Sicurezza e impostazioni, **Microsoft Monitoring Agent**.

Nella scheda **Analisi dei log di Azure (OMS)** cercare un segno di spunta verde. Un'icona con un segno di spunta verde conferma che l'agente può comunicare con il servizio di Azure.

Un'icona di avviso gialla indica problemi di comunicazione tra l'agente e Log Analytics. Un motivo comune è che il servizio Microsoft Monitoring Agent è stato arrestato. Usare Gestione controllo servizi per riavviare il servizio.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>D: Come si arresta la comunicazione tra un agente e Log Analytics?

R: In System Center Operations Manager rimuovere il computer dall'elenco di computer gestiti da OMS. Operations Manager aggiorna la configurazione dell'agente affinché non invii altri report a Log Analytics. È possibile impedire la comunicazione degli agenti connessi direttamente a Log Analytics tramite il Pannello di controllo, Sicurezza e impostazioni, **Microsoft Monitoring Agent**.
In **Analisi dei log di Azure (OMS)** rimuovere tutte le aree di lavoro elencate.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>D: Perché viene visualizzato un errore quando si tenta di spostare l'area di lavoro da una sottoscrizione di Azure a un'altra?

R: Se si usa il portale di Azure, verificare solo che l'area di lavoro sia selezionata per lo spostamento. Non selezionare le soluzioni; queste si sposteranno automaticamente in seguito allo spostamento dell'area di lavoro. 

Verificare di avere l'autorizzazione in entrambe le sottoscrizioni di Azure.

## <a name="agent-data"></a>Dati dell'agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>D: Quanti dati è possibile inviare tramite l'agente a Log Analytics? È prevista una quantità massima di dati per ogni cliente?
R. Il piano gratuito prevede un limite giornaliero di 500 MB per area di lavoro. I piani Standard e Premium non hanno limiti per la quantità di dati caricati. Come servizio cloud, Log Analytics è progettato per passare automaticamente a un piano superiore per gestire il volume proveniente da un cliente, anche se si tratta di diversi terabyte al giorno.

L'agente di Log Analytics è stato progettato in modo da avere un impatto minimo. Il volume dei dati varia a seconda delle soluzioni attivate. È possibile trovare informazioni dettagliate sul volume di dati e visualizzare la suddivisione per soluzioni nella pagina [Uso](log-analytics-usage.md).

Per altre informazioni, è possibile leggere un [blog di un cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) che mostra i risultati dopo aver valutato l'utilizzo delle risorse (footprint) dell'agente OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>D: Quanta larghezza di banda di rete viene usata da Microsoft Management Agent (MMA) quando si inviano dati a Log Analytics?

R. La larghezza di banda è una funzione della quantità di dati inviati. I dati vengono compressi quando vengono inviati in rete.

### <a name="q-how-much-data-is-sent-per-agent"></a>D: Quanti dati vengono inviati per ogni agente?

R. La quantità di dati inviati per ciascun agente dipende da:

* le soluzioni abilitate
* il numero di log e di contatori delle prestazioni che vengono raccolti
* il volume di dati nei log

Il piano tariffario Gratuito è una valida soluzione per caricare diversi server e misurare il volume di dati tipico. L'utilizzo complessivo viene visualizzato nella pagina [Utilizzo](log-analytics-usage.md) .

Per i computer che possono eseguire l'agente WireData, usare la query seguente per visualizzare la quantità di dati inviati:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Log Analytics](../azure-monitor/overview.md) per altre informazioni su Log Analytics e per essere operativi in pochi minuti.
