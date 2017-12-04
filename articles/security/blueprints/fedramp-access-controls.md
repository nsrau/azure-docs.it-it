---
title: Programma FedRAMP per l'automazione dei progetti di Azure - Controllo di accesso
description: Applicazioni Web per FedRAMP - Controllo di accesso
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>Controllo di accesso

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-ac-1"></a>Controllo NIST 800-53 AC-1

#### <a name="access-control-policy-and-procedures"></a>Criteri e procedure di controllo di accesso

**AC-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: ruoli o personale definiti dall'organizzazione] criteri di controllo di accesso relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di controllo di accesso e dei controlli di accesso associati. Esamina e aggiorna inoltre i criteri di controllo di accesso correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure di controllo di accesso [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di controllo di accesso a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2a"></a>Controllo NIST 800-53 AC-2.a

#### <a name="account-management"></a>Gestione degli account

**AC-2.a** L'organizzazione identifica e seleziona i tipi seguenti di account del sistema informatico per supportare gli obiettivi e/o le funzioni aziendali dell'organizzazione: [Assegnazione: tipi di account del sistema informatico definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure è basato su e implementa questi tipi di account del sistema informatico: utenti di Azure Active Directory (usati per distribuire la soluzione e gestire l'accesso alle risorse di Azure), utenti del sistema operativo Windows (gestiti da Active Directory), account del servizio SQL Server. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2b"></a>Controllo NIST 800-53 AC-2.b

#### <a name="account-management"></a>Gestione degli account

**AC-2.b** L'organizzazione assegna responsabili degli account per gli account del sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'assegnazione di responsabili agli account identificati nel controllo AC-2.a. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2c"></a>Controllo NIST 800-53 AC-2.c

#### <a name="account-management"></a>Gestione degli account

**AC-2.c** L'organizzazione stabilisce le condizioni per l'appartenenza ai gruppi e ai ruoli.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della definizione dei criteri di appartenenza ai ruoli e ai gruppi per i tipi di account controllati dal cliente stesso (vedere AC-2.a). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2d"></a>Controllo NIST 800-53 AC-2.d

#### <a name="account-management"></a>Gestione degli account

**AC-2.d** L'organizzazione specifica gli utenti autorizzati del sistema informatico, l'appartenenza ai gruppi e ai ruoli e le autorizzazioni di accesso, ovvero i privilegi, e altri attributi (come richiesto) per ogni account.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo di autorizzazione degli account a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2e"></a>Controllo NIST 800-53 AC-2.e

#### <a name="account-management"></a>Gestione degli account

**AC-2.e** L'organizzazione richiede le approvazioni di [Assegnazione: ruoli o personale definiti dall'organizzazione] per le richieste di creazione di account del sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo di autorizzazione degli account a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2f"></a>Controllo NIST 800-53 AC-2.f

#### <a name="account-management"></a>Gestione degli account

**AC-2.f** L'organizzazione crea, abilita, modifica, disabilita e rimuove account del sistema informatico in base a [Assegnazione: procedure o condizioni definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a un processo di gestione degli account a livello aziendale definito. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2g"></a>Controllo NIST 800-53 AC-2.g

#### <a name="account-management"></a>Gestione degli account

**AC-2.g** L'organizzazione monitora l'uso degli account del sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il dashboard Identità e accesso della soluzione di sicurezza e controllo di OMS. Questo dashboard consente ai responsabili degli account di monitorare l'uso degli account del sistema informatico. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2h"></a>Controllo NIST 800-53 AC-2.h

#### <a name="account-management"></a>Gestione degli account

**AC-2.h** L'organizzazione comunica ai responsabili degli account quando gli account non sono più necessari, quando gli utenti lasciano l'organizzazione o vengono trasferiti e quando l'utilizzo o le necessità dei singoli utenti del sistema informatico cambiano.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo per comunicare al responsabile degli account appropriato quando un account non è più necessario. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2i"></a>Controllo NIST 800-53 AC-2.i

#### <a name="account-management"></a>Gestione degli account

**AC-2.i** L'organizzazione autorizza l'accesso al sistema informatico in base a un'autorizzazione di accesso valida, all'utilizzo del sistema previsto e ad altri attributi in base a quanto richiesto dall'organizzazione o dagli obiettivi e/o dalle funzioni aziendali associati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo di autorizzazione degli accessi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2j"></a>Controllo NIST 800-53 AC-2.j

#### <a name="account-management"></a>Gestione degli account

**AC-2.j** L'organizzazione esamina gli account per la conformità ai requisiti di gestione degli account [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della revisione degli account controllati dal cliente stesso alla frequenza richiesta per determinare se siano conformi a tutti i requisiti dell'organizzazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-2k"></a>Controllo NIST 800-53 AC-2.k

#### <a name="account-management"></a>Gestione degli account

**AC-2.k** L'organizzazione stabilisce un processo per la nuova emissione di credenziali degli account condivisi/di gruppo (se distribuite) quando vengono rimossi singoli utenti dal gruppo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo per la gestione delle credenziali degli account di gruppo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-1"></a>Controllo NIST 800-53 AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Gestione degli account | Gestione automatica degli account del sistema

**AC-2 (1)** L'organizzazione impiega meccanismi automatici per supportare la gestione degli account del sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il dashboard Identità e accesso della soluzione di sicurezza e controllo di OMS. Questo dashboard consente ai responsabili degli account di monitorare l'uso degli account del sistema informatico. È possibile configurare OMS per inviare avvisi in caso di attività atipica sospetta o se si verificano altri eventi predefiniti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-2"></a>Controllo NIST 800-53 AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Gestione degli account | Rimozione di account temporanei/di emergenza

**AC-2 (2)** Il sistema informatico [Selezione: rimuove; disabilita] automaticamente account temporanei e di emergenza dopo [Assegnazione: periodo di tempo definito dall'organizzazione per ogni tipo di account].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure non distribuisce account temporanei o di emergenza. Se gli account non vengono disabilitati manualmente, il controller di dominio distribuito disabilita automaticamente tutti gli account inattivi dopo 35 giorni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-3"></a>Controllo NIST 800-53 AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Gestione degli account | Disabilitazione di account inattivi

**AC-2 (3)** Il sistema informatico disabilita automaticamente gli account inattivi dopo [Assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il controller di dominio distribuito da questo progetto di Azure è configurato per la disabilitazione di tutti gli account utente dopo 35 giorni di inattività. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-4"></a>Controllo NIST 800-53 AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Gestione degli account | Azioni di controllo automatiche

**AC-2 (4)** Il sistema informatico controlla automaticamente creazione, modifica, abilitazione, disabilitazione e rimozione di azioni degli account e invia una notifica a [Assegnazione: ruoli o personale definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa questi tipi di account del sistema: utenti di Azure Active Directory, utenti del sistema operativo Windows, account del servizio SQL Server. Le azioni di gestione degli account Azure Active Directory generano un evento nel log attività di Azure. Le azioni di gestione degli account a livello di sistema operativo generano un evento nel log di sistema. Questi log vengono raccolti da Log Analytics e archiviati nel repository di OMS. È possibile configurare OMS per inviare avvisi quando si verificano eventi predefiniti.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-5"></a>Controllo NIST 800-53 AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Gestione degli account | Disconnessione per inattività

**AC-2 (5)** L'organizzazione richiede che gli utenti si disconnettano dopo/quando [Assegnazione: periodo di tempo definito dall'organizzazione per l'inattività prevista o descrizione dei casi in cui disconnettersi].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono stabilire che gli utenti si disconnettano quando prevedono di essere inattivi per un certo periodo di tempo (o altri fattori). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-7a"></a>Controllo NIST 800-53 AC-2 (7)

#### <a name="account-management--role-based-schemes"></a>Gestione degli account | Schemi basati sui ruoli

**AC-2 (7).a** L'organizzazione stabilisce e amministra gli account utente con privilegi secondo uno schema di accesso in base al ruolo che organizza in ruoli i privilegi e l'accesso al sistema informatico consentiti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa questi tipi di account del sistema: utenti di Azure Active Directory, utenti del sistema operativo Windows, account del servizio SQL Server. I privilegi degli account Azure Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a ruoli. I privilegi degli account Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a gruppi di sicurezza. Questi schemi basati sui ruoli possono essere estesi dal cliente per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-7b"></a>Controllo NIST 800-53 AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>Gestione degli account | Schemi basati sui ruoli

**AC-2 (7).b** L'organizzazione monitora le assegnazioni di ruolo con privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il dashboard Identità e accesso della soluzione di sicurezza e controllo di OMS. Questo dashboard consente ai responsabili degli account di monitorare l'uso degli account del sistema informatico. È possibile interrogare la soluzione per segnalare le assegnazioni di ruolo con privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-7c"></a>Controllo NIST 800-53 AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>Gestione degli account | Schemi basati sui ruoli

**AC-2 (7).c** L'organizzazione esegue [Assegnazione: azioni definite dall'organizzazione] quando le assegnazioni di ruolo con privilegi non sono più appropriate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di intervenire sugli account controllati dal cliente stesso quando le assegnazioni di ruolo con privilegi non sono più appropriate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-9"></a>Controllo NIST 800-53 AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Gestione degli account | Restrizioni relative all'uso di account condivisi/di gruppo

**AC-2 (9)** L'organizzazione consente solo l'uso di account condivisi/di gruppo che soddisfano [Assegnazione: condizioni definite dall'organizzazione per stabilire account condivisi/di gruppo].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nessun account condiviso/di gruppo è consentito nelle risorse distribuite da questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-10"></a>Controllo NIST 800-53 AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Gestione degli account | Terminazione delle credenziali degli account condivisi/di gruppo

**AC-2 (10)** Il sistema informatico termina le credenziali degli account condivisi/di gruppo quando i membri lasciano il gruppo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nessun account condiviso/di gruppo è consentito nelle risorse distribuite da questo progetto di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-11"></a>Controllo NIST 800-53 AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Gestione degli account | Condizioni di utilizzo

**AC-2 (11)** Il sistema informatico applica [Assegnazione: condizioni di utilizzo e/o casi definiti dall'organizzazione] per [Assegnazione: account del sistema informatico definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. È possibile definire criteri di gruppo in Active Directory e configurarli per implementare restrizioni per determinati momenti del giorno o in base ad altre condizioni di utilizzo degli account. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-12a"></a>Controllo NIST 800-53 AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestione degli account | Monitoraggio/Utilizzo atipico degli account

**AC-2 (12).a** L'organizzazione monitora gli account del sistema informatico per [Assegnazione: utilizzo atipico definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il dashboard Identità e accesso della soluzione di sicurezza e controllo di OMS. Questo dashboard consente ai responsabili degli account di monitorare i tentativi di accesso sulle risorse distribuite. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-12b"></a>Controllo NIST 800-53 AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gestione degli account | Monitoraggio/Utilizzo atipico degli account

**AC-2 (12).b** L'organizzazione segnala l'utilizzo atipico degli account del sistema informatico a [Assegnazione: ruoli o personale definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il dashboard Identità e accesso della soluzione di sicurezza e controllo di OMS. Questo dashboard consente ai responsabili degli account di monitorare i tentativi di accesso sulle risorse distribuite. È possibile configurare la soluzione per inviare avvisi in caso di attività atipica sospetta o se si verificano altri eventi predefiniti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-2-13"></a>Controllo NIST 800-53 AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Gestione degli account | Disabilitazione degli account per singoli utenti ad alto rischio

**AC-2 (13)** L'organizzazione disabilita gli account di utenti che pongono un rischio significativo entro [Assegnazione: periodo di tempo definito dall'organizzazione] dall'individuazione del rischio.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di controllo di accesso a livello aziendale del cliente possono stabilire condizioni per la disabilitazione degli account per gli utenti che pongono un rischio significativo per l'organizzazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-3"></a>Controllo NIST 800-53 AC-3

#### <a name="access-enforcement"></a>Applicazione dell'accesso

**AC-3** Il sistema informatico applica autorizzazioni approvate per l'accesso logico alle risorse informatiche e di sistema in base a criteri di controllo di accesso validi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure applica autorizzazioni di accesso logico tramite il controllo degli accessi in base al ruolo amministrato da Azure Active Directory assegnando utenti a ruoli, da Active Directory assegnando utenti a gruppi di sicurezza e da controlli a livello di sistema operativo Windows. I ruoli di Azure Active Directory assegnati a utenti o gruppi controllano l'accesso logico alle risorse in Azure a livello di risorsa, gruppo o sottoscrizione. I gruppi di sicurezza di Active Directory controllano l'accesso logico a risorse e funzioni a livello di sistema operativo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-4"></a>Controllo NIST 800-53 AC-4

#### <a name="information-flow-enforcement"></a>Applicazione del controllo dei flussi di informazioni

**AC-4** Il sistema informatico applica autorizzazioni approvate per il controllo dei flussi di informazioni nel sistema e tra sistemi interconnessi in base a [Assegnazione: criteri di controllo dei flussi di informazioni definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure impone restrizioni relative ai flussi di informazioni tramite l'uso di gruppi di sicurezza di rete applicati alle subnet in cui sono distribuite le risorse, al gateway applicazione e al servizio di bilanciamento del carico. I gruppi di sicurezza di rete fanno sì che il flusso di informazioni tra le risorse sia controllato in base a regole approvate. Il gateway applicazione e il servizio di bilanciamento del carico instradano dinamicamente il traffico a risorse specifiche in base a regole approvate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-4-8"></a>Controllo NIST 800-53 AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Applicazione del controllo dei flussi di informazioni | Filtri per i criteri di sicurezza

**AC-4 (8)** Il sistema informatico applica il controllo dei flussi di informazioni tramite [Assegnazione: filtri per i criteri di sicurezza definiti dall'organizzazione] come base per decisioni relative al controllo dei flussi per [Assegnazione: flussi di informazioni definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'applicazione del controllo dei flussi di informazioni nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-4-21"></a>Controllo NIST 800-53 AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Applicazione del controllo dei flussi di informazioni | Separazione fisica/logica dei flussi di informazioni

**AC-4 (21)** Il sistema informatico separa logicamente o fisicamente i flussi di informazioni tramite [Assegnazione: tecniche e/o meccanismi definiti dall'organizzazione] per eseguire [Assegnazione: separazioni necessarie definite dall'organizzazione in base ai tipi di informazioni].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della separazione dei flussi di informazioni nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-5a"></a>Controllo NIST 800-53 AC-5.a

#### <a name="separation-of-duties"></a>Separazione dei compiti

**AC-5.a** L'organizzazione separa [Assegnazione: compiti definiti dall'organizzazione per singoli utenti].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della separazione dei compiti tra gli account controllati dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-5b"></a>Controllo NIST 800-53 AC-5.b

#### <a name="separation-of-duties"></a>Separazione dei compiti

**AC-5.b** L'organizzazione documenta la separazione dei compiti di singoli utenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della documentazione della separazione dei compiti tra gli account controllati dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-5c"></a>Controllo NIST 800-53 AC-5.c

#### <a name="separation-of-duties"></a>Separazione dei compiti

**AC-5.c** L'organizzazione definisce le autorizzazioni di accesso al sistema informatico per supportare la separazione dei compiti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il controllo degli accessi in base al ruolo, che può essere configurato per separare i compiti in base ai requisiti dell'organizzazione. I privilegi degli account Azure Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a ruoli. I privilegi degli account Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a gruppi di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-6"></a>Controllo NIST 800-53 AC-6

#### <a name="least-privilege"></a>Privilegi minimi

**AC-6** L'organizzazione adotta il principio dei privilegi minimi, che consente agli utenti (o ai processi che agiscono per conto degli utenti) solo gli accessi autorizzati necessari per svolgere le attività assegnate in base agli obiettivi e alle funzioni aziendali dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il controllo degli accessi in base al ruolo per limitare gli utenti ai soli privilegi assegnati esplicitamente. I privilegi degli account Azure Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a ruoli. I privilegi degli account Active Directory vengono implementati tramite il controllo degli accessi in base al ruolo assegnando utenti a gruppi di sicurezza.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-1"></a>Controllo NIST 800-53 AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Privilegi minimi | Autorizzazione dell'accesso a funzioni di sicurezza

**AC-6 (1)** L'organizzazione autorizza esplicitamente l'accesso a [Assegnazione: funzioni di sicurezza (distribuite in hardware, software e firmware) e informazioni relative alla sicurezza definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo di autorizzazione degli accessi che include l'accesso a funzioni di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-2"></a>Controllo NIST 800-53 AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Privilegi minimi | Accesso senza privilegi per funzioni non di sicurezza

**AC-6 (2)** L'organizzazione richiede che gli utenti di account, o ruoli, del sistema informatico con accesso a [Assegnazione: funzioni di sicurezza o informazioni relative alla sicurezza definite dall'organizzazione] usino account o ruoli senza privilegi quando accedono a funzioni non di sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono richiedere che gli utenti usino account senza privilegi quando accedono a funzioni non di sicurezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-3"></a>Controllo NIST 800-53 AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Privilegi minimi | Accesso di rete a comandi con privilegi

**AC-6 (3)** L'organizzazione autorizza l'accesso di rete a [Assegnazione: comandi con privilegi definiti dall'organizzazione] solo per [Assegnazione: esigenze operative pressanti definite dall'organizzazione] e documenta i motivi di tale accesso nel piano di sicurezza per il sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono definire comandi con privilegi cui è possibile accedere tramite una rete. Nota: i clienti non hanno accesso fisico all'infrastruttura di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-5"></a>Controllo NIST 800-53 AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Privilegi minimi | Account con privilegi

**AC-6 (5)** L'organizzazione limita gli account con privilegi nel sistema informatico a [Assegnazione: ruoli o personale definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono definire restrizioni relative all'uso di account con privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-7a"></a>Controllo NIST 800-53 AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>Privilegi minimi | Revisione dei privilegi degli utenti

**AC-6 (7).a** L'organizzazione esamina [Assegnazione: frequenza definita dall'organizzazione] i privilegi assegnati a [Assegnazione: ruoli o classi di utenti definiti dall'organizzazione] per convalidare la necessità di tali privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della revisione dei privilegi degli utenti di account controllati dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-7b"></a>Controllo NIST 800-53 AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>Privilegi minimi | Revisione dei privilegi degli utenti

**AC-6 (7).b** L'organizzazione riassegna o rimuove privilegi, se necessario, in modo che corrispondano correttamente agli obiettivi e alle esigenze aziendali dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della riassegnazione o della rimozione dei privilegi per account controllati dal cliente stesso nei casi appropriati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-8"></a>Controllo NIST 800-53 AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Privilegi minimi | Livelli di privilegi per l'esecuzione di codice

**AC-6 (8)** Il sistema informatico impedisce l'esecuzione di [Assegnazione: software definito dall'organizzazione] a livelli di privilegi superiori rispetto a quelli degli utenti che usano il software.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il controllo degli accessi in base al ruolo per limitare gli utenti ai soli privilegi assegnati esplicitamente. Le misure di protezione a livello di sistema operativo delle macchine virtuali non consentono l'esecuzione di software a livelli di privilegi superiori rispetto a quelli degli utenti che usano il software. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-9"></a>Controllo NIST 800-53 AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Privilegi minimi | Controllo dell'uso di funzioni con privilegi

**AC-6 (9)** Il sistema informatico controlla l'esecuzione di funzioni con privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il servizio Log Analytics in OMS. Le macchine virtuali e gli account di archiviazione di Diagnostica di Azure distribuiti sono origini connesse a Log Analytics che garantiscono il controllo dell'esecuzione di funzioni con privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-6-10"></a>Controllo NIST 800-53 AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Privilegi minimi | Proibizione dell'esecuzione di funzioni con privilegi a utenti senza privilegi

**AC-6 (10)** Il sistema informatico impedisce a utenti senza privilegi di eseguire funzioni con privilegi per includere la disabilitazione, l'elusione o la modifica delle misure/contromisure di sicurezza implementate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure implementa il controllo degli accessi in base al ruolo per limitare gli utenti ai soli privilegi assegnati esplicitamente.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-7a"></a>Controllo NIST 800-53 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Tentativi di accesso non riusciti

**AC-7.a** Il sistema informatico applica un limite di [Assegnazione: numero definito dall'organizzazione] tentativi di accesso non validi consecutivi effettuati da un utente per [Assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il portale di Azure limita i tentativi di accesso non validi consecutivi effettuati dagli utenti. Vengono applicati criteri di gruppo a livello di sistema operativo per tutte le macchine virtuali distribuite tramite questo progetto di Azure. I criteri limitano i tentativi di accesso non validi consecutivi effettuati dagli utenti a non più di tre in un periodo di tempo di 15 minuti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-7b"></a>Controllo NIST 800-53 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Tentativi di accesso non riusciti

**AC-7.b** Il sistema informatico [Selezione: blocca l'account/il nodo per [Assegnazione: periodo di tempo definito dall'organizzazione]; blocca l'account/il nodo fino al rilascio da parte di un amministratore; posticipa la successiva richiesta di accesso in base a [Assegnazione: algoritmo di ritardo definito dall'organizzazione]] automaticamente quando viene superato il numero massimo di tentativi non riusciti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il portale di Azure blocca gli account dopo un certo numero di tentativi di accesso non validi consecutivi effettuati dagli utenti. Vengono applicati criteri di gruppo a livello di sistema operativo per tutte le macchine virtuali distribuite tramite questo progetto di Azure. I criteri bloccano gli account per tre ore dopo tre tentativi di accesso non validi consecutivi effettuati dagli utenti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-7-2"></a>Controllo NIST 800-53 AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Tentativi di accesso non riusciti | Pulizia/Cancellazione del dispositivo mobile

**AC-7 (2)** Il sistema informatico ripulisce/cancella le informazioni da [Assegnazione: dispositivi mobili definiti dall'organizzazione] in base a [Assegnazione: requisiti/tecniche di pulizia/cancellazione definiti dall'organizzazione] dopo [Assegnazione: numero definito dall'organizzazione] tentativi di accesso non riusciti consecutivi al dispositivo.

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | I dispositivi mobili non rientrano nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente dispositivi mobili nell'ambito di Azure. Per questo motivo, questo controllo non è applicabile a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>Controllo NIST 800-53 AC-8.a

#### <a name="system-use-notification"></a>Notifica sull'uso del sistema

**AC-8.a** Prima di concedere l'accesso, il sistema informatico visualizza agli utenti [Assegnazione: messaggio o banner di notifica sull'uso del sistema definito dall'organizzazione] che fornisce notifiche su privacy e sicurezza ai sensi di leggi federali, ordini esecutivi, direttive, criteri, normative, standard e linee guida in vigore e indica che gli utenti stanno accedendo a un sistema informatico del governo degli Stati Uniti. L'utilizzo del sistema può essere monitorato, registrato e soggetto a controllo; l'utilizzo non autorizzato del sistema informatico è vietato e soggetto a sanzioni penali e civili e l'uso del sistema informatico indica il consenso al monitoraggio e alla registrazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Criteri di gruppo implementano una notifica sull'uso del sistema che viene visualizzata agli utenti prima dell'accesso. Nota: il progetto di Azure implementa una notifica sull'uso del sistema di esempio. Il cliente deve modificare questo testo per soddisfare i requisiti dell'organizzazione e/o dell'organismo regolatore. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-8b"></a>Controllo NIST 800-53 AC-8.b

#### <a name="system-use-notification"></a>Notifica sull'uso del sistema

**AC-8.b** Il sistema informatico mantiene il messaggio o il banner di notifica sullo schermo finché gli utenti non confermano di aver letto le condizioni di utilizzo e agiscono esplicitamente per connettersi o accedere al sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Criteri di gruppo implementano una notifica sull'uso del sistema che viene visualizzata agli utenti prima dell'accesso. L'utente deve confermare di aver letto la notifica per poter accedere. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-8c"></a>Controllo NIST 800-53 AC-8.c

#### <a name="system-use-notification"></a>Notifica sull'uso del sistema

**AC-8.c** Il sistema informatico visualizza informazioni sull'uso del sistema per sistemi accessibili pubblicamente [Assegnazione: condizioni definite dall'organizzazione] prima di concedere ulteriore accesso; visualizza i riferimenti, se disponibili, per il monitoraggio, la registrazione o il controllo ai sensi delle disposizioni sulla privacy per tali sistemi che in genere vietano queste attività e include una descrizione degli usi autorizzati del sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della visualizzazione di una notifica sull'uso del sistema in tutte le risorse distribuite dal cliente stesso e accessibili pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-10"></a>Controllo NIST 800-53 AC-10

#### <a name="concurrent-session-control"></a>Controllo delle sessioni simultanee

**AC-10** Il sistema informatico limita il numero di sessioni simultanee per ogni [Assegnazione: account e/o tipo di account definito dall'organizzazione] a [Assegnazione: numero definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Vengono implementati criteri per il sistema operativo per le macchine virtuali distribuite da questo progetto di Azure. I criteri implementano le restrizioni relative alle sessioni simultanee (due sessioni). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-11a"></a>Controllo NIST 800-53 AC-11.a

#### <a name="session-lock"></a>Blocco della sessione

**AC-11.a** Il sistema informatico impedisce ulteriore accesso al sistema avviando un blocco della sessione dopo [Assegnazione: periodo di tempo definito dall'organizzazione] di inattività o alla ricezione di una richiesta da un utente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Criteri di gruppo implementano un blocco di inattività per sessioni RDP. Gli utenti possono avviare manualmente il blocco. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-11b"></a>Controllo NIST 800-53 AC-11.b

#### <a name="session-lock"></a>Blocco della sessione

**AC-11.b** Il sistema informatico mantiene il blocco della sessione finché l'utente non ristabilisce l'accesso usando procedure di identificazione e autenticazione definite.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Criteri di gruppo implementano un blocco di inattività per sessioni RDP. Gli utenti devono autenticarsi di nuovo per sbloccare la sessione.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-11-1"></a>Controllo NIST 800-53 AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Blocco della sessione | Schermate per nascondere le informazioni

**AC-11 (1)** Il sistema informatico nasconde, tramite il blocco della sessione, informazioni precedentemente visibili sullo schermo con un'immagine visualizzabile pubblicamente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Criteri di gruppo implementano un blocco di inattività per sessioni RDP. Il blocco della sessione nasconde informazioni precedentemente visibili. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-12"></a>Controllo NIST 800-53 AC-12

#### <a name="session-termination"></a>Terminazione della sessione

**AC-12** Il sistema informatico termina automaticamente una sessione utente dopo [Assegnazione: condizioni o eventi di attivazione definiti dall'organizzazione che richiedono la disconnessione della sessione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | È possibile definire la configurazione host della sessione Desktop remoto per le macchine virtuali Windows distribuite da questo progetto di Azure per soddisfare i requisiti di terminazione della sessione dell'organizzazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-12-1a"></a>Controllo NIST 800-53 AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Terminazione della sessione | Disconnessioni avviate dall'utente/Visualizzazione di messaggi

**AC-12 (1).a** Il sistema informatico fornisce una funzionalità di disconnessione per le sessioni di comunicazione avviate dall'utente ogni volta che si usa l'autenticazione per accedere a [Assegnazione: risorse informatiche definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il portale di Azure e i sistemi operativi delle macchine virtuali distribuiti da questo progetto di Azure consentono agli utenti di avviare una disconnessione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-12-1b"></a>Controllo NIST 800-53 AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Terminazione della sessione | Disconnessioni avviate dall'utente/Visualizzazione di messaggi

**AC-12 (1).b** Il sistema informatico visualizza agli utenti un messaggio di disconnessione esplicito, che indica la terminazione affidabile delle sessioni di comunicazione con autenticazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il portale di Azure e i sistemi operativi delle macchine virtuali distribuiti da questo progetto di Azure consentono agli utenti di avviare una disconnessione. Il processo di disconnessione fornisce un'indicazione agli utenti riguardo all'avvenuta terminazione della sessione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-14a"></a>Controllo NIST 800-53 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Azioni consentite senza identificazione o autenticazione

**AC-14.a** L'organizzazione identifica [Assegnazione: azioni utente definite dall'organizzazione] che possono essere eseguite nel sistema informatico senza identificazione o autenticazione in accordo con gli obiettivi e/o le funzioni aziendali dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'identificazione delle azioni che possono essere eseguite nelle risorse distribuite dal cliente stesso senza identificazione o autenticazione, ad esempio la visualizzazione di una pagina Web accessibile pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-14b"></a>Controllo NIST 800-53 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Azioni consentite senza identificazione o autenticazione

**AC-14.b** L'organizzazione documenta e specifica i motivi a supporto nel piano di sicurezza per il sistema informatico le azioni utente che non richiedono l'identificazione o l'autenticazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della presentazione della documentazione per le azioni utente che non richiedono l'identificazione o l'autenticazione nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-17a"></a>Controllo NIST 800-53 AC-17.a

#### <a name="remote-access"></a>Accesso remoto

**AC-17.a** L'organizzazione stabilisce e documenta le restrizioni di utilizzo, i requisiti di configurazione/connessione e le linee guida di implementazione per ogni tipo di accesso remoto consentito.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono definire restrizioni di utilizzo per l'accesso remoto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-17b"></a>Controllo NIST 800-53 AC-17.b

#### <a name="remote-access"></a>Accesso remoto

**AC-17.b** L'organizzazione autorizza l'accesso remoto al sistema informatico prima di consentire tali connessioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo di autorizzazione dell'accesso remoto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-1"></a>Controllo NIST 800-53 AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Accesso remoto | Monitoraggio/Controllo automatico

**AC-17 (1)** Il sistema informatico monitora e controlla i metodi di accesso remoto.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure consente l'accesso remoto al sistema informatico tramite il portale di Azure, una connessione Desktop remoto con un jumpbox e un'applicazione Web implementata dal cliente. Gli accessi tramite il portale di Azure e le sessioni Desktop remoto vengono controllati e possono essere monitorati con OMS. Il cliente deve implementare controlli di accesso remoto, in base a quanto necessario, per l'applicazione Web. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-2"></a>Controllo NIST 800-53 AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Accesso remoto | Protezione della riservatezza/integrità tramite crittografia

**AC-17 (2)** Il sistema informatico implementa meccanismi di crittografia per proteggere la riservatezza e l'integrità delle sessioni di accesso remoto.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso remoto alle risorse distribuite da questo progetto di Azure, inclusi il portale di Azure, la connessione Desktop remoto e il gateway di applicazioni Web, è protetto tramite TLS. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-3"></a>Controllo NIST 800-53 AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Accesso remoto | Punti di controllo di accesso gestiti

**AC-17 (3)** Il sistema informatico instrada tutti gli accessi remoti tramite [Assegnazione: numero definito dall'organizzazione] punti di controllo di accesso di rete gestiti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso remoto all'applicazione Web distribuita da questo progetto di Azure avviene tramite un gateway applicazione. L'accesso remoto a tutte le altre risorse avviene tramite un jumpbox. Non sono presenti altri endpoint accessibili pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-4a"></a>Controllo NIST 800-53 AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>Accesso remoto | Comandi/Accesso con privilegi

**AC-17 (4).a** L'organizzazione autorizza l'esecuzione di comandi e accesso con privilegi a informazioni relative alla sicurezza tramite l'accesso remoto solo per [Assegnazione: esigenze definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono definire comandi con privilegi cui è possibile accedere in remoto e includono i motivi. Nota: i clienti non hanno accesso di rete diretto all'infrastruttura di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-4b"></a>Controllo NIST 800-53 AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>Accesso remoto | Comandi/Accesso con privilegi

**AC-17 (4).b** L'organizzazione documenta i motivi per tale accesso nel piano di sicurezza per il sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono definire comandi con privilegi cui è possibile accedere in remoto e includono i motivi. Nota: i clienti non hanno accesso di rete diretto all'infrastruttura di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-17-9"></a>Controllo NIST 800-53 AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Accesso remoto | Disconnessione/Disabilitazione dell'accesso

**AC-17 (9)** L'organizzazione consente di disconnettere o disabilitare rapidamente l'accesso remoto al sistema informatico entro [Assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto di Azure consente l'accesso remoto al sistema informatico tramite il portale di Azure, una connessione Desktop remoto con un jumpbox e un'applicazione Web. Se un account Azure Active Directory viene disabilitato o rimosso, l'accesso al portale di Azure viene disconnesso immediatamente. Analogamente, se un account a livello di sistema operativo di una macchina virtuale viene disabilitato o rimosso, l'accesso tramite Desktop remoto con il jumpbox viene disconnesso immediatamente. I clienti devono implementare la disconnessione dell'accesso remoto per l'applicazione Web. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-18a"></a>Controllo NIST 800-53 AC-18.a

#### <a name="wireless-access"></a>Accesso wireless

**AC-18.a** L'organizzazione stabilisce le restrizioni di utilizzo, i requisiti di configurazione/connessione e le linee guida di implementazione per l'accesso wireless.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure stabilisce restrizioni di utilizzo, requisiti di configurazione/connessione e linee guida di implementazione per l'accesso wireless tramite lo standard di sicurezza di rete, che vieta esplicitamente l'uso dell'accesso wireless nell'ambiente Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>Controllo NIST 800-53 AC-18.b

#### <a name="wireless-access"></a>Accesso wireless

**AC-18.b** L'organizzazione autorizza l'accesso wireless al sistema informatico prima di consentire tali connessioni.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso wireless in data center di Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>Controllo NIST 800-53 AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Accesso wireless | Autenticazione e crittografia

**AC-18 (1)** Il sistema informatico protegge l'accesso wireless al sistema usando l'autenticazione di [Selezione (uno o più): utenti; dispositivi] e la crittografia.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso wireless nell'ambiente Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>Controllo NIST 800-53 AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Accesso wireless | Disabilitazione delle reti wireless

**AC-18 (3)** L'organizzazione disabilita, quando non sono destinate all'uso, le funzionalità di rete wireless integrate nei componenti del sistema informatico prima del rilascio e della distribuzione.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso wireless nell'ambiente Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>Controllo NIST 800-53 AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Accesso wireless | Restrizioni relative alle configurazioni da parte degli utenti

**AC-18 (4)** L'organizzazione identifica e autorizza esplicitamente gli utenti cui è consentito configurare in modo indipendente le funzionalità di rete wireless.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso wireless nell'ambiente Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>Controllo NIST 800-53 AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Accesso wireless | Antenne/Potenza di trasmissione

**AC-18 (5)** L'organizzazione seleziona le antenne radio e calibra la potenza di trasmissione per ridurre la probabilità che segnali utilizzabili possano essere ricevuti al di fuori dei confini controllati dall'organizzazione.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | L'accesso wireless non è consentito nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso wireless nell'ambiente Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>Controllo NIST 800-53 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Controllo di accesso per dispositivi mobili

**AC-19.a** L'organizzazione stabilisce restrizioni di utilizzo, requisiti di configurazione, requisiti di connessione e linee guida di implementazione per i dispositivi mobili controllati dall'organizzazione.

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi mobili controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente dispositivi mobili nell'ambito di Azure. Per questo motivo, questo controllo non è applicabile a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>Controllo NIST 800-53 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Controllo di accesso per dispositivi mobili

**AC-19.b** L'organizzazione autorizza la connessione di dispositivi mobili a sistemi informatici aziendali.

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi mobili controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente dispositivi mobili nell'ambito di Azure. Per questo motivo, questo controllo non è applicabile a Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>Controllo NIST 800-53 AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Controllo di accesso per dispositivi mobili | Crittografia dispositivi completa/basata su contenitori

**AC-19 (5)** L'organizzazione impiega [Selezione: crittografia dispositivi completa; crittografia basata su contenitori] per proteggere la riservatezza e l'integrità delle informazioni in [Assegnazione: dispositivi mobili definiti dall'organizzazione].

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi mobili controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente dispositivi mobili nell'ambito di Azure. Per questo motivo, questo controllo non è applicabile a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>Controllo NIST 800-53 AC-20.a

#### <a name="use-of-external-information-systems"></a>Uso di sistemi informatici esterni

**AC-20.a** L'organizzazione stabilisce termini e condizioni, coerentemente con qualsiasi relazione di trust definita con altre organizzazioni che possiedono, eseguono e/o gestiscono sistemi informatici esterni, che consentono a singoli utenti di accedere al sistema informatico da sistemi informatici esterni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono includere una disposizione relativa all'uso di servizi cloud ai sensi di FedRAMP. Ad Azure è stata concessa un'autorizzazione P-ATO (Provisional Authorities to Operate) dall'ente JAB (Joint Authorization Board) di FedRAMP che consente l'uso in acquisizione di servizi cloud da parte di enti pubblici. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-20b"></a>Controllo NIST 800-53 AC-20.b

#### <a name="use-of-external-information-systems"></a>Uso di sistemi informatici esterni

**AC-20.b** L'organizzazione stabilisce termini e condizioni, coerentemente con qualsiasi relazione di trust definita con altre organizzazioni che possiedono, eseguono e/o gestiscono sistemi informatici esterni, che consentono a singoli utenti autorizzati di elaborare, archiviare o trasmettere informazioni controllate dall'organizzazione tramite sistemi informatici esterni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono includere una disposizione relativa all'uso di servizi cloud ai sensi di FedRAMP. Ad Azure è stata concessa un'autorizzazione P-ATO (Provisional Authorities to Operate) dall'ente JAB (Joint Authorization Board) di FedRAMP che consente l'uso in acquisizione di servizi cloud da parte di enti pubblici. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-20-1"></a>Controllo NIST 800-53 AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Uso di sistemi informatici esterni | Limiti all'uso autorizzato

**AC-20 (1)** L'organizzazione consente a singoli utenti autorizzati di usare un sistema informatico esterno per accedere al sistema informatico o per elaborare, archiviare o trasmettere informazioni controllate dall'organizzazione solo quando l'organizzazione verifica l'implementazione dei controlli di sicurezza necessari nel sistema esterno in base a quanto specificato nei criteri di sicurezza delle informazioni e nel piano di sicurezza dell'organizzazione oppure mantiene la connessione al sistema informatico approvato o i contratti per il trattamento delle informazioni con l'entità aziendale che ospita il sistema informatico esterno.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il gruppo IT aziendale del cliente può verificare la conformità del provider di servizi cloud ai requisiti di sicurezza delle informazioni dell'organizzazione e concedere a livello aziendale l'approvazione a usare i servizi cloud associati. Ad Azure è stata concessa un'autorizzazione P-ATO (Provisional Authorities to Operate) dall'ente JAB (Joint Authorization Board) di FedRAMP. Azure viene valutato da una società di valutazione di terze parti approvata da FedRAMP per la verifica della conformità ai requisiti relativi ai controlli di sicurezza FedRAMP e di altro tipo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ac-20-2"></a>Controllo NIST 800-53 AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Uso di sistemi informatici esterni | Dispositivi di archiviazione portatili

**AC-20 (2)** L'organizzazione [Selezione: limita; vieta] l'uso di dispositivi di archiviazione portatili controllati dall'organizzazione da parte di singoli utenti autorizzati in sistemi informatici esterni.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft non consente dispositivi di archiviazione portatili controllati dal cliente nell'ambiente Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>Controllo NIST 800-53 AC-21.a

#### <a name="information-sharing"></a>Condivisione delle informazioni

**AC-21.a** L'organizzazione semplifica la condivisione delle informazioni consentendo a utenti autorizzati di determinare se le autorizzazioni di accesso assegnate al partner di condivisione corrispondono alle restrizioni di accesso applicate alle informazioni per [Assegnazione: casi di condivisione delle informazioni definiti dall'organizzazione in cui è richiesto il giudizio dell'utente].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di controllo di accesso a livello aziendale del cliente possono includere disposizioni relative alla condivisione di informazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 AC-21.b

#### <a name="information-sharing"></a>Condivisione delle informazioni

**AC-21.b** L'organizzazione impiega [Assegnazione: meccanismi automatici o processi manuali definiti dall'organizzazione] per supportare gli utenti nell'adottare decisioni relative alla condivisione di informazioni/collaborazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a una funzionalità di supporto per le decisioni sulla condivisione di informazioni a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-22a"></a>Controllo NIST 800-53 AC-22.a

#### <a name="publicly-accessible-content"></a>Contenuto accessibile pubblicamente

**AC-22.a** L'organizzazione designa singoli utenti autorizzati a inserire informazioni in un sistema informatico accessibile pubblicamente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono designare singoli utenti autorizzati a inserire informazioni accessibili pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-22b"></a>Controllo NIST 800-53 AC-22.b

#### <a name="publicly-accessible-content"></a>Contenuto accessibile pubblicamente

**AC-22.b** L'organizzazione istruisce singoli utenti autorizzati a verificare che le informazioni accessibili pubblicamente non contengano informazioni non pubbliche.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può ricorrere a formazione a livello aziendale per singoli utenti autorizzati a inserire informazioni accessibili pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-22c"></a>Controllo NIST 800-53 AC-22.c

#### <a name="publicly-accessible-content"></a>Contenuto accessibile pubblicamente

**AC-22.c** L'organizzazione esamina il contenuto proposto delle informazioni prima di inserirle nel sistema informatico accessibile pubblicamente, in modo da garantire che non siano incluse informazioni non pubbliche.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo di revisione del contenuto proposto per l'inserimento in un sistema accessibile pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ac-22d"></a>Controllo NIST 800-53 AC-22.d

#### <a name="publicly-accessible-content"></a>Contenuto accessibile pubblicamente

**AC-22.d** L'organizzazione esamina il contenuto nel sistema informatico accessibile pubblicamente per individuare eventuali informazioni non pubbliche [Assegnazione: frequenza definita dall'organizzazione] e rimuove queste informazioni, se presenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di controllo di accesso a livello aziendale del cliente possono stabilire un processo per la revisione periodica del contenuto inserito in sistemi accessibili pubblicamente. |
| **Provider (Microsoft Azure)** | Non applicabile |

