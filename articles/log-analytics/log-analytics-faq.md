---
title: Domande frequenti su Log Analytics | Documentazione Microsoft
description: Risposte alle domande frequenti sul servizio Log Analytics di Azure.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 0b27386cd0f9f3ae50314b8c5d7708aea3e3d028
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="log-analytics-faq"></a>Domande frequenti su Log Analytics
Le Domande frequenti Microsoft sono un elenco di domande frequenti su Log Analytics in Microsoft Azure. Per altre domande su Log Analytics, visitare il [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="general"></a>Generale

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>D: Log Analytics usa lo stesso agente del Centro sicurezza di Azure?

R. All'inizio di giugno 2017 il Centro sicurezza di Azure ha iniziato a usare Microsoft Monitoring Agent per la raccolta e l'archiviazione dei dati. Per altre informazioni, vedere [Domande frequenti sulla migrazione della piattaforma del Centro sicurezza di Azure](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>D: Quali controlli vengono eseguiti dalle soluzioni di valutazione SQL e AD?

R. La query seguente illustra una descrizione di tutti i controlli attualmente eseguiti:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

I risultati possono quindi essere esportati in Excel per analizzarli più attentamente.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>D: perché non viene visualizzato OMS nella console di System Center Operations Manager?

R: A seconda dell'aggiornamento cumulativo di Operations Manager in uso, viene visualizzato un nodo relativo a *System Center Advisor*, *Operational Insights* o *Log Analytics*.

L'aggiornamento della stringa di testo a *OMS* è incluso in un Management Pack, che deve essere importato manualmente. Per visualizzare il testo e le funzionalità correnti, seguire le istruzioni nell'ultimo articolo della KB sull'aggiornamento cumulativo di System Center Operations Manager e aggiornare la console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>D: Esiste una versione locale di Log Analytics?

R: No. Log Analytics è un servizio cloud scalabile che elabora e archivia grandi quantità di dati. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>D: Come si verifica se Log Analytics non sta più raccogliendo dati?

R: Se l'utente ha il piano tariffario gratuito e ha inviato più di 500 MB di dati in un giorno, la raccolta dati si interrompe per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.

Log Analytics crea un evento di tipo *Operazione* quando la raccolta dei dati si avvia e si arresta. 

Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti:`Type=Operation OperationCategory="Data Collection Status"`

Quando la raccolta dei dati si interrompe, *OperationStatus* è **Attenzione**. Quando la raccolta dei dati si avvia, *OperationStatus* è **Riuscito**. 

La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:

| Motivi di interruzione della raccolta dati                       | Per riprendere la raccolta dati |
| -------------------------------------------------- | ----------------  |
| Limite giornaliero di dati gratuiti raggiunto<sup>1</sup>       | Attendere fino al giorno successivo; la raccolta riprenderà automaticamente oppure<br> passare a un piano tariffario a pagamento |
| La sottoscrizione di Azure è in sospeso perché: <br> la versione di prova gratuita è terminata <br> Azure Pass è scaduto <br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio                          | Passare a una sottoscrizione a pagamento <br> Passare a una sottoscrizione a pagamento <br> Rimuovere il limite oppure attendere fino ripristino del limite |

<sup>1</sup> Se l'area di lavoro ha un piano tariffario gratuito, è possibile inviare solo 500 MB di dati al giorno al servizio. Quando si raggiunge il limite giornaliero, si interrompe la raccolta dei dati fino al giorno successivo. I dati inviati quando la raccolta dati è sospesa non vengono indicizzati e non sono disponibili per la ricerca. Quando la raccolta dei dati riprende, l'elaborazione avviene solo per i nuovi dati inviati. 

Log Analytics usa l'ora UTC e ogni giorno inizia a mezzanotte UTC. Se l'area di lavoro raggiunge il limite giornaliero, l'elaborazione riprenderà la prima ora del giorno UTC successivo.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>D: Come inviare una notifica all'utente quando la raccolta dati si interrompe?

R: Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in [Creare una regola di avviso](log-analytics-alerts-creating.md#create-an-alert-rule).

Quando si crea l'avviso per l'interruzione della raccolta dati, applicare le seguenti impostazioni:
- **Nome** su *Data collection stopped* (Raccolta dati interrotta)
- **Gravità** su *Avviso*
- **Query di ricerca** su `Type=Operation OperationCategory="Data Collection Status" OperationStatus=Warning`
- **Intervallo di tempo** su *2 Hours* (2 ore).
- **Frequenza di avviso** su un'ora, dal momento che i dati di utilizzo vengono aggiornati solo una volta ogni ora.
- **Genera l'avviso in base a** sul *numero di risultati*
- **Numero di risultati** su *Maggiore di 0*

Seguire la procedura descritta in [Aggiungere azioni alle regole di avviso in Log Analytics](log-analytics-alerts-actions.md) per configurare un'azione di posta elettronica, webhook o runbook per la regola di avviso.


## <a name="configuration"></a>Configurazione
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>D: È possibile modificare il nome del contenitore di tabelle/BLOB usato per leggere da Diagnostica di Azure?

R. No, non è attualmente possibile leggere da tabelle o contenitori arbitrari in Archiviazione di Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>D: Quali indirizzi IP usa il servizio Log Analytics? Come assicurarsi che il firewall consenta solo il traffico ai servizi di Log Analytics?

R. Il servizio Log Analytics è basato su Azure. Gli indirizzi IP di Log Analytics si trovano in [Microsoft Azure Datacenter IP Ranges](http://www.microsoft.com/download/details.aspx?id=41653) (Intervalli di indirizzi IP dei data center di Microsoft Azure).

Quando vengono eseguite distribuzioni di servizi, gli indirizzi IP effettivi del servizio Log Analytics vengono modificati. I nomi DNS consentiti attraverso il firewall sono documentati in [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>D: Si usa ExpressRoute per connettersi ad Azure. Il traffico di Log Analytics userà la connessione ExpressRoute?

R. I diversi tipi di traffico di ExpressRoute vengono descritti nella [Documentazione di ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Il traffico verso Log Analytics usa il circuito ExpressRoute di peer pubblico.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>D: Esiste un modo semplice per spostare un'area di lavoro di Log Analytics esistente in un'altra area di lavoro di Log Analytics/della sottoscrizione di Azure?

R. Il cmdlet `Move-AzureRmResource` consente di spostare un'area di lavoro di Log Analytics e anche un account di Automazione da una sottoscrizione di Azure a un'altra. Per altre informazioni, vedere [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

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
In **Analisi dei log di Azure (OMS)**rimuovere tutte le aree di lavoro elencate.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>D: Perché viene visualizzato un errore quando si tenta di spostare l'area di lavoro da una sottoscrizione di Azure a un'altra?

R: Se si usa il portale di Azure, verificare solo che l'area di lavoro sia selezionata per lo spostamento. Non selezionare le soluzioni; queste si sposteranno automaticamente in seguito allo spostamento dell'area di lavoro. 

Verificare di avere l'autorizzazione in entrambe le sottoscrizioni di Azure.

## <a name="agent-data"></a>Dati dell'agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>D: Quanti dati è possibile inviare tramite l'agente a Log Analytics? È prevista una quantità massima di dati per ogni cliente?
R. Il piano gratuito prevede un limite giornaliero di 500 MB per area di lavoro. I piani Standard e Premium non hanno limiti per la quantità di dati caricati. Come servizio cloud, Log Analytics è progettato per passare automaticamente a un piano superiore per gestire il volume proveniente da un cliente, anche se si tratta di diversi terabyte al giorno.

L'agente di Log Analytics è stato progettato in modo da avere un impatto minimo. Uno dei clienti ha scritto un blog sui test eseguiti sull'agente e su come ne sia rimasto impressionato. Il volume dei dati varia a seconda delle soluzioni attivate. È possibile trovare informazioni dettagliate sul volume di dati e visualizzare la suddivisione per soluzioni nella pagina [Uso](log-analytics-usage.md).

Per altre informazioni, è possibile consultare il [blog di un cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sull'impatto ridotto dell'agente OMS.

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
* [Introduzione a Log Analytics](log-analytics-get-started.md) per altre informazioni su Log Analytics e per essere operativi in pochi minuti.

