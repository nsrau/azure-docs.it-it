---
title: Criteri di base di sicurezza di Azure per il benchmark di sicurezza di Azure
description: La linea di base di sicurezza dei criteri di Azure fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b18da5e41e235b560918ad26117a0162078862c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540983"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Criteri di base di sicurezza di Azure per il benchmark di sicurezza di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../../../security/benchmarks/overview.md) ai criteri di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **domini di conformità** e ai controlli di **sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ai criteri di Azure. I **controlli** non applicabili ai criteri di Azure sono stati esclusi. Per informazioni sul mapping completo di criteri di Azure al benchmark di sicurezza di Azure, vedere il [file di mapping di base di sicurezza dei criteri](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure.

Per un mapping dei controlli benchmark di sicurezza di Azure alle definizioni di criteri predefinite tramite l'iniziativa predefinita, vedere [conformità normativa: benchmark di sicurezza di Azure](../samples/azure-security-benchmark.md).

I criteri di Azure usano il termine _proprietario_ al posto della _responsabilità_. Per informazioni dettagliate sulla _Proprietà_, vedere [definizioni dei criteri di criteri di Azure](./definition-structure.md#type) e [responsabilità condivisa nel cloud](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: i criteri di Azure usano i log attività, abilitati automaticamente, per includere origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

* [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../../azure-monitor/platform/diagnostic-settings.md)

* [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. 

È anche possibile abilitare una soluzione di accesso just-in-time/just-enough usando [Azure ad Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) ruoli con privilegi o [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso ai criteri di Azure.

* [Autorizzazioni RBAC di Azure in criteri di Azure](../overview.md#azure-rbac-permissions-in-azure-policy)

* [Come configurare RBAC di Azure](../../../role-based-access-control/role-assignments-portal.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con i log attività per creare avvisi per le modifiche apportate ai criteri di Azure.

* [Come creare avvisi per gli eventi del log attività di Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia. Usare l'effetto _modifica_ criteri di Azure per creare report e applicare la conformità e la governance dei tag coerenti.

* [Esercitazione: creare e gestire criteri](../tutorials/create-and-manage.md)

* [Esercitazione: Gestire la governance dei tag](../tutorials/govern-tags.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle definizioni dei criteri approvate e delle assegnazioni di criteri in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

* [Come configurare e gestire Criteri di Azure](../tutorials/create-and-manage.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../../../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../../../security/benchmarks/security-baselines-overview.md)
