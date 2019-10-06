---
title: Indice di progetti di esempio
description: Indice degli esempi di ambienti standard e conformità per la distribuzione con Azure Blueprints.
author: DCtheGeek
ms.service: blueprints
ms.topic: sample
ms.date: 09/24/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: f5f29ec3aa5dd87a891d53847bf5a3be3a121e63
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978381"
---
# <a name="azure-blueprints-samples"></a>Esempi di Azure Blueprints

La tabella seguente include collegamenti a esempi per Azure Blueprints. Ogni esempio è di qualità produzione ed è pronto per la distribuzione immediata, rispettando i vari requisiti di conformità.

## <a name="standards-based-blueprint-samples"></a>Progetti di esempio basati su standard

|  |  |
|---------|---------|
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Fornisce misure di tutela per la conformità a Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Fornisce un set di criteri per garantire la conformità alle raccomandazioni di CIS Microsoft Azure Foundations Benchmark. |
| [IRS 1075](./irs-1075/index.md) | Fornisce misure di tutela per la conformità a IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Fornisce misure di tutela per la conformità a ISO 27001. |
| [Servizi condivisi ISO 27001](./iso27001-shared/index.md) | Offre un set di modelli di infrastrutture conformi e limiti di criteri utili per ottenere l'attestazione ISO 27001. |
| [Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001](./iso27001-ase-sql-workload/index.md) | Offre un'infrastruttura aggiuntiva per il progetto di esempio [Servizi condivisi ISO 27001](./iso27001-shared/index.md). |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Fornisce misure di salvaguardia per la conformità a NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornisce un set di criteri per agevolare la conformità allo standard PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Supporta la conformità a SWIFT CSP-CSCF V2020. |
| [Governance di UK OFFICIAL e UK NHS](./ukofficial/index.md) | Offre un set di modelli di infrastrutture conformi e misure di tutela basate su criteri utili per ottenere le attestazioni UK OFFICIAL e UK NHS. |
| [Definizione della base di Cloud Adoption Framework](./caf-foundation/index.md) | Offre un set di controlli per facilitare la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona di destinazione per la migrazione di Cloud Adoption Framework](./caf-migrate-landing-zone/index.md) | Offre un set di controlli per facilitare la configurazione per la migrazione del primo carico di lavoro e la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Strategia degli esempi

![Strategia dei progetti di esempio](../media/blueprint-samples-strategy.png)

I progetti per la definizione della base di Cloud Adoption Framework e la zona di destinazione per la migrazione di Cloud Adoption Framework presuppongono che il cliente prepari una sottoscrizione singola pulita in cui eseguire la migrazione di carichi di lavoro/asset locali ad Azure
(area A e B nella figura precedente).  

Oltre all'opportunità di eseguire l'iterazione dei progetti di esempio e cercare criteri di personalizzazione applicati da un cliente, è anche possibile gestire progetti di settori specifici, come servizi finanziari e e-commerce (parte superiore dell'area B). Analogamente, è stata prevista la creazione di progetti che tengono conto di particolari esigenze architetturali, come più sottoscrizioni, disponibilità elevata, risorse tra aree e clienti che implementano controlli su sottoscrizioni e risorse esistenti (area C e D).

Sono disponibili progetti di esempio per la gestione di scenari cliente con requisiti di conformità elevati e architettura particolarmente complessa (area E nella figura precedente). L'area F è quella destinata a clienti e partner che sfruttano i progetti di esempio e li personalizzano in base ad esigenze specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).