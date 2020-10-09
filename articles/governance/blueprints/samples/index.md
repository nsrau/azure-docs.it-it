---
title: Indice degli esempi di progetto
description: Indice di esempi di conformità e standard per la distribuzione di ambienti, criteri ed elementi di base di Cloud Adoptions Framework con Azure Blueprints.
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: def5e51a3db7e0733e0d7e1ef5c6895aedb7128d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530982"
---
# <a name="azure-blueprints-samples"></a>Esempi di Azure Blueprints

La tabella seguente include collegamenti a esempi per Azure Blueprints. Ogni esempio è di qualità produzione ed è pronto per la distribuzione immediata, rispettando i vari requisiti di conformità.

## <a name="standards-based-blueprint-samples"></a>Progetti di esempio basati su standard

| Esempio | Descrizione |
|---------|---------|
| [Australian Government ISM PROTECTED](./ism-protected/index.md) | Fornisce misure di tutela per la conformità ad Australian Government ISM PROTECTED. |
| [Azure Security Benchmark](./azure-security-benchmark.md) | Fornisce misure di tutela per la conformità ad [Azure Security Benchmark](../../../security/benchmarks/overview.md). |
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Fornisce misure di tutela per la conformità a Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1-1-0.md)| Fornisce un set di criteri per garantire la conformità alle raccomandazioni di CIS Microsoft Azure Foundations Benchmark. |
| [DoD Impact Level 4](./dod-impact-level-4/index.md) | Fornisce un set di criteri per garantire la conformità a DoD Impact Level 4. |
| [DoD Impact Level 5](./dod-impact-level-5/index.md) | Fornisce un set di criteri per garantire la conformità a DoD Impact Level 5. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Fornisce un set di criteri per garantire la conformità a FedRAMP Moderate. |
| [FedRAMP High](./fedramp-h/index.md) | Fornisce un set di criteri per garantire la conformità a FedRAMP High. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | Fornisce un set di criteri per garantire la conformità a HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Fornisce misure di tutela per la conformità a IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Fornisce misure di tutela per la conformità a ISO 27001. |
| [Servizi condivisi ISO 27001](./iso27001-shared/index.md) | Offre un set di modelli di infrastrutture conformi e limiti di criteri utili per ottenere l'attestazione ISO 27001. |
| [Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001](./iso27001-ase-sql-workload/index.md) | Offre un'infrastruttura aggiuntiva per il progetto di esempio [Servizi condivisi ISO 27001](./iso27001-shared/index.md). |
| [Supporti](./media/index.md) | Fornisce un set di criteri per garantire la conformità ai controlli MPAA per Media. |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | Fornisce misure di salvaguardia per la conformità a NIST SP 800-53 R4. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | Fornisce misure di salvaguardia per la conformità a NIST SP 800-171 R2. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornisce un set di criteri per agevolare la conformità allo standard PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Supporta la conformità a SWIFT CSP-CSCF V2020. |
| [Governance di UK OFFICIAL e UK NHS](./ukofficial/index.md) | Offre un set di modelli di infrastrutture conformi e misure di tutela basate su criteri utili per ottenere le attestazioni UK OFFICIAL e UK NHS. |
| [Definizione della base di Cloud Adoption Framework](./caf-foundation/index.md) | Offre un set di controlli per facilitare la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona di destinazione per la migrazione di Cloud Adoption Framework](./caf-migrate-landing-zone/index.md) | Offre un set di controlli per facilitare la configurazione per la migrazione del primo carico di lavoro e la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Strategia degli esempi

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="Diagramma della posizione degli esempi di progetto ai fini del confronto tra complessità dell'architettura e requisiti di conformità." border="false":::
   Illustra un sistema di coordinate in cui la complessità dell'architettura corrisponde all'asse X e i requisiti di conformità corrispondono all'asse Y.  Quando i requisiti di conformità e la complessità dell'architettura sono più elevati, adottare esempi di progetto standard del portale designato nell'area E. Per i clienti che iniziano con Azure, sfruttare i progetti di base e della zona di destinazione di Cloud Adoption Framework designati dalle aree A e B. Lo spazio rimanente viene attribuito ai progetti personalizzati creati dai clienti che sono partner per le aree C, D e F. :::image-end:::

I progetti per la definizione della base di Cloud Adoption Framework e la zona di destinazione per la migrazione di Cloud Adoption Framework presuppongono che il cliente prepari una sottoscrizione singola pulita in cui eseguire la migrazione di carichi di lavoro e asset locali ad Azure
(area A e B nella figura).  

Oltre all'opportunità di eseguire l'iterazione dei progetti di esempio e cercare criteri di personalizzazione applicati da un cliente, è anche possibile gestire progetti di settori specifici, come servizi finanziari e e-commerce (parte superiore dell'area B). Analogamente, è stata prevista la creazione di progetti che tengono conto di particolari esigenze architetturali, come più sottoscrizioni, disponibilità elevata, risorse tra aree e clienti che implementano controlli su sottoscrizioni e risorse esistenti (area C e D).

Sono disponibili progetti di esempio per la gestione di scenari cliente con requisiti di conformità elevati e architettura particolarmente complessa (area E nella figura). L'area F nella figura è quella destinata a clienti e partner che sfruttano i progetti di esempio e li personalizzano in base a esigenze specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).
