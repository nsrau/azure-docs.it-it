---
title: Baseline della sicurezza di Azure Resource Graph per il benchmark di sicurezza di Azure
description: La linea di base di sicurezza di Azure Resource Graph fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a8b271a030de2ddd3aa40b5bb0af871319c0e1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171806"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Baseline della sicurezza di Azure Resource Graph per il benchmark di sicurezza di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../../../security/benchmarks/overview.md) al grafico delle risorse di Azure. Il benchmark di sicurezza di Azure offre consigli su come proteggere le soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al grafico delle risorse di Azure. I **controlli** non applicabili al grafico delle risorse di Azure sono stati esclusi. Per informazioni sul mapping completo di Azure Resource Graph al benchmark di sicurezza di Azure, vedere il file di mapping di base per la [sicurezza della rete virtuale](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure.



## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Resource Graph fornisce l'accesso a proprietà e tipi di risorse in base ai controlli degli accessi in base al ruolo (RBAC). Controllare e verificare l'accesso concesso alle entità di sicurezza (utenti, gruppi e account del servizio) a intervalli regolari per assicurarsi che le query restituiscano risultati per le risorse appropriate.

* [Autorizzazioni in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Come usare le verifiche di accesso alle identità di Azure](../../../active-directory/governance/access-reviews-overview.md)


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo per controllare l'accesso ai dati e alle risorse. Per usare il grafico delle risorse di Azure, è necessario disporre anche dell'accesso appropriato alle risorse su cui si vuole eseguire la query. Questo accesso deve avere un ambito di sola lettura ed essere concesso solo al personale richiesto.

* [Autorizzazioni in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Come configurare RBAC in Azure](../../../role-based-access-control/role-assignments-rest.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse supportate nelle sottoscrizioni, nei gruppi di gestione e nei tenant. Assicurarsi di disporre delle autorizzazioni appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

* [Come creare query con Azure Resource Graph](../first-query-portal.md)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../../role-based-access-control/overview.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione. Usare Azure Resource Graph per eseguire una query per le risorse di Azure approvate e la cronologia delle modifiche (anteprima) per esaminare gli snapshot e vedere cosa è cambiato.

* [Eseguire query sulle risorse di Azure con il grafico risorse di Azure](../first-query-portal.md)

* [Ottenere le modifiche delle risorse](../how-to/get-resource-changes.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare le risorse nelle sottoscrizioni, nei gruppi di gestione e nei tenant. Assicurarsi che tutte le risorse di Azure nell'ambiente siano approvate.

* [Eseguire query sulle risorse di Azure con il grafico risorse di Azure](../first-query-portal.md)

* [Esempi: query Starter per il grafico delle risorse di Azure](../samples/starter.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../../../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../../../security/benchmarks/security-baselines-overview.md)
