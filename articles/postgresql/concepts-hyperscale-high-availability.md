---
title: Disponibilità elevata - Hyperscale (Citus) - Database di Azure per PostgreSQLHigh availability – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Concetti relativi alla disponibilità elevata e al ripristino di emergenzaHigh availability and disaster recovery concepts
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975534"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Disponibilità elevata nel database di Azure per PostgreSQL - Hyperscale (Citus)High availability in Azure Database for PostgreSQL – Hyperscale (Citus)

La disponibilità elevata consente di evitare i tempi di inattività del database mantenendo le repliche di standby di ogni nodo in un gruppo di server. Se un nodo non è attivo, Hyperscale passa le connessioni in ingresso dal nodo in cui si è verificato l'errore allo standby. Il failover avviene in pochi minuti e i nodi alzati di livello hanno sempre dati aggiornati tramite la replica sincrona del flusso PostgreSQL.Failover happens within few minutes, and promoted nodes always have fresh data through PostgreSQL synchronous streaming replication.

Per sfruttare i vantaggi della configurazione a prova di accesso a prova nel nodo coordinatore, le applicazioni di database devono rilevare e riprovare le connessioni interrotte e le transazioni non riuscite. Il coordinatore appena promosso sarà accessibile con la stessa stringa di connessione.

Il ripristino può essere suddiviso in tre fasi: rilevamento, failover e ripristino completo.  Hyperscale esegue controlli periodici di integrità su ogni nodo e, dopo quattro controlli non riusciti, determina che un nodo è inattivo. Hyperscale promuove quindi uno standby allo stato del nodo primario (failover) ed effettua il provisioning di un nuovo standby.Hyperscale then promotes a standby to primary node status (failover), and provisions a new standby-to-be.
Viene a clato la replica di streaming, che porta il nuovo nodo aggiornato.  Quando tutti i dati sono stati replicati, il nodo ha raggiunto il ripristino completo.

### <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [abilitare](howto-hyperscale-high-availability.md) la disponibilità elevata in un gruppo di server Hyperscale.Learn how to enable high availability in a Hyperscale server group.
