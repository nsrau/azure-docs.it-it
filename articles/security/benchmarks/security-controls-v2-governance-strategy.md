---
title: Benchmark di sicurezza di Azure V2-governance e strategia
description: Governance e strategia di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059273"
---
# <a name="security-control-governance-and-strategy"></a>Controllo di sicurezza: governance e strategia

Il servizio di backup e ripristino copre i controlli per garantire che i backup dei dati e della configurazione nei diversi livelli di servizio vengano eseguiti, convalidati e protetti.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: definire la strategia di gestione e di protezione degli asset

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire le priorità di individuazione, valutazione, protezione e monitoraggio dei sistemi e dei dati cruciali per l'azienda. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza nei rischi e nell'inventario degli asset 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure per l'uso 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati aziendali

-   Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

-   Requisiti di crittografia dei dati per i casi d'uso in transito e a riposo

-   Standard crittografici appropriati

Nota: l'approccio per la gestione e la protezione delle risorse per il cloud e l'ambiente locale può variare in base a più fattori, ad esempio il servizio dell'applicazione o il modello di hosting, i rischi aziendali e i requisiti di conformità. 

- [Raccomandazione sull'architettura della sicurezza di Azure-archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Nozioni fondamentali sulla sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../fundamentals/encryption-overview.md)

- [Framework di adozione cloud: procedure consigliate per la sicurezza e la crittografia dei dati di Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark di sicurezza di Azure-gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark di sicurezza di Azure-protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: definire la strategia di gestione delle posture di sicurezza

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Misura e attenua continuamente i rischi per le singole risorse e per l'ambiente in cui sono ospitate. Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Benchmark di sicurezza di Azure-gestione di posture e vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: allineare ruoli organizzazione, responsabilità e responsabilità

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-3 | N/D | PL, PM |

Assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità per fornire una chiara responsabilità per le decisioni relative alla sicurezza, la formazione sul modello di responsabilità condivisa e la formazione tecnica per la sicurezza nel cloud. 

- [Procedura consigliata per la sicurezza di Azure 1 – people: educare i team al percorso di sicurezza del cloud](https://aka.ms/AzSec1)

- [Procedura consigliata per la sicurezza di Azure 2-persone: istruire i team sulla tecnologia di sicurezza cloud](https://aka.ms/AzSec2)

- [Procedura consigliata per la sicurezza di Azure 3-processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](https://aka.ms/AzSec3)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: definire la strategia di sicurezza di rete

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-4 | 9 | CA, SC |

Definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato con la strategia di segmentazione aziendale

-   Strategia di monitoraggio e aggiornamento in diversi scenari di minaccia e attacco

-   Internet Edge e strategia di ingresso e uscita

-   Strategia di interconnettività locale e cloud ibrido

-   Elementi di sicurezza di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Nota: l'approccio alla sicurezza di rete per il cloud e l'ambiente locale può variare in base a più fattori, ad esempio il modello del servizio applicazioni, l'esposizione alle minacce e la configurazione della rete ibrida.

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](https://aka.ms/AzSec11)

- [Benchmark di sicurezza di Azure-sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: definire la strategia di identità e di accesso con privilegi

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Definire un approccio di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione di utenti con privilegi elevati

-   Monitoraggio e gestione delle attività degli utenti anomalie  

-   Verifica dell'identità e dell'accesso dell'utente e processo di riconciliazione

Nota: l'identità e l'approccio di accesso con privilegi per il cloud e l'ambiente locale possono essere diversi a seconda di diversi fattori, ad esempio il percorso di accesso ai dati e alle applicazioni, il modello di servizio e la strategia di accesso cliente/partner.

- [Benchmark di sicurezza di Azure-gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark di sicurezza di Azure-accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](https://aka.ms/AzSec11)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../fundamentals/identity-management-overview.md) 

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: definire la strategia di registrazione e di risposta alle minacce

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di qualità elevata ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (secops) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata delle informazioni sulla correlazione e sulle minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme di terze parti e native di Azure per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione e eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività post-evento, ad esempio le lezioni apprese e la conservazione delle prove

Nota: l'approccio di registrazione e rilevamento delle minacce per il cloud e l'ambiente locale può essere diverso a seconda di diversi fattori, ad esempio i requisiti di conformità, il panorama delle minacce e la funzionalità di rilevamento e correzione. 

- [Benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark di sicurezza di Azure-risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Procedura consigliata per la sicurezza di Azure 4-processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](https://aka.ms/AzSec11)

- [Guida alla decisione sul Framework di adozione di Azure, la registrazione e la creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: definire la strategia di backup e ripristino

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-7 | 10 | CP |

Definire una strategia di backup e ripristino di Azure per l'organizzazione. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Obiettivo del tempo di ripristino (RTO) e definizioni di obiettivi del punto di ripristino (RPO) in base agli obiettivi di resilienza aziendale

-   Progettazione della ridondanza nella configurazione delle applicazioni e dell'infrastruttura

-   Protezione del backup tramite il controllo di accesso e la crittografia dei dati

Nota: l'approccio di backup e ripristino per il cloud e l'ambiente locale può essere diverso a seconda dei fattori più importanti, ad esempio la ridondanza dell'infrastruttura, il modello di servizio/hosting dell'applicazione e i requisiti di conformità.

- [Benchmark di sicurezza di Azure: backup e ripristino](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Framework Azure well-Architecture: backup e ripristino di emergenza per le applicazioni Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Framework di adozione di Azure: continuità aziendale e ripristino di emergenza](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

