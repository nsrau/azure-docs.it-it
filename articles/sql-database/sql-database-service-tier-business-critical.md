---
title: Livello Business critical - Servizio di database SQL di Azure | Microsoft Docs
description: Informazioni sul livello Business critical del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 90989a9105405f1784b3be9ab59f55cd3433feaf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479218"
---
# <a name="business-critical-tier---azure-sql-database"></a>Livello Business critical - Servizio di database SQL di Azure

> [!NOTE]
> Il livello Business critical è chiamato Premium nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli di architettura usati nel database SQL di Azure:
- Utilizzo generico/Standard 
- Business critical/Premium
- Hyperscale

Il modello di livello di servizio Premium/Business critical è basato su un cluster di processi del motore di database. Questo modello di architettura si basa sul fatto che c’è sempre un quorum di nodi di motore di database disponibili e ha un impatto minimo sulle prestazioni sul carico di lavoro anche durante le attività di manutenzione.

Azure aggiorna e applica le patch al sistema operativo, ai driver e al motore di database di SQL Server in modo trasparente con tempi di inattività minimi per gli utenti finali. 

Nei livelli di servizio Premium e Business critical del database SQL di Azure è abilitata la disponibilità Premium, progettata per carichi di lavoro elevati che non tollerano un impatto sulle prestazioni dovuto alle operazioni di manutenzione continua.

Nel modello Premium il database SQL di Azure integra calcolo e archiviazione nel singolo nodo. La disponibilità elevata in questo modello di architettura è ottenuta dalla replica di archiviazione (unità SSD collegata al computer locale) e calcolo (processo del motore di database SQL Server) distribuita in cluster con quattro nodi mediante una tecnologia simile ai [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server.

![Cluster di nodi di motore di database](media/sql-database-managed-instance/business-critical-service-tier.png)

Sia il processo del motore di database SQL che i file mdf/ldf sottostanti vengono posizionati nello stesso nodo con una risorsa di archiviazione SSD collegata in locale che offre bassa latenza al carico di lavoro. La disponibilità elevata è implementata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server. Ogni database è un cluster di nodi del database con un database primario accessibile per il carico di lavoro del cliente e tre processi secondari contenenti le copie dei dati. Il nodo primario esegue il push costante delle modifiche ai nodi secondari per garantire che i dati siano disponibili nelle repliche secondarie se il nodo primario si arresta per un qualsiasi motivo. Il failover viene gestito dal motore di database di SQL Server: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per garantire un numero sufficiente di nodi nel cluster. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario.

Inoltre, nel cluster business critical è integrata una funzionalità di [scalabilità in lettura](sql-database-read-scale-out.md) che fornisce un nodo di sola lettura integrato e gratuito che può essere usato per l'esecuzione di query di sola lettura (ad esempio report) che non dovrebbero incidere sulle prestazioni del carico di lavoro primario.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio Business critical è progettato per le applicazioni che richiedono risposte a bassa latenza dalla risorsa di archiviazione SSD sottostante (in media 1-2 ms) e ripristino rapido in caso di errore dell'infrastruttura sottostante o che devono delegare report, analisi e query di sola lettura alla replica secondaria leggibile gratuita del database primario.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, IO, memorie) del livello Business Critical in [istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)singola del database in [modello basato su vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) oppure [modello basato su DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), o elastico pool di applicazioni [modello basato su vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) e [modello basato su DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Informazioni sui livelli [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
