---
title: Panoramica del benchmark di sicurezza di Azure V2
description: Panoramica di Azure Security benchmark V2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb6813875858ab6cc438df5abb78a75391adfeac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280658"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Panoramica del benchmark di sicurezza di Azure (v2)

Il benchmark di sicurezza di Azure (ASB) fornisce procedure consigliate e consigli per migliorare la sicurezza dei carichi di lavoro, dei dati e dei servizi in Azure.

Questo benchmark è parte di una serie di indicazioni di sicurezza olistiche che includono anche:

- **Framework di adozione del cloud** : linee guida sulla sicurezza, tra cui [strategie](/azure/cloud-adoption-framework/strategy/define-security-strategy), [ruoli e responsabilità](/azure/cloud-adoption-framework/organize/cloud-security), procedure consigliate per la [sicurezza di Azure Top 10](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)e [implementazione di riferimento](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Framework ben architettato di Azure** : informazioni aggiuntive sulla [protezione dei carichi di lavoro](https://docs.microsoft.com/assessments/?mode=pre-assessment&session=local) in Azure.
- Procedure consigliate per la **sicurezza Microsoft** : [raccomandazioni](/security/compass/microsoft-security-compass-introduction) con esempi in Azure.

 Il benchmark di sicurezza di Azure è incentrato sulle aree di controllo incentrate sul cloud. Questi controlli sono coerenti con i benchmark di sicurezza noti, ad esempio quelli descritti da Center for Internet Security (CIS) Controls versione 7,1 e National Institute of Standards and Technology (NIST) SP800-53.
I controlli seguenti sono inclusi nel benchmark di sicurezza di Azure:

| Domini di controllo ASB | Descrizione 
|--|--|
| [Sicurezza di rete (NS)](security-controls-v2-network-security.md) | La sicurezza di rete copre i controlli per proteggere e proteggere le reti di Azure, tra cui la protezione delle reti virtuali, la creazione di connessioni private, la prevenzione e la mitigazione degli attacchi esterni e la protezione del DNS. |
| [Gestione delle identità (IM)](security-controls-v2-identity-management.md) | Gestione delle identità copre i controlli per stabilire un'identità e controlli di accesso sicuri usando Azure Active Directory, tra cui l'uso di Single Sign-On, le autenticazioni complesse, le identità gestite (e i principi di servizio) per le applicazioni, l'accesso condizionale e il monitoraggio delle anomalie dell'account. |
| [Accesso con privilegi (PA)](security-controls-v2-privileged-access.md) | Accesso con privilegi copre i controlli per proteggere l'accesso con privilegi al tenant e alle risorse di Azure, inclusa una gamma di controlli per proteggere il modello amministrativo, gli account amministrativi e le workstation con accesso con privilegi da rischi intenzionali e involontari. |
| [Protezione dei dati (DP)](security-controls-v2-data-protection.md) | La protezione dei dati copre il controllo della protezione dei dati inattivi, in transito e tramite meccanismi di accesso autorizzati, tra cui l'individuazione, la classificazione, la protezione e il monitoraggio di asset di dati riservati usando il controllo di accesso, la crittografia e la registrazione in Azure. |
| [Gestione delle risorse](security-controls-v2-asset-management.md) | Gestione asset copre i controlli per garantire la visibilità e la governance della sicurezza sulle risorse di Azure, incluse le raccomandazioni sulle autorizzazioni per il personale di sicurezza, l'accesso alla sicurezza per l'inventario delle risorse e la gestione delle approvazioni per servizi e risorse (inventario, traccia e corrette). |
| [Registrazione e rilevamento delle minacce (LT)](security-controls-v2-logging-threat-detection.md) | Registrazione e rilevamento delle minacce copre i controlli per il rilevamento di minacce in Azure e l'abilitazione, la raccolta e l'archiviazione dei log di controllo per i servizi di Azure, tra cui l'abilitazione dei processi di rilevamento, analisi e correzione con i controlli per generare avvisi di alta qualità con il rilevamento delle minacce nativo nei servizi di Azure. include anche la raccolta di log con monitoraggio di Azure, la centralizzazione dell'analisi della sicurezza con Sentinel di Azure, la sincronizzazione dell'ora e la conservazione dei log. |
| [Risposta agli eventi imprevisti](security-controls-v2-incident-response.md) | La risposta agli eventi imprevisti riguarda i controlli in ciclo di vita delle risposte agli eventi imprevisti, attività di preparazione, rilevamento e analisi, contenimento e attività post-evento imprevisto, incluso l'uso di servizi di Azure come il Centro sicurezza di Azure e Sentinel per automatizzare il processo di risposta |
| [Gestione delle posture e delle vulnerabilità (PV)](security-controls-v2-posture-vulnerability-management.md) | La gestione di posture e vulnerabilità è incentrata sui controlli per la valutazione e il miglioramento del comportamento di sicurezza di Azure, tra cui analisi delle vulnerabilità, test di penetrazione e correzione, nonché rilevamento della configurazione della sicurezza, creazione di report e correzione nelle risorse di Azure. |
| [Sicurezza endpoint](security-controls-v2-endpoint-security.md) | Endpoint Security copre i controlli nel rilevamento e nella risposta degli endpoint, incluso l'uso di endpoint Detection and Response (EDR) e del servizio anti-malware per gli endpoint negli ambienti di Azure. |
| [Backup e ripristino (BR)](security-controls-v2-backup-recovery.md) | Il servizio di backup e ripristino copre i controlli per garantire che i backup dei dati e della configurazione nei diversi livelli di servizio vengano eseguiti, convalidati e protetti. |
| [Governance e strategia (GS)](security-controls-v2-governance-strategy.md) | Governance e strategia fornisce linee guida per garantire una strategia di sicurezza coerente e un approccio di governance documentato per guidare e sostenere la garanzia di sicurezza, tra cui la definizione di ruoli e responsabilità per le diverse funzioni di sicurezza cloud, la strategia tecnica unificata e i criteri e gli standard di supporto. |

È anche possibile scaricare il benchmark di sicurezza di Azure in [formato foglio di calcolo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="azure-security-benchmark-recommendations"></a>Raccomandazioni sui benchmark di sicurezza di Azure

Ogni raccomandazione include le informazioni seguenti:

- **ID di Azure**: ID del benchmark di sicurezza di Azure corrispondente all'indicazione.
- **Controlli CIS v 7.1 ID**: controlli CIS v 7.1 (s) che corrispondono a questa raccomandazione.
- **NIST SP800-53 R4 ID**: i controlli NIST SP800-53 R4 (moderato) che corrispondono a questa raccomandazione.
- **Dettagli**: la logica per la raccomandazione e i collegamenti alle indicazioni su come implementarla. Se la raccomandazione è supportata dal centro sicurezza di Azure, verranno elencate anche le informazioni.
- **Responsabilità**: se il cliente, il provider di servizi o entrambi sono responsabili dell'implementazione di questa raccomandazione. Le responsabilità di sicurezza sono condivise nel cloud pubblico. Alcuni controlli di sicurezza sono disponibili solo per il provider di servizi cloud e pertanto il provider è responsabile di risolverli. Queste sono osservazioni generali: per alcuni singoli servizi, la responsabilità sarà diversa da quella elencata nel benchmark di sicurezza di Azure. Queste differenze sono descritte nelle raccomandazioni di base per il singolo servizio.
- **Stakeholder**per la sicurezza dei clienti: [le funzioni di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) dell'organizzazione del cliente che possono essere responsabili, responsabili o consultate per il rispettivo controllo. Può essere diverso dall'organizzazione all'organizzazione, a seconda della struttura dell'organizzazione di sicurezza della società e dei ruoli e delle responsabilità configurati in relazione alla sicurezza di Azure.

> [!NOTE]
> I mapping dei controlli tra ASB e i benchmark di settore (ad esempio NIST e CIS) indicano solo che una specifica funzionalità di Azure può essere usata per soddisfare completamente o parzialmente un requisito di controllo definito in NIST o CIS. È necessario tenere presente che tale implementazione non si traduce necessariamente nella conformità completa del controllo corrispondente in CIS o NIST.

Siamo lieti di ricevere commenti e suggerimenti dettagliati e partecipare attivamente allo sforzo di benchmark di sicurezza di Azure. Se si vuole fornire l'input diretto del team di benchmark di sicurezza di Azure, compilare il modulo all'indirizzo https://aka.ms/AzSecBenchmark


- Vedere il primo controllo di sicurezza: [sicurezza di rete](security-control-network-security.md)
- Leggi l' [Introduzione al benchmark di sicurezza di Azure](introduction.md)
- Scaricare il [foglio di calcolo di Excel del benchmark di sicurezza di Azure V2](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)