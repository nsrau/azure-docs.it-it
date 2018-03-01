---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP - Manutenzione
description: Automazione di applicazioni Web per FedRAMP - Manutenzione
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="maintenance-ma"></a>Manutenzione (MA)

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-ma-1"></a>Controllo NIST 800-53 MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Procedure e criteri di manutenzione dei sistemi

**MA-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: ruoli o personale definiti dall'organizzazione] criteri di manutenzione dei sistemi relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di manutenzione dei sistemi e dei controlli di manutenzione associati. Esamina e aggiorna inoltre i criteri di manutenzione dei sistemi correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure di manutenzione dei sistemi [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure del cliente di manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-2a"></a>Controllo NIST 800-53 MA-2.a

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.a** L'organizzazione pianifica, esegue, documenta ed esamina i record di manutenzione e riparazione sui componenti dei sistemi informatici in base alle specifiche del produttore o del fornitore e/o ai requisiti dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione controllata. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-2b"></a>Controllo NIST 800-53 MA-2.b

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.b** L'organizzazione approva e monitora tutte le attività di manutenzione, eseguite in loco o in remoto, sia per i casi in cui il lavoro sull'apparecchiatura viene eseguito in loco sia per quelli in cui l'apparecchiatura viene spostata in un altro luogo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione controllata. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-2c"></a>Controllo NIST 800-53 MA-2.c

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.c** L'organizzazione richiede l'approvazione esplicita da parte di [Assegnazione: ruoli o personale definiti dall'organizzazione] della rimozione del sistema informatico o di componenti del sistema dalle strutture dell'organizzazione per interventi di manutenzione o di riparazione fuori sede.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure richiede che per gli asset di proprietà (ad esempio dispositivo di rete o server) che necessitano di trasferimento fuori sede venga fornita l'approvazione esplicita del proprietario dell'asset. |


 ## <a name="nist-800-53-control-ma-2d"></a>Controllo NIST 800-53 MA-2.d

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.d** L'organizzazione bonifica le apparecchiature rimuovendo tutte le informazioni dai supporti associati prima di rimuoverle dalle strutture dell'organizzazione per interventi di manutenzione o di riparazione fuori sede.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Lo standard di protezione degli asset di Microsoft Azure definisce le precauzioni per la gestione degli asset necessarie per il trasferimento degli asset fuori sede. Lo standard di protezione degli asset richiede che gli asset di archiviazione dei dati vengano cancellati/ripuliti in modo coerente con le linee guida NIST SP 800-88 per la bonifica dei supporti prima di lasciare il data center. |


 ## <a name="nist-800-53-control-ma-2e"></a>Controllo NIST 800-53 MA-2.e

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.e** L'organizzazione verifica tutti i controlli di sicurezza potenzialmente interessati per assicurarsi che funzionino correttamente dopo gli interventi di manutenzione o riparazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione controllata. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-2f"></a>Controllo NIST 800-53 MA-2.f

#### <a name="controlled-maintenance"></a>Manutenzione controllata

**MA-2.f** L'organizzazione include [Assegnazione: informazioni relative alla manutenzione definite dall'organizzazione] nei record di manutenzione dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione controllata. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-2-2a"></a>Controllo NIST 800-53 MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Manutenzione controllata | Attività di manutenzione automatizzate

**MA-2 (2).a** L'organizzazione usa meccanismi automatizzati per pianificare, svolgere e documentare interventi di manutenzione e riparazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'automazione delle attività di manutenzione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-2-2b"></a>Controllo NIST 800-53 MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Manutenzione controllata | Attività di manutenzione automatizzate

**MA-2 (2).b** L'organizzazione produce record aggiornati, accurati e completi di tutti gli interventi di manutenzione e riparazione richiesti, pianificati, in corso e completati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'automazione delle attività di manutenzione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-3"></a>Controllo NIST 800-53 MA-3

#### <a name="maintenance-tools"></a>Strumenti di manutenzione

**MA-3** L'organizzazione approva, controlla e monitora gli strumenti di manutenzione dei sistemi informatici.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure usa diversi strumenti per la manutenzione. Gli strumenti di manutenzione software sono approvati, controllati e gestiti tramite il processo di modifica e rilascio di Microsoft Azure. <br /> Il team che si occupa dei servizi per il sito gestisce un inventario degli strumenti di manutenzione approvati per l'uso nel data center (vedere MA-3). Al personale addetto alla manutenzione viene richiesto di usare gli strumenti di manutenzione forniti. Per usare strumenti non forniti dal data center, è necessaria l'approvazione del team di gestione del data center. Gli strumenti manuali fisici (cacciaviti, chiavi inglesi e così via) sono esenti da questo controllo. <br /> Ogni struttura contiene una cassetta di sicurezza fisica sottoposta a restrizioni o una stanza con accesso controllato per lo stoccaggio degli strumenti di manutenzione specializzati, come tester EtherScope Fluke, tester Fibre Channel Fluke, generatori di toni Ethernet e così via. Il team che si occupa dei servizi per il sito esegue controlli di routine dell'inventario per verificare lo stato di tutti gli strumenti. L'accesso alla cassetta di sicurezza o alla stanza di stoccaggio degli strumenti di manutenzione viene registrato nei log dei lettori di badge, disponibili in caso di indagine. |


 ### <a name="nist-800-53-control-ma-3-1"></a>Controllo NIST 800-53 MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Strumenti di manutenzione | Ispezione degli strumenti

**MA-3 (1)** L'organizzazione ispeziona gli strumenti di manutenzione introdotti in una struttura dal personale addetto alla manutenzione per rilevare eventuali modifiche non corrette o non autorizzate.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Il team che si occupa dei servizi per il sito di Microsoft Azure gestisce un inventario degli strumenti di manutenzione approvati per l'uso nel data center (vedere MA-3 per altri dettagli). Al personale addetto alla manutenzione viene richiesto di usare gli strumenti di manutenzione forniti. Per usare strumenti non forniti dal data center, è necessaria l'approvazione del team di gestione del data center. |


 ### <a name="nist-800-53-control-ma-3-2"></a>Controllo NIST 800-53 MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Strumenti di manutenzione | Ispezione dei supporti

**MA-3 (2)** L'organizzazione controlla i supporti contenenti programmi di diagnostica e di test per verificare la presenza di codice dannoso prima di usarli nel sistema informatico.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure vieta l'uso di dispositivi mobili o supporti di archiviazione nell'ambiente di produzione dei data center di Microsoft Azure senza l'approvazione del team di gestione del data center. L'uso di supporti personali nell'ambiente di produzione dei data center di Microsoft Azure è proibito. <br /> Microsoft Azure ha implementato un processo di ispezione dei portatili prima dell'uso nell'ambiente di produzione dei data center di Microsoft Azure. I responsabili della sicurezza ricevono una formazione in merito a come gestire il personale che usa computer portatili nell'ambiente di produzione, per verificare che i computer siano stati sottoposti a un'ispezione e l'abbiano superata. |


 ### <a name="nist-800-53-control-ma-3-3"></a>Controllo NIST 800-53 MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Strumenti di manutenzione | Prevenzione della rimozione non autorizzata

**MA-3 (3)** L'organizzazione impedisce la rimozione non autorizzata delle apparecchiature di manutenzione contenenti informazioni dell'organizzazione, verificando che nelle apparecchiature non siano presenti tali informazioni, bonificando o distruggendo le apparecchiature, conservando le apparecchiature all'interno della struttura o ottenendo un'esenzione da [Assegnazione: ruoli o personale definiti dall'organizzazione] che autorizzi in modo esplicito la rimozione dell'apparecchiatura dalla struttura.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure usa strumenti di manutenzione specifici del data center che vengono conservati all'interno della struttura e non vengono rimossi. Ogni struttura contiene una cassetta di sicurezza fisica sottoposta a restrizioni o una stanza per lo stoccaggio degli strumenti di manutenzione, come tester EtherScope Fluke, tester Fibre Channel Fluke, generatori di toni Ethernet e così via. L'accesso alla cassetta di sicurezza o alla stanza per lo stoccaggio viene controllato in base a quanto stabilito dalla specifica DCAT, per impedire l'accesso non autorizzato agli strumenti di manutenzione. <br /> Le informazioni dell'organizzazione sono protette durante gli interventi di manutenzione in base a quanto stabilito dai controlli in MA-4. Per accedere alle informazioni dell'organizzazione, l'utente deve avere autenticatori e account con privilegi. |


 ## <a name="nist-800-53-control-ma-4a"></a>Controllo NIST 800-53 MA-4.a

#### <a name="nonlocal-maintenance"></a>Manutenzione non locale

**MA-4.a** L'organizzazione approva e monitora le attività di diagnostica e di manutenzione non locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione non locale sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-4b"></a>Controllo NIST 800-53 MA-4.b

#### <a name="nonlocal-maintenance"></a>Manutenzione non locale

**MA-4.b** L'organizzazione consente l'uso di strumenti di manutenzione e diagnostica non locali solo nella misura prevista dai criteri dell'organizzazione e in base a quanto documentato nel piano di sicurezza per il sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione non locale sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-4c"></a>Controllo NIST 800-53 MA-4.c

#### <a name="nonlocal-maintenance"></a>Manutenzione non locale

**MA-4.c** L'organizzazione usa autenticatori affidabili per l'avvio di sessioni di diagnostica e di manutenzione non locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione non locale sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-4d"></a>Controllo NIST 800-53 MA-4.d

#### <a name="nonlocal-maintenance"></a>Manutenzione non locale

**MA-4.d** L'organizzazione documenta le attività di diagnostica e di manutenzione non locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione non locale sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-4e"></a>Controllo NIST 800-53 MA-4.e

#### <a name="nonlocal-maintenance"></a>Manutenzione non locale

**MA-4.e** L'organizzazione termina le connessioni di rete e di sessione al completamento della manutenzione non locale.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della manutenzione non locale sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-4-2"></a>Controllo NIST 800-53 MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Manutenzione non locale | Documentazione della manutenzione non locale

**MA-4 (2)** L'organizzazione documenta nel piano di sicurezza per il sistema informatico i criteri e le procedure per l'avvio e l'uso di connessioni di diagnostica e manutenzione non locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di documentare nel piano di sicurezza la manutenzione non locale per i sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-4-3"></a>Controllo NIST 800-53 MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Manutenzione non locale | Sicurezza comparabile/bonifica

**MA-4 (3)** L'organizzazione richiede che i servizi di diagnostica e di manutenzione non locali vengano eseguiti da un sistema informatico che implementa funzionalità di sicurezza comparabili a quelle implementate nel sistema sottoposto a manutenzione oppure rimuove il componente da sottoporre a manutenzione dal sistema informatico prima dei servizi di diagnostica o manutenzione non locali, bonifica il componente (per quanto riguarda le informazioni dell'organizzazione) prima della rimozione dalle strutture dell'organizzazione e, dopo l'esecuzione del servizio, ispeziona e bonifica il componente (per quanto riguarda il software potenzialmente dannoso) prima di riconnetterlo al sistema informatico.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'esecuzione di tutti gli interventi di manutenzione non locali sui sistemi operativi distribuiti dal cliente stesso da un sistema informatico con sicurezza comparabile. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-4-6"></a>Controllo NIST 800-53 MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Manutenzione non locale | Protezione tramite crittografia

**MA-4 (6)** Il sistema informatico implementa meccanismi di crittografia per proteggere l'integrità e la riservatezza delle comunicazioni di diagnostica e di manutenzione non locali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione di meccanismi di crittografia quando vengono eseguiti interventi di manutenzione e di diagnostica non locali sui sistemi operativi distribuiti dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-5a"></a>Controllo NIST 800-53 MA-5.a

#### <a name="maintenance-personnel"></a>Personale addetto alla manutenzione

**MA-5.a** L'organizzazione stabilisce un processo per l'autorizzazione del personale addetto alla manutenzione e gestisce un elenco delle organizzazioni o degli addetti alla manutenzione autorizzati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure del cliente di autorizzazione e scorta del personale addetto alla manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-5b"></a>Controllo NIST 800-53 MA-5.b

#### <a name="maintenance-personnel"></a>Personale addetto alla manutenzione

**MA-5.b** L'organizzazione garantisce che il personale non scortato che svolge interventi di manutenzione sul sistema informatico ha le autorizzazioni di accesso richieste.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure del cliente di autorizzazione e scorta del personale addetto alla manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-5c"></a>Controllo NIST 800-53 MA-5.c

#### <a name="maintenance-personnel"></a>Personale addetto alla manutenzione

**MA-5.c** L'organizzazione designa addetti dell'organizzazione con le autorizzazioni di accesso richieste e la competenza tecnica necessaria per supervisionare le attività di manutenzione svolte da personale che non ha le autorizzazioni di accesso richieste.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure del cliente di autorizzazione e scorta del personale addetto alla manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-5-1a"></a>Controllo NIST 800-53 MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personale addetto alla manutenzione | Individui senza autorizzazioni di accesso appropriate

**MA-5 (1).a** L'organizzazione implementa le procedure per l'uso di addetti alla manutenzione senza nulla osta di sicurezza appropriati o che non sono cittadini degli Stati Uniti, che includono i requisiti seguenti: gli addetti alla manutenzione che non hanno le autorizzazioni di accesso, le approvazioni di accesso formali o i nulla osta necessari vengono scortati e controllati durante l'esecuzione delle attività di manutenzione e di diagnostica sul sistema informatico da parte di personale dell'organizzazione approvato, completamente autorizzato, con autorizzazioni di accesso appropriate e qualificato dal punto di vista tecnico. Prima dell'inizio delle attività di manutenzione o di diagnostica da parte di personale che non ha le autorizzazioni di accesso, le approvazioni di accesso formali o i nulla osta necessari, tutti i componenti di archiviazione delle informazioni di tipo volatile nel sistema informatico vengono bonificati e tutti i componenti di archiviazione all'interno del sistema di informazioni e tutti i supporti di archiviazione di tipo non volatile vengono rimossi o scollegati fisicamente dal sistema e protetti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure del cliente di autorizzazione e scorta del personale addetto alla manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ma-5-1b"></a>Controllo NIST 800-53 MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Personale addetto alla manutenzione | Individui senza autorizzazioni di accesso appropriate

**MA-5 (1).b** L'organizzazione sviluppa e implementa misure di sicurezza alternative nel caso in cui un componente del sistema informatico non possa essere bonificato, rimosso o disconnesso dal sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure del cliente di autorizzazione e scorta del personale addetto alla manutenzione dei sistemi a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ma-6"></a>Controllo NIST 800-53 MA-6

#### <a name="timely-maintenance"></a>Manutenzione tempestiva

**MA-6** L'organizzazione ottiene supporto per la manutenzione e/o parti di ricambio per [Assegnazione: componenti del sistema informatico definiti dall'organizzazione] entro [Assegnazione: intervallo di tempo definito dall'organizzazione] dal momento dell'errore.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | I data center di Microsoft Azure dispongono di personale addetto alla manutenzione residente per il supporto dei sistemi di infrastruttura del data center critici, oltre che delle operazioni del data center. I team hanno identificato componenti di sistema di sicurezza e tecnologici critici per i quali hanno a disposizione parti di ricambio in loco. I sistemi critici sono designati in configurazioni N+1 e i servizi sono progettati per offrire resilienza. Ciò consente al team di gestione del data center di soddisfare gli obiettivi di ripristino in caso di interruzione di un servizio o di una situazione che richieda un piano di emergenza. Per i servizi critici del sistema informatico viene effettuato il provisioning da più di un data center, per evitare l'interruzione del servizio a causa di un evento imprevisto in uno dei data center. Le applicazioni dei clienti sono responsabili della distribuzione in più data center per fornire ridondanza e resilienza. |
