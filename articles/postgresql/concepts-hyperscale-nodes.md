---
title: Nodi-iperscala (CITUS)-database di Azure per PostgreSQL
description: Informazioni sui due tipi di nodi, coordinatore e ruoli di lavoro, in un gruppo di server in database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382798"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nodi nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

Il tipo di hosting iperscalabile (CITUS) consente ai server di database di Azure per PostgreSQL (detti nodi) di coordinarsi tra loro in un'architettura "Shared Nothing". I nodi in un gruppo di server contengono collettivamente più dati e utilizzano più core CPU di quanti ne sarebbero possibili in un singolo server. L'architettura consente inoltre la scalabilità del database aggiungendo più nodi al gruppo di server.

## <a name="coordinator-and-workers"></a>Coordinatore e ruoli di lavoro

Ogni gruppo di server dispone di un nodo coordinatore e di più ruoli di lavoro. Le applicazioni inviano le query al nodo coordinatore, che lo inoltra ai dipendenti pertinenti e accumula i risultati. Le applicazioni non sono in grado di connettersi direttamente ai thread di lavoro.

Iperscale (CITUS) consente all'amministratore del database di *distribuire* tabelle, archiviando righe diverse in nodi di lavoro diversi. Le tabelle distribuite sono la chiave per le prestazioni con iperscalabilità. Se non si riesce a distribuire le tabelle, queste rimangono interamente nel nodo coordinatore e non possono sfruttare il parallelismo tra computer.

Per ogni query sulle tabelle distribuite, il coordinatore lo instrada a un singolo nodo di lavoro o parallelizzazione tra diversi a seconda che i dati necessari si trovino in un singolo nodo o in più. Il coordinatore decide cosa fare consultando le tabelle di metadati. In queste tabelle vengono rilevati i nomi DNS e l'integrità dei nodi del ruolo di lavoro e la distribuzione dei dati tra i nodi.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [tabelle distribuite](concepts-hyperscale-distributed-data.md)
