---
title: Domande frequenti su Log Analytics | Microsoft Docs
description: Risposte alle domande frequenti sul servizio Log Analytics.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="log-analytics-faq"></a>Domande frequenti su Log Analytics
Queste domande frequenti Microsoft sono relative a Log Analytics in Microsoft Operations Management Suite (OMS). Per altre domande su Log Analytics, visitare il [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e inviare una domanda. Un membro dalla community fornirà supporto agli utenti per individuare le risposte. Se una domanda viene posta più volte, verrà aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="general"></a>Generale
**D. Quali controlli vengono eseguiti dalle soluzioni di valutazione di AD e di SQL?**

R. La query seguente illustra una descrizione di tutti i controlli attualmente eseguiti:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

I risultati possono quindi essere esportati in Excel per analizzarli più attentamente.

**D: Perché viene visualizzato un elemento diverso da *OMS* nell'amministrazione SCOM?**

R: A seconda dell'aggiornamento cumulativo di SCOM in uso, viene visualizzato un nodo relativo a *System Center Advisor*, *Operational Insights* o *Log Analytics*.

L'aggiornamento della stringa di testo a *OMS* è incluso in un Management Pack, che deve essere importato manualmente. Seguire le istruzioni nell'articolo più recente della KB sull'aggiornamento cumulativo di SCOM e aggiornare la console di OMS per visualizzare gli aggiornamenti più recenti nel nodo *OMS* .

**D: Esiste una versione *locale* di OMS?**

R: No. Log Analytics elabora e archivia quantità molto grandi di dati. Come servizio cloud, Log Analytics può aumentare le prestazioni, se necessario, ed evitare qualsiasi impatto sulle prestazioni per l'ambiente.

Essendo un servizio cloud, inoltre, non è necessario mantenere l'infrastruttura di Log Analytics operativa ed è possibile ricevere aggiornamenti e correzioni frequenti delle funzionalità.

## <a name="configuration"></a>Configurazione
**D. È possibile modificare il nome del contenitore di tabelle/BLOB usato per leggere da Diagnostica di Azure?**  

A.  No, attualmente non è possibile, ma è previsto per una versione futura.

**D. Quali indirizzi IP sono usati dai servizi OMS? Come assicurarsi che il firewall consenta solo il traffico ai servizi OMS?**  

R. Il servizio Log Analytics si basa su Azure e gli endpoint ricevono gli indirizzi IP compresi negli [intervalli IP per i data center di Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Quando vengono eseguite distribuzioni di servizi, gli indirizzi IP effettivi dei servizi OMS vengono modificati. I nomi DNS consentiti attraverso il firewall sono documentati in [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

**D. Si usa ExpressRoute per connettersi ad Azure. Il traffico di Log Analytics userà la connessione ExpressRoute?**  

R. I diversi tipi di traffico di ExpressRoute vengono descritti nella [Documentazione di ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Il traffico verso Log Analytics usa il circuito ExpressRoute di peer pubblico.

**D. Esiste un modo semplice per spostare un'area di lavoro di Log Analytics esistente in un'altra area di lavoro di Analytics/sottoscrizione di Azure?**   Ci sono diverse aree di lavoro OMS del cliente che si stanno testando e valutando nella sottoscrizione di Azure e che stanno passando alla produzione e si vuole quindi spostarle nella relativa sottoscrizione di Azure/OMS.  

A. Il cmdlet `Move-AzureRmResource` consente di spostare un'area di lavoro di Log Analytics e anche un account di Automazione da una sottoscrizione di Azure a un'altra. Per altre informazioni, vedere [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Questa modifica può essere apportata anche nel portale di Azure.

Non è possibile spostare dati da un'area di lavoro di Log Analytics a un'altra o cambiare l'area in cui sono archiviati i dati di Log Analytics.

**D: Come si aggiunge OMS a SCOM?**

R: Il passaggio all'aggiornamento cumulativo più recente e l'importazione dei Management Pack consentirà di connettere SCOM a Log Analytics.

Si noti che la connessione SCOM a Log Analytics è disponibile solo per SCOM 2012 SP1 e versioni successive.

**D: Come è possibile verificare che un agente può comunicare con Log Analytics?**

R: Per assicurarsi che l'agente possa comunicare con OMS, andare a: Pannello di controllo, Sicurezza e impostazioni, **Microsoft Monitoring Agent**.

Nella scheda **Analisi dei log di Azure (OMS)** cercare un segno di spunta verde. Un'icona con un segno di spunta verde conferma che l'agente può comunicare con il servizio OMS.

Un'icona con un avviso giallo indica problemi di comunicazione tra l'agente e OMS. Un motivo comune è che il servizio Microsoft Monitoring Agent è stato arrestato e deve essere riavviato.

**D: Come si arresta la comunicazione tra un agente e Log Analytics?**

R: In SCOM rimuovere il computer dall'elenco gestito OMS. In questo modo verrà arrestata ogni comunicazione tramite SCOM per tale agente. È possibile impedire che gli agenti connessi direttamente a OMS comunichino con OMS da Pannello di controllo, Sicurezza e impostazioni, **Microsoft Monitoring Agent**.
In **Analisi dei log di Azure (OMS)**rimuovere tutte le aree di lavoro elencate.

## <a name="agent-data"></a>Dati dell'agente
**D. Quanti dati è possibile inviare tramite l'agente a Log Analytics? È prevista una quantità massima di dati per ogni cliente?**  
R. Il piano gratuito prevede un limite giornaliero di 500 MB per area di lavoro. I piani Standard e Premium non hanno limiti per la quantità di dati caricati. Come servizio cloud, Log Analytics in OMS è progettato per passare automaticamente a un piano superiore per gestire il volume proveniente da un cliente, anche se si tratta di diversi terabyte al giorno.

L'agente di Log Analytics è stato progettato in modo da avere un impatto minimo ed eseguire la compressione di base dei dati. Uno dei clienti ha in effetti scritto un blog sui test eseguiti sull'agente e su come ne sia rimasto impressionato. Il volume dei dati varierà a seconda delle soluzioni abilitate dai clienti. È possibile trovare informazioni dettagliate sul volume di dati e visualizzare la suddivisione per soluzioni nel riquadro **Utilizzo** della pagina di panoramica di OMS.

Per altre informazioni, è possibile consultare il [blog di un cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sull'impatto ridotto dell'agente OMS.

**D. Quanta larghezza di banda di rete viene usata da Microsoft Management Agent (MMA) quando si inviano dati a Log Analytics?**

R. La larghezza di banda è una funzione della quantità di dati inviati. I dati vengono compressi quando vengono inviati in rete.

**D. Quanti dati vengono inviati per ogni agente?**

R. Questo dipende soprattutto dagli elementi seguenti:

* Soluzioni abilitate
* Numero di log e di contatori delle prestazioni che vengono raccolti
* Volume di dati nei log

Il piano tariffario Gratuito è una valida soluzione per caricare diversi server e misurare il volume di dati tipico. L'utilizzo complessivo viene visualizzato nella pagina **Utilizzo** .
Per i computer che possono eseguire l'agente WireData, è possibile visualizzare la quantità di dati inviati usando la query seguente:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Log Analytics](log-analytics-get-started.md) per altre informazioni su Log Analytics e per essere operativi in pochi minuti.

<!--HONumber=Oct16_HO2-->


