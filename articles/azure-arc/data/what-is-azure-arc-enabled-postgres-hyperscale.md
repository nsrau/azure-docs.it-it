---
title: Che cos'è Azure Arc abilitata per l'iperscalabilità di PostgreSQL?
description: Che cos'è Azure Arc abilitata per l'iperscalabilità di PostgreSQL?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e8746d9b29b0b4af30ba799628328acc3fd8bc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939072"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Che cos'è Azure Arc abilitata per l'iperscalabilità di PostgreSQL?

L'iperscalabilità di PostgreSQL abilitata per Azure Arc è uno dei servizi di database disponibili come parte di Azure Arc Enabled Data Services. Azure Arc consente di eseguire i servizi dati di Azure in locale, nel perimetro e in ambienti cloud pubblici con Kubernetes e l'infrastruttura desiderata. La proposta di valore di Azure Arc Enabled Data Services si articola intorno a quanto segue:
- Sempre aggiornati
- Scalabilità elastica
- Provisioning self-service
- Gestione unificata
- Supporto di scenari disconnessi

Per ulteriori informazioni, vedere:
- [Che cosa sono i servizi dati con abilitazione di Azure Arc](overview.md)
- [Modalità e requisiti di connettività](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Confrontare soluzioni

Questa sezione descrive il modo in cui Azure Arc abilitata per l'iperscalabilità di PostgreSQL è diversa da quella del database di Azure per PostgreSQL (CITUS)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Database di Azure per PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Iperscalabilità di database SQL di Azure per PostgreSQL (CITUS)":::

Questo è il fattore di forma di iperscalabilità del motore di database Postgres disponibile come database come servizio in Azure (PaaS). È alimentato dall'estensione CITUS che consente l'esperienza di iperscalabilità. In questo fattore di forma, il servizio viene eseguito nei Data Center Microsoft ed è gestito da Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Iperscalabilità PostgreSQL abilitata per Azure Arc

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Iperscalabilità PostgreSQL abilitata per Azure Arc":::

Questo è il fattore di forma di iperscalabilità del motore di database Postgres disponibile con Azure Arc Enabled Data Services. È anche basato sull'estensione CITUS che consente l'esperienza di iperscalabilità. In questo fattore di forma, i clienti forniscono l'infrastruttura che ospita i sistemi e li gestisce.

## <a name="next-steps"></a>Passaggi successivi
- **Creare**
   > **Si desidera fare semplicemente una prova?**  
   > Inizia rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.

   - **Creare**
      - [Installare gli strumenti client](install-client-tools.md)
      - [Creare il controller dati di Azure Arc](create-data-controller.md) (richiede prima di tutto l'installazione degli strumenti client)
      - [Creare un gruppo di server per l'iperscalabilità di database di Azure per PostgreSQL in Azure Arc](create-postgresql-hyperscale-server-group.md) (è necessario creare prima di tutto un controller dati di Azure Arc).
- [**Scopri di più su Azure Arc Enabled Data Services**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**Scopri di più su Azure Arc**](https://aka.ms/azurearc)
