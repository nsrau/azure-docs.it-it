---
title: Aree che supportano zone di disponibilità in Azure
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b181316ac2c6998676a17589fff246cf88ff50c2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123803"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Aree che supportano zone di disponibilità in Azure

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Per altre informazioni sulle zone di disponibilità, vedere [aree e zone di disponibilità in Azure](az-overview.md).

## <a name="services-support-by-region"></a>Supporto dei servizi in base all'area

Questa sezione elenca i servizi e le aree di Azure che supportano zone di disponibilità.

I servizi disponibili in ogni area, insieme alla prossima roadmap per la disponibilità, sono reperibili in [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).

|                                 |Americhe |              |           |           | Europa |              |          |              | Asia Pacifico |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |Stati Uniti centrali|Stati Uniti orientali|Stati Uniti orientali 2|Stati Uniti occidentali 2|Francia centrale|Europa settentrionale|Regno Unito meridionale|Europa occidentale|Giappone orientale|Asia sud-orientale|Australia orientale|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |                |
| Macchine virtuali Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Macchine virtuali Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Set di scalabilità di macchine virtuali      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| ILB ambienti del servizio app Azure | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Servizio Azure Kubernetes        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Archiviazione con ridondanza della zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Funzionalità di rete**                     |            |              |           |           |                |              |          |             |            |                |                |
| Indirizzo IP standard        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Load Balancer Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Gateway VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Gateway ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Gateway applicazione (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Firewall di Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Database**                     |            |              |           |           |                |              |          |             |            |                |                |
| Esplora dati di Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Database SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (anteprima) | &#10003;       | &#10003; (anteprima)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Cache Redis di Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;    |
| **Analisi**                       |            |              |           |           |                |              |          |             |            |                |                |
| Hub eventi                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integrazione**                     |            |              |           |           |                |              |          |             |            |                |                |
| Bus di servizio (solo livello Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Griglia di eventi | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identità**                     |            |              |           |           |                |              |          |             |            |                |                |
| Servizi di dominio Azure Active Directory | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |


## <a name="next-steps"></a>Passaggi successivi

- [Aree e zone di disponibilità in Azure](az-overview.md)
