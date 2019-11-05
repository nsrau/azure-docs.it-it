---
title: Disponibilità elevata in database di Azure per PostgreSQL – iperscalabilità (CITUS)
description: Concetti relativi a disponibilità elevata e ripristino di emergenza
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514745"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Disponibilità elevata in database di Azure per PostgreSQL – iperscalabilità (CITUS)

La disponibilità elevata (HA) evita tempi di inattività del database mantenendo le repliche di standby di ogni nodo in un gruppo di server. Se un nodo diventa inattivo, l'iperscalabilità passa le connessioni in ingresso dal nodo con errore alla relativa modalità standby. Il failover avviene entro pochi minuti e i nodi promossi hanno sempre dati aggiornati tramite la replica di streaming sincrono PostgreSQL.

Per sfruttare i vantaggi offerti da disponibilità elevata nel nodo coordinatore, le applicazioni di database devono rilevare e ripetere le connessioni eliminate e le transazioni non riuscite. Il coordinatore appena innalzato di nuovo sarà accessibile con la stessa stringa di connessione.

Il ripristino può essere suddiviso in tre fasi: rilevamento, failover e ripristino completo.  Iperscale esegue controlli di integrità periodici su ogni nodo e dopo quattro verifiche non riuscite determina che un nodo è inattivo. Con iperscalabilità viene quindi innalzato di livello uno stato del nodo primario (failover) e viene provisionato un nuovo standby.
Viene avviata la replica di flusso, che consente di aggiornare il nuovo nodo.  Quando tutti i dati sono stati replicati, il nodo ha raggiunto il recupero completo.

### <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [abilitare la disponibilità elevata](howto-hyperscale-high-availability.md) in un gruppo di server con iperscalabilità.
