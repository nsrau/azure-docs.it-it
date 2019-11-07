---
title: Controllo degli accessi in base al ruolo in Azure Cosmos DB con integrazione Azure Active Directory
description: Informazioni su come Azure Cosmos DB fornisce la protezione dei database con l'integrazione di Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: e519df8c116244b0c74be6b189d99599d89dee77
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582836"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controllo degli accessi in base al ruolo in Azure Cosmos DB

Azure Cosmos DB fornisce il controllo degli accessi in base al ruolo (RBAC) predefinito per gli scenari di gestione comuni in Azure Cosmos DB. Un utente che dispone di un profilo in Azure Active Directory può assegnare questi ruoli RBAC a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso a risorse e operazioni su Azure Cosmos DB risorse. Le assegnazioni di ruolo hanno come ambito solo l'accesso al piano di controllo, che include l'accesso ad account, database, contenitori e offerte (velocità effettiva) di Azure Cosmos.

## <a name="built-in-roles"></a>Ruoli predefiniti

Di seguito sono riportati i ruoli predefiniti supportati da Azure Cosmos DB:

|**Ruolo predefinito**  |**Descrizione**  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB.|
|[Lettore account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB.|
|[Operatore Cosmos backup](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Può inviare la richiesta di ripristino per un database o un contenitore di Azure Cosmos.|
|[Operatore Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Può effettuare il provisioning di account, database e contenitori di Azure Cosmos ma non può accedere alle chiavi necessarie per accedere ai dati.|

> [!IMPORTANT]
> Il supporto RBAC in Azure Cosmos DB si applica solo alle operazioni del piano di controllo. Le operazioni del piano dati sono protette tramite chiavi master o token delle risorse. Per altre informazioni, vedere [proteggere l'accesso ai dati in Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identity and Access Management (IAM)

Il riquadro di **controllo di accesso (IAM)** nella portale di Azure viene usato per configurare il controllo degli accessi in base al ruolo nelle risorse di Azure Cosmos. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi. Lo screenshot seguente mostra l'integrazione di Active Directory (RBAC) tramite il controllo di accesso (IAM) nel portale di Azure:

![Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Ruoli personalizzati

Oltre ai ruoli predefiniti, gli utenti possono anche creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure e applicare questi ruoli alle entità servizio in tutte le sottoscrizioni all'interno del tenant Active Directory. I ruoli personalizzati forniscono agli utenti un modo per creare definizioni di ruolo RBAC con un set personalizzato di operazioni del provider di risorse. Per informazioni sulle operazioni disponibili per la creazione di ruoli personalizzati per Azure Cosmos DB vedere [Azure Cosmos DB operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Prevenzione delle modifiche da Cosmos SDK

Il provider di risorse Cosmos può essere bloccato per evitare modifiche alle risorse, ad esempio l'account Cosmos, i database, i contenitori e la velocità effettiva da qualsiasi client che si connette tramite chiavi dell'account (ad esempio, le applicazioni che si connettono tramite Cosmos SDK). Quando è impostato, le modifiche apportate a qualsiasi risorsa devono provenire da un utente con il ruolo e le credenziali del controllo RBAC appropriato. Questa funzionalità viene impostata con `disableKeyBasedMetadataWriteAccess` valore della proprietà nel provider di risorse Cosmos. Di seguito è riportato un esempio di modello di Azure Resource Manager con questa impostazione di proprietà.

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

- [Informazioni sul controllo degli accessi in base al ruolo (RBAC) per le risorse di Azure](../role-based-access-control/overview.md)
- [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
- [Operazioni del provider di risorse Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
