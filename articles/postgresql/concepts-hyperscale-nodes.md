---
title: Nodi-iperscala (CITUS)-database di Azure per PostgreSQL
description: Informazioni sui due tipi di nodi, coordinatore e ruoli di lavoro, in un gruppo di server in database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974003"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nodi nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

Il tipo di hosting iperscalabile (CITUS) consente ai server di database di Azure per PostgreSQL (detti nodi) di coordinarsi tra loro in un'architettura "Shared Nothing". I nodi in un gruppo di server contengono collettivamente più dati e utilizzano più core CPU di quanti ne sarebbero possibili in un singolo server. L'architettura consente inoltre la scalabilità del database aggiungendo più nodi al gruppo di server.

## <a name="coordinator-and-workers"></a>Coordinatore e ruoli di lavoro

Ogni gruppo di server dispone di un nodo coordinatore e di più ruoli di lavoro. Le applicazioni inviano le query al nodo coordinatore, che lo inoltra ai dipendenti pertinenti e accumula i risultati. Le applicazioni non sono in grado di connettersi direttamente ai thread di lavoro.

Per ogni query, il coordinatore lo instrada a un singolo nodo di lavoro o parallelizzazione tra diversi a seconda che i dati richiesti si trovino in un singolo nodo o in più. Il coordinatore decide cosa fare consultando le tabelle di metadati. In queste tabelle vengono rilevati i nomi DNS e l'integrità dei nodi del ruolo di lavoro e la distribuzione dei dati tra i nodi.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle modalità di archiviazione [dei dati distribuiti](concepts-hyperscale-distributed-data.md) nei nodi
