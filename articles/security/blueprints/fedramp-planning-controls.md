---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP - Pianificazione
description: Automazione di applicazioni Web per FedRAMP - Pianificazione
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="planning-pl"></a>Pianificazione (PL, Planning)

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-pl-1"></a>Controllo PL-1 NIST 800-53

#### <a name="security-planning-policy-and-procedures"></a>Procedure e criteri di pianificazione della sicurezza

**PL-1** L'organizzazione sviluppa, documenta e distribuisce a [assegnazione: ruoli o personale definiti dall'organizzazione] criteri di pianificazione della sicurezza relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di pianificazione della sicurezza e dei controlli associati. Esamina e aggiorna inoltre i criteri di pianificazione della sicurezza correnti [assegnazione: frequenza definita dall'organizzazione] e le procedure pianificazione della sicurezza [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di pianificazione della sicurezza a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-2a"></a>Controllo PL-2.a NIST 800-53

#### <a name="system-security-plan"></a>Piano di sicurezza del sistema

**PL-2** L'organizzazione sviluppa un piano di sicurezza per il sistema informativo coerente con l'architettura aziendale dell'organizzazione, definisce in modo esplicito il limite di autorizzazione per il sistema e descrive il contesto operativo del sistema informativo in termini di missioni e processi aziendali. Fornisce la categorizzazione di sicurezza del sistema informativo inclusa la logica di supporto, descrive l'ambiente operativo del sistema informativo e le relazioni o le connessioni ad altri sistemi informativi. Fornisce una panoramica dei requisiti di sicurezza del sistema e identifica le sovrapposizioni rilevanti, se applicabili. Descrive i controlli di sicurezza predisposti o pianificati per soddisfare tali requisiti inclusa la logica per le decisioni adattate alla situazione. Il piano viene esaminato e approvato dal responsabile delle autorizzazioni o da un rappresentante designato prima dell'implementazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo di un piano di sicurezza del sistema (SSP) che soddisfi i criteri definiti dall'entità di autorizzazione di destinazione, ad esempio FedRAMP. I clienti possono fare riferimento alla pubblicazione speciale NIST 800-18 R1, Guide for Developing Security Plans for Federal Information Systems. L'SSP del cliente deve eseguire i controlli ereditati da Microsoft Azure e fare riferimento all'SSP di Microsoft Azure per i dettagli dell'implementazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-2b"></a>Controllo PL-2.b NIST 800-53

#### <a name="system-security-plan"></a>Piano di sicurezza del sistema

**PL-2.b** L'organizzazione distribuisce copie del piano di sicurezza e comunica le successive modifiche al piano a [assegnazione: ruoli o personale definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della distribuzione del piano di sicurezza del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-2c"></a>Controllo PL-2.c NIST 800-53

#### <a name="system-security-plan"></a>Piano di sicurezza del sistema

**PL-2.c** L'organizzazione esamina il piano di sicurezza del sistema informativo [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di esaminare il piano di sicurezza del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-2d"></a>Controllo PL-2.d NIST 800-53

#### <a name="system-security-plan"></a>Piano di sicurezza del sistema

**PL-2.d** L'organizzazione aggiorna il piano per inserire le modifiche al sistema informativo/all'ambiente operativo o per fare fronte a problemi identificati durante l'implementazione del piano o le valutazioni dei controlli di sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'aggiornamento del piano di sicurezza del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-2e"></a>Controllo PL-2.e NIST 800-53

#### <a name="system-security-plan"></a>Piano di sicurezza del sistema

**PL-2.e** L'organizzazione protegge il piano di sicurezza da modifiche e da divulgazione non autorizzate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della protezione del piano di sicurezza del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-pl-2-3"></a>Controllo PL-2 (3) NIST 800-53

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Piano di protezione del sistema | Pianificazione/coordinamento con altre entità organizzative

**PL-2 (3)** L'organizzazione pianifica e coordina le attività relative alla sicurezza che interessano il sistema informativo con [assegnazione: gruppi o individui definiti dall'organizzazione] prima dell'esecuzione di tali attività per ridurre l'impatto sulle altre entità organizzative.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della pianificazione e del coordinamento delle attività relative alla sicurezza per ridurre l'impatto sulle altre entità organizzative. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-4a"></a>Controllo PL-4.a NIST 800-53

#### <a name="rules-of-behavior"></a>Regole di comportamento

**PL-4.a** L'organizzazione stabilisce e rende disponibili agli utenti che richiedono l'accesso al sistema informativo le regole che descrivono le responsabilità e il comportamento previsto per l'uso delle informazioni e del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le regole di comportamento a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-4b"></a>Controllo PL-4.b NIST 800-53

#### <a name="rules-of-behavior"></a>Regole di comportamento

**PL-4.b** L'organizzazione riceve una conferma scritta dai singoli utenti per indicare che hanno letto, compreso e accettato di attenersi alle regole di comportamento prima di autorizzare l'accesso alle informazioni e al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le regole di comportamento a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-4c"></a>Controllo PL-4.c NIST 800-53

#### <a name="rules-of-behavior"></a>Regole di comportamento

**PL-4.c** L'organizzazione esamina e aggiorna le regole di comportamento [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le regole di comportamento a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-4d"></a>Controllo PL-4.d NIST 800-53

#### <a name="rules-of-behavior"></a>Regole di comportamento

**PL-4.d** L'organizzazione richiede agli utenti che hanno firmato una versione precedente delle regole di comportamento di leggere e firmare nuovamente quando le regole di comportamento vengono modificate o aggiornate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le regole di comportamento a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-pl-4-1"></a>Controllo PL-4 (1) NIST 800-53

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Regole di comportamento | Restrizioni di rete e social media

**PL-4 (1)** L'organizzazione include nelle regole di comportamento delle restrizioni specifiche relative all'uso dei siti di rete/dei social media e sulla pubblicazioni di informazioni sull'organizzazione su siti Web pubblici.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le regole di comportamento a livello aziendale del cliente possono includere restrizioni sui social media/siti di rete. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-8a"></a>Controllo PL-8.a NIST 800-53

#### <a name="information-security-architecture"></a>Architettura di sicurezza delle informazioni

**PL-8.a** L'organizzazione sviluppa un'architettura di sicurezza delle informazioni per il sistema informativo che descrive la filosofia, i requisiti e l'approccio generale per quanto riguarda la protezione della riservatezza, l'integrità e la disponibilità delle informazioni dell'organizzazione. Descrive il modo in cui l'architettura di sicurezza delle informazioni è integrata e supporta l'architettura aziendale e descrive i presupposti e le dipendenze della sicurezza delle informazioni da servizi esterni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo di un'architettura di sicurezza delle informazioni per le risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-8b"></a>Controllo PL-8.b NIST 800-53

#### <a name="information-security-architecture"></a>Architettura di sicurezza delle informazioni

**PL-8.b** L'organizzazione esamina e aggiorna l'architettura di sicurezza delle informazioni [assegnazione: frequenza definita dall'organizzazione] in base agli aggiornamenti dell'architettura aziendale.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della revisione e dell'aggiornamento dell'architettura di sicurezza delle informazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pl-8c"></a>Controllo PL-8.c NIST 800-53

#### <a name="information-security-architecture"></a>Architettura di sicurezza delle informazioni

**PL-8.c** L'organizzazione garantisce che le modifiche pianificate all'architettura di sicurezza delle informazioni siano riflesse nel piano di sicurezza, nel CONOPS (Concept of Operations) di sicurezza e nelle acquisizioni/appalti dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'inserimento delle modifiche pianificate nell'architettura di sicurezza delle informazioni. |
| **Provider (Microsoft Azure)** | Non applicabile |
