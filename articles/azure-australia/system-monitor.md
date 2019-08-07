---
title: Monitoraggio del sistema per la sicurezza in Azure Australia
description: Linee guida per la configurazione del monitoraggio del sistema nelle aree australiane per soddisfare i requisiti specifici dei criteri, delle normative e delle normative del governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779316"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Monitoraggio del sistema per la sicurezza in Azure Australia

Avere strategie di sicurezza affidabili che includono il monitoraggio in tempo reale e le valutazioni della sicurezza di routine sono fondamentali per migliorare la sicurezza operativa giornaliera degli ambienti IT, incluso il cloud.

La sicurezza del cloud è uno sforzo comune tra il cliente e il provider di servizi cloud. Sono disponibili quattro servizi che Microsoft Azure fornisce per facilitare questi requisiti, considerando i consigli contenuti in ACSC ( [Information Security Manual Controls) del Centro sicurezza di Internet](https://acsc.gov.au/infosec/ism/index.htm) (ISM), in particolare, l'implementazione della registrazione centralizzata degli eventi, il controllo del registro eventi e la valutazione e la gestione delle vulnerabilità di sicurezza. I servizi Microsoft Azure sono:

* Centro sicurezza di Azure
* Monitoraggio di Azure
* Azure Advisor
* Criteri di Azure

Il ACSC consiglia di usare questi servizi per i dati **protetti** . Usando questi servizi, è possibile monitorare e analizzare in modo proattivo gli ambienti IT e prendere decisioni informate su dove allocare meglio le risorse per migliorare la sicurezza. Ognuno di questi servizi fa parte di una soluzione combinata che fornisce le informazioni più dettagliate, le raccomandazioni e la protezione possibili.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) offre una console di gestione della sicurezza unificata usata per monitorare e migliorare la sicurezza delle risorse di Azure e dei dati ospitati. Il Centro sicurezza di Azure include il Punteggio sicuro, un punteggio basato su un'analisi dello stato della configurazione delle procedure consigliate da Azure Advisor e la conformità complessiva dei criteri di Azure.

Il Centro sicurezza di Azure offre ai clienti di Azure le funzionalità seguenti:

* Criteri, valutazione e consigli per la sicurezza
* Raccolta di eventi di sicurezza e ricerca
* Accesso e controlli dell'applicazione
* Rilevamento avanzato delle minacce
* Controllo di accesso delle macchine virtuali JIT
* Sicurezza ibrida

L'ambito delle risorse monitorate dal centro sicurezza di Azure può essere espanso per includere le risorse locali supportate in un ambiente cloud ibrido. Sono incluse le risorse locali attualmente monitorate da una versione supportata di System Center Operations Manager.

Il livello "standard" del Centro sicurezza fornisce anche i controlli di sicurezza basati sul cloud richiesti da [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Questi includono l'inserimento nell'elenco elementi consentiti e la restrizione dei privilegi amministrativi tramite l'accesso just-in-time.

### <a name="azure-monitor"></a>Monitoraggio di Azure

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) è la soluzione di registrazione centralizzata per tutte le risorse di Azure e include Log Analytics e Application Insights. Vengono raccolti due tipi di dati chiave dalle risorse di Azure: log e metriche. Una volta raccolti in monitoraggio di Azure, le informazioni di registrazione possono essere utilizzate da un'ampia gamma di strumenti e per diversi scopi.

![Panoramica di Monitoraggio di Azure](media/overview.png)

Monitoraggio di Azure include anche il "log attività di Azure". Il registro SActivity archivia tutti gli eventi a livello di sottoscrizione che si sono verificati in Azure. Consente ai clienti di Azure di vedere il "chi, cosa e quando" dietro le operazioni effettuate sulle risorse di Azure. La registrazione basata sulle risorse inviata a monitoraggio di Azure e gli eventi del log attività di Azure può essere analizzata usando il linguaggio di query kusto incorporato. Questi log possono quindi essere esportati, usati per creare dashboard e visualizzazioni personalizzati e configurati per attivare avvisi e notifiche.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) analizza le risorse di Azure supportate, i file di log generati dal sistema e le configurazioni delle risorse correnti nella sottoscrizione di Azure. L'analisi fornita in Azure Advisor viene generata in tempo reale e in base alle procedure consigliate di Microsoft. Tutte le risorse di Azure supportate aggiunte all'ambiente verranno analizzate e verranno fornite indicazioni appropriate. Azure Advisor consigli sono suddivisi in quattro categorie di procedure consigliate:

* Security
* Disponibilità elevata
* Prestazioni
* Costi

Le raccomandazioni sulla sicurezza generate da Azure Advisor formano parte dell'analisi di sicurezza complessiva fornita dal centro sicurezza di Azure.

Le informazioni raccolte da Azure Advisor forniscono agli amministratori:

* Informazioni sulla configurazione delle risorse che non soddisfa la procedura consigliata
* Indicazioni su azioni correttive specifiche da intraprendere
* Classificazioni che indicano le azioni correttive da sottoporre a priorità alta

### <a name="azure-policy"></a>Criteri di Azure

[Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview) offre la possibilità di applicare regole che controllano i tipi di risorse di Azure e la loro configurazione consentita. I criteri possono essere usati per controllare la creazione e la configurazione delle risorse oppure possono essere usati per controllare le impostazioni di configurazione in un ambiente. Questi risultati di controllo possono essere utilizzati per creare la base delle attività di correzione. Criteri di Azure differisce dal controllo degli accessi in base al ruolo (RBAC); Criteri di Azure viene usato per limitare le risorse e la relativa configurazione, RBAC viene usato per limitare l'accesso con privilegi agli utenti di Azure.

Se vengono applicati i criteri specifici o se l'effetto del criterio è in corso di controllo, la conformità dei criteri viene continuamente monitorata e le informazioni di conformità complessive e specifiche delle risorse vengono fornite agli amministratori. I dati di conformità dei criteri di Azure vengono forniti al centro sicurezza di Azure e formano parte del Punteggio sicuro.

## <a name="key-design-considerations"></a>Principali considerazioni sulla progettazione

Quando si implementa una strategia di registro eventi, ACSC ISM evidenzia le considerazioni seguenti:

* Funzionalità di registrazione centralizzata
* Eventi specifici da registrare
* Protezione del registro eventi
* Conservazione registro eventi
* Controllo del registro eventi

In aggiunta alla raccolta e alla gestione dei log, ISM consiglia anche la valutazione della vulnerabilità di routine dell'ambiente IT di un'organizzazione.

### <a name="centralised-logging"></a>Registrazione centralizzata

Qualsiasi soluzione di registrazione dovrebbe, laddove possibile, consolidare i log acquisiti in un unico repository di dati. Questa operazione non solo riduce la complessità operativa e impedisce la creazione di più silo di dati, consente di analizzare i dati raccolti da più origini per consentire l'identificazione di tutti gli eventi correlati. Questo è fondamentale per il rilevamento e la gestione dell'ambito di eventuali eventi imprevisti di sicurezza informatici.

Questo requisito viene soddisfatto per tutti i clienti di Azure con monitoraggio di Azure. Questa offerta non solo fornisce un repository di registrazione centralizzato in Azure per tutte le risorse di Azure, ma consente anche di trasmettere i dati a un hub eventi di Azure. Hub eventi di Azure fornisce un servizio di inserimento dati in tempo reale completamente gestito. Una volta trasmessi i dati di monitoraggio di Azure a un hub eventi di Azure, i dati possono anche essere facilmente connessi ai repository di gestione di informazioni ed eventi di sicurezza supportati esistenti e ad altri strumenti di monitoraggio di terze parti.

Microsoft offre anche la propria soluzione SIEM nativa di Azure, Sentinel di Azure. Azure Sentinel supporta un'ampia gamma di connettori di dati e può essere usato per monitorare gli eventi di sicurezza in un'intera azienda. Combinando i dati dei [connettori di dati](https://docs.microsoft.com/azure/sentinel/connect-data-sources)supportati, il machine learning integrato di Azure Sentinel e il linguaggio di query kusto, gli amministratori della sicurezza sono forniti con una singola soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e risposta alle minacce. Sentinel fornisce anche una funzionalità di caccia e notebook che consente agli amministratori della sicurezza di registrare tutti i passaggi eseguiti nell'ambito di un'indagine di sicurezza in un PlayBook riutilizzabile che può essere condiviso all'interno di un'organizzazione. Gli amministratori della sicurezza possono anche usare l' [analisi utente](https://docs.microsoft.com/azure/sentinel/user-analytics) predefinita per esaminare le azioni di un singolo utente designato.

### <a name="logged-events-and-log-detail"></a>Dettagli log e eventi registrati

ISM fornisce un elenco dettagliato dei tipi di log eventi che devono essere inclusi in qualsiasi strategia di registrazione. Tutti i log acquisiti devono contenere informazioni sufficienti per essere usati in modo pratico per eseguire analisi e analisi.

I log raccolti in Azure rientrano in una delle tre categorie seguenti:

* **Log di controllo e gestione**: Questi log forniscono informazioni sulle operazioni di creazione, aggiornamento ed eliminazione di Azure Resource Manager.

* **Log del piano dati**: Questi contengono eventi generati come parte dell'utilizzo delle risorse di Azure. Include origini quali i registri eventi di Windows, inclusi i log di sistema, della sicurezza e dell'applicazione.

* **Eventi elaborati**: Questi eventi contengono informazioni sugli eventi e gli avvisi che sono stati elaborati automaticamente per conto del cliente da Azure. Un esempio di evento elaborato è un avviso del Centro sicurezza di Azure.

Il monitoraggio delle macchine virtuali di Azure viene migliorato mediante la distribuzione dell'agente di macchine virtuali sia per Windows che per Linux. Questo aumenta notevolmente le informazioni di registrazione raccolte. La distribuzione di questo agente può essere configurata in modo che venga eseguita automaticamente tramite il Centro sicurezza di Azure.

Microsoft fornisce informazioni dettagliate sui log specifici delle risorse di Azure e sui relativi [schemi](https://docs.microsoft.com/azure/security/fundamentals/log-audit).

### <a name="log-retention-and-protection"></a>Conservazione e protezione dei log

 I registri eventi devono essere archiviati in modo sicuro per il periodo di conservazione necessario. ISM informa che i log vengono conservati per almeno sette anni. Azure offre un numero di mezzi per garantire la durata procurata dei log raccolti. Per impostazione predefinita, gli eventi di log di Azure vengono archiviati per 90 giorni. I dati di log acquisiti da monitoraggio di Azure possono essere spostati e archiviati in un account di archiviazione di Azure come richiesto per la conservazione a lungo termine. I log attività archiviati in un account di archiviazione di Azure possono essere conservati per un determinato numero di giorni o, se necessario, a tempo indefinito.

Gli account di archiviazione di Azure usati per archiviare gli eventi di log di Azure possono essere resi con ridondanza geografica e possono essere sottoposti a backup con backup di Azure. Una volta acquisita da backup di Azure, eventuali eliminazioni di backup contenenti log richiedono l'approvazione amministrativa e i backup contrassegnati per l'eliminazione sono conservati per 14 giorni per il ripristino. Backup di Azure consente copie 9999 di un'istanza protetta, fornendo oltre 27 anni di backup giornalieri.

Il controllo degli accessi in base al ruolo deve essere usato per controllare l'accesso alle risorse usate per la registrazione di Azure. Il monitoraggio di Azure, gli account di archiviazione di Azure e i backup di Azure devono essere configurati con controlli degli accessi in base al ruolo per garantire la sicurezza dei dati contenuti nei log.

### <a name="log-auditing"></a>Controllo log

Il valore effettivo dei log è stato creato dopo l'analisi. Con l'analisi automatizzata e manuale e con la possibilità di acquisire familiarità con gli strumenti disponibili, è possibile rilevare e gestire le violazioni dei criteri di sicurezza dell'organizzazione e degli eventi imprevisti relativi alla sicurezza informatica. Monitoraggio di Azure offre un set completo di strumenti per analizzare i log raccolti. Il risultato di questa analisi può quindi essere condiviso tra sistemi, visualizzati o distribuiti in più formati.

I dati di log archiviati in monitoraggio di Azure vengono conservati in un'area di lavoro Log Analytics. Tutte le analisi iniziano con una query. Le query di monitoraggio di Azure sono scritte nel linguaggio di query kusto. Le query costituiscono la base di tutti gli output di monitoraggio di Azure, dai dashboard di Azure alle regole di avviso.

![Panoramica delle query di log di Azure](media/queries-overview.png)

Il controllo dei log può essere migliorato tramite l'uso di soluzioni di monitoraggio. Si tratta di soluzioni preconfezionate che contengono la logica di raccolta, le query e le visualizzazioni di visualizzazione dei dati. Microsoft [offre](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) una serie di soluzioni di monitoraggio e altre soluzioni dei fornitori di prodotti disponibili in Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Valutazione della vulnerabilità e gestione

ISM rileva che la gestione e la valutazione delle vulnerabilità di routine sono essenziali. L'ambiente IT è in continua evoluzione e la minaccia per la sicurezza esterna sta cambiando a infinito. Con il Centro sicurezza di Azure è possibile eseguire valutazioni automatizzate delle vulnerabilità e ottenere indicazioni su come pianificare ed eseguire attività di correzione.

Il Punteggio sicuro nel centro sicurezza di Azure offre un elenco di raccomandazioni che, quando applicate, miglioreranno la sicurezza dell'ambiente. L'elenco è ordinato in base all'effetto sul punteggio sicuro complessivo dal più alto al più basso. L'ordinamento dell'elenco in base all'effetto consente di concentrarsi sulle raccomandazioni con priorità più elevata che presentano il maggior valore per migliorare la sicurezza.

I criteri di Azure svolgono anche una parte essenziale della valutazione della vulnerabilità in corso. I tipi di criteri disponibili nell'intervallo di criteri di Azure dall'applicazione dei tag e dei valori delle risorse, per limitare le aree di Azure in cui è possibile creare le risorse, bloccano completamente la creazione di determinati tipi di risorse. Un set di criteri di Azure può essere raggruppato in iniziative. Le iniziative vengono usate per applicare i criteri di Azure correlati che, quando vengono applicati insieme come gruppo, costituiscono la base di un obiettivo di sicurezza o conformità specifico.

Criteri di Azure include una raccolta di definizioni di criteri predefinite in continua crescita. Portale di Azure offre inoltre la possibilità di creare definizioni personalizzate per i criteri di Azure. Dopo aver individuato un criterio nella libreria esistente o averne creato uno nuovo, è possibile assegnare i criteri alle risorse di Azure. Queste assegnazioni possono essere [limitate](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) a diversi livelli nella gerarchia di gestione delle risorse. L'assegnazione dei criteri viene ereditata, ovvero tutte le risorse figlio all'interno di un ambito ricevono la stessa assegnazione di criteri. Le risorse possono anche essere escluse dall'assegnazione dei criteri con ambito, se necessario.

Tutti i criteri di Azure distribuiti contribuiscono al Punteggio sicuro di un'organizzazione. In un ambiente a elevata scalabilità, è possibile creare e distribuire definizioni di criteri personalizzati di Azure per fornire informazioni di controllo personalizzate a carichi di lavoro specifici.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare il Centro sicurezza di Azure e sfruttare al meglio il monitoraggio di Azure, gli Advisor e i criteri, Microsoft consiglia i passaggi iniziali seguenti:

* Abilitare il Centro sicurezza di Azure
* Eseguire l'aggiornamento al livello standard
* Abilitare il provisioning automatico del Microsoft Monitoring Agent alle macchine virtuali di Azure supportate
* Esaminare, assegnare priorità e attenuare le raccomandazioni e gli avvisi di sicurezza nel dashboard del Centro sicurezza

## <a name="next-steps"></a>Passaggi successivi

Leggi i [criteri di Azure e i progetti di Azure](azure-policy.md) per informazioni dettagliate sull'implementazione della governance e del controllo delle risorse di Azure in Australia per garantire la conformità alle normative
