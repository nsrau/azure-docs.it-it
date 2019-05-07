---
title: Controllo di accesso basato sui ruoli in Azure Cosmos DB con l'integrazione di Azure Active Directory
description: Informazioni su come Azure Cosmos DB fornisce protezione del database con l'integrazione di Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078941"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controllo degli accessi in base al ruolo in Azure Cosmos DB

Azure Cosmos DB offre controllo di accesso predefinito basato sui ruoli (RBAC) per scenari comuni di gestione in Azure Cosmos DB. Una persona che dispone di un profilo in Azure Active Directory può assegnare questi ruoli RBAC a utenti, gruppi, le entità servizio o gestite le identità per concedere o negare l'accesso alle risorse e operazioni sulle risorse di Azure Cosmos DB. Le assegnazioni di ruolo sono limitate a piano di controllo solo accesso, che include l'accesso in un account Azure Cosmos database, contenitori e offre (velocità effettiva).

## <a name="built-in-roles"></a>Ruoli predefiniti

Di seguito sono i ruoli predefiniti supportati da Azure Cosmos DB:

|**Ruolo predefinito**  |**Descrizione**  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | È in grado di gestire account Azure Cosmos DB.  |
|[COSMOS DB Account lettore](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Può leggere i dati degli account Azure Cosmos DB.        |
|[Operatore di Backup COSMOS](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Può inviare una richiesta di ripristino per un database Cosmos Azure o in un contenitore.       |
|[Operatore di COSMOS DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Possibile effettuare il provisioning degli account, database e dei contenitori Cosmos Azure ma non è possibile accedere alle chiavi necessarie per accedere ai dati.         |

> [!IMPORTANT]
> Supporto RBAC in Azure Cosmos DB si applica al solo operazioni del piano di controllo. Operazioni del piano dati sono protetto tramite le chiavi master o i token delle risorse. Per altre informazioni, vedere [proteggere l'accesso ai dati in Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identity and Access Management (IAM)

Il **controllo di accesso (IAM)** riquadro nel portale di Azure consente di configurare il controllo di accesso basato sui ruoli sulle risorse di Azure Cosmos. I ruoli vengono applicati a utenti, gruppi, le entità servizio e identità gestite in Active Directory. È possibile usare i ruoli predefiniti o ruoli personalizzati per utenti singoli e gruppi. Lo screenshot seguente illustra l'integrazione di Active Directory (RBAC) tramite il controllo di accesso (IAM) nel portale di Azure:

![Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Ruoli personalizzati

Oltre ai ruoli predefiniti, gli utenti possono anche creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure e si applicano questi ruoli per le entità servizio per tutte le sottoscrizioni all'interno di tenant di Active Directory. Ruoli personalizzati forniscono agli utenti un modo per creare definizioni di ruolo RBAC con un set personalizzato di risorse operazioni di provider. Per informazioni su quali operazioni sono disponibili per la creazione di ruoli personalizzati per Azure Cosmos DB, vedere [operazioni di provider di risorse di Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il controllo di accesso basato sui ruoli (RBAC) per le risorse di Azure](../role-based-access-control/overview.md)
- [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
- [Operazioni di provider di Azure Cosmos DB risorsa](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
