---
title: "Programma FedRAMP per l'automazione dei progetti di Azure - Controllo e responsabilità"
description: "Applicazioni Web per FedRAMP - Controllo e responsabilità"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Controllo e responsabilità

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-au-1"></a>Controllo NIST 800-53 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Criteri e procedure di controllo e responsabilità

**AU-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: personale o ruoli definiti dall'organizzazione] criteri di controllo e responsabilità relativi a scopo, ambito, ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di controllo e responsabilità e dei controlli associati. Esamina e aggiorna inoltre i criteri di controllo e responsabilità correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure di controllo e responsabilità [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di controllo e responsabilità a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-2a"></a>Controllo NIST 800-53 AU-2.a

#### <a name="audit-events"></a>Eventi di controllo

**AU-2.a** L'organizzazione determina che il sistema informativo è in grado di controllare gli eventi seguenti: [Assegnazione: eventi controllabili definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le funzionalità di controllo per questo progetto di Azure sono fornite da Monitoraggio di Azure e dal servizio Log Analytics in OMS. Monitoraggio di Azure fornisce log di controllo dettagliati sull'attività associata alle risorse distribuite. Questi log e quelli a livello del sistema operativo vengono raccolti da Log Analytics e archiviati nel repository di OMS. Log Analytics mette in correlazione i dati di controllo tra le risorse distribuite da questa soluzione e può essere esteso all'applicazione Web distribuita dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-2b"></a>Controllo NIST 800-53 AU-2.b

#### <a name="audit-events"></a>Eventi di controllo

**AU-2.b** L'organizzazione coordina la funzione di controllo di sicurezza con altre entità organizzative che richiedono informazioni relative al controllo per migliorare il supporto reciproco e per facilitare la selezione di eventi controllabili.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo definito a livello aziendale che determina gli eventi controllabili. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-2c"></a>Controllo NIST 800-53 AU-2.c

#### <a name="audit-events"></a>Eventi di controllo

**AU-2.c** L'organizzazione fornisce una spiegazione logica per cui gli eventi controllabili sono considerati adeguati a supportare un'analisi degli eventi imprevisti correlati alla sicurezza dopo che si sono verificati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli eventi controllati da questo progetto di Azure includono informazioni sufficienti a determinare quando si verificano gli eventi, l'origine dell'evento, il risultato dell'evento e altre informazioni dettagliate che supportano l'analisi degli eventi imprevisti correlati alla sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-2d"></a>Controllo NIST 800-53 AU-2.d

#### <a name="audit-events"></a>Eventi di controllo

**AU-2.d** L'organizzazione determina che devono essere controllati gli eventi seguenti all'interno del sistema informativo: [Assegnazione: eventi controllati definiti dall'organizzazione (subset di eventi controllabili definiti in AU-2 a.) oltre alla frequenza dei controlli (o alla situazione che richiede tali controlli) per ogni evento identificato].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli eventi controllati da questo progetto di Azure includono quelli controllati dai log attività di Azure per le risorse distribuite, dai log a livello del sistema operativo, dai log di Active Directory e dai log di SQL Server. I clienti possono selezionare altri eventi da controllare per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-2-3"></a>Controllo NIST 800-53 AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Eventi di controllo | Verifiche e aggiornamenti

**AU-2 (3)** L'organizzazione esamina e aggiorna gli eventi controllati [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo definito a livello aziendale di verifica e aggiornamento periodici per il set di eventi controllati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-3"></a>Controllo NIST 800-53 AU-3

#### <a name="content-of-audit-records"></a>Contenuto dei record di controllo

**AU-3** Il sistema informativo genera i record di controllo contenenti informazioni che stabiliscono che tipo di evento si è verificato, quando e dove si è verificato l'evento, l'origine dell'evento, il risultato dell'evento e l'identità di individui o soggetti associati all'evento.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure si basa sulle funzionalità di controllo predefinite di Azure, Windows Server e SQL Server. Queste soluzioni di controllo acquisiscono record di controllo di acquisizione di soluzioni con dettagli sufficienti a soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-3-1"></a>Controllo NIST 800-53 AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Contenuto dei record di controllo | Informazioni di controllo aggiuntive

**AU-3 (1)** Il sistema informativo genera record di controllo contenenti le informazioni aggiuntive seguenti: [Assegnazione: informazioni aggiuntive più dettagliate definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli eventi del log attività di Azure usano uno schema dettagliato contenente campi per più di 20 tipi di informazioni di controllo. Oltre al log attività, questo progetto di Azure distribuisce la soluzione Log Analytics in OMS, che supporta un'ampia gamma di origini dati, inclusi log di Windows, log di Linux, log di Diagnostica di Azure e log del cliente.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-3-2"></a>Controllo NIST 800-53 AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Contenuto dei record di controllo | Gestione centralizzata del contenuto dei record di controllo pianificati

**AU-3 (2)** Il sistema informativo fornisce la gestione e la configurazione centralizzate del contenuto da acquisire nei record di controllo generati da [Assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Tutte le macchine virtuali distribuite da questo progetto di Azure vengono aggiunte al dominio di Active Directory distribuito. Tutte le macchine virtuali aggiunte al dominio implementano criteri di gruppo che possono essere configurati per gestire centralmente la configurazione del sistema di controllo a livello del sistema operativo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-4"></a>Controllo NIST 800-53 AU-4

#### <a name="audit-storage-capacity"></a>Capacità dell'archiviazione di controllo

**AU-4** L'organizzazione alloca la capacità di archiviazione dei record di controllo in base a [Assegnazione: requisiti di archiviazione dei record di controllo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure alloca una capacità di archiviazione sufficiente a conservare i record di controllo per un periodo di un anno. Tutti i record di controllo vengono raccolti da Log Analytics che è configurato per la conservazione di un anno. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-5a"></a>Controllo NIST 800-53 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Risposta a errori di elaborazione di controllo

**AU-5.a** Gli avvisi del sistema informativo [Assegnazione: personale o ruoli definiti dall'organizzazione] in caso di un errore di elaborazione di controllo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Lo stato del servizio per Monitoraggio di Azure e Log Analytics è disponibile nel sito Web dello stato di Azure e nel pannello dell'integrità del servizio nel portale di Azure. Gli avvisi possono essere configurati tramite Log Analytics per fornire la notifica di altri tipi di errori di elaborazione di controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-5b"></a>Controllo NIST 800-53 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Risposta a errori di elaborazione di controllo

**AU-5.b** Il sistema informativo esegue le azioni aggiuntive seguenti: [Assegnazione: azioni da eseguire definite dall'organizzazione (ad esempio, arrestare il sistema informativo, sovrascrivere i record di controllo meno recenti, interrompere la generazione di record di controllo)].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Tutti i record di controllo generati dalle risorse distribuite da questo progetto di Azure vengono raccolti da Log Analytics e conservati per un periodo di un anno. L'allocazione della memoria per questa archiviazione dei record di controllo viene allocata dinamicamente per garantire che sia disponibile una capacità sufficiente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-5-1"></a>Controllo NIST 800-53 AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Risposta a errori di elaborazione di controllo | Capacità dell'archiviazione di controllo

**AU-5 (1)** Il sistema informativo fornisce un avviso a [Assegnazione: personale, ruoli e/o località definite dall'organizzazione] entro [Assegnazione: periodo di tempo definito dall'organizzazione] quando il volume di archiviazione dei record di controllo allocato raggiunge il [Assegnazione: percentuale definita dall'organizzazione] della capacità massima di archiviazione dei record di controllo del repository.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Tutti i record di controllo generati dalle risorse distribuite da questo progetto di Azure vengono raccolti da Log Analytics e conservati per un periodo di un anno. L'allocazione della memoria per questa archiviazione dei record di controllo viene allocata dinamicamente per garantire che sia disponibile una capacità sufficiente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-5-2"></a>Controllo NIST 800-53 AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Risposta a errori di elaborazione di controllo | Avvisi in tempo reale

**AU-5 (2)** Il sistema informativo fornisce un avviso in [Assegnazione: periodo in tempo reale definito dall'organizzazione] a [Assegnazione: personale, ruoli e/o località definite dall'organizzazione] quando si verificano gli eventi di errore di controllo seguenti: [Assegnazione: eventi di errore di controllo definiti dall'organizzazione che richiedono avvisi in tempo reale].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Lo stato del servizio per Azure è disponibile nel pannello dell'integrità del servizio nel portale di Azure. Gli avvisi possono essere configurati tramite Log Analytics per fornire la notifica di altri tipi di errori di elaborazione di controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-6a"></a>Controllo NIST 800-53 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Verifica, analisi e report di controllo

**AU-6.a** L'organizzazione verifica e analizza i record di controllo del sistema informativo [Assegnazione: frequenza definita dall'organizzazione] per indicazioni di [Assegnazione: attività inappropriate o insolite definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica e dell'analisi dei record di controllo delle risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-6b"></a>Controllo NIST 800-53 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Verifica, analisi e report di controllo

**AU-6.b** L'organizzazione comunica i risultati a [Assegnazione: personale o ruoli definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della comunicazione dei risultati relativi ad attività inappropriate o insolite (definite in AU-6.a) correlate alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-1"></a>Controllo NIST 800-53 AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Verifica, analisi e report di controllo | Integrazione dei processi

**AU-6 (1)** L'organizzazione usa meccanismi automatizzati per integrare i processi di verifica, analisi e report di controllo per supportare i processi organizzativi per l'indagine e la risposta ad attività sospette.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a funzionalità di verifica, analisi e report di controllo centralizzate di livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-3"></a>Controllo NIST 800-53 AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Verifica, analisi e report di controllo | Correlazione dei repository di controllo

**AU-6 (3)** L'organizzazione analizza e mette in correlazione i record di controllo tra repository diversi per ottenere una consapevolezza della situazione a livello dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa la soluzione Log Analytics in OMS per centralizzare i dati di controllo tra le risorse distribuite, a supporto della consapevolezza della situazione a livello dell'organizzazione. I clienti possono scegliere di integrare ulteriormente Log Analytics con altri sistemi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-4"></a>Controllo NIST 800-53 AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Verifica, analisi e report di controllo | Verifica e analisi centralizzate

**AU-6 (4)** Il sistema informativo garantisce le funzionalità necessarie per verificare e analizzare in modo centralizzato i record di controllo da più componenti all'interno del sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa la soluzione Log Analytics in OMS per centralizzare i dati di controllo tra le risorse distribuite, a supporto delle funzionalità di verifica, analisi e report centralizzate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-5"></a>Controllo NIST 800-53 AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Verifica, analisi e report di controllo | Integrazione/capacità di analisi e monitoraggio

**AU-6 (5)** L'organizzazione integra l'analisi dei record di controllo con l'analisi di [Selezione (una o più opzioni): informazioni di analisi delle vulnerabilità, dati sulle prestazioni, informazioni di monitoraggio del sistema informativo, [Assegnazione: dati definiti dall'organizzazione/informazioni raccolte da altre fonti]] per migliorare ulteriormente la capacità di identificare attività inappropriate o insolite.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce la soluzione Sicurezza e controllo di OMS. Questa soluzione offre una panoramica completa delle condizioni di sicurezza. Il dashboard Sicurezza e controllo fornisce una visione generale dello stato di sicurezza delle risorse distribuite usando i dati disponibili nelle soluzioni OMS distribuite, integrando dati di log e dati di vulnerabilità dalla valutazione della baseline e delle patch. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-6"></a>Controllo NIST 800-53 AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Verifica, analisi e report di controllo | Correlazione con il monitoraggio fisico

**AU-6 (6)** L'organizzazione mette in correlazione le informazioni provenienti dai record di controllo con le informazioni ottenute dal monitoraggio dell'accesso fisico per migliorare ulteriormente la capacità di identificare attività sospette, inappropriate, insolite o pericolose.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Il team SIM di Microsoft Azure usa i dati del monitoraggio fisico correlati e li mette in correlazione con i record di controllo per determinare se si è verificata una violazione logica associata o un comportamento sospetto al momento del rilevamento degli eventi fisici imprevisti. |


 ### <a name="nist-800-53-control-au-6-7"></a>Controllo NIST 800-53 AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Verifica, analisi e report di controllo | Azioni consentite

**AU-6 (7)** L'organizzazione specifica le azioni consentite per ogni [Selezione (una o più opzioni): processo del sistema informativo, ruolo, utente] associato alla verifica, all'analisi e al report delle informazioni di controllo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali Windows distribuite da questo progetto di Azure implementano autorizzazioni a livello del sistema operativo che limitano le azioni che un utente può eseguire rispetto alle informazioni di controllo. In Azure gli utenti o i gruppi di utenti possono essere assegnati ai ruoli (ad esempio, proprietario, collaboratore, lettore o un ruolo personalizzato) per limitare le azioni disponibili rispetto alle risorse o alle soluzioni distribuite, incluso Log Analytics.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-6-10"></a>Controllo NIST 800-53 AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Verifica, analisi e report di controllo | Adeguamento del livello di controllo

**AU-6 (10)** L'organizzazione adegua il livello di verifica, analisi e report di controllo nell'ambito del sistema informativo quando il livello di rischio cambia sulla base di informazioni provenienti dalle autorità giudiziarie, informazioni di intelligence o altre fonti di informazioni attendibili.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'adeguamento del livello di verifica analisi e report di controllo per le risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software) quando il livello di rischio cambia sulla base di informazioni provenienti dalle autorità giudiziarie, informazioni di intelligence o altre fonti attendibili. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-7a"></a>Controllo NIST 800-53 AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Riduzione del controllo e generazione di report

**AU-7.a** Il sistema informativo fornisce una funzionalità di riduzione del controllo e generazione di report in grado di supportare i requisiti di verifica, analisi e report di controllo su richiesta e un'analisi degli eventi imprevisti correlati alla sicurezza dopo che si sono verificati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa la soluzione Log Analytics in OMS. Log Analytics fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-7b"></a>Controllo NIST 800-53 AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Riduzione del controllo e generazione di report

**AU-7.b** Il sistema informativo fornisce una funzionalità di riduzione del controllo e generazione di report che non altera il contenuto originale o l'ordine cronologico dei record di controllo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa la soluzione Log Analytics in OMS. Log Analytics fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Il contenuto e l'ordine cronologico dei record di controllo non sono alterati quando vengono raccolti da Log Analytics. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-7-1"></a>Controllo NIST 800-53 AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Riduzione del controllo e generazione di report | Elaborazione automatica

**AU-7 (1)** Il sistema informativo fornisce una funzionalità di elaborazione dei record di controllo per gli eventi di interesse sulla base di [Assegnazione: campi di controllo definiti dall'organizzazione all'interno dei record di controllo].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa la soluzione Log Analytics in OMS. Log Analytics fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione. Log Analytics include un linguaggio di query avanzato per estrarre i dati archiviati nel repository. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-8a"></a>Controllo NIST 800-53 AU-8.a

#### <a name="time-stamps"></a>Timestamp

**AU-8.a** Il sistema informativo usa orologi interni del sistema per generare timestamp per i record di controllo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano orologi interni del sistema per generare timestamp per i record di controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-8b"></a>Controllo NIST 800-53 AU-8.b

#### <a name="time-stamps"></a>Timestamp

**AU-8.b** Il sistema informativo registra i timestamp per i record di controllo che possono essere associati a orari in formato UTC (Coordinated Universal Time) o GMT (Greenwich Mean Time) e soddisfano [Assegnazione: granularità della misurazione del tempo definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano orologi interni del sistema per generare timestamp per i record di controllo. I timestamp vengono registrati in formato UTC. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-8-1a"></a>Controllo NIST 800-53 AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Timestamp | Sincronizzazione con un'origine di ora autorevole

**AU-8 (1).a** Il sistema informativo confronta gli orologi interni del sistema informativo [Assegnazione: frequenza definita dall'organizzazione] con [Assegnazione: origine di ora autorevole definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano orologi interni del sistema per generare timestamp per i record di controllo. Gli orologi interni del sistema sono configurati per la sincronizzazione con un'origine di ora autorevole. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-8-1b"></a>Controllo NIST 800-53 AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Timestamp | Sincronizzazione con un'origine di ora autorevole

**AU-8 (1).b** Il sistema informativo sincronizza gli orologi interni del sistema con l'origine di ora autorevole quando la differenza di tempo è maggiore di [Assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano orologi interni del sistema per generare timestamp per i record di controllo. Gli orologi interni del sistema sono configurati per la sincronizzazione con un'origine di ora autorevole. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-9"></a>Controllo NIST 800-53 AU-9

#### <a name="protection-of-audit-information"></a>Protezione delle informazioni di controllo

**AU-9** Il sistema informativo protegge le informazioni di controllo e gli strumenti di controllo da accesso, modifica ed eliminazione non autorizzati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I controlli dell'accesso logico vengono usati per proteggere le informazioni e gli strumenti di controllo in questo progetto di Azure da accesso, modifica ed eliminazione non autorizzati. Azure Active Directory applica l'accesso logico approvato tramite le appartenenze ai gruppi basati sui ruoli. La possibilità di visualizzare le informazioni di controllo e usare gli strumenti di controllo può essere limitata agli utenti che necessitano di tali autorizzazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-9-2"></a>Controllo NIST 800-53 AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Protezione delle informazioni di controllo | Backup di controllo in sistemi/componenti fisici distinti

**AU-9 (2)** Il sistema informativo esegue un backup dei record di controllo [Assegnazione: frequenza definita dall'organizzazione] in un sistema o componente di sistema fisicamente distinto dal sistema o dal componente controllato.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il servizio Log Analytics in OMS. Le macchine virtuali distribuite e gli account di archiviazione diagnostica di Azure sono origini connesse a Log Analytics e vengono conservati separatamente dall'origine. I dati vengono raccolti da OMS quasi in tempo reale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-9-3"></a>Controllo NIST 800-53 AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Protezione delle informazioni di controllo | Protezione crittografica

**AU-9 (3)** Il sistema informativo implementa meccanismi di crittografia per proteggere l'integrità delle informazioni di controllo e degli strumenti di controllo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il servizio Log Analytics in OMS. Per garantire che i dati in arrivo provengano da un'origine attendibile, Log Analytics convalida i certificati e l'integrità dei dati con l'autenticazione di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-9-4"></a>Controllo NIST 800-53 AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Protezione delle informazioni di controllo | Accesso in base a un subset di utenti con privilegi

**AU-9 (4)** L'organizzazione autorizza l'accesso alla gestione delle funzionalità di controllo solo per [Assegnazione: subset di utenti con privilegi definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I controlli dell'accesso logico vengono usati per proteggere le informazioni e gli strumenti di controllo in questo progetto di Azure da accesso, modifica ed eliminazione non autorizzati. Azure Active Directory applica l'accesso logico approvato tramite le appartenenze ai gruppi basati sui ruoli. La possibilità di visualizzare le informazioni di controllo e usare gli strumenti di controllo può essere limitata agli utenti che necessitano di tali autorizzazioni.
 |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-10"></a>Controllo NIST 800-53 AU-10

#### <a name="non-repudiation"></a>Non ripudio

**AU-10** Il sistema informativo fornisce una protezione nel caso in cui un singolo utente (o un processo che agisce per conto di un singolo utente) neghi in maniera falsa di avere eseguito [Assegnazione: azioni definite dall'organizzazione e coperte dal non ripudio].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le funzionalità di controllo per questo progetto di Azure sono fornite da Monitoraggio di Azure e dal servizio Log Analytics in OMS. Monitoraggio di Azure fornisce log di controllo dettagliati sull'attività associata alle risorse distribuite. Questi log e quelli a livello del sistema operativo vengono raccolti da Log Analytics e archiviati nel repository di OMS. Questi log contengono record dettagliati di eventi del sistema informativo e assicurano protezione dal non ripudio. Inoltre, l'accesso ai dati di log è limitato tramite il controllo degli accessi in base al ruolo per impedire la modifica o l'eliminazione non autorizzate dei dati di log. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-11"></a>Controllo NIST 800-53 AU-11

#### <a name="audit-record-retention"></a>Conservazione dei record di controllo

**AU-11** L'organizzazione conserva i record di controllo per [Assegnazione: periodo di tempo definito dall'organizzazione conforme ai criteri di conservazione dei record] per supportare un'analisi degli eventi imprevisti correlati alla sicurezza dopo che si sono verificati e per soddisfare i requisiti di conservazione delle informazioni normativi e specifici dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il servizio Log Analytics in OMS. Log Analytics fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Dopo essere stati raccolti, i dati vengono conservati per un anno in base alla configurazione di Log Analytics. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-12a"></a>Controllo NIST 800-53 AU-12.a

#### <a name="audit-generation"></a>Generazione di controllo

**AU-12.a** Il sistema informativo fornisce funzionalità di generazione di record di controllo per gli eventi controllabili definiti in AU-2 a. in [Assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli eventi controllati da questo progetto di Azure includono quelli controllati dai log attività di Azure per le risorse distribuite, dai log a livello del sistema operativo, dai log di Active Directory e dai log di SQL Server. I clienti possono selezionare altri eventi da controllare per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-12b"></a>Controllo NIST 800-53 AU-12.b

#### <a name="audit-generation"></a>Generazione di controllo

**AU-12.b** Il sistema informativo consente a [Assegnazione: personale o ruoli definiti dall'organizzazione] di selezionare quali eventi controllabili devono essere controllati da componenti specifici del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso alle funzioni di controllo è limitato tramite il controllo degli accessi in base al ruolo in Azure e a livello del sistema operativo della macchina virtuale. La configurazione degli eventi selezionati per essere controllati dalle risorse distribuite da questo progetto di Azure può essere impostata dagli utenti che dispongono di autorizzazioni appropriate basate sui ruoli. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-au-12c"></a>Controllo NIST 800-53 AU-12.c

#### <a name="audit-generation"></a>Generazione di controllo

**AU-12.c** Il sistema informativo genera record di controllo per gli eventi definiti in AU-2.d. con il contenuto definito in AU-3.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli eventi controllati da questo progetto di Azure includono quelli controllati dai log attività di Azure per le risorse distribuite, dai log a livello del sistema operativo, dai log di Active Directory e dai log di SQL Server. I clienti possono selezionare altri eventi da controllare per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-12-1"></a>Controllo NIST 800-53 AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Generazione di controllo | Audit trail a livello di sistema/correlato al tempo

**AU-12 (1)** Il sistema informativo consente di compilare record di controllo da [Assegnazione: componenti del sistema informativo definiti dall'organizzazione] in un audit trail (logico o fisico) a livello di sistema che è correlato al tempo entro [Assegnazione: livello di tolleranza definito dall'organizzazione per la relazione tra i timestamp dei singoli record nell'audit trail].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il servizio Log Analytics in OMS. Log Analytics fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. I timestamp dei record di controllo non vengono modificati, pertanto l'audit trail è correlato al tempo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-au-12-3"></a>Controllo NIST 800-53 AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Generazione di controllo | Modifiche di utenti autorizzati

**AU-12 (3)** Il sistema informativo offre la possibilità a [Assegnazione: singoli utenti o ruoli definiti dall'organizzazione] di modificare il controllo da eseguire su [Assegnazione: componenti del sistema informativo definiti dall'organizzazione] in base a [Assegnazione: criteri di eventi selezionabili definiti dall'organizzazione] entro [Assegnazione: soglie temporali definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso alle funzioni di controllo è limitato tramite il controllo degli accessi in base al ruolo in Azure e a livello del sistema operativo della macchina virtuale. La configurazione degli eventi selezionati per essere controllati dalle risorse distribuite da questo progetto di Azure può essere impostata dagli utenti che dispongono di autorizzazioni appropriate basate sui ruoli. |
| **Provider (Microsoft Azure)** | Non applicabile |
