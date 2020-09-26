---
title: Benchmark di sicurezza di Azure V2-protezione dei dati
description: Protezione dei dati del benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ab42acbc07072f48d28b3adb5bc4c65672f257bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314318"
---
# <a name="security-control-v2-data-protection"></a>Controllo di sicurezza V2: protezione dei dati

La protezione dei dati copre il controllo della protezione dei dati inattivi, in transito e tramite meccanismi di accesso autorizzati. Sono inclusi l'individuazione, la classificazione, la protezione e il monitoraggio di asset di dati sensibili tramite il controllo di accesso, la crittografia e la registrazione in Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: individuazione, classificazione e assegnazione di etichette ai dati sensibili

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Individuare, classificare e assegnare etichette ai dati sensibili per poter progettare i controlli appropriati per garantire che le informazioni riservate vengano archiviate, elaborate e trasmesse in modo sicuro dai sistemi tecnologici dell'organizzazione. 

Usare Azure Information Protection (e lo strumento di analisi associato) per informazioni riservate nei documenti di Office in Azure, in locale, in Office 365 e in altre posizioni. 

È possibile usare Information Protection SQL di Azure per semplificare la classificazione e l'assegnazione di etichette alle informazioni archiviate nei database SQL di Azure.

- [Contrassegnare le informazioni riservate usando Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Come implementare l'individuazione dati SQL di Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Sicurezza dei dati](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: proteggere i dati sensibili

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Proteggi i dati sensibili limitando l'accesso usando il controllo degli accessi in base al ruolo di Azure (RBAC), i controlli di accesso basati sulla rete e controlli specifici nei servizi di Azure, ad esempio la crittografia in SQL e altri database. 

Per garantire un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere allineati alla strategia di segmentazione aziendale. La strategia di segmentazione aziendale deve anche essere informata dalla posizione dei dati e dei sistemi sensibili o aziendali critici.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../fundamentals/protection-customer-data.md)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicurezza dei dati](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitoraggio per il trasferimento non autorizzato di dati sensibili

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Monitorare il trasferimento non autorizzato dei dati a posizioni esterne a visibilità e controllo aziendali. Ciò comporta in genere il monitoraggio di attività anomale (trasferimenti grandi o insoliti) che potrebbero indicare dati non autorizzati exfiltration. 

Azure Storage Advanced Threat Protection (ATP) e Azure SQL ATP possono avvertire un trasferimento anomalo di informazioni che potrebbero indicare trasferimenti non autorizzati di informazioni riservate. 

Azure Information Protection (AIP) fornisce funzionalità di monitoraggio per le informazioni classificate e con etichetta. 

Se necessario per la conformità della prevenzione della perdita dei dati (DLP), è possibile usare una soluzione DLP basata su host per applicare controlli di detective e/o preventivo per impedire i dati exfiltration.

- [Abilitare Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Abilitare Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Per completare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati. 

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.  

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure. 

- [Informazioni sulla crittografia in transito con Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem)

- [Crittografia doppia per i dati di Azure in transito](../fundamentals/double-encryption.md#data-in-transit)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicurezza dei dati](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: crittografare i dati sensibili inattivi

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

Per completare i controlli di accesso, i dati inattivi devono essere protetti da attacchi fuori banda, ad esempio l'accesso alla risorsa di archiviazione sottostante, usando la crittografia. Ciò consente di garantire che gli utenti malintenzionati non possano leggere o modificare facilmente i dati. 

Per impostazione predefinita, Azure fornisce la crittografia per i dati inattivi. Per i dati altamente sensibili, sono disponibili opzioni per implementare la crittografia aggiuntiva dei dati inattivi in tutte le risorse di Azure, se disponibili. Azure gestisce le chiavi di crittografia per impostazione predefinita, ma Azure fornisce opzioni per gestire le proprie chiavi (chiavi gestite dal cliente) per determinati servizi di Azure.

- [Informazioni sulla crittografia inattiva in Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Come configurare le chiavi di crittografia gestite dal cliente](../../storage/common/storage-encryption-keys-portal.md)

- [Modello di crittografia e tabella di gestione delle chiavi](../fundamentals/encryption-models.md)

- [Crittografia doppia dati inattivi in Azure](../fundamentals/double-encryption.md#data-at-rest)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Sicurezza dei dati](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

