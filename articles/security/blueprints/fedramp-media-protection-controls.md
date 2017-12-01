---
title: Automazione FedRAMP di Azure Blueprint - Protezione dei supporti
description: Applicazioni Web per FedRAMP - Protezione dei supporti
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>Protezione dei supporti

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti come parte del documento NIST Special Publication 800-53, Revisione 4. Consultare la pubblicazione NIST 800-53 Rev. 4 per informazioni sulle procedure e le indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-mp-1"></a>Controllo MP-1 NIST 800-53

#### <a name="media-protection-policy-and-procedures"></a>Procedure e criteri di protezione dei supporti

**MP-1** L'organizzazione sviluppa, documenta e distribuisce a [assegnazione: ruoli o personale definiti dall'organizzazione] criteri di protezione dei supporti relativi a scopo, ambito, ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre a procedure per semplificare l'implementazione dei criteri di protezione dei supporti e dei controlli associati. Esamina e aggiorna i criteri di protezione dei supporti correnti [assegnazione: frequenza definita dall'organizzazione] e le procedure di protezione dei supporti [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di protezione dei supporti a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-mp-2"></a>Controllo MP-2 NIST 800-53

#### <a name="media-access"></a>Accesso ai supporti

**MP-2** L'organizzazione limita l'accesso a [assegnazione: i tipi definiti dall'organizzazione di supporti digitali e/o non digitali] a [assegnazione: ruolo i personale definito dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure ha implementato l'accesso ai supporti tramite l'implementazione dei criteri di sicurezza di Microsoft. L'accesso logico ai supporti digitali è controllata tramite gli oggetti Criteri di gruppo di Active Directory e i gruppi di sicurezza. L'accesso fisico a tutti i supporti è limitato dal processo di accesso ai datacenter. L'accesso è limitato a persone che hanno uno scopo aziendale legittimo per accedere ai dati. Consultare PE-3, controllo di accesso fisico, per altre informazioni sui controlli di accesso al datacenter presente. Lo Standard di protezione delle risorse definisce le misure di sicurezza necessarie per proteggere la riservatezza, l'integrità e la disponibilità degli risorse informatiche all'interno dei datacenter di Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>Controllo MP-3.a NIST 800 53

#### <a name="media-marking"></a>Contrassegno dei supporti

**MP-3.a** L'organizzazione contrassegna il supporto del sistema informatico che indica i limiti di distribuzione, le indicazioni di gestione e di sicurezza applicabile (se presenti) delle informazioni.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure contrassegna le risorse all'interno dei datacenter di Microsoft con una designazione HBI, MBI o LBI, ovvero a impatto elevato, moderato o basso, che richiede diversi livelli di sicurezza e precauzione di gestione. I proprietari delle risorse devono classificare le risorse archiviate all'interno di un datacenter Microsoft. Per altre informazioni fare riferimento agli standard di classificazione delle risorse e agli standard di protezione delle risorse. |


 ## <a name="nist-800-53-control-mp-3b"></a>Controllo MP-3.b NIST 800 53

#### <a name="media-marking"></a>Contrassegno dei supporti

**MP-3.b** L'organizzazione esonera [assegnazione: i tipi definiti dall'organizzazione di supporti per i sistemi informatici] dal contrassegno fino a quando il supporto rimane all'interno di [assegnazione: aree controllate definite dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure richiede ai proprietari di assegnare le proprie risorse con una classificazione; nessuna risorsa è esente da questo requisito. Nell'ambiente datacenter di Microsoft, con il termine risorse si fa riferimento ai server, ai dispositivi di rete e i nastri magnetici. Altri supporti digitali, quali unità flash/chiavette USB, unità di dischi rigidi esterne o rimovibili oppure CD/DVD non vengono usati. Nel datacenter non vengono usati i supporti non digitali. |


 ## <a name="nist-800-53-control-mp-4a"></a>Controllo MP-4.a NIST 800 53

#### <a name="media-storage"></a>Conservazione dei supporti

**MP-4.a** L'organizzazione controlla fisicamente e conserva in modo sicuro [assegnazione: tipi definiti dall'organizzazione di supporti digitali e/o non digitali] all'interno di [assegnazione: aree controllate definite dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Le risorse dei supporti digitali di Microsoft Azure vengono conservate fisicamente e in modo sicuro in una stanza di condivisione per i datacenter. I datacenter di Microsoft hanno più livelli di controlli di accesso fisico, quali badge di accesso, biometria, (vedere PE 3 per altri dettagli sui controlli di accesso fisico) e dispongono di video-sorveglianza per garantire una conservazione protetta. I supporti digitali includono server, dispositivi di rete e nastri magnetici usati per il backup. Nell'ambiente del datacenter non vengono usati i supporti non digitali. |


 ## <a name="nist-800-53-control-mp-4b"></a>Controllo MP-4.b NIST 800 53

#### <a name="media-storage"></a>Conservazione dei supporti

**MP-4.b** L'organizzazione protegge i supporti dei sistemi informatici fino a quando questi non vengono distrutti o purificati tramite apparecchiature, tecniche e procedure approvate.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Le risorse dei supporti digitali di Microsoft Azure sono protetti in ambienti di condivisione del datacenter di Microsoft tramite i controlli di accesso fisico (PE-3) e accesso logico (IA-2) per tutta la durata della risorsa. Le risorse di Microsoft Azure sono cancellate, ripulite o distrutte con i metodi coerenti con NIST SP 800-88 prima dell'eliminazione delle risorse. Per la distruzione delle risorse, Microsoft Azure usa i servizi di distruzione delle risorse in loco. |


 ## <a name="nist-800-53-control-mp-5a"></a>Controllo MP-5.a NIST 800 53

#### <a name="media-transport"></a>Trasporto dei supporti

**MP-5.a** L'organizzazione protegge e controlla [assegnazione: tipi definiti dall'organizzazione di supporti per i sistemi informatici] durante il trasporto all'esterno di aree controllate tramite [assegnazione: misure di sicurezza definite dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | I supporti digitali di Microsoft Azure nei datacenter di Microsoft sono costituiti da server, dispositivi di rete e nastri e dischi magnetici per il backup, laddove sia possibile. I datacenter di Microsoft non usano supporti non digitali. Microsoft usa tre metodi per proteggere i supporti trasportati all'esterno del datacenter: 1) trasporto protetto, 2) crittografia, 3) pulizia, cancellazione o distruzione. |


 ## <a name="nist-800-53-control-mp-5b"></a>Controllo MP-5.b NIST 800 53

#### <a name="media-transport"></a>Trasporto dei supporti

**MP-5.b** L'organizzazione è responsabile per il supporto dei supporti per i sistemi informatici durante il trasporto all'esterno delle aree controllate.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure è responsabile delle risorse che lasciano il datacenter tramite l'uso delle istruzioni di NIST SP 800-88: cancellazione/eliminazione coerente, distruzione delle risorse, crittografia, inventario accurato, rilevamento e protezione della catena di custodia durante il trasporto. |


 ## <a name="nist-800-53-control-mp-5c"></a>Controllo MP-5.c NIST 800 53

#### <a name="media-transport"></a>Trasporto dei supporti

**MP-5.c** L'organizzazione documenta le attività associate al trasporto dei supporti per i sistemi informatici.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure mantiene i record di inventario precedenti al trasporto, al rilevamento e alla protezione della catena di custodia durante il trasporto, la cancellazione/eliminazione della risorsa, la distruzione e la ricezione della risorsa,nonché la convalida dell'inventario dopo il trasporto. |


 ## <a name="nist-800-53-control-mp-5d"></a>Controllo MP-5.d NIST 800 53

#### <a name="media-transport"></a>Trasporto dei supporti

**MP-5.c** L'organizzazione limita le attività associate al trasporto dei supporti per i sistemi informatici al personale autorizzato.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure limita le attività di trasporto delle risorse al personale autorizzato tramite la protezione della catena di custodia. L'uso di lucchetti, sigilli a prova di manomissione, nonché la richiesta di convalida degli inventari delle risorse garantisce che solo il personale autorizzato siano coinvolto nel trasporto delle risorse. |


 ### <a name="nist-800-53-control-mp-5-4"></a>Controllo MP-5 (4) NIST 800-53

#### <a name="media-transport--cryptographic-protection"></a>Trasporto dei supporti | Protezione crittografica

**MP-5 (4)** Il sistema informatico implementa meccanismi di crittografia per proteggere la riservatezza e l'integrità delle informazioni archiviate su supporti digitali durante il trasporto all'esterno delle aree controllate.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure usa il servizio di protezione dati per gestire le chiavi crittografiche tramite un modulo di crittografia convalidato di livello 3 FIPS 140-2 (cert #1694) e il modulo di protezione hardware (cert #1178) per proteggere i dati a 256 bit AES sui nastri magnetici. |


 ## <a name="nist-800-53-control-mp-6a"></a>Controllo MP-6.a NIST 800 53

#### <a name="media-sanitization"></a>Purificazione dei supporti

**MP-6.a** L'organizzazione purifica [assegnazione: supporto per i sistemi informatici definito dall'organizzazione] prima dell'eliminazione, del rilascio esterno all'azienda o del rilascio per il riutilizzo tramite [assegnazione: procedure e tecniche di purificazione definite dall'organizzazione] in base ai criteri e agli standard federali e aziendali applicabili.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure richiede che i supporti digitali nell'ambiente datacenter di Microsoft Azure vengano cancellati o eliminati tramite strumenti approvati da Microsoft Azure e in conformità con NIST SP 800-88, indicazioni per la purificazione dei supporti, prima di essere riutilizzati o eliminati. Nell'ambiente del datacenter Microsoft Azure non usa supporti non digitali. |


 ## <a name="nist-800-53-control-mp-6b"></a>Controllo MP-6.b NIST 800 53

#### <a name="media-sanitization"></a>Purificazione dei supporti

**MP-6.b** L'organizzazione usa i meccanismi di purificazione con il livelli di impegno e integrità proporzionati alla categoria di sicurezza o alla classificazione delle informazioni.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure usa unità e processi di cancellazione dei dati per cancellare /eliminare i dati in conformità con NIST SP 800-88 e in modo proporzionato alla classificazione delle risorse di Microsoft Azure. Quando si rende necessari la distruzione delle risorse, Microsoft Azure usa i servizi di distruzione delle risorse in loco. |


 ### <a name="nist-800-53-control-mp-6-1"></a>Controllo MP-6 (1) NIST 800-53

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Purificazione dei supporti | Esaminare/Approvare/Tracciare/Documentare/Verificare

**MP-6 (1)** L'organizzazione esamina, approva, traccia, documenta e verifica le azioni di purificazione ed eliminazione dei supporti.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure ha implementato procedure di purificazione dei supporti in conformità con le linee guida in NIST SP 800-88 relativamente agli Standard di classificazione e protezione delle risorse. Tutti i supporti magnetici o elettronici vengono purificati o cancellati in base alle specifiche seguenti di NIST SP 800-88 in conformità con la classificazione delle risorse di Azure. Azure usa le unità di cancellazione dei dati di Extreme Protocol Solutions. Il software EPS supporta i requisiti di NIST SP 800-88 per la cancellazione, la purificazione e l'eliminazione sicura. |


 ### <a name="nist-800-53-control-mp-6-2"></a>Controllo MP-6 (2) NIST 800-53

#### <a name="media-sanitization--equipment-testing"></a>Purificazione dei supporti | Test delle apparecchiature

**MP-6 (2)** L'organizzazione verifica le apparecchiature e le procedure di purificazione [assegnazione: frequenza definita dall'organizzazione] per verificare di ottenere la purificazione desiderata.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure usa unità e processi di cancellazione dei dati per purificare/eliminare i dati in conformità con NIST SP 800-88. Ogni 180 giorni, le operazioni DCS testano le unità e i processi di cancellazione dei dati di Microsoft Azure. Nel test le operazioni DCS verificano che si ottenga la purificazione desiderata tramite un'analisi forense di dischi rigidi testati per verificare che i dati siano stati purificati dalle unità di cancellazione dati |


 ### <a name="nist-800-53-control-mp-6-3"></a>Controllo MP-6 (3) NIST 800-53

#### <a name="media-sanitization--nondestructive-techniques"></a>Purificazione dei supporti | Tecniche non distruttive

**MP-6 (3)** L'organizzazione applica tecniche di purificazione non distruttive ai dispositivi di archiviazione portatili prima di connetterli al sistema informatico nelle seguenti circostanze: [assegnazione: circostanze definite dall'organizzazione che richiedono la purificazione dei dispositivi di archiviazione portatili].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure garantisce che i datacenter di Azure seguano la Procedura di sicurezza per i supporti rimovibili e gli strumenti nel Runbook dei servizi datacenter per evitare che l'ambiente degli enti pubblici venga infettato da malware nei dispositivi di archiviazione portatili. La procedura specifica che prima di usare le unità USB negli ambienti degli enti pubblici è necessario eseguire le operazioni seguenti: <br /> (1) Formattare l'unità USB, quando le unità vengono acquistate dal produttore o dall fornitore, prima di usarle per la prima volta o quando vengono riutilizzate con uno strumento diverso. <br /> (2) Eseguire la scansione di malware di tutte le unità USB da usare in un'area designata dagli enti pubblici, prima di portare l'unità nell'area. <br /> (3) Dopo aver usato un'unità all'interno di un'area designata dagli enti pubblici, formattare l'unità prima di lasciare l'area. <br /> La Procedura di sicurezza per i supporti rimovibili e gli strumenti prevede anche che tutte le chiavette perse, rimosse, rubato o spostate non vengano mai reinserite nei datacenter di Azure, ma che vengano invece catalogate e distrutte. |


 ## <a name="nist-800-53-control-mp-7"></a>Controllo MP-7 NIST 800-53

#### <a name="media-use"></a>Uso dei supporti

**MP-7** L'organizzazione [selezione: limita; impedisce] l'uso di [assegnazione: tipi definiti dall'organizzazione di supporto per i sistemi informatici] in [assegnazione: sistemi informatici o componenti del sistema definiti dall'organizzazione] tramite [assegnazione: misure di sicurezza definite dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure richiede ai proprietari di assegnare le proprie risorse con una classificazione; nessuna risorsa è esente da questo requisito. Nell'ambiente datacenter di Microsoft Azure, con il termine risorse si fa riferimento ai server e ai dispositivi di rete. Altri supporti digitali, ad esempio unità flash/chiavette USB vengono gestite da criteri e procedure specifici che regolano la gestione dei dispositivi. CD/DVD non vengono usati. Nel datacenter non vengono usati i supporti non digitali. L'uso di supporti digitali in ambienti datacenter di Microsoft Azure è monitorato 24 ore al giorno, 7 giorni alla settimana mediante la copertura CCTV. Per altre informazioni, vedere PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>Controllo MP-7 (1) NIST 800-53

#### <a name="media-use--prohibit-use-without-owner"></a>Uso dei supporti | Proibire l'uso senza il proprietario

**MP-7 (1)** l'organizzazione non consente l'uso di dispositivi di archiviazione portatili nei sistemi informatici aziendali quando il proprietario di tali dispositivi non è identificabile.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti supporti controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft limita l'uso dei supporti rimovibile e modificabili ai supporti che sono stati approvati in modo esplicito dalla Gestione datacenter tramite gli strumenti DC e la Procedura per i supporti rimovibile. I supporti di proprietà personale o il cui proprietario non è identificabile non sono consentiti in qualsiasi area di produzione come indicato nel documento Norme e regole per l'uso dei datacenter Microsoft. |
