---
title: Integrazione con Operations Management Suite (OMS) | Microsoft Docs
description: Oltre a usare le funzionalità standard di OMS, è possibile integrarlo con altre applicazioni e servizi di gestione per offrire un ambiente di gestione ibrida, scenari di gestione personalizzati specifici dell'ambiente o un'esperienza di gestione personalizzata ai clienti.  Questo articolo offre una panoramica delle diverse opzioni per l'integrazione con OMS e include collegamenti ad articoli contenenti informazioni tecniche dettagliate.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: bwren

---
# <a name="integrating-with-operations-management-suite-(oms)"></a>Integrazione con Operations Management Suite (OMS)
Operations Management Suite è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.  Oltre a usare le funzionalità standard di OMS, è possibile integrarlo con altre applicazioni e servizi di gestione per offrire un ambiente di gestione ibrida, scenari di gestione personalizzati specifici dell'ambiente o un'esperienza di gestione personalizzata ai clienti.  Questo articolo offre una panoramica delle diverse opzioni per l'integrazione con i servizi OMS e include collegamenti ad articoli contenenti informazioni tecniche dettagliate. 

## <a name="log-analytics"></a>Log Analytics
I dati di gestione raccolti da Log Analytics vengono archiviati in un repository ospitato in Azure.  Tutti i dati archiviati nel repository sono disponibili nelle ricerche log che offrono analisi rapide in volumi di dati estremamente grandi.  I requisiti di integrazione possono richiedere l'inserimento di nuovi dati nel repository, rendendoli così disponibili per l'analisi, o l'estrazione dei dati presenti nel repository per offrire una nuova visualizzazione o integrarli con un altro strumento di gestione.

I dati nel repository vengono archiviati come record.  Quando si inseriscono i dati nel repository, è necessario indicare agli utenti il tipo di record usato dalla soluzione e specificare una descrizione delle relative proprietà.  Queste informazioni sui dati con i quali si lavora sono necessarie al momento del recupero dei dati.

![Popolamento del repository OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Popolare il repository Log Analytics
Sono disponibili diversi metodi per il popolamento del repository OMS.  Il metodo da usare dipende da fattori come la posizione in cui si trova l'origine dei dati, il formato dei dati e i clienti da supportare.  Una volta che i dati sono stati archiviati nel repository, le modalità di raccolta non fanno alcuna differenza.

Le sezioni seguenti descrivono le diverse opzioni per il popolamento del repository OMS.

#### <a name="connected-sources-and-data-sources"></a>Origini dati e origini connesse
Le origini connesse sono le posizioni in cui è possibile recuperare i dati per il repository OMS.  Le origini dati e le soluzioni vengono eseguite nelle origini connesse e definiscono i dati specifici che vengono raccolti.  Se l'applicazione scrive i dati in una di queste origini dati, è possibile raccoglierli configurando l'origine dati.  Se ad esempio l'applicazione crea eventi Syslog, essi possono essere raccolti dall'origine dati Syslog in un agente Linux.

* [Origini dati in Log Analytics](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluzioni
Le soluzioni estendono le funzionalità di OMS.  Una soluzione può raccogliere dati dall'origine connessa o eseguire analisi sui record già raccolti nel repository.  Ogni soluzione offerta da Microsoft possiede un singolo articolo che specifica i dettagli sui dati raccolti.

* [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>API di raccolta dati HTTP
L'API di raccolta dati HTTP di Log Analytics è un'API REST che consente di aggiungere dati JSON nel repository di Log Analytics.  È possibile sfruttare questa API quando un'applicazione non specifica dati tramite una delle altre origini dati o soluzioni.  Può essere usata per popolare il repository dai client che possono chiamare l'API e non si basano sulla pianificazione raccolta di qualsiasi origine dati o soluzione.

* [Log Analytics HTTP Data Collector API](../log-analytics/log-analytics-data-collector-api.md) (API di raccolta dati HTTP di Log Analytics)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperare i dati dal repository di Log Analytics
Sono disponibili diversi metodi per il recupero dei dati dal repository OMS.  È possibile consentire agli utenti di recuperare i dati con la console di OMS e offrire loro diversi tipi di visualizzazioni e analisi.  È anche possibile recuperare i dati da un processo esterno, ad esempio un'altra soluzione di gestione.

#### <a name="log-searches"></a>Ricerche log
Tutti i dati archiviati nel repository OMS sono disponibili tramite le ricerche log.  Gli utenti possono eseguire le proprie analisi ad hoc nella console di OMS o creare un dashboard con una visualizzazione per una specifica ricerca log.  Le soluzioni possono contenere visualizzazioni personalizzate con le visualizzazioni basate sulle ricerche predefinite.  È possibile usare l'API di ricerca log per accedere ai dati nel repository OMS da un'applicazione o uno strumento di gestione esterni.  

* [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
* [API REST di Log Analytics per la ricerca nei log](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics cmdlets](https://msdn.microsoft.com/library/mt188224.aspx) (Cmdlet di Log Analytics)

#### <a name="custom-views"></a>Visualizzazioni personalizzate
Con Progettazione viste è possibile creare visualizzazioni personalizzate nella console di OMS che consentono agli utenti di visualizzare e analizzare i dati nella soluzione.  Ogni visualizzazione include un riquadro visualizzato nella pagina principale della console e un numero qualsiasi di parti di visualizzazione basate sulle ricerche log definite.

* [Log Analytics View Designer](../log-analytics/log-analytics-view-designer.md) (Progettazione viste di Log Analytics)

#### <a name="power-bi"></a>Power BI
Log Analytics può esportare automaticamente dati dal repository OMS in Power BI per poterne sfruttare gli strumenti di analisi e le visualizzazioni.  Esegue l'esportazione in base a una pianificazione in modo che i dati vengano mantenuti aggiornati. 

* [Esportare i dati di Log Analytics in Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automazione
Con OMS è possibile automatizzare i processi per reagire ai dati raccolti o eseguire altre funzioni di gestione.  È possibile raccogliere i dati dall'applicazione e inserirli nel repository OMS oppure automatizzare la correzione di un problema noto in risposta a dati presenti nel repository. 

![Automazione](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook
I runbook in Automazione di Azure eseguono script e flussi di lavoro PowerShell nel cloud di Azure.  Possono essere usati per gestire le risorse in Azure o qualsiasi altra risorsa accessibile dal cloud.  I runbook possono anche essere eseguiti in un datacenter locale tramite un ruolo di lavoro ibrido per runbook.  È possibile avviare un runbook dal portale di Azure o da processi esterni usando alcuni metodi, ad esempio PowerShell o l'API di Automazione.

* [Avvio di un Runbook in Automazione di Azure](../automation/automation-starting-a-runbook.md)
* [Azure Automation cmdlets](https://msdn.microsoft.com/library/dn690262.aspx) (Cmdlet di Automazione di Azure)
* [Automation REST API](https://msdn.microsoft.com/library/mt662285.aspx) (API REST di Automazione)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx) (Automazione .NET)

### <a name="alerts"></a>Avvisi
Le regole di avviso eseguono automaticamente le ricerche log in base a una pianificazione.  Se i risultati corrispondono a particolari criteri, l'avviso risultante può avviare un runbook in Automazione di Azure o chiamare un webhook che può avviare un processo esterno.  Entrambe queste risposte possono includere i dettagli dell'avviso tra cui i dati restituiti nella ricerca log.

* [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md)
* [API REST degli avvisi di Log Analytics](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Backup e Site Recovery
Backup di Azure e Site Recovery offrono servizi per la protezione dei dati aziendali e per assicurare la disponibilità di server e applicazioni.  È possibile usare questi servizi per eseguire scenari come offrire servizi di backup per l'applicazione o avviare un failover di una macchina virtuale.

* [Azure Backup cmdlets](https://msdn.microsoft.com/library/mt619253.aspx) (Cmdlet di Backup di Azure)
* [Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx) (API REST di Azure Site Recovery)
* [Azure Site Recovery Cmdlets](https://msdn.microsoft.com/library/mt637930.aspx) (Cmdlet di Azure Site Recovery)

## <a name="custom-solutions"></a>Soluzioni personalizzate
È possibile incapsulare la logica di integrazione in una soluzione personalizzata per l'esecuzione nell'area di lavoro o nell'area di lavoro del cliente.  La soluzione può includere uno dei metodi di integrazione descritti in questo articolo oltre alle altre risorse per offrire uno scenario di gestione completa.  Le risorse nella soluzione sono incluse nel pacchetto in modo che quando la soluzione viene rimossa tutte le risorse da essa create vengano rimosse dall'area di lavoro OMS e dalla sottoscrizione Azure.

La soluzione potrebbe includere ad esempio un runbook di Automazione per raccogliere ed elaborare i dati e quindi compilare il repository di Log Analytics tramite l'API di raccolta dati HTTP.  È anche possibile includere una visualizzazione personalizzata che presenta e analizza i dati raccolti.  

* Creazione di soluzioni personalizzate (presto disponibile)    

## <a name="next-steps"></a>Passaggi successivi
* Vedere [OMS SDK](operations-management-suite-sdk.md) per informazioni tecniche su come automatizzare i servizi OMS.  

<!--HONumber=Oct16_HO2-->


