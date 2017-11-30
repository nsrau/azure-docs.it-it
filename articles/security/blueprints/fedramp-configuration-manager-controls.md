---
title: Automazione della soluzione Azure Blueprint per FedRAMP - Gestione della configurazione
description: Applicazioni Web per FedRAMP - Gestione della configurazione
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: e93aa430b7150f07210f5d1f37e2027d95334a59
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="configuration-management-cm"></a>Gestione della configurazione (CM, Configuration Management)

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti come parte del documento NIST Special Publication 800-53, Revisione 4. Consultare la pubblicazione NIST 800-53 Rev. 4 per informazioni sulle procedure e le indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-cm-1"></a>Controllo CM-1 NIST 800-53

#### <a name="configuration-management-policy-and-procedures"></a>Procedure e criteri di gestione della configurazione

**CM-1** L'organizzazione sviluppa, documentai e distribuisce a [assegnazione: personale o ruoli definiti dall'organizzazione] i criteri di gestione della configurazione in relazione a scopi, ambiti, ruoli, responsabilità, impegni, coordinamento tra le entità aziendali e conformità e le procedure per facilitare l'implementazione dei criteri di gestione della configurazione con i controlli associati ed esamina e aggiorna i criteri di gestione della configurazione correnti [assegnazione: frequenza definita dall'organizzazione], nonché le procedure di gestione della configurazione [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di gestione della configurazione a livello di organizzazione del cliente possono essere sufficienti nell'ambito di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-2"></a>Controllo CM-2 NIST 800-53

#### <a name="baseline-configuration"></a>Configurazione di base

**CM-2** L'organizzazione sviluppa, documenta e gestisce nell'ambito del controllo una configurazione di base corrente del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I modelli di Azure Resource Manager e le relative risorse che costituiscono questa soluzione Azure Blueprint rappresentano una base di tipo "configurazione come codice" per l'architettura distribuita. La soluzione viene fornita tramite GitHub, che può essere usato anche per il controllo della configurazione. La soluzione include una funzione di base di configurazione dello stato desiderato (DSC, Desired State Configuration) per ogni macchina virtuale distribuita. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-1a"></a>Controllo CM-2 (1).a NIST 800-53

#### <a name="baseline-configuration--reviews-and-updates"></a>Configurazione di base | Verifiche e aggiornamenti

**CM-2 (1).a** L'organizzazione esamina e aggiorna la configurazione di base del sistema informativo [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica e dell'aggiornamento della configurazione di base delle risorse distribuite dal cliente stesso (per includere applicazioni, sistemi operativi, database e software). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-1b"></a>Controllo CM-2 (1).b NIST 800-53

#### <a name="baseline-configuration--reviews-and-updates"></a>Configurazione di base | Verifiche e aggiornamenti

**CM-2 (1).b** L'organizzazione esamina e aggiorna la configurazione di base del sistema informativo quando è necessario a causa di [assegnazione: circostanze definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica e dell'aggiornamento della configurazione di base delle risorse distribuite dal cliente stesso, quando necessario. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-1c"></a>Controllo CM-2 (1).c NIST 800-53

#### <a name="baseline-configuration--reviews-and-updates"></a>Configurazione di base | Verifiche e aggiornamenti

**CM-2 (1).c** L'organizzazione esamina e aggiorna la configurazione di base del sistema informativo come parte integrante di aggiornamenti e installazioni dei componenti del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica e dell'aggiornamento della configurazione di base delle risorse distribuite dal cliente stesso, quando necessario. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-2"></a>Controllo CM-2 (2) NIST 800-53

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configurazione di base | Supporto all'automazione per accuratezza/diffusione

**CM-2 (2)** L'organizzazione l'organizzazione usa meccanismi automatizzati per mantenere aggiornata, completa, accurata e immediatamente disponibile una configurazione di base del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I modelli di Azure Resource Manager e le relative risorse che costituiscono questa soluzione Azure Blueprint rappresentano una base di tipo "configurazione come codice" per l'architettura distribuita. La soluzione viene fornita tramite GitHub, che può essere usato anche per il controllo della configurazione. Nel portale di Azure è disponibile uno script di automazione per tutte le risorse distribuite di cui offre anche una rappresentazione sempre aggiornata.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-3"></a>Controllo CM-2 (3) NIST 800-53

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configurazione di base | Conservazione delle configurazioni precedenti

**CM-2 (3)** L'organizzazione mantiene [assegnazione: versioni precedenti delle configurazioni di base del sistema informativo definite dall'organizzazione] per supportare il ripristino dello stato precedente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della conservazione delle versioni precedenti delle configurazioni di base per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-2-7a"></a>Controllo CM-2 (7).a NIST 800-53

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configurazione di base | Configurare sistemi, componenti o dispositivi per aree ad alto rischio

**CM-2 (7).a** L'organizzazione emette [assegnazione: sistemi informativi, componenti di sistema o dispositivi definiti dall'organizzazione] con [assegnazione: configurazioni definite dall'organizzazione] a individui che si spostano in sedi particolarmente a rischio.

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi fisici controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Il contenuto del cliente di Microsoft Azure non viene mai archiviato all'esterno di Microsoft Azure, che si trova fisicamente all'interno degli Stati Uniti continentali. Il personale di Microsoft Azure non si sposta con dispositivi presenti nell'inventario di Microsoft Azure. Per questo motivo, questo controllo non è applicabile a Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>Controllo CM-2 (7).b NIST 800-53

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configurazione di base | Configurare sistemi, componenti o dispositivi per aree ad alto rischio

**CM-2 (7).b** L'organizzazione applica [assegnazione: misure di sicurezza definiti dall'organizzazione] ai dispositivi quando gli individui tornano.

**Responsabilità:** `Not Applicable`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi fisici controllati dal cliente nell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Il contenuto del cliente di Microsoft Azure non viene mai archiviato all'esterno di Microsoft Azure e il personale di Microsoft Azure non si sposta con dispositivi presenti nell'inventario di Microsoft Azure, pertanto questo controllo non è applicabile. |


 ## <a name="nist-800-53-control-cm-3a"></a>Controllo CM-3.a NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.a** L'organizzazione determina i tipi di modifiche al sistema informativo controllate dalla configurazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della definizione dei tipi di modifiche alle risorse distribuite dal cliente stesso (per includere applicazioni, sistemi operativi, database e software) controllate dalla configurazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3b"></a>Controllo CM-3.b NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.b** L'organizzazione esamina le modifiche controllate dalla configurazione proposte al sistema informativo e approva o disapprova tali modifiche tenendo esplicitamente in considerazione le analisi con impatto sulla sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle modifiche controllate dalla configurazione proposte alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3c"></a>Controllo CM-3.c NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.c** L'organizzazione documenta le decisioni di modifica della configurazione associate al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della documentazione delle modifiche controllate dalla configurazione associate alle risorse distribuite dal cliente stesso (vedere CM-03.b). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3d"></a>Controllo CM-3.d NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.d** L'organizzazione implementa le modifiche controllate dalla configurazione approvate per il sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione delle modifiche controllate dalla configurazione approvate nel controllo CM-03.b. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3e"></a>Controllo CM-3.e NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.e** L'organizzazione mantiene record delle modifiche controllate dalla configurazione apportate al sistema informativo per [assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della conservazione di un record delle modifiche controllate dalla configurazione apportate alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3f"></a>Controllo CM-3.f NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.f** L'organizzazione controlla ed esamina le attività associate alle modifiche controllate dalla configurazione al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del controllo e della verifica delle modifiche della configurazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-3g"></a>Controllo CM-3.g NIST 800-53

#### <a name="configuration-change-control"></a>Controllo delle modifiche della configurazione

**CM-3.g** L'organizzazione coordina e supervisiona le attività di controllo delle modifiche della configurazione tramite [assegnazione: elemento di controllo delle modifiche della configurazione definito dall'organizzazione, ad esempio comitato, commissione] che convoca [selezione (una o più opzioni): [assegnazione: frequenza definita dall'organizzazione]; [assegnazione: condizioni di modifica della configurazione definite dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del coordinamento e della supervisione delle attività di controllo delle modifiche delle configurazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1a"></a>Controllo CM-3 (1).a NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).a** L'organizzazione usa meccanismi automatizzati per documentare le modifiche proposte al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di meccanismi automatizzati per documentare le modifiche proposte (vedere CM-03.b). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1b"></a>Controllo CM-3 (1).b NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).b** L'organizzazione usa meccanismi automatizzati per comunicare a [assegnazione: autorità di approvazione definite dall'organizzazione] di modifiche proposte al sistema informativo e per richiedere l'approvazione delle modifiche.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di un meccanismo automatizzato per indirizzare e richiedere l'approvazione per le modifiche proposte alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1c"></a>Controllo CM-3 (1).c NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).c** L'organizzazione usa meccanismi automatizzati per evidenziare modifiche proposte al sistema informativo che non state approvate o disapprovate entro [assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di un meccanismo automatizzato per evidenziare le proposte di modifica non esaminate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1d"></a>Controllo CM-3 (1).d NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).d** L'organizzazione usa meccanismi automatizzati per vietare modifiche al sistema informativo fino a quando le approvazioni stabilite non sono state ricevute.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di meccanismi automatizzati per vietare l'implementazione di modifiche non approvate alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1e"></a>Controllo CM-3 (1).e NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).e** L'organizzazione usa meccanismi automatizzati per documentare tutte le modifiche al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di un meccanismo automatico per documentare tutte le modifiche implementate alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-1f"></a>Controllo CM-3 (1).f NIST 800-53

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controllo delle modifiche della configurazione | Automazione di documenti/notifiche/divieto di modifiche

**CM-3 (1).f** L'organizzazione usa meccanismi automatizzati per comunicare a [assegnazione: personale definito dall'organizzazione] quando le modifiche approvate al sistema informativo sono completate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di un meccanismo automatizzato per inviare notifiche quando le modifiche approvate alle risorse distribuite dal cliente stesso sono completate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-2"></a>Controllo CM-3 (2) NIST 800-53

#### <a name="configuration-change-control--test--validate--document-changes"></a>Controllo delle modifiche della configurazione | Testare/convalidare/documentare le modifiche

**CM-3 (2)** L'organizzazione testa, convalida e documenta le modifiche al sistema informativo prima di implementare le modifiche nel sistema operativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del test, della convalida e della documentazione delle modifiche alle risorse distribuite dal cliente stesso prima dell'implementazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-4"></a>Controllo CM-3 (4) NIST 800-53

#### <a name="configuration-change-control--security-representative"></a>Controllo delle modifiche di configurazione | Rappresentante della sicurezza

**CM-3 (4)** L'organizzazione richiede a un rappresentante della sicurezza delle informazioni di essere un membro di [assegnazione: elemento di controllo delle modifiche della configurazione definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'assegnazione di un rappresentante della sicurezza delle informazioni come membro dell'elemento di controllo delle modifiche definito in CM-03.g. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-3-6"></a>Controllo CM-3 6 NIST 800-53

#### <a name="configuration-change-control--cryptography-management"></a>Controllo delle modifiche di configurazione | Gestione della crittografia

**CM-3 (6)** L'organizzazione verifica che i meccanismi di crittografia usati per implementare [assegnazione: misure di sicurezza definite dall'organizzazione] si trovino nell'ambito della gestione della configurazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica che i meccanismi di crittografia si trovino nell'ambito della gestione della configurazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-4"></a>Controllo CM-4 NIST 800-53

#### <a name="security-impact-analysis"></a>Analisi dell'impatto sulla sicurezza

**CM-4** L'organizzazione analizza le modifiche al sistema informativo per determinare impatti potenziali sulla sicurezza prima dell'implementazione delle modifiche.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'analisi delle modifiche proposte alle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-4-1"></a>Controllo CM-4 (1) NIST 800-53

#### <a name="security-impact-analysis--separate-test-environments"></a>Analisi dell'impatto sulla sicurezza | Separare gli ambienti di test

**CM-4 (1)** L'organizzazione analizza le modifiche al sistema informativo in un ambiente di test separate prima dell'implementazione in un ambiente operativo per verificare eventuali impatti sulla sicurezza a causa di problemi, vulnerabilità, incompatibilità o dolo intenzionale.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'analisi delle modifiche proposte alle risorse distribuite dal cliente stesso in un ambiente di test prima dell'implementazione in un ambiente operativo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-5"></a>Controllo CM-5 NIST 800-53

#### <a name="access-restrictions-for-change"></a>Restrizioni di accesso per le modifiche

**CM-5** L'organizzazione definisce, documenta, approva e applica restrizioni di accesso logiche e fisiche associate alle modifiche al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I privilegi degli account di Azure Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a ruoli specifici e monitorando con attenzione gli utenti che possono visualizzare e controllare le risorse distribuite. I privilegi degli account di Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a gruppi di sicurezza. Tali gruppi controllano le azioni che gli utenti possono eseguire in relazione alla configurazione del sistema operativo. Questi schemi basati sui ruoli possono essere estesi dal cliente per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-5-1"></a>Controllo CM-5 (1) NIST 800-53

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Restrizioni di accesso per le modifiche | Applicazione/controllo dell'accesso automatizzato

**CM-5 (1)** Il sistema informativo applica restrizioni di accesso e supporta il controllo delle azioni necessarie per l'implementazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I privilegi degli account di Azure Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a ruoli specifici e monitorando con attenzione gli utenti che possono visualizzare e controllare le risorse distribuite. I privilegi degli account di Active Directory vengono implementati mediante il controllo degli accessi in base al ruolo, ovvero assegnando utenti a gruppi di sicurezza. Tali gruppi controllano le azioni che gli utenti possono eseguire in relazione alla configurazione del sistema operativo. Tutti gli accessi e i tentativi di accesso vengono controllati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-5-2"></a>Controllo CM-5 (2) NIST 800-53

#### <a name="access-restrictions-for-change--review-system-changes"></a>Restrizioni di accesso per le modifiche | Esaminare le modifiche del sistema

**CM-5 (2)** L'organizzazione esamina le modifiche al sistema informativo [assegnazione: frequenza definita dall'organizzazione] e [assegnazione: circostanze definite dall'organizzazione] per determinare se sono state apportate modifiche non autorizzate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle modifiche alle risorse distribuite dal cliente stesso per determinare se sono state apportate modifiche non autorizzate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-5-3"></a>Controllo CM-5 (3) NIST 800-53

#### <a name="access-restrictions-for-change--signed-components"></a>Restrizioni all'accesso per le modifiche | Componenti firmati

**CM-5 (3)** Il sistema informativo impedisce l'installazione di [assegnazione: componenti software e firmware definiti dall'organizzazione] senza che sia stata eseguita una verifica sulla firma digitale del componente tramite un certificato riconosciuto e approvato dall'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questa soluzione Azure Blueprint implementano Windows AppLocker per specificare quali utenti possono installare e/o eseguire applicazioni specifiche. Tutti gli aggiornamenti del sistema operativo Windows sono inoltre firmati digitalmente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-5-5a"></a>Controllo CM-5 (5).a NIST 800-53

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrizioni sull'accesso per le modifiche | Limite ai privilegi di produzione/operativi

**CM-5 (5).a** L'organizzazione limita i privilegi per modificare i componenti del sistema informativo e le informazioni correlate al sistema in un ambiente operativo o di produzione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dei limiti all'esecuzione di modifiche in ambienti operativi o di produzione distribuiti dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-5-5b"></a>Controllo CM-5 (5).b NIST 800-53

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restrizioni sull'accesso per le modifiche | Limite ai privilegi di produzione/operativi

**CM-5 (5).b** L'organizzazione esamina e valuta nuovamente i privilegi [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'analisi e della nuova valutazione dei privilegi definiti in CM-05(05).a. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-6a"></a>Controllo CM-6.a NIST 800-53

#### <a name="configuration-settings"></a>Impostazioni di configurazione

**CM-6.a** L'organizzazione stabilisce e documenta le impostazioni di configurazione per i prodotti di tecnologia informativa usati nel sistema informativo tramite [assegnazione: elenchi di controllo per la configurazione della sicurezza definiti dall'organizzazione] che riflettono la modalità più restrittiva coerente con i requisiti operativi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint include una funzione di base di configurazione dello stato desiderato (DSC, Desired State Configuration) per ogni macchina virtuale distribuita. Questi script dichiarativi di PowerShell definiscono e configurano le risorse a cui sono applicati. La funzione di base DSC inclusa per le risorse distribuite da questa soluzione può essere estesa dal cliente per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-6b"></a>Controllo CM-6.b NIST 800-53

#### <a name="configuration-settings"></a>Impostazioni di configurazione

**CM-6.b** L'organizzazione implementa le impostazioni di configurazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint include una funzione di base di configurazione dello stato desiderato (DSC, Desired State Configuration) per ogni macchina virtuale distribuita. Le funzioni di base vengono applicate automaticamente alle macchine virtuali durante la distribuzione usando l'estensione di macchina virtuale di script personalizzati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-6c"></a>Controllo CM-6.c NIST 800-53

#### <a name="configuration-settings"></a>Impostazioni di configurazione

**CM-6.c** L'organizzazione identifica, documenta e approva tutte le deviazioni dalle impostazioni di configurazione per [assegnazione: componenti del sistema informativo definiti dall'organizzazione] in base a [assegnazione: requisiti operativi definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'identificazione, della documentazione e dell'approvazione di tutte le deviazioni dalle impostazioni di configurazione stabilite per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-6d"></a>Controllo CM-6.d NIST 800-53

#### <a name="configuration-settings"></a>Impostazioni di configurazione

**CM-6.d** L'organizzazione monitora e controlla le modifiche alle impostazioni di configurazione in base ai criteri e alle procedure dell'organizzazione stessa.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce Automation DSC che allinea le configurazioni di computer con una configurazione specifica definita dall'organizzazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-6-1"></a>Controllo CM-6 (1) NIST 800-53

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Impostazioni di configurazione | Gestione/applicazione/verifica centrale automatizzata

**CM-6 (1)** L'organizzazione usa meccanismi automatizzati per gestire, applicare e verificare in modo centralizzato le impostazioni di configurazione per [assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce Automation DSC per Azure che allinea le configurazioni di computer con una configurazione specifica definita dall'organizzazione ed esegue continuamente il monitoraggio delle modifiche. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-6-2"></a>Controllo CM-6 (2) NIST 800-53

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Impostazioni di configurazione | Rispondere alle modifiche non autorizzate

**CM-6 (2)** L'organizzazione usa [assegnazione: misure di sicurezza definite dall'organizzazione] per rispondere alle modifiche non autorizzate alle [assegnazione: impostazioni di configurazione definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce Automation DSC per Azure Componente di OMS (Operations Management Suite) di Azure, Automation DSC può essere configurato per generare un avviso o per risolvere errori di configurazione, se rilevati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-7a"></a>Controllo CM-7.a NIST 800-53

#### <a name="least-functionality"></a>Funzionalità minima

**CM-7.a** L'organizzazione configura il sistema informativo per distribuire solo funzionalità di base.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le risorse distribuite da questa soluzione Azure Blueprint sono configurate per offrire la funzionalità minima per lo scopo per cui sono implementate. Una funzione di base di configurazione dello stato desiderato (DSC, Desired State Configuration) è inclusa per ogni macchina virtuale distribuita. Questi script dichiarativi di PowerShell definiscono e configurano le risorse a cui sono applicati. La funzione di base DSC inclusa per le risorse distribuite da questa soluzione può essere estesa dal cliente per limitare ulteriormente la funzionalità per soddisfare le esigenze correlate agli obiettivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-7b"></a>Controllo CM-7.b NIST 800-53

#### <a name="least-functionality"></a>Funzionalità minima

**CM-7.b** L'organizzazione vieta o limita l'uso delle funzioni e porte e dei protocolli e/o servizi seguenti: [assegnazione: funzioni, porte, protocolli e/o servizi vietati o limitati definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce il gateway applicazione di Azure e gruppi di sicurezza di rete per limitare l'uso esclusivamente alle porte e ai protocolli necessari. Il gateway applicazione, i gruppi di sicurezza di rete e le funzioni di base DSC per le macchine virtuali possono essere configurati ulteriormente per limitare l'uso di funzioni, porte, protocolli e servizi esclusivamente per la funzionalità desiderata. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-1a"></a>Controllo CM-7 (1).a NIST 800-53

#### <a name="least-functionality--periodic-review"></a>Funzionalità minima | Verifica periodica

**CM-7 (1).a** L'organizzazione esamina il sistema informativo [assegnazione: frequenza definita dall'organizzazione] per identificare funzioni, porte, protocolli e servizi non necessari e/o non sicuri.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica delle risorse distribuite dal cliente stesso (per includere applicazioni, sistemi operativi, database e software) al fine di identificare funzioni, porte, protocolli e servizi non necessari e/o non sicuri. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-1b"></a>Controllo CM-7 (1).b NIST 800-53

#### <a name="least-functionality--periodic-review"></a>Funzionalità minima | Verifica periodica

**CM-7 (1).b** L'organizzazione disabilita [assegnazione: funzioni, porte, protocolli e servizi definiti dall'organizzazione, presenti nel sistema informativo e ritenuti non necessari e/o non sicuri].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della disabilitazione di funzioni, porte, protocolli e servizi ritenuti non necessari o non sicuri. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-2"></a>Controllo CM-7 (2) NIST 800-53

#### <a name="least-functionality--prevent-program-execution"></a>Funzionalità minima | Impedire l'esecuzione di programmi

**CM-7 (2)** Il sistema di informazione impedisce l'esecuzione di programmi in base a [selezione (una o più opzioni): [assegnazione: criteri definiti dall'organizzazione per l'uso e le limitazioni dei programmi software], regole per l'autorizzazione delle condizioni d'uso dei programmi software].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del divieto di esecuzione dei programmi in base ai criteri per l'uso dei programmi software definiti dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-5a"></a>Controllo CM-7 (5).a NIST 800-53

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funzionalità minima | Software autorizzato/elenco elementi consentiti

**CM-7 (5).a** L'organizzazione identifica [assegnazione: programmi software definiti dall'organizzazione autorizzati a essere eseguiti nel sistema informativo].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'identificazione dei programmi software autorizzati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-5b"></a>Controllo CM-7 (5).b NIST 800-53

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funzionalità minima | Software autorizzato/elenco elementi consentiti

**CM-7 (5).b** L'organizzazione usa criteri di negazione completa e di autorizzazione basata su eccezioni per consentire l'esecuzione di programmi software autorizzati nel sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di criteri di negazione completa e di autorizzazione basata su eccezioni per consentire l'esecuzione di programmi software autorizzati nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-7-5c"></a>Controllo CM-7 (5) NIST 800-53

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funzionalità minima | Software autorizzato/elenco elementi consentiti

**CM-7 (5).c** L'organizzazione esamina e aggiorna l'elenco di programmi software autorizzati [assegnazioni: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della verifica e dell'aggiornamento dell'elenco di programmi software autorizzati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-8a"></a>Controllo CM-8.a NIST 800-53

#### <a name="information-system-component-inventory"></a>Inventario dei componenti del sistema informativo

**CM-8.a** L'organizzazione sviluppa e documenta un inventario dei componenti del sistema informativo che riflette in modo accurato il sistema corrente, include tutti i componenti nell'ambito delle autorizzazioni del sistema informativo, dispone del livello di granularità ritenuto necessario ai fini della registrazione e della creazione di report e include [assegnazione: informazioni definite dall'organizzazione ritenute necessarie per catalogare in modo effettivo i componenti del sistema informativo].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. In Azure Resource Manager è presente un elenco sempre aggiornato di risorse distribuite che può essere personalizzato per contrassegnare e raggruppare risorse per la gestione dell'inventario. Alle risorse distribuite da questa soluzione viene assegnato un tag specifico di risorsa che può essere associato al limite del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-8b"></a>Controllo CM-8.b NIST 800-53

#### <a name="information-system-component-inventory"></a>Inventario dei componenti del sistema informativo

**CM-8.b** L'organizzazione esamina e aggiorna l'inventario dei componenti del sistema informativo [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. In Azure Resource Manager è presente un elenco sempre aggiornato di risorse distribuite disponibili per la verifica nel portale di Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-1"></a>Controllo CM-8 (1) NIST 800-53

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventario dei componenti del sistema informativo | Aggiornamenti durante le installazioni/rimozioni

**CM-8 (1)** organizzazione Aggiorna l'inventario dei componenti di sistema informazioni come parte integrante di installazione del componente, rimozioni e gli aggiornamenti del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. Nel pannello delle risorse nel portale di Azure sono elencate tutte le risorse distribuite ed è disponibile pertanto un inventario sempre aggiornato in base alle distribuzione e alla rimozione delle risorse. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-2"></a>Controllo CM-8 (2) NIST 800-53

#### <a name="information-system-component-inventory--automated-maintenance"></a>Inventario dei componenti del sistema informativo | Gestione automatizzata

**CM-8 (2)** L'organizzazione usa meccanismi automatizzati per gestire un inventario aggiornato, completo, preciso e immediatamente disponibile dei componenti del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. Nel pannello delle risorse nel portale di Azure sono elencate tutte le risorse distribuite ed è disponibile pertanto un inventario sempre aggiornato in base alle distribuzione e alla rimozione delle risorse. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-3a"></a>Controllo CM-8 (3).a NIST 800-53

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventario componenti del sistema informativo | Rilevamento automatizzato dei componenti non autorizzati

**CM-8 (3).a** L'organizzazione usa meccanismi automatizzati [assegnazione: frequenza definita dall'organizzazione] per rilevare la presenza di componenti hardware, software e firmware non autorizzati nel sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'uso di meccanismi automatizzati per rilevare la presenza di software non autorizzato nelle risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-3b"></a>Controllo CM-8 (3).b NIST 800-53

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventario componenti del sistema informativo | Rilevamento automatizzato dei componenti non autorizzati

**CM-8 (3).b** L'organizzazione esegue queste azioni quando vengono rilevati componenti non autorizzati: [selezione (una o più opzioni): disabilita l'accesso alla rete da parte di tali componenti, isola i componenti, invia una notifica a [assegnazione: personale o ruoli definiti dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'esecuzione dell'azione da eseguire quando viene rilevato software non autorizzato. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-4"></a>Controllo CM-8 (4) NIST 800-53

#### <a name="information-system-component-inventory--accountability-information"></a>Inventario dei componenti del sistema informativo | Informazioni di contatto

**CM-8 (4)** L'organizzazione include nei dati di inventario dei componenti del sistema informativo uno strumento per identificare in base a [selezione (una o più opzioni): nome, posizione, ruolo] individui responsabili/di contatto per amministrare tali componenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. I tag delle risorse di Azure sono coppie chiave/valore che consentono di classificare le risorse per scopi di responsabilità e/o gestione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-8-5"></a>Controllo CM-8 (5) NIST 800-53

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventario dei componenti del sistema informativo | Account non duplicati dei componenti

**CM-8 (5)** L'organizzazione verifica che tutti i componenti nell'ambito delle autorizzazioni del sistema informativo non vengano duplicati in altri inventari di componenti del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questa soluzione Azure Blueprint distribuisce tutte le risorse in un gruppo di risorse di Azure Resource Manager. In Azure Resource Manager è presente un elenco di risorse distribuite sempre aggiornato. Alle risorse distribuite da questa soluzione viene assegnato un tag specifico di risorsa che può essere associato al limite del sistema. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-9a"></a>Controllo CM-9.a NIST 800-53

#### <a name="configuration-management-plan"></a>Piano di gestione della configurazione

**CM-9.a** L'organizzazione sviluppa, documenta e implementa un piano di gestione della configurazione per il sistema informativo in relazione a ruoli, responsabilità e processi e procedure di gestione della configurazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo, della documentazione e dell'implementazione di un piano di gestione della configurazione per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-9b"></a>Controllo CM-9.b NIST 800-53

#### <a name="configuration-management-plan"></a>Piano di gestione della configurazione

**CM-9.b** L'organizzazione sviluppa, documenta e implementa un piano di gestione della configurazione per il sistema informativo che definisce un processo per identificare gli elementi di configurazione durante il ciclo di vita dello sviluppo del sistema e per gestire la configurazione di tali elementi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo, della documentazione e dell'implementazione di un piano di gestione della configurazione per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-9c"></a>Controllo CM-9.c NIST 800-53

#### <a name="configuration-management-plan"></a>Piano di gestione della configurazione

**CM-9.c** L'organizzazione sviluppa, documenta e implementa un piano di gestione della configurazione per il sistema informativo che definisce gli elementi di configurazione per il sistema informativo e li posiziona nell'ambito della gestione della configurazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo, della documentazione e dell'implementazione di un piano di gestione della configurazione per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-9d"></a>Controllo CM-9.d NIST 800-53

#### <a name="configuration-management-plan"></a>Piano di gestione della configurazione

**CM-9.d** L'organizzazione sviluppa, documenta e implementa un piano di gestione della configurazione per il sistema informativo in grado di offrire protezione da divulgazione e modifiche non autorizzate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dello sviluppo, della documentazione e dell'implementazione di un piano di gestione della configurazione per le risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-10a"></a>Controllo CM-10.a NIST 800-53

#### <a name="software-usage-restrictions"></a>Restrizioni all'uso del software

**CM-10.a** L'organizzazione usa software e la documentazione associata in conformità ai contratti e alle norme sul copyright.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Licenze Windows e SQL Server sono incluse per le risorse distribuite da questa soluzione Azure Blueprint. Si tratta di una funzionalità predefinita di Azure. Le organizzazioni con contratti di licenza software esistenti possono prendere in considerazione la distribuzione di modelli di licenza alternativi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-10b"></a>Controllo CM-10.b NIST 800-53

#### <a name="software-usage-restrictions"></a>Restrizioni all'uso del software

**CM-10.b** L'organizzazione tiene traccia dell'uso del software e della documentazione associata protetta da licenze basate sulla quantità per controllare la copia e la distribuzione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Licenze Windows e SQL Server sono incluse per le risorse distribuite da questa soluzione Azure Blueprint. Non è necessario che l'utente tenga traccia separatamente dell'uso delle licenze. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-10c"></a>Controllo CM-10.c NIST 800-53

#### <a name="software-usage-restrictions"></a>Restrizioni all'uso del software

**CM-10.c** L'organizzazione controlla e documenta l'uso della tecnologia di condivisione file peer-to-peer per garantire che questa funzionalità non venga usata per distribuire, visualizzare, presentare o riprodurre in modo non autorizzato contenuto protetto da copyright.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Non è presente alcuna funzionalità di condivisione file peer-to-peer distribuita da questa soluzione Azure Blueprint. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-10-1"></a>Controllo CM-10 (1) NIST 800-53

#### <a name="software-usage-restrictions--open-source-software"></a>Restrizioni all'uso del software | Software open source

**CM-10 (1)** L'organizzazione stabilisce le restrizioni seguenti all'uso del software open source: [assegnazione: restrizioni definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri di gestione della configurazione a livello di organizzazione del cliente possono interessare le restrizioni all'uso del software open source. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-11a"></a>Controllo CM-11.a NIST 800-53

#### <a name="user-installed-software"></a>Software installato dall'utente

**CM-11.a** L'organizzazione stabilisce [assegnazione: criteri definiti dall'organizzazione] che regolano l'installazione del software da parte degli utenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della definizione di criteri per regolare l'installazione del software da parte degli utenti in risorse distribuite dal cliente stesso. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-11b"></a>Controllo CM-11.b NIST 800-53

#### <a name="user-installed-software"></a>Software installato dall'utente

**CM-11.b** L'organizzazione applica i criteri di installazione del software tramite [assegnazione: metodi definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'applicazione dei criteri di installazione del software. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-cm-11c"></a>Controllo CM-11.c NIST 800-53

#### <a name="user-installed-software"></a>Software installato dall'utente

**CM-11.c** L'organizzazione controlla la conformità ai criteri ogni [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile del controllo della conformità delle risorse distribuite dal cliente stesso ai criteri identificati in CM-11.a. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-cm-11-1"></a>Controllo CM-11 (1) NIST 800-53

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Software installato dall'utente | Avvisi per installazioni non autorizzate

**CM-11 (1)** Il sistema informativo avvisa [assegnazione: personale o ruoli definiti dall'organizzazione] quando viene rilevato software non autorizzato.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'invio di avvisi quando viene rilevata un'installazione di software non autorizzata. |
| **Provider (Microsoft Azure)** | Non applicabile |

