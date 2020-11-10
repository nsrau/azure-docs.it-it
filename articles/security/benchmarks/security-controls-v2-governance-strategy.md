---
title: Benchmark di sicurezza di Azure V2-governance e strategia
description: Governance e strategia di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ae0930e0845e8e8bd6dc4571dc3e8e27491a7be6
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408793"
---
# <a name="security-control-v2-governance-and-strategy"></a>Controllo di sicurezza V2: governance e strategia

Governance e strategia fornisce linee guida per garantire una strategia di sicurezza coerente e un approccio di governance documentato per guidare e sostenere la garanzia di sicurezza, tra cui la definizione di ruoli e responsabilità per le diverse funzioni di sicurezza cloud, la strategia tecnica unificata e i criteri e gli standard di supporto.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definire la strategia di gestione delle risorse e della protezione dei dati

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire le priorità di individuazione, valutazione, protezione e monitoraggio dei sistemi e dei dati cruciali per l'azienda. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Standard di classificazione dei dati in base ai rischi aziendali

- Visibilità dell'organizzazione della sicurezza nei rischi e nell'inventario degli asset 

- Approvazione dell'organizzazione della sicurezza dei servizi di Azure per l'uso 

- Sicurezza degli asset attraverso il ciclo di vita

- Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati aziendali

- Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

- Requisiti di crittografia dei dati per i casi d'uso in transito e a riposo

- Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure-archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%252fsecurity%252fcompass%252fbreadcrumb%252ftoc.json&toc=%252fsecurity%252fcompass%252ftoc.json)

- [Nozioni fondamentali sulla sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../fundamentals/encryption-overview.md)

- [Framework di adozione cloud: procedure consigliate per la sicurezza e la crittografia dei dati di Azure](../fundamentals/data-encryption-best-practices.md?amp;bc=%252fazure%252fcloud-adoption-framework%252f_bread%252ftoc.json&toc=%252fazure%252fcloud-adoption-framework%252ftoc.json)

- [Benchmark di sicurezza di Azure-gestione delle risorse](security-controls-v2-asset-management.md)

- [Benchmark di sicurezza di Azure-protezione dei dati](security-controls-v2-data-protection.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definire la strategia di segmentazione aziendale

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-2 | 4, 9, 16 | CA, CA, SC |

Definire una strategia a livello aziendale per segmentare l'accesso alle risorse tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allinea la segmentazione della rete con la strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definire la strategia di gestione delle attitudini per la sicurezza

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Misura e attenua continuamente i rischi per le singole risorse e per l'ambiente in cui sono ospitate. Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Benchmark di sicurezza di Azure-gestione di posture e vulnerabilità](security-controls-v2-posture-vulnerability-management.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: allinea ruoli organizzazione, responsabilità e responsabilità

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-4 | N/D | PL, PM |

Assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità per fornire una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per proteggere il cloud.

- [Procedura consigliata per la sicurezza di Azure 1 – people: educare i team al percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura consigliata per la sicurezza di Azure 2-persone: istruire i team sulla tecnologia di sicurezza cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura consigliata per la sicurezza di Azure 3-processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: definire la strategia di sicurezza di rete

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Gestione centralizzata della rete e responsabilità della sicurezza

- Modello di segmentazione della rete virtuale allineato con la strategia di segmentazione aziendale

- Strategia di monitoraggio e aggiornamento in diversi scenari di minaccia e attacco

- Internet Edge e strategia di ingresso e uscita

- Strategia di interconnettività locale e cloud ibrido

- Elementi di sicurezza di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark di sicurezza di Azure-sicurezza di rete](security-controls-v2-network-security.md)

- [Panoramica della sicurezza di rete di Azure](../fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definire la strategia di identità e di accesso con privilegi

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Definire un approccio di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Sistema di identità e autenticazione centralizzato e relativa interconnettività con altri sistemi di identità interni ed esterni

- Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

- Protezione di utenti con privilegi elevati

- Monitoraggio e gestione delle attività degli utenti anomalie  

- Verifica dell'identità e dell'accesso dell'utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Benchmark di sicurezza di Azure-gestione delle identità](security-controls-v2-identity-management.md)

- [Benchmark di sicurezza di Azure-accesso con privilegi](security-controls-v2-privileged-access.md)

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../fundamentals/identity-management-overview.md)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definire la strategia di registrazione e di risposta alle minacce

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di qualità elevata ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (secops) 

- Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

- Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

- Visibilità centralizzata delle informazioni sulla correlazione e sulle minacce, uso di SIEM, funzionalità native di Azure e altre origini 

- Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

- Uso di piattaforme di terze parti e native di Azure per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione e eliminazione degli attacchi

- Processi per la gestione degli eventi imprevisti e delle attività post-evento, ad esempio le lezioni apprese e la conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:
- [Benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](security-controls-v2-logging-threat-detection.md)

- [Benchmark di sicurezza di Azure-risposta agli eventi imprevisti](security-controls-v2-incident-response.md)

- [Procedura consigliata per la sicurezza di Azure 4-processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guida alla decisione sul Framework di adozione di Azure, la registrazione e la creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: definire la strategia di backup e ripristino

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| GS-8 | 10 | CP |

Definire una strategia di backup e ripristino di Azure per l'organizzazione. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Obiettivo del tempo di ripristino (RTO) e definizioni di obiettivi del punto di ripristino (RPO) in base agli obiettivi di resilienza aziendale

- Progettazione della ridondanza nella configurazione delle applicazioni e dell'infrastruttura

- Protezione del backup tramite il controllo di accesso e la crittografia dei dati

Per altre informazioni, vedere i riferimenti seguenti:
- [Benchmark di sicurezza di Azure: backup e ripristino](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework-backup e ripristino di emergenza per le applicazioni Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Framework di adozione di Azure: continuità aziendale e ripristino di emergenza](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilità** : Customer

**Stakeholder** per la sicurezza dei clienti ( [altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Tutte le parti interessate](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)