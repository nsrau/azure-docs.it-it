---
title: Disponibilità elevata-iperscala (CITUS)-database di Azure per PostgreSQL
description: Concetti relativi a disponibilità elevata e ripristino di emergenza
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975534"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Disponibilità elevata in database di Azure per PostgreSQL – iperscalabilità (CITUS)

Il livello Disponibilità elevata evita il tempo di inattività dei database mantenendo repliche di standby di ogni nodo in un gruppo di server. In caso di inattività di un nodo, Hyperscale sposta le connessioni in ingresso dal nodo con errore al rispettivo standby. Il failover viene eseguito entro pochi minuti e i nodi promossi hanno sempre a disposizione dati aggiornati grazie alla replica di streaming sincrona di PostgreSQL.

Per sfruttare i vantaggi offerti da disponibilità elevata nel nodo coordinatore, le applicazioni di database devono rilevare e ripetere le connessioni eliminate e le transazioni non riuscite. Il coordinatore appena innalzato di nuovo sarà accessibile con la stessa stringa di connessione.

Il ripristino può essere suddiviso in tre fasi: rilevamento, failover e ripristino completo.  Iperscale esegue controlli di integrità periodici su ogni nodo e dopo quattro verifiche non riuscite determina che un nodo è inattivo. Con iperscalabilità viene quindi innalzato di livello uno stato del nodo primario (failover) e viene provisionato un nuovo standby.
Viene avviata la replica di flusso, che consente di aggiornare il nuovo nodo.  Quando tutti i dati sono stati replicati, il nodo ha raggiunto il recupero completo.

### <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [abilitare la disponibilità elevata](howto-hyperscale-high-availability.md) in un gruppo di server con iperscalabilità.
