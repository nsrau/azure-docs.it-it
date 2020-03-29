---
title: Nodi - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Informazioni sui due tipi di nodi, coordinatore e lavoratori, in un gruppo di server in Database di Azure per PostgreSQL.Learn about the two types of nodes, coordinator and workers, in a server group in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974003"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nodi nel database di Azure per PostgreSQL – Hyperscale (Citus)

Il tipo di hosting Hyperscale (Citus) consente al database di Azure per i server PostgreSQL (denominato nodi) di coordinarsi tra loro in un'architettura "shared nothing". I nodi di un gruppo di server contengono collettivamente più dati e utilizzano più core della CPU di quelli possibili in un singolo server. L'architettura consente inoltre al database di scalare aggiungendo più nodi al gruppo di server.

## <a name="coordinator-and-workers"></a>Coordinatore e lavoratori

Ogni gruppo di server ha un nodo coordinatore e più lavoratori. Le applicazioni inviano le loro query al nodo coordinatore, che lo inoltra ai lavoratori interessati e accumula i loro risultati. Le applicazioni non sono in grado di connettersi direttamente ai lavoratori.

Per ogni query, il coordinatore la instrada a un singolo nodo di lavoro o la parallelizza tra più elementi a seconda che i dati richiesti risiedano su un singolo nodo o su più. Il coordinatore decide cosa fare consultando le tabelle dei metadati. Queste tabelle tengono traccia dei nomi DNS e dell'integrità dei nodi di lavoro e della distribuzione dei dati tra i nodi.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come i nodi archiviano i [dati distribuiti](concepts-hyperscale-distributed-data.md)
