---
title: Confronto tra i prodotti per il monitoraggio Microsoft | Microsoft Docs
description: "Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.  Questo articolo identifica i diversi servizi inclusi in OMS e fornisce i collegamenti al contenuto dettagliato."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
# <a name="microsoft-monitoring-product-comparison"></a>Confronto tra i prodotti per il monitoraggio Microsoft
Questo articolo offre un confronto tra System Center Operations Manager (SCOM) e Log Analytics in Operations Management Suite (OMS) dal punto di vista dell'architettura, della logica con cui monitorano le risorse e di come eseguono l'analisi dei dati raccolti.  L'obiettivo è quello di fornire una conoscenza di base delle differenze e dei relativi punti di forza.  

## <a name="basic-architecture"></a>Architettura di base
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Tutti i componenti SCOM vengono installati nel data center.  [Gli agenti vengono installati](http://technet.microsoft.com/library/hh551142.aspx) in computer Windows e Linux gestiti da SCOM.  Gli agenti si connettono ai [server di gestione](https://technet.microsoft.com/library/hh301922.aspx) che comunicano con il data warehouse e il database SCOM.  Gli agenti si basano sull'autenticazione del dominio per connettersi ai server di gestione.  Quelli esterni a un dominio trusted possono eseguire l'autenticazione del certificato o connettersi a un [server gateway](https://technet.microsoft.com/library/hh212823.aspx).

SCOM richiede due database SQL, uno per i dati operativi e un altro data warehouse per supportare la creazione di report e l'analisi dei dati.  Un [server di report](https://technet.microsoft.com/library/hh298611.aspx) esegue SQL Reporting Services per creare report sui dati dal data warehouse. 

SCOM può monitorare le risorse cloud usando i Management Pack per prodotti come [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708) e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Questi Management Pack usano uno o più agenti locali come proxy per trovare le risorse cloud ed eseguire flussi di lavoro per misurarne le prestazioni e la disponibilità.  Gli agenti proxy vengono usati anche per [monitorare i dispositivi di rete](https://technet.microsoft.com/library/hh212935.aspx) e altre risorse esterne.

La Console operatore è un'applicazione Windows che si connette a uno dei server di gestione e consente all'amministratore di visualizzare e analizzare i dati raccolti e di configurare l'ambiente SCOM.  Una console basata sul Web può essere ospitata da un server IIS e offre l'analisi dei dati tramite un browser.

![Architettura SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
La maggior parte dei componenti OMS si trova nel cloud di Azure, quindi è possibile distribuirli e gestirli con attività amministrative e costi ridotti al minimo.  Tutti i dati raccolti da Log Analytics vengono archiviati nel repository OMS.

Log Analytics può raccogliere dati da una delle tre origini seguenti:

* Macchine virtuali e computer fisici che eseguono Windows e [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) o Linux e l'[agente Operations Management Suite per Linux](https://technet.microsoft.com/library/mt622052.aspx).  Questi computer possono essere locali oppure macchine virtuali in Azure o in un altro cloud.
* Un account di archiviazione di Azure con dati di [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) raccolti dalla macchina virtuale, dal ruolo Web o dal ruolo di lavoro di Azure.
* [Connessione a un gruppo di gestione SCOM](https://technet.microsoft.com/library/mt484104.aspx).  In questa configurazione gli agenti comunicano con i server di gestione SCOM che inviano i dati al database SCOM da dove vengono quindi inviati all'archivio dati OMS.
  Gli amministratori analizzano i dati raccolti e configurano Log Analytics con il portale OMS ospitato in Azure e accessibile da qualsiasi browser.  Per le piattaforme standard sono disponibili app per dispositivi mobili per accedere a questi dati.

![Architettura di Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrazione di SCOM e Log Analytics
Quando SCOM viene usato come origine dati per Log Analytics, è possibile sfruttare le funzionalità di entrambi i prodotti in un ambiente di monitoraggio ibrido.  È possibile configurare gli agenti SCOM esistenti nella Console operatore da gestire con OMS, oltre a continuare a eseguire i Management Pack da SCOM.  
I dati da un gruppo di gestione SCOM connesso vengono inviati a Log Analytics usando uno dei quattro metodi seguenti:

* Gli eventi e i dati sulle prestazioni vengono raccolti dall'agente e inviati a SCOM.  I server di gestione in SCOM inviano quindi i dati a Log Analytics.
* Alcuni eventi, ad esempio i log di IIS e gli eventi di sicurezza, continuano a essere inviati direttamente a Log Analytics dall'agente.
* Alcune soluzioni forniranno software aggiuntivo all'agente o richiederanno che il software da installare raccolga dati aggiuntivi.  Questi dati in genere verranno inviati direttamente a Log Analytics.
* Alcune soluzioni raccoglieranno direttamente dai server di gestione SCOM i dati che non hanno origine dall'agente.  Ad esempio, la [soluzione Gestione avvisi](https://technet.microsoft.com/library/mt484092.aspx) raccoglie gli avvisi da SCOM dopo che sono stati creati.

## <a name="monitoring-logic"></a>Logica di monitoraggio
SCOM e Log Analytics usano dati simili raccolti dagli agenti, ma presentano differenze significative nel modo di definire e implementare la logica per la raccolta dati e di analizzare i dati raccolti.

### <a name="operations-manager"></a>Operations Manager
La logica di monitoraggio per SCOM viene implementata nei [Management Pack](https://technet.microsoft.com/library/hh457558.aspx) che contengono la logica per trovare i componenti da monitorare, per misurare l'integrità di tali componenti e per raccogliere i dati da analizzare.  Il monitoraggio dei dati può essere semplice quanto la raccolta di un contatore delle prestazioni o degli eventi oppure può usare una logica complessa implementata in uno script.  Sono disponibili Management Pack che includono il monitoraggio completo per svariate [applicazioni Microsoft e di terze parti](http://go.microsoft.com/fwlink/?LinkId=82105) oltre che per dispositivi hardware e di rete.  È possibile [creare i propri Management Pack](http://aka.ms/mpauthor) per applicazioni personalizzate.

I Management Pack contengono più flussi di lavoro ognuno dei quali esegue una funzione di monitoraggio diversa, ad esempio il campionamento di un contatore delle prestazioni, il controllo dello stato di un servizio o l'esecuzione di uno script.  Ogni flusso di lavoro viene eseguito in modo indipendente e definisce i propri risultati, ad esempio in quale database scriverà e se genererà un avviso. 

È possibile eseguire l'override dei dettagli del flusso di lavoro, ad esempio la frequenza di esecuzione, la soglia in cui un errore viene preso in considerazione e la gravità dell'avviso generato.  È anche possibile fornire altre funzionalità aggiungendo i propri flussi di lavoro.

![Override](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

I Management Pack vengono installati nel database di Operations Manager e distribuiti automaticamente agli agenti dai server di gestione.  Ogni agente scaricherà automaticamente i Management Pack e caricherà i flussi di lavoro pertinenti alle applicazioni installate.  I dati raccolti dall'agente vengono inviati di nuovo al server di gestione per l'inserimento nel data warehouse e nel database SCOM.  La Console operatore consente di visualizzare e analizzare questi dati con viste personalizzate, dashboard e report inclusi nel Management Pack.

La distribuzione dei Management Pack è illustrata nel diagramma seguente.

![Flusso di Management Pack](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Raccolta di eventi e prestazioni
Log Analytics raccoglie gli eventi e i contatori delle prestazioni dai sistemi degli agenti, usando origini come il registro eventi di Windows, i log di IIS e Syslog.  È possibile definire i criteri in base ai quali raccogliere i dati con il portale di Log Analytics e quindi creare query di log per analizzare i dati raccolti.  Un set di criteri standard viene definito quando si crea l'area di lavoro di OMS ed è possibile definire dati aggiuntivi per applicazioni specifiche. 

![Definizione di registri eventi in Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Mentre SCOM ha diversi flussi di lavoro dettagliati che in genere definiscono criteri specifici per i dati e l'azione da eseguire in risposta, Log Analytics ha invece criteri più generali per la raccolta dati.  Le soluzioni e le query di log forniscono criteri più mirati per l'analisi e l'intervento su dati specifici nel cloud dopo che sono stati raccolti.

#### <a name="solutions"></a>Soluzioni
Le soluzioni forniscono la logica aggiuntiva per la raccolta e l'analisi dei dati.  È possibile selezionare le soluzioni da aggiungere alla sottoscrizione di OMS dalla Raccolta soluzioni.

![Raccolta soluzioni](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Le soluzioni vengono eseguite principalmente nel cloud e forniscono l'analisi di eventi e contatori delle prestazioni raccolti nel repository OMS.  Possono anche definire dati aggiuntivi da raccogliere, che possono essere analizzati con query di log o con l'interfaccia utente aggiuntiva fornita dalla soluzione nel dashboard di OMS. 

Ad esempio, la [soluzione di rilevamento modifiche](https://technet.microsoft.com/library/mt484099.aspx) rileva le modifiche alla configurazione nei sistemi degli agenti e scrive gli eventi nel repository OMS che possono essere analizzati con diverse viste grafiche che riepilogano le modifiche rilevate.  È possibile eseguire il drill-down dalla vista di riepilogo alle query di log che visualizzano i dati dettagliati raccolti dalla soluzione.

Anche se è possibile selezionare le soluzioni da aggiungere alla sottoscrizione, attualmente non è possibile creare soluzioni personalizzate.  È possibile selezionare gli eventi e i contatori delle prestazioni da raccogliere e creare visualizzazioni personalizzate basate sulle query di log.

La logica di monitoraggio per Log Analytics è riepilogata nel diagramma seguente.

![Flusso di soluzioni di Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitoraggio dell'integrità
### <a name="operations-manager"></a>Operations Manager
SCOM può modellare i diversi componenti di un'applicazione e offrire l'integrità in tempo reale per ognuno.  Questo consente non solo di visualizzare gli errori rilevati e le prestazioni nel corso del tempo, ma anche di convalidare l'integrità effettiva di un'applicazione o di un sistema e di ogni componente in un determinato momento.  Il motore di integrità in SCOM, conoscendo i periodi di tempo in cui un'applicazione è disponibile, supporta anche contratti di servizio che analizzano e creano report sulla disponibilità di un'applicazione nel corso del tempo.

La visualizzazione seguente, ad esempio, mostra l'integrità in tempo reale dei motori di database SQL monitorati da SCOM.  L'integrità di ogni database per uno dei motori di database viene indicata nella metà inferiore della visualizzazione.

![Vista di stato](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Esplora stati per uno dei motori di database viene illustrato sotto con i monitor usati per determinare l'integrità generale.  Questi monitor vengono definiti nel Management Pack di SQL ed eseguiti in tutti i motori di database SQL trovati da SCOM.

![Esplora stati](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

È possibile combinare componenti di più sistemi per misurare l'integrità di un'applicazione distribuita.  Questo può essere particolarmente utile per le applicazioni line-of-business che includono più componenti distribuiti.  È possibile creare un modello che misura l'integrità di ogni componente ed eseguire così il rollup della disponibilità per l'applicazione.

Active Directory è un esempio di Management Pack che fornisce un modello per analizzare i componenti distribuiti.  Il diagramma di esempio seguente mostra l'integrità dell'intero ambiente e la relazione tra foreste, domini e controller di dominio.  Ogni componente include sottocomponenti e più monitor simili all'esempio di SQL precedente.

![Vista diagramma SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS non include un motore comune per modellare le applicazioni o misurarne l'integrità in tempo reale.  Singole soluzioni possono valutare l'integrità generale di determinati servizi in base ai dati raccolti e possono installare la logica personalizzata nell'agente per eseguire l'analisi in tempo reale.  Poiché le soluzioni vengono eseguite nel cloud con l'accesso al repository OMS, spesso possono fornire un'analisi più approfondita di quella eseguita in genere dai Management Pack. 

Ad esempio, le [soluzioni di valutazione di AD e di SQL](https://technet.microsoft.com/library/mt484102.aspx) analizzano i dati raccolti e forniscono una valutazione per diversi aspetti dell'ambiente.  Sono inclusi suggerimenti sui miglioramenti che possono essere apportati per aumentare la disponibilità e le prestazioni dell'ambiente.

![Soluzioni di valutazione di AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analisi dei dati
SCOM e Log Analytics forniscono funzionalità diverse per analizzare i dati raccolti.  SCOM ha viste e dashboard nella Console operatore per l'analisi dei dati recenti in svariati formati e report per la presentazione dei dati dal data warehouse in formato tabulare.  Log Analytics fornisce un linguaggio di query di log completo e un'interfaccia per l'analisi dei dati nel repository OMS.  Quando SCOM viene usato come origine dati per Log Analytics, il repository include i dati raccolti da SCOM, quindi si possono usare gli strumenti di Log Analytics per analizzare i dati da entrambi i sistemi.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Visualizzazioni
Le viste nella Console operatore consentono di visualizzare tipi di dati diversi raccolti da SCOM in formati diversi, in genere tabulare per eventi, avvisi e dati di stato e grafici a linee per i dati sulle prestazioni.  Le viste eseguono un'analisi o un consolidamento dei dati minimo, ma consentono di filtrarli in base a determinati criteri. 

![Visualizzazioni](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

I Management Pack forniscono in genere più viste che supportano l'applicazione o il sistema monitorato.  Sono incluse viste di stato per i diversi oggetti trovati dal Management Pack, viste Avvisi per i problemi rilevati e viste Prestazioni per i contatoti.

Le viste sono particolarmente adatte per l'analisi dello stato corrente dell'ambiente, inclusi gli avvisi aperti e lo stato di integrità dei sistemi e degli oggetti monitorati.  È possibile eseguire il drill-down su un evento dettagliato o sui dati sulle prestazioni supportando un particolare avviso per diagnosticare la causa radice. Analogamente, è possibile visualizzare le prestazioni e l'integrità di componenti diversi di un'applicazione per valutarne l'integrità corrente.

#### <a name="dashboards"></a>Dashboard
I dashboard nella Console operatore usano per lo più gli stessi dati delle viste, ma sono più personalizzabili e possono includere visualizzazioni più avanzate.  È disponibile un set di dashboard standard che è possibile personalizzare facilmente per le proprie esigenze.  È anche possibile usare un widget di PowerShell che può visualizzare i dati restituiti da una query di PowerShell.

![Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Gli sviluppatori hanno la possibilità di aggiungere componenti personalizzati ai dashboard inclusi nei Management Pack.  Possono essere altamente specializzati per una particolare applicazione, ad esempio il dashboard nel Management Pack di SQL illustrato sotto.  Questo dashboard può anche essere usato come modello per le versioni personalizzate.

![Dashboard di SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Report
I report in SCOM analizzano i dati dal data warehouse in formato tabulare.  È possibile stamparli e pianificarne il recapito automatizzato in formati di file diversi, inclusi PDF, CSV e Word.  I report usano i dati del data warehouse, quindi sono particolarmente adatti per l'analisi delle tendenze a lungo termine.

I Management Pack forniscono in genere report personalizzati per una determinata applicazione.  È anche possibile selezionare un report da una raccolta report di generici che è possibile personalizzare per le proprie applicazioni o per l'esecuzione di un'analisi ad hoc.

Il seguente è un report sulle prestazioni di esempio che illustra i dati raccolti dal Management Pack Active Directory.

![Report](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics ha un [linguaggio di query](https://technet.microsoft.com/library/mt484120.aspx) che è possibile usare per eseguire l'analisi sui dati di più applicazioni senza dover creare una vista o un report personalizzato.  Poiché OMS viene implementato nel cloud, le prestazioni delle query e l'analisi dei dati non sono soggette a limitazioni ed è possibile analizzare rapidamente query che includono milioni di record. 

Le query in Log Analytics sono alla base anche di altre funzionalità.  È possibile salvare una query, esportarne i risultati in Excel o eseguirla automaticamente a intervalli regolari e generare un avviso se i risultati corrispondono a determinati criteri.  

![Flusso di query di log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Di seguito è riportato un esempio di una query di Log Analytics.  In questo esempio vengono restituiti e raggruppati per ID evento tutti gli eventi che contengono "started" nel nome.  L'utente si limita a fornire la query e Log Analytics genera in modo dinamico l'interfaccia utente per eseguire l'analisi.  Selezionando un elemento nell'elenco, verranno restituiti i dati dettagliati dell'evento.

![Query di log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Oltre a fornire l'analisi ad hoc, le query in Log Analytics possono essere salvate per un uso futuro e anche aggiunte al [dashboard OMS](http://technet.microsoft.com/library/mt484090.aspx) come illustrato nell'esempio seguente.

![dashboard OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi
* Distribuire [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Iscriviti a [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics).  


