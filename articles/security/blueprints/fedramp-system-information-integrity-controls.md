---
title: "Programma FedRAMP per l'automazione dei progetti di Azure - Integrità del sistema e delle informazioni"
description: "Applicazioni Web per FedRAMP - Integrità del sistema e delle informazioni"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>Integrità del sistema e delle informazioni

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-si-1"></a>Controllo SI-1 NIST 800-53

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Procedure e criteri di integrità del sistema e delle informazioni

**SI-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: ruoli o personale definiti dall'organizzazione] criteri di integrità del sistema e delle informazioni relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di integrità del sistema e delle informazioni e dei relativi controlli. Esamina e aggiorna inoltre i criteri di integrità del sistema e delle informazioni correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure di integrità del sistema e delle informazioni [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di integrità del sistema e delle informazioni a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-2a"></a>Controllo SI-2.a NIST 800-53

#### <a name="flaw-remediation"></a>Correzione degli errori

**SI-2.a** L'organizzazione individua, segnala e corregge gli errori a livello di sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce la soluzione OMS Automation & Control per tenere traccia dello stato degli aggiornamenti per le macchine virtuali di Windows distribuite in questa architettura. Nel riquadro Gestione aggiornamenti del dashboard OMS viene visualizzato lo stato della correzione degli errori per tutti i server Windows distribuiti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-2b"></a>Controllo SI-2.b NIST 800-53

#### <a name="flaw-remediation"></a>Correzione degli errori

**SI 2.b** L'organizzazione esegue il test degli aggiornamenti software e firmware associati alla correzione degli errori per verificarne l'efficacia e i potenziali effetti collaterali prima dell'installazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'esecuzione dei test degli aggiornamenti correlati alla correzione degli errori per verificarne l'efficacia e i potenziali effetti collaterali prima dell'installazione nelle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-2c"></a>Controllo SI-2.c NIST 800-53

#### <a name="flaw-remediation"></a>Correzione degli errori

**SI-2.c** L'organizzazione installa gli aggiornamenti software e hardware rilevanti per la sicurezza entro [Assegnazione: periodo di tempo definito dall'organizzazione] dal rilascio degli aggiornamenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali Windows distribuite tramite questo progetto di Azure vengono configurate per impostazione predefinita per ricevere aggiornamenti automatici dal servizio Windows Update. Questa soluzione distribuisce anche la soluzione OMS Automation & Control tramite la quale è possibile creare distribuzioni di aggiornamento per distribuire patch nei server Windows in base alle esigenze. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-2d"></a>Controllo SI-2.d NIST 800-53

#### <a name="flaw-remediation"></a>Correzione degli errori

**SI-2.d** L'organizzazione include la correzione degli errori nel processo di gestione della configurazione dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'inclusione della correzione degli errori nella gestione della configurazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-2-1"></a>Controllo SI-2 (1) NIST 800-53

#### <a name="flaw-remediation--central-management"></a>Correzione degli errori | Gestione centrale

**SI-2 (1)** L'organizzazione gestisce a livello centrale il processo di correzione degli errori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce la soluzione OMS Automation & Control per tenere traccia dello stato degli aggiornamenti per le macchine virtuali di Windows distribuite in questa architettura. Nel riquadro Gestione aggiornamenti del dashboard OMS viene visualizzato lo stato della correzione degli errori per tutti i server Windows distribuiti. È possibile creare distribuzioni di aggiornamento per distribuire le patch ai server Windows quando necessario. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-2-2"></a>Controllo SI-2 (2) NIST 800-53

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Correzione degli errori | Stato della correzione automatizzata degli errori

**SI-2 (2)** L'organizzazione adotta meccanismi automatizzati [Assegnazione: frequenza definita dall'organizzazione] per determinare lo stato dei componenti del sistema informativo relativamente alla correzione degli errori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce la soluzione OMS Automation & Control per tenere traccia dello stato degli aggiornamenti per le macchine virtuali di Windows distribuite in questa architettura. Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti, l'API Windows viene chiamata per eseguire una query per la data/ora dell'ultimo aggiornamento e determinare se lo stato è stato modificato. In caso affermativo viene avviata un'analisi di conformità. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-2-3a"></a>Controllo SI-2 (3).a NIST 800-53

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correzione degli errori | Intervallo di correzione degli errori/Benchmark per le azioni correttive

**SI-2 (3).a** L'organizzazione misura il tempo trascorso tra l'individuazione degli errori e la correzione degli errori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della correzione degli errori nelle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-2-3b"></a>Controllo SI-2 (3).b NIST 800-53

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correzione degli errori | Intervallo di correzione degli errori/Benchmark per le azioni correttive

**SI-2 (3).b** L'organizzazione definisce [Assegnazione: benchmark definiti dall'organizzazione] per l'esecuzione delle azioni correttive.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può affidarsi a benchmark a livello aziendale per i processi di correzione degli errori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-3a"></a>Controllo SI-3.a NIST 800-53

#### <a name="malicious-code-protection"></a>Protezione dal malware

**SI-3.a** L'organizzazione implementa meccanismi di protezione dal malware a livello di punti di ingresso e uscita per rilevare e rimuovere il malware.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-3b"></a>Controllo SI-3.b NIST 800-53

#### <a name="malicious-code-protection"></a>Protezione dal malware

**SI-3.b** L'organizzazione aggiorna i meccanismi di protezione dal malware nel momento in cui vengono rilasciate nuove versioni in modo conforme alle procedure e ai criteri di gestione della configurazione dell'organizzazione stessa.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. Questa estensione è configurata in modo da aggiornare automaticamente sia il motore antimalware sia le firme di protezione nel momento in cui vengono rilasciate nuove versioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-3c"></a>Controllo SI-3.c NIST 800-53

#### <a name="malicious-code-protection"></a>Protezione dal malware

**SI-3.c** L'organizzazione configura meccanismi di protezione dal malware in modo che vengano eseguite analisi periodiche del sistema informativo [Assegnazione: frequenza definita dall'organizzazione] ed analisi in tempo reale dei file da origini esterne a livello di [Selezione (una o più opzioni); endpoint; punti di ingresso/uscita della rete] quando i file vengono scaricati, aperti o eseguiti in modo conforme ai criteri di sicurezza dell'organizzazione; ed eseguite azioni specifiche [Selezione (una o più opzioni): blocco del malware; quarantena del malware; invio di avvisi all'amministratore; [Assegnazione: azione definita dall'organizzazione]] in risposta al rilevamento del malware.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. Questa estensione viene configurata per eseguire analisi in tempo reale e periodiche (settimanali), aggiornare automaticamente il motore antimalware e le firme di protezione ed eseguire azioni automatiche di correzione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-3d"></a>Controllo SI-3.d NIST 800-53

#### <a name="malicious-code-protection"></a>Protezione dal malware

**SI-3.d** L'organizzazione gestisce la ricezione di risposte di tipo 'falso positivo' durante il rilevamento e la rimozione del malware e il potenziale impatto corrispondente sulla disponibilità del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della protezione dal malware. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-3-1"></a>Controllo SI-3 (1) NIST 800-53

#### <a name="malicious-code-protection--central-management"></a>Protezione dal malware | Gestione centrale

**SI-3 (1)** L'organizzazione gestisce a livello centralizzato i meccanismi di protezione dal malware.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. OMS Azure fornisce una funzionalità centralizzata per esaminare lo stato corrente della soluzione antimalware. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-3-2"></a>Controllo SI-3 (2) NIST 800-53

#### <a name="malicious-code-protection--automatic-updates"></a>Protezione dal malware | Aggiornamenti automatici

**SI-3 (2)** Il sistema informativo aggiorna automaticamente i meccanismi di protezione dal malware.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. Questa estensione è configurata in modo da aggiornare automaticamente sia il motore antimalware sia le firme di protezione nel momento in cui vengono rilasciate nuove versioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-3-7"></a>Controllo SI-3 (7) NIST 800-53

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Protezione dal malware | Rilevamento basato sull'assenza della firma

**SI-3 (7)** Nel sistema informativo vengono implementati meccanismi di rilevamento di malware basati sull'assenza della firma.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce funzionalità di protezione antimalware basate su host per tutte le macchine virtuali Windows distribuite, implementate tramite l'estensione per macchine virtuali Microsoft Antimalware. Questa estensione è configurata per eseguire il rilevamento euristico. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4a"></a>Controllo SI-4.a NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.a** L'organizzazione esegue il monitoraggio del sistema informativo per rilevare gli attacchi e gli indicatori di potenziali attacchi in modo conforme a [Assegnazione: obiettivi di monitoraggio definiti dall'organizzazione], nonché delle connessioni remote, di rete e locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce il servizio Log Analytics e la soluzione OMS Sicurezza e controllo. Questa soluzione fornisce una panoramica completa a livello di condizioni di sicurezza, attacchi e indicatori dei potenziali attacchi. Il dashboard Sicurezza e controllo offre informazioni di alto livello sullo stato di sicurezza delle risorse distribuite usando i dati disponibili nelle soluzioni OMS distribuite. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4b"></a>Controllo SI-4.b NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.b** L'organizzazione rileva l'uso non autorizzato del sistema informativo tramite [Assegnazione: metodi e tecniche definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce la soluzione OMS Sicurezza e controllo. Il dominio Identify and Access (Identificazione e accesso) fornisce un dashboard con una panoramica sullo stato delle identità nel sistema informativo, ad esempio il numero di tentativi di accesso non riusciti e il numero corrente di account che hanno eseguito l'accesso. Le informazioni disponibili in questo dashboard consentono di identificare una potenziale attività sospetta. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4c"></a>Controllo SI-4.c NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.c** L'organizzazione distribuisce dispositivi di monitoraggio all'interno del sistema informativo per raccogliere le informazioni ritenute strategiche dall'organizzazione e in posizioni ad hoc nel sistema per tenere traccia di tipi specifici di transazioni ritenuti di particolare interesse per l'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce il servizio Log Analytics e la soluzione OMS Sicurezza e controllo. Il dashboard Sicurezza e controllo offre informazioni di alto livello sullo stato di sicurezza delle risorse distribuite usando i dati disponibili nelle soluzioni OMS distribuite, nonché informazioni dettagliate sui dati di monitoraggio del sistema operativo delle macchine virtuali. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4d"></a>Controllo SI-4.d NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.d** L'organizzazione protegge le informazioni ottenute dagli strumenti di monitoraggio delle intrusioni derivanti da eventi di accesso, modifica ed eliminazione non autorizzati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I controlli dell'accesso logico vengono usati per proteggere le informazioni di monitoraggio in questo progetto di Azure da eventi di accesso, modifica ed eliminazione non autorizzati. Azure Active Directory applica l'accesso logico approvato tramite le appartenenze ai gruppi basati sui ruoli. La possibilità di visualizzare le informazioni di monitoraggio e usare gli strumenti di monitoraggio può essere limitata agli utenti che necessitano di tali autorizzazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4e"></a>Controllo SI-4.e NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.e** L'organizzazione aumenta il livello delle attività di monitoraggio dei sistemi informativi ogni volta che viene rilevato un maggiore rischio per le operazioni e le risorse dell'organizzazione, per singoli utenti, per altre organizzazioni o per la nazione in base a informazioni sull'applicazione delle normative di legge, informazioni di intelligence o altre fonti attendibili di informazioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di monitorare le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4f"></a>Controllo SI-4.f NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.f** L'organizzazione ottiene un parere legale in merito alle attività di monitoraggio dei sistemi informativi in conformità con le legge federali applicabili, i provvedimenti legislativi, le direttive, i criteri o le normative.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di monitorare le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-4g"></a>Controllo SI-4.g NIST 800-53

#### <a name="information-system-monitoring"></a>Monitoraggio dei sistemi informativi

**SI-4.g** L'organizzazione fornisce [Assegnazione: informazioni di monitoraggio dei sistemi informativi definite dall'organizzazione] a [Assegnazione: personale o ruoli definiti dall'organizzazione] [Selezione (una o più opzioni): se necessario; [Assegnazione: frequenza definita dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di monitorare le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-1"></a>Controllo SI-4 (1) NIST 800-53

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitoraggio dei sistemi informativi | Sistema di rilevamento delle intrusioni a livello di sistema

**SI-4 (1)** L'organizzazione collega e configura singoli strumenti di rilevamento delle intrusioni in un sistema di rilevamento delle intrusioni a livello di sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di monitorare le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-2"></a>Controllo SI-4 (2) NIST 800-53

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitoraggio dei sistemi informativi | Strumenti automatizzati per l'analisi in tempo reale

**SI-4 (2)** L'organizzazione usa strumenti automatizzati a supporto dell'analisi quasi in tempo reale degli eventi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce il servizio Log Analytics e varie soluzioni OMS, ad esempio la soluzione Sicurezza e controllo. Log Analytics fornisce analisi quasi in tempo reale degli eventi nelle risorse distribuite. Le soluzioni OMS offrono una panoramica completa sul comportamento di sicurezza tra domini di soluzioni. OMS offre informazioni di alto livello sullo stato di sicurezza delle risorse distribuite usando i dati disponibili nelle soluzioni OMS distribuite. OMS può essere configurato in modo da generare avvisi in base ai criteri definiti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-4"></a>Controllo SI-4 (4) NIST 800-53

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitoraggio dei sistemi informativi | Traffico delle comunicazioni in ingresso e in uscita

**SI-4 (4)** Il sistema informativo esegue il monitoraggio del traffico delle comunicazioni in ingresso e in uscita [Assegnazione: frequenza definita dall'organizzazione] per rilevare attività o condizioni insolite o non autorizzate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di monitorare le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-5"></a>Controllo SI-4 (5) NIST 800-53

#### <a name="information-system-monitoring--system-generated-alerts"></a>Monitoraggio dei sistemi informativi | Avvisi generati dal sistema

**SI-4 (5)** Il sistema informativo avvisa [Assegnazione: personale o ruoli definiti dall'organizzazione] quando si presentano gli indicatori di compromissione o potenziale compromissione seguenti: [Assegnazione: indicatori di compromissione definiti dall'utente].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce varie soluzioni OMS, ad esempio la soluzione Sicurezza e controllo. Log Analytics fornisce analisi quasi in tempo reale degli eventi nelle risorse distribuite. Le soluzioni OMS offrono una panoramica completa sul comportamento di sicurezza tra domini di soluzioni. OMS può essere configurato in modo da generare avvisi in base ai criteri definiti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-11"></a>Controllo SI-4 (11) NIST 800-53

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitoraggio dei sistemi informativi | Analizzare le anomalie del traffico delle comunicazioni

**SI-4 (11)** L'organizzazione analizza il traffico delle comunicazioni in uscita in corrispondenza del limite esterno del sistema informativo e dell'elemento [Assegnazione: punti interni definiti dall'organizzazione, ad esempio subnet, sottosistemi] selezionato per individuare eventuali anomalie.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'analisi delle anomalie del traffico delle comunicazioni per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-14"></a>Controllo SI-4 (14) NIST 800-53

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitoraggio dei sistemi informativi | Rilevamento delle intrusioni wireless

**SI-4 (14)** L'organizzazione usa un sistema di rilevamento delle intrusioni wireless per individuare dispositivi wireless non autorizzati e per rilevare tentativi di attacco e potenziali compromissioni/violazioni del sistema informativo.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Nei data center Azure non sono consentiti hardware controllati dai clienti, compresi i dispositivi wireless. |
| **Provider (Microsoft Azure)** | Microsoft Azure esegue regolarmente il monitoraggio dei segnali wireless non autorizzati ogni trimestre, come descritto in AC-18. <br /> Microsoft Azure implementa questo controllo per conto dei clienti PaaS e IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>Controllo SI-4 (16) NIST 800-53

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitoraggio dei sistemi informativi | Creare correlazioni tra informazioni di monitoraggio

**SI-4 (16)** L'organizzazione crea correlazioni tra le informazioni provenienti dagli strumenti di monitoraggio implementati nel sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce il servizio Log Analytics e varie soluzioni OMS, ad esempio la soluzione Sicurezza e controllo. OMS offre informazioni di alto livello sullo stato di sicurezza delle risorse distribuite usando i dati disponibili nelle soluzioni OMS distribuite. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-18"></a>Controllo SI-4 (18) NIST 800-53

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitoraggio dei sistemi informativi | Analizzare il traffico/esfiltrazioni nascoste

**SI-4 (18)** L'organizzazione analizza il traffico delle comunicazioni in uscita in corrispondenza del limite esterno del sistema informativo (perimetro del sistema) e dell'elemento [Assegnazione: punti interni definiti dall'organizzazione all'interno del sistema, ad esempio sottosistemi, subnet] selezionato per rilevare l'esfiltrazione nascosta di informazioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'analisi del traffico delle comunicazioni per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-19"></a>Controllo SI-4 (19) NIST 800-53

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitoraggio dei sistemi informativi | Singoli utenti con livello di rischio più alto

**SI-4 (19)** L'organizzazione implementa [Assegnazione: monitoraggio aggiuntivo definito dall'organizzazione] di singoli utenti individuati da [Assegnazione: origini definite dall'organizzazione] come elementi caratterizzati da un livello di rischio più alto.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del monitoraggio dei singoli utenti caratterizzati da un livello di rischio più alto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-20"></a>Controllo SI-4 (20) NIST 800-53

#### <a name="information-system-monitoring--privileged-users"></a>Monitoraggio dei sistemi informativi | Utenti con privilegi

**SI-4 (20)** L'organizzazione implementa [Assegnazione: monitoraggio aggiuntivo definito dall'organizzazione] degli utenti con privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del monitoraggio degli utenti con privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-22"></a>Controllo SI-4 (22) NIST 800-53

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitoraggio dei sistemi informativi | Servizi di rete non autorizzati

**SI-4 (22)** Il sistema informativo rileva i servizi di rete non autorizzati o non approvati da [Assegnazione: processi di autorizzazione o approvazione definiti dall'organizzazione] e [Selezione (una o più opzioni): controlli; avvisi [Assegnazione: personale o ruoli definiti dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del rilevamento dei servizi di rete non autorizzati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-23"></a>Controllo SI-4 (23) NIST 800-53

#### <a name="information-system-monitoring--host-based-devices"></a>Monitoraggio dei sistemi informativi | Dispositivi basati su host

**SI-4 (23)** L'organizzazione implementa [Assegnazione: meccanismi di monitoraggio basati su host e definiti dall'organizzazione] in corrispondenza di [Assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure raccoglie dati di monitoraggio dalle risorse implementate, inclusi i dati dalle funzionalità di monitoraggio basate su host. Microsoft Monitoring Agent viene installato in tutte le macchine virtuali Windows per raccogliere i dati di monitoraggio usati da Log Analytics e da altre soluzioni OMS. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-4-24"></a>Controllo SI-4 (24) NIST 800-53

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitoraggio dei sistemi informativi | Indicatori di compromissione

**SI-4 (24)** Il sistema informativo rileva, raccoglie, distribuisce e usa gli indicatori di compromissione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'individuazione, la raccolta, la distribuzione e l'uso degli indicatori di compromissione relativi alle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-5a"></a>Controllo SI-5.a NIST 800-53

#### <a name="security-alerts-advisories-and-directives"></a>Avvisi, avvisi di sicurezza e direttive

**SI-5.a** L'organizzazione riceve continuativamente avvisi, avvisi di sicurezza e direttive relativi al sistema informativo da [Assegnazione: organizzazioni esterne definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli avvisi, avvisi di sicurezza e direttive relativi alle risorse distribuite al cliente (inclusi applicazioni, sistemi operativi, database e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-5b"></a>Controllo SI-5.b NIST 800-53

#### <a name="security-alerts-advisories-and-directives"></a>Avvisi, avvisi di sicurezza e direttive

**SI-5.b** L'organizzazione genera avvisi, avvisi di sicurezza e direttive interni in base alle necessità.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli avvisi, avvisi di sicurezza e direttive relativi alle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-5c"></a>Controllo SI-5.c NIST 800-53

#### <a name="security-alerts-advisories-and-directives"></a>Avvisi, avvisi di sicurezza e direttive

**SI-5.c** L'organizzazione invia avvisi, avvisi di sicurezza e direttive a: [Selezione (una o più opzioni): [Assegnazione: personale o ruoli definiti dall'organizzazione]; [Assegnazione: elementi definiti dall'organizzazione all'interno dell'organizzazione]; [Assegnazione: organizzazioni esterne definite dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli avvisi, avvisi di sicurezza e direttive relativi alle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-5d"></a>Controllo SI-5.d NIST 800-53

#### <a name="security-alerts-advisories-and-directives"></a>Avvisi, avvisi di sicurezza e direttive

**SI-5.d** L'organizzazione implementa le direttive di sicurezza in conformità con gli intervalli di tempo definiti oppure comunica il grado di non conformità all'organizzazione di certificazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli avvisi, avvisi di sicurezza e direttive relativi alle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-5-1"></a>Controllo SI-5 (1) NIST 800-53

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Avvisi, avvisi di sicurezza e direttive | Avvisi e avvisi di sicurezza automatizzati

**SI-5 (1)** L'organizzazione usa meccanismi automatizzati per rendere disponibili le informazioni su avvisi e avvisi di sicurezza a livello di organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli avvisi, avvisi di sicurezza e direttive relativi alle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-6a"></a>Controllo SI-6.a NIST 800-53

#### <a name="security-function-verification"></a>Verifica della funzione di sicurezza

**SI-6.a** Il sistema informativo verifica il corretto funzionamento di [Assegnazione: funzioni di sicurezza definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle funzioni di sicurezza per le risorse distribuite al cliente (inclusi applicazioni, sistemi operativi, database e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-6b"></a>Controllo SI-6.b NIST 800-53

#### <a name="security-function-verification"></a>Verifica della funzione di sicurezza

**SI-6.b** Il sistema informativo esegue questa verifica [Selezione (una o più opzioni): [Assegnazione: stati delle transazioni definiti dall'organizzazione]; all'esecuzione del comando da parte dell'utente con privilegi appropriati; [Assegnazione: frequenza definita dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle funzioni di sicurezza per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-6c"></a>Controllo SI-6.c NIST 800-53

#### <a name="security-function-verification"></a>Verifica della funzione di sicurezza

**SI-6.c** Il sistema informativo comunica i test di verifica della sicurezza non riusciti a [Assegnazione: personale o ruoli definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle funzioni di sicurezza per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-6d"></a>Controllo SI-6.d NIST 800-53

#### <a name="security-function-verification"></a>Verifica della funzione di sicurezza

**SI-6.d** Il sistema informativo [Selezione (una o più opzioni): arresta il sistema informativo; riavvia il sistema informativo; [Assegnazione: azione/i alternativa/e definita/e dall'organizzazione]] quando vengono rilevate anomalie.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle funzioni di sicurezza per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-7"></a>Controllo SI-7 NIST 800-53

#### <a name="software-firmware-and-information-integrity"></a>Integrità di software, firmware e informazioni

**SI-7** L'organizzazione adotta strumenti di verifica dell'integrità per rilevare modifiche non autorizzate apportate a [Assegnazione: software, firmware e informazioni definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto di Azure eseguono sistemi operativi Windows. Windows fornisce servizi di convalida dell'integrità dei file in tempo reale, di protezione e di ripristino dei file di sistema di base, installati nell'ambito di Windows o di aggiornamenti di sistema di Windows autorizzati, tramite la funzionalità Protezione risorse di Windows. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-7-1"></a>Controllo SI-7 (1) NIST 800-53

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integrità di software, firmware e informazioni | Controlli di integrità

**SI-7 (1)** Il sistema informativo esegue un controllo di integrità di [Assegnazione: software, firmware e informazioni definiti dall'organizzazione] [Selezione (una o più opzioni): all'avvio; in caso di [Assegnazione: stati transazione o eventi rilevanti per la sicurezza definiti dall'organizzazione]; [Assegnazione: frequenza definita dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto di Azure eseguono sistemi operativi Windows. Windows fornisce servizi di convalida dell'integrità dei file in tempo reale, di protezione e di ripristino dei file di sistema di base, installati nell'ambito di Windows o di aggiornamenti di sistema di Windows autorizzati, tramite la funzionalità Protezione risorse di Windows. La funzionalità Protezione risorse di Windows consente l'esecuzione del controllo di integrità in tempo reale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-7-2"></a>Controllo SI-7 (2) NIST 800-53

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integrità di software, firmware e informazioni | Notifiche automatiche di violazioni di integrità

**SI-7 (2)** L'organizzazione usa strumenti automatizzati che generano notifiche che vengono inviate a [Assegnazione: personale o ruoli definiti dall'organizzazione] quando vengono rilevate discrepanze durante la verifica dell'integrità.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto di Azure eseguono sistemi operativi Windows. Windows fornisce servizi di convalida dell'integrità dei file in tempo reale, di protezione e di ripristino dei file di sistema di base, installati nell'ambito di Windows o di aggiornamenti di sistema di Windows autorizzati, tramite la funzionalità Protezione risorse di Windows.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-7-5"></a>Controllo SI-7 (5) NIST 800-53

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integrità di software, firmware e informazioni | Risposta automatica alle violazioni di integrità

**SI-7 (5)** Il sistema informativo [Selezione (una o più opzioni): arresta il sistema informativo; riavvia il sistema informativo; implementa [Assegnazione: misure di sicurezza definite dall'organizzazione]] automaticamente quando vengono rilevate violazioni di integrità.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile delle risposte automatiche alle violazioni di integrità all'interno delle risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-7-7"></a>Controllo SI-7 (7) NIST 800-53

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integrità di software, firmware e informazioni | Integrazione delle funzionalità di rilevamento e risposta

**SI-7 (7)** L'organizzazione incorpora la funzionalità di rilevamento di [Assegnazione: modifiche rilevanti per la sicurezza implementate nel sistema informativo e definite dall'organizzazione] non autorizzate nella propria funzionalità di risposta agli incidenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della protezione dell'integrità di software e informazioni per le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-7-14"></a>Controllo SI-7 (14) NIST 800-53

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integrità di software, firmware e informazioni | Codice eseguibile binario o a livello di computer

**SI-7 (14)** L'organizzazione impedisce l'uso di codice binario o eseguibile a livello di computer da origini con garanzie di sicurezza limitate o assenti e sprovviste del provisioning del codice sorgente, nonché definisce eccezioni per i requisiti del codice sorgente solo in caso di requisiti operatativi/strategici specifici e con l'approvazione del responsabile dell'autorizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di integrità di sistema e informazioni del cliente a livello aziendale possono definire i requisiti per il recupero del codice sorgente del codice eseguibile binario o a livello di computer da alcune origini. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-8a"></a>Controllo SI-8.a NIST 800-53

#### <a name="spam-protection"></a>Protezione dalla posta indesiderata

**SI-8.a** L'organizzazione usa meccanismi di protezione dalla posta indesiderata in corrispondenza dei punti di ingresso e uscita del sistema informativo per rilevare i messaggi indesiderati ed eseguire le opportune azioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Non sono presenti server di posta distribuiti nell'ambito di questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-8b"></a>Controllo SI-8.b NIST 800-53

#### <a name="spam-protection"></a>Protezione dalla posta indesiderata

**SI-8.b** L'organizzazione aggiorna i meccanismi di protezione dalla posta indesiderata nel momento in cui vengono rilasciate nuove versioni in modo conforme alle procedure e ai criteri di gestione della configurazione dell'organizzazione stessa.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Non sono presenti server di posta distribuiti nell'ambito di questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-8-1"></a>Controllo SI-8 (1) NIST 800-53

#### <a name="spam-protection--central-management"></a>Protezione dalla posta indesiderata | Gestione centrale

**SI-8 (1)** L'organizzazione gestisce a livello centralizzato i meccanismi di protezione dalla posta indesiderata.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Non sono presenti server di posta distribuiti nell'ambito di questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-si-8-2"></a>Controllo SI-8 (2) NIST 800-53

#### <a name="spam-protection--automatic-updates"></a>Protezione dalla posta indesiderata | Aggiornamenti automatici

**SI-8 (2)** Il sistema informativo aggiorna automaticamente i meccanismi di protezione dalla posta indesiderata.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Non sono presenti server di posta distribuiti nell'ambito di questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-10"></a>Controllo SI-10 NIST 800-53

#### <a name="information-input-validation"></a>Convalida dell'input di informazioni

**SI-10** Il sistema informativo verifica la validità di [Assegnazione: input di informazioni definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della convalida degli input di informazioni per le risorse distribuite al cliente (inclusi applicazioni, sistemi operativi, database e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-11a"></a>Controllo SI-11.a NIST 800-53

#### <a name="error-handling"></a>Gestione degli errori

**SI-11.a** Il sistema informativo genera messaggi di errore che forniscono le informazioni necessarie per le azioni correttive da eseguire senza tuttavia divulgare informazioni riservate che potrebbero essere usate da avversari o malware.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano sistemi operativi e applicazioni software commerciali. Questo software usa procedure consigliate specifiche del settore per garantire la non divulgazione di informazioni riservate tramite i messaggi di errore. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-11b"></a>Controllo SI-11.b NIST 800-53

#### <a name="error-handling"></a>Gestione degli errori

**SI-11.b** Il sistema informativo rende disponibili i messaggi di errore solo a [Assegnazione: personale o ruoli definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questo progetto di Azure usano sistemi operativi e applicazioni software commerciali. Questo software usa procedure consigliate specifiche del settore per generare messaggi di errore appropriati al contesto di ricezione del messaggio. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-12"></a>Controllo SI-12 NIST 800-53

#### <a name="information-handling-and-retention"></a>Gestione e conservazione delle informazioni

**SI-12** L'organizzazione gestisce e conserva le informazioni all'interno del sistema informativo e l'output delle informazioni dal sistema in modo conforme alle leggi federali applicabili, agli ordini esecutivi, alle direttive, ai criteri, alle normative, agli standard e ai requisiti operativi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione e conservazione delle informazioni all'interno delle risorse distribuite al cliente (inclusi applicazioni, sistemi operativi, database e software), oltre che dell'output delle informazioni da tali risorse. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-si-16"></a>Controllo SI-16 NIST 800-53

#### <a name="memory-protection"></a>Protezione della memoria

**SI-16** Il sistema informativo implementa [Assegnazione: misure di sicurezza definite dall'organizzazione] per proteggere la memoria dall'esecuzione di codice non autorizzata.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto di Azure eseguono sistemi operativi Windows. Windows ha messo a punto le seguenti funzionalità di protezione per impedire l'esecuzione di codice in posizioni con capacità di memoria limitata: NX (No Execute), ASLR (Address Space Layout Randomization) e Protezione esecuzione programmi. |
| **Provider (Microsoft Azure)** | Non applicabile |
