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
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: ca907e145067043b41dd6000248d44ecc1e3434f
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510443"
---
# <a name="business-critical-tier---azure-sql-database"></a>Livello Business critical - Servizio di database SQL di Azure

> [!NOTE]
> Il livello Business critical è chiamato Premium nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-service-tiers.md).

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli di architettura usati nel database SQL di Azure:
- Utilizzo generico/Standard 
- Business critical/Premium
- Hyperscale

Il modello di servizio Premium/Business critical è basato su un cluster di processi del motore di database. Questo modello di architettura si basa sul fatto che c’è sempre un quorum di nodi di motore di database disponibili e ha un impatto minimo sulle prestazioni sul carico di lavoro anche durante le attività di manutenzione.

Azure aggiorna e applica le patch al sistema operativo, ai driver e al motore di database di SQL Server in modo trasparente con tempi di inattività minimi per gli utenti finali. 

Nei livelli di servizio Premium e Business critical del database SQL di Azure è abilitata la disponibilità Premium, progettata per carichi di lavoro elevati che non tollerano un impatto sulle prestazioni dovuto alle operazioni di manutenzione continua.

Nel modello Premium il database SQL di Azure integra calcolo e archiviazione nel singolo nodo. La disponibilità elevata in questo modello di architettura è ottenuta dalla replica di archiviazione (unità SSD collegata al computer locale) e calcolo (processo del motore di database SQL Server) distribuita in cluster con quattro nodi mediante una tecnologia simile ai [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server.

![Cluster di nodi di motore di database](media/sql-database-managed-instance/business-critical-service-tier.png)

Sia il processo del motore di database SQL che i file mdf/ldf sottostanti vengono posizionati nello stesso nodo con una risorsa di archiviazione SSD collegata in locale che offre bassa latenza al carico di lavoro. La disponibilità elevata è implementata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server. Ogni database è un cluster di nodi del database con un database primario accessibile per il carico di lavoro del cliente e tre processi secondari contenenti le copie dei dati. Il nodo primario esegue il push costante delle modifiche ai nodi secondari per garantire che i dati siano disponibili nelle repliche secondarie se il nodo primario si arresta per un qualsiasi motivo. Il failover viene gestito dal motore di database di SQL Server: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per garantire un numero sufficiente di nodi nel cluster. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario.

Inoltre, nel cluster business critical è integrata una funzionalità di [scalabilità in lettura](sql-database-read-scale-out.md) che fornisce un nodo di sola lettura integrato e gratuito che può essere usato per l'esecuzione di query di sola lettura (ad esempio report) che non dovrebbero incidere sulle prestazioni del carico di lavoro primario.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio Business critical è progettato per le applicazioni che richiedono risposte a bassa latenza dalla risorsa di archiviazione SSD sottostante (in media 1-2 ms) e ripristino rapido in caso di errore dell'infrastruttura sottostante o che devono delegare report, analisi e query di sola lettura alla replica secondaria leggibile gratuita del database primario.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui livelli [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
