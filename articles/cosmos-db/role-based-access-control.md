---
title: Controllo degli accessi in base al ruolo in Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB offre la protezione del database con l'integrazione di Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445093"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controllo degli accessi in base al ruolo in Azure Cosmos DB

Azure Cosmos DB offre il controllo degli accessi in base al ruolo incorporato per scenari di gestione comuni in Azure Cosmos DB. Una persona che dispone di un profilo in Azure Active Directory può assegnare questi ruoli RBAC a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso alle risorse e alle operazioni nelle risorse del database Cosmos di Azure.A individual who has a profile in Azure Active Directory can assign these RBAC roles to users, groups, service principals, or managed identities to grant or deny access to resources and operations on Azure Cosmos DB resources. L'ambito delle assegnazioni di ruolo è limitato al solo piano di controllo, che include l'accesso ad account Cosmos di Azure, database, contenitori e offerte (velocità effettiva).

## <a name="built-in-roles"></a>Ruoli predefiniti

The following are the built-in roles supported by Azure Cosmos DB:

|**Ruolo predefinito**  |**Descrizione**  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB.|
|[Lettore di account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB.|
|[Operatore di backup Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Può inviare una richiesta di ripristino per un database di Azure Cosmos o un contenitore.|
|[Operatore Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Può eseguire il provisioning di account, database e contenitori di Azure Cosmos, ma non può accedere alle chiavi necessarie per accedere ai dati.|

> [!IMPORTANT]
> Il supporto RBAC in Azure Cosmos DB si applica solo alle operazioni piano di controllo. Le operazioni del piano dati sono protette tramite chiavi master o token di risorse. Per altre informazioni, vedere [Proteggere l'accesso ai dati in Azure Cosmos DBTo](secure-access-to-data.md) learn more, see Secure access to data in Azure Cosmos DB

## <a name="identity-and-access-management-iam"></a>Gestione di identità e accessi (IAM)

Il riquadro **Controllo di accesso (IAM)** nel portale di Azure viene usato per configurare il controllo degli accessi in base al ruolo nelle risorse di Azure Cosmos.The Access control (IAM) pane in the Azure portal is used to configure role-based access control on Azure Cosmos resources. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi. La schermata seguente mostra l'integrazione di Active Directory (RBAC) usando il controllo di accesso (IAM) nel portale di Azure:The following screenshot shows Active Directory integration (RBAC) using access control (IAM) in the Azure portal:

![Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Ruoli personalizzati

Oltre ai ruoli predefiniti, gli utenti possono anche creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure e applicarli alle entità servizio in tutte le sottoscrizioni all'interno del tenant di Active Directory. I ruoli personalizzati forniscono agli utenti un modo per creare definizioni di ruolo RBAC con un set personalizzato di operazioni del provider di risorse. Per informazioni sulle operazioni disponibili per la creazione di ruoli personalizzati per il database Cosmos di Azure, vedere Operazioni del provider di [risorse di Azure Cosmos DBTo](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) learn which operations are available for building custom roles for Azure Cosmos DB see, Azure Cosmos DB resource provider operations

## <a name="preventing-changes-from-cosmos-sdk"></a>Impedire le modifiche da Cosmos SDK

Il provider di risorse Cosmos può essere bloccato per impedire qualsiasi modifica alle risorse, tra cui account Cosmos, database, contenitori e velocità effettiva da qualsiasi client che si connette tramite chiavi di account (ad esempio, applicazioni che si connettono tramite Cosmos SDK). Quando è impostata, le modifiche a qualsiasi risorsa devono provenire da un utente con il ruolo RBAC e le credenziali appropriati. Questa funzionalità viene `disableKeyBasedMetadataWriteAccess` impostata con il valore della proprietà nel provider di risorse Cosmos.This capability is set with property value in the Cosmos resource provider. Di seguito è riportato un esempio di modello di Azure Resource Manager con questa impostazione di proprietà.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il controllo degli accessi in base al ruolo per le risorse di AzureWhat is role-based access control (RBAC) for Azure resources](../role-based-access-control/overview.md)
- [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
- [Operazioni del provider di risorse di Azure Cosmos DBAzure Cosmos DB resource provider operations](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
