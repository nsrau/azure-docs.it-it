---
title: Aree che supportano zone di disponibilità in Azure
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853161"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Aree che supportano zone di disponibilità in Azure

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Per altre informazioni sulle zone di disponibilità, vedere [aree e zone di disponibilità in Azure](az-overview.md).

Questa sezione elenca i servizi e le aree di Azure che supportano zone di disponibilità.

I servizi disponibili in ogni area, insieme alla prossima roadmap per la disponibilità, sono reperibili in [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Americhe

| Servizio | Stati Uniti centrali | Stati Uniti orientali | Stati Uniti orientali 2 | Stati Uniti occidentali 2 |
| --- | :---: | :---: | :---: | :---: |
| **Calcolo** |  |  |  |  |
| Macchine virtuali Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Macchine virtuali Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Set di scalabilità di macchine virtuali         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambienti del servizio app Azure | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Servizio Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Archiviazione con ridondanza della zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |  |
| Indirizzo IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer Standard             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway applicazione (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall di Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Database** |  |  |  |  |
| Esplora dati di Azure                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Database SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_check_mark: (anteprima) |
| Cache Redis di Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |  |
| Hub eventi                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione** |  |  |  |  |
| Bus di servizio (solo livello Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Griglia di eventi                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identità** |  |  |  |  |
| Servizi di dominio Azure Active Directory           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>Europa

| Servizio | Francia centrale | Europa settentrionale | Regno Unito meridionale | Europa occidentale |
| --- | :---: | :---: | :---: | :---: |
| **Calcolo** |  |  |  |  |
| Macchine virtuali Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Macchine virtuali Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Set di scalabilità di macchine virtuali         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambienti del servizio app Azure | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Servizio Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Archiviazione con ridondanza della zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |  |
| Indirizzo IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer Standard             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway applicazione (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall di Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Database** |  |  |  |  |
| Esplora dati di Azure                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Database SQL                       | :heavy_check_mark: | : heavy_check_mark: (anteprima) | :heavy_check_mark: | :heavy_check_mark: |
| Cache Redis di Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |  |
| Hub eventi                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione**  |  |  |  |  |
| Bus di servizio (solo livello Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Griglia di eventi                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identità** |  |  |  |  |
| Servizi di dominio Azure Active Directory           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>Asia Pacifico

| Servizio | Giappone orientale | Asia sud-orientale | Australia orientale |
| --- | :---: | :---: | :---: |
| **Calcolo** |  |  |  |
| Macchine virtuali Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Macchine virtuali Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Set di scalabilità di macchine virtuali         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambienti del servizio app Azure | :heavy_check_mark: | :heavy_check_mark: |  |
| Servizio Azure Kubernetes           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Archiviazione con ridondanza della zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |
| Indirizzo IP standard                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Load Balancer Standard             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway applicazione (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall di Azure                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **Database** |  |  |  |
| Esplora dati di Azure                | :heavy_check_mark: | :heavy_check_mark: |  |
| Database SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Cache Redis di Azure              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |
| Hub eventi                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione** |  |  |  |
| Bus di servizio (solo livello Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Griglia di eventi                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **Identità** |  |  |  |
| Servizi di dominio Azure Active Directory           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Altro

Azure offre anche il supporto zone di disponibilità nelle aree seguenti:

- US Gov Virginia
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Canada centrale

Per ulteriori informazioni sul supporto zone di disponibilità in queste quattro aree, contattare il rappresentante Microsoft per le vendite o il cliente o aprire una richiesta di supporto tecnico.

## <a name="next-steps"></a>Passaggi successivi

- [Aree e zone di disponibilità in Azure](az-overview.md)
