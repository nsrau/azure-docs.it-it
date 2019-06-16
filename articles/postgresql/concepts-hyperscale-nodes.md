---
title: Nodi nel Database di Azure per PostgreSQL con Iperscalabilità (Citus) (anteprima)
description: I due tipi di nodi in un gruppo di server.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077276"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Nodi nel Database di Azure per PostgreSQL con Iperscalabilità (Citus) (anteprima)

Il tipo di hosting il (Citus) (anteprima) su scala molto vasta consente a Database di Azure per i server PostgreSQL (denominati nodi) coordinarsi tra loro in un'architettura "shared nothing". I nodi in un gruppo di server collettivamente contenere molti più dati e usano più core CPU rispetto a quanto consentito in un singolo server. L'architettura consente inoltre il database per la scalabilità aggiungendo più nodi per il gruppo di server.

## <a name="coordinator-and-workers"></a>Coordinatore e ruoli di lavoro

Ogni gruppo di server ha un nodo coordinatore e più ruoli di lavoro. Le applicazioni inviano le query per il nodo coordinatore, che inoltra ai ruoli di lavoro pertinenti e accumula i relativi risultati. Le applicazioni non sono in grado di connettersi direttamente ai ruoli di lavoro.

Per ogni query, il coordinatore instrada a un singolo nodo di lavoro oppure viene eseguita la parallelizzazione in diversi a seconda del fatto che i dati necessari si trovano su un nodo singolo o multiplo. Il coordinatore decide come procedere consultando le tabelle di metadati. Queste tabelle di rilevare i nomi DNS e dell'integrità dei nodi di lavoro e la distribuzione dei dati tra i nodi.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come archiviano i nodi [dati distribuiti](concepts-hyperscale-distributed-data.md)
