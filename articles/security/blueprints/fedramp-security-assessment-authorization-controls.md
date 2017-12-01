---
title: Automazione dei progetti Azure FedRAMP - Valutazione della sicurezza e autorizzazione
description: Applicazioni Web per FedRAMP - Valutazione della sicurezza e autorizzazione
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 47c5914d-0d76-498a-9298-b3d9040791f8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 24c3e53d12f1479ba2d6b17bf28ebb7e5912206e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="security-assessment-and-authorization-ca"></a>Valutazione della sicurezza e autorizzazione

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti come parte del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-ca-1"></a>Controllo CA-1 NIST 800-53

#### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Criteri e procedure di valutazione della sicurezza e autorizzazione

**CA-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: ruoli o personale definiti dall'organizzazione] criteri di valutazione della sicurezza e autorizzazione relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di valutazione della sicurezza e autorizzazione e dei controlli associati. Esamina e aggiorna inoltre i criteri di valutazione della sicurezza e autorizzazione correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure valutazione della sicurezza e autorizzazione [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-2a"></a>Controllo CA-2.a NIST 800-53-2

#### <a name="security-assessments"></a>Valutazione della sicurezza

**CA-2.a** L'organizzazione sviluppa un piano di valutazione della sicurezza che descrive l'ambito della valutazione, inclusi i controlli di sicurezza e i miglioramenti della sicurezza oggetto della valutazione; le procedure di valutazione da utilizzare per determinare l'efficacia dei controlli di sicurezza e l'ambiente di valutazione, il team di valutazione e i ruoli e le responsabilità relativi alla valutazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo di un piano di valutazione della sicurezza per il sistema distribuito dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-2b"></a>Controllo CA-2.b NIST 800-53-2

#### <a name="security-assessments"></a>Valutazione della sicurezza

**CA-2.b** L'organizzazione valuta i controlli di sicurezza nel sistema informatico e nel suo ambiente operativo [Assegnazione: frequenza definita dall'organizzazione] per determinare la misura in cui i controlli sono implementati correttamente, funzionano come previsto e producono il risultato desiderato soddisfacendo i requisiti di sicurezza stabiliti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della valutazione dei controlli di sicurezza definiti in CA-02.a nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-2c"></a>Controllo CA-2.c NIST 800-53

#### <a name="security-assessments"></a>Valutazione della sicurezza

**CA-2.c** L'organizzazione produce un report di valutazione della sicurezza che documenta i risultati della valutazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della creazione di un report di valutazione della sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-2d"></a>Controllo CA-2.d NIST 800-53

#### <a name="security-assessments"></a>Valutazione della sicurezza

**CA-2.d** L'organizzazione fornisce i risultati della valutazione dei controlli di sicurezza a [Assegnazione: individui o ruoli definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di fornire i risultati della valutazione della sicurezza agli individui o ruoli richiesti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-2-1"></a>Controllo CA-2 (1) NIST 800-53

#### <a name="security-assessments--independent-assessors"></a>Valutazioni della sicurezza | Ispettori indipendenti

**CA-2 (1)** L'organizzazione assume ispettori o team di valutazione con [Assegnazione: livello di indipendenza definito dall'azienda] per condurre valutazioni dei controlli di sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di ricorrere a ispettori o team di valutazione indipendenti per condurre le valutazioni dei controlli di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-2-2"></a>Controllo CA-2 (2) NIST 800-53

#### <a name="security-assessments--specialized-assessments"></a>Valutazioni della sicurezza | Valutazioni specializzate

**CA-2 (2)** L'organizzazione include come parte delle valutazioni dei controlli di sicurezza, [Assegnazione: frequenza definita dall'organizzazione], [Selezione: annunciato; non annunciato], [Selezione (una o più): monitoraggio approfondito; analisi vulnerabilità; test utenti malintenzionati; valutazione delle minacce interne; test di carico/delle prestazioni; [Assegnazione: altre forme di valutazione della sicurezza definite dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile per la selezione di test aggiuntivi da includere come parte delle valutazioni dei controlli di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-2-3"></a>Controllo CA-2 (3) NIST 800-53

#### <a name="security-assessments--external-organizations"></a>Valutazioni della sicurezza | Organizzazioni esterne

**CA-2 (3)** L'organizzazione accetta i risultati di una valutazione di [Assegnazione: sistema informatico definito dall'organizzazione] eseguita da [Assegnazione: organizzazione esterna definita dall'organizzazione] quando la valutazione rispetta [Assegnazione: requisiti definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente possono riguardare l'accettazione dei risultati della valutazione delle offerte di servizi cloud (es. Azure) eseguite da un'organizzazione esterna nell'ambito di FedRAMP (es. una società di valutazione di terze parti o un'altra agenzia). Azure viene valutato da una società di valutazione di terze parti approvata da FedRAMP per la verifica della conformità ai requisiti relativi ai controlli di sicurezza FedRAMP e di altro tipo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-3a"></a>Controllo CA-3.a NIST 800-53

#### <a name="system-interconnections"></a>Interconnessioni del sistema

**CA-3.a** L'organizzazione autorizza connessioni dal sistema informatico ad altri sistemi informatici tramite l'uso di accordi di sicurezza per le interconnessioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente possono riguardare l'autorizzazione delle interconnessioni del sistema. Nota: FedRAMP non richiede ISA tra un CSP e un'agenzia federale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-3b"></a>Controllo CA-3.b NIST 800-53

#### <a name="system-interconnections"></a>Interconnessioni del sistema

**CA-3.b** L'organizzazione documenta, per ciascuna interconnessione, le caratteristiche dell'interfaccia, i requisiti di sicurezza e la natura delle informazioni comunicate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente definiscono i requisiti per stabilire interconnessioni nel sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-3c"></a>Controllo CA-3.c della NIST 800-53

#### <a name="system-interconnections"></a>Interconnessioni del sistema

**CA-3.c** L'organizzazione esamina e aggiorna gli accordi di sicurezza delle interconnessioni [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente possono stabilire processi di analisi e aggiornamento degli ISA. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-3-3"></a>Controllo CA-3 (3) NIST 800-53

#### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Interconnessioni del sistema | Connessioni del sistema di sicurezza non nazionali non classificate

**CA-3 (3)** L'organizzazione proibisce la connessione diretta di un [Assegnazione: sistema di sicurezza non nazionale non classificato definito dall'organizzazione] a una rete esterna senza l'uso di [Assegnazione: dispositivo di protezione dei limiti definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di valutazione della sicurezza e autorizzazione a livello aziendale del cliente possono definire requisiti di protezione dei limiti per le interconnessioni del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-3-5"></a>Controllo CA-3 (5) NIST 800-53

#### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Interconnessioni del sistema | Restrizioni relative alle connessioni esterne al sistema

**CA-3 (5)** L'organizzazione utilizza un criterio [Selezione: consenti tutti, nega per eccezione; nega tutti, consenti per eccezione] per consentire [Assegnazione: sistemi informatici definiti dall'organizzazione] di connettersi a sistemi informatici esterni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Per limitare la connettività esterna alle risorse distribuite da questo progetto Azure, si distribuiscono gateway applicazione e gruppi di sicurezza di rete. I set di regole applicati ai gruppi di sicurezza di rete sono configurati in base a uno schema Nega per impostazione predefinita. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-5a"></a>Controllo CA-5.a NIST 800-53

#### <a name="plan-of-action-and-milestones"></a>Piano di azione e attività cardine

**CA-5.a** L'organizzazione sviluppa un piano di azione e attività cardine per il sistema informatico al fine di documentare le azioni correttive previste dall'organizzazione per correggere le debolezze o le mancanze riscontrate durante la valutazione dei controlli di sicurezza e ridurre o eliminare le vulnerabilità note nel sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo di un piano di azioni e attività cardine relative alle risorse distribuite dal cliente stesso (inclusi sistemi operativi, database, applicazioni e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-5b"></a>Controllo CA-5.b NIST 800-53

#### <a name="plan-of-action-and-milestones"></a>Piano di azione e attività cardine

**CA-5.b** L'organizzazione aggiorna il piano di azione e le attività cardine esistenti [Assegnazione: frequenza definita dall'organizzazione] in base ai risultati delle valutazioni dei controlli della sicurezza, delle analisi dell'impatto della sicurezza e delle attività di monitoraggio continuo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'aggiornamento del piano di azione e delle attività cardine definiti in CA-05.a. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-6a"></a>Controllo CA-6.a NIST 800-53

#### <a name="security-authorization"></a>Autorizzazione di sicurezza

**CA-6.a** L'organizzazione nomina un dirigente o manager di alto livello come responsabile delle autorizzazioni per il sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo a livello aziendale quando nomina i responsabili delle autorizzazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-6b"></a>Controllo CA-6.b NIST 800-53

#### <a name="security-authorization"></a>Autorizzazione di sicurezza

**CA-6.b** L'organizzazione garantisce che il responsabile delle autorizzazioni autorizzi il sistema informatico per l'elaborazione prima di iniziare le operazioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo di autorizzazione del sistema a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-6c"></a>Controllo CA-6.c NIST 800-53

#### <a name="security-authorization"></a>Autorizzazione di sicurezza

**CA-6.c** L'organizzazione aggiorna l'autorizzazione di sicurezza [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo di autorizzazione del sistema a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7a"></a>Controllo CA-7.a NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.a** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include la definizione di [Assegnazione: metriche definite dall'organizzazione] da monitorare.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7b"></a>Controllo Ca-7.b NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.b** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include la definizione di [Assegnazione: frequenze definite dall'organizzazione] per il monitoraggio e [Assegnazione: frequenze definite dall'organizzazione] per le valutazioni a supporto di tale monitoraggio.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7c"></a>Controllo Ca-7.c NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.c** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include una valutazione continua dei controlli di sicurezza in conformità alla strategia aziendale di monitoraggio continuo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7d"></a>Controllo Ca-7.d NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.d** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include il monitoraggio dello stato di sicurezza delle metriche definite dall'organizzazione in conformità alla strategia aziendale di monitoraggio continuo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7e"></a>Controllo CA-7.e NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.e** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include correlazione e analisi delle informazioni relative alla sicurezza generate da valutazioni e monitoraggio.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7f"></a>Controllo CA-7.f NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.f** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include azioni di risposta per agire sui risultati dell'analisi delle informazioni relative alla sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-7g"></a>Controllo CA-7.g NIST 800-53

#### <a name="continuous-monitoring"></a>Monitoraggio continuo

**CA-7.g** L'organizzazione sviluppa una strategia di monitoraggio continuo e implementa un programma di monitoraggio continuo che include la documentazione dello stato di sicurezza dell'organizzazione e del sistema informatico a [Assegnazione: personale o ruoli definiti dall'organizzazione] [Assegnazione: frequenze definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può essere sufficiente per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-7-1"></a>Controllo CA-7 (1) NIST 800-53

#### <a name="continuous-monitoring--independent-assessment"></a>Monitoraggio continuo | Valutazione indipendente

**CA-7 (1)** L'organizzazione assume ispettori o team di valutazione con [Assegnazione: livello di indipendenza definito dall'azienda] per monitorare i controlli di sicurezza nel sistema informatico su base continua.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può definire un processo per la valutazione indipendente dei controlli di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-7-3"></a>Controllo CA-7 (3) NIST 800-53

#### <a name="continuous-monitoring--trend-analyses"></a>Monitoraggio continuo | Analisi delle tendenze

**CA-7 (3)** L'organizzazione ricorre all'analisi delle tendenze per determinare se le implementazioni dei controlli di sicurezza, la frequenza delle attività di monitoraggio continuo e/o i tipi di attività usate nel processo di monitoraggio continuo debbano essere modificate in base ai dati empirici.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il programma di monitoraggio continuo a livello aziendale del cliente può definire un processo di analisi delle tendenze. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-8"></a>Controllo CA-8 NIST 800-53

#### <a name="penetration-testing"></a>Test di penetrazione

**CA-8** L'organizzazione conduce un test di penetrazione [Assegnazione: frequenza definita dall'organizzazione] su [Assegnazione: sistemi informatici o componenti del sistema definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a una valutazione a livello aziendale del test di penetrazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ca-8-1"></a>Controllo CA-8 (1) NIST 800-53

#### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Test di penetrazione | Agente o team di penetrazione indipendente

**CA-8 (1)** L'organizzazione ricorre a un agente o a un team di penetrazione indipendente per eseguire il test di penetrazione sul sistema informatico o su componenti del sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a una valutazione del test di penetrazione a livello aziendale eseguita da un agente o da un team indipendente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-9a"></a>Controllo CA-9.a NIST 800-53

#### <a name="internal-system-connections"></a>Connessioni di sistema interne

**CA-9.a** L'organizzazione autorizza connessioni interne di [Assegnazione: componenti o classi di componenti del sistema informatico definiti dall'organizzazione] al sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'autorizzazione di connessioni interne nelle risorse distribuite dal cliente stesso (es. connessioni del sistema a macchine virtuali). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ca-9b"></a>Controllo CA-9.b NIST 800-53

#### <a name="internal-system-connections"></a>Connessioni di sistema interne

**CA-9.b** L'organizzazione documenta, per ciascuna connessione interna, le caratteristiche dell'interfaccia, i requisiti di sicurezza e la natura delle informazioni comunicate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della documentazione relativa ai dettagli di ogni connessione interna tra le classi/risorse definite in CA-09.a. |
| **Provider (Microsoft Azure)** | Non applicabile |
