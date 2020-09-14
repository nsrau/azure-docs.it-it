---
title: Benchmark di sicurezza di Azure V2-sicurezza degli endpoint
description: Sicurezza degli endpoint di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c04e4233ded34ceaeec9cd9afb240d3d1ac864e0
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059260"
---
# <a name="security-control-endpoint-security"></a>Controllo di sicurezza: Endpoint Security

Endpoint Security copre i controlli nel rilevamento e nella risposta degli endpoint. Questo include l'uso del servizio di rilevamento e risposta dell'endpoint e del servizio antimalware per gli endpoint negli ambienti di Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usare il rilevamento e la risposta degli endpoint (EDR)

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Abilitare le funzionalità di rilevamento e risposta degli endpoint per server e client ed eseguire l'integrazione con i processi SIEM e operazioni di sicurezza.

Microsoft Defender Advanced Threat Protection offre la funzionalità EDR come parte di una piattaforma di sicurezza degli endpoint aziendali per prevenire, rilevare, analizzare e rispondere a minacce avanzate. 

- [Panoramica di Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Servizio Microsoft Defender ATP per server Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Servizio Microsoft Defender ATP per server non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: utilizzo del software antimalware moderno gestito centralmente

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Usare una soluzione antimalware per endpoint gestita a livello centralizzato in grado di analizzare in tempo reale e periodica

Il Centro sicurezza di Azure può identificare automaticamente l'uso di una serie di soluzioni antimalware comuni per le macchine virtuali e segnalare lo stato di esecuzione di Endpoint Protection e creare raccomandazioni. 

Microsoft antimalware per servizi cloud di Azure è l'anti-malware predefinito per le macchine virtuali (VM) Windows. Per le macchine virtuali Linux, usare una soluzione antimalware di terze parti.  È anche possibile usare il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione di Azure. 

- [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](../fundamentals/antimalware.md)

- [Soluzioni di protezione endpoint supportate](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: assicurarsi che il software e le firme anti-malware siano aggiornati

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

Verificare che le firme anti-malware vengano aggiornate in modo rapido e coerente. 

Seguire le raccomandazioni nel centro sicurezza di Azure: " &amp; app di calcolo" per assicurarsi che tutti gli endpoint siano aggiornati con le firme più recenti. Per impostazione predefinita, Microsoft antimalware installerà automaticamente le firme e gli aggiornamenti del motore più recenti. Per Linux, usare una soluzione antimalware di terze parti.

- [Come distribuire Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../fundamentals/antimalware.md)

**Responsabilità**: Customer

**Stakeholder**per la sicurezza dei clienti:

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security)

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestione della conformità della sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

