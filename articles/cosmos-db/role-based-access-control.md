---
title: Controllo degli accessi in base al ruolo in Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB fornisce la protezione dei database con l'integrazione di Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: cbb97dd260e5aee53595afc24e577ce08334e2b2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027019"
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
|[Operatore di Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Può effettuare il provisioning di account, database e contenitori di Azure Cosmos ma non può accedere alle chiavi necessarie per accedere ai dati.|

> [!IMPORTANT]
> Il supporto RBAC in Azure Cosmos DB si applica solo alle operazioni del piano di controllo. Le operazioni del piano dati sono protette tramite chiavi master o token delle risorse. Per altre informazioni, vedere [proteggere l'accesso ai dati in Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestione delle identità e degli accessi (IAM)

Il riquadro di **controllo di accesso (IAM)** nella portale di Azure viene usato per configurare il controllo degli accessi in base al ruolo nelle risorse di Azure Cosmos. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi. Lo screenshot seguente mostra l'integrazione di Active Directory (RBAC) tramite il controllo di accesso (IAM) nel portale di Azure:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database":::

## <a name="custom-roles"></a>Ruoli personalizzati

Oltre ai ruoli predefiniti, gli utenti possono anche creare [ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure e applicare questi ruoli alle entità servizio in tutte le sottoscrizioni all'interno del tenant Active Directory. I ruoli personalizzati forniscono agli utenti un modo per creare definizioni di ruolo RBAC con un set personalizzato di operazioni del provider di risorse. Per informazioni sulle operazioni disponibili per la creazione di ruoli personalizzati per Azure Cosmos DB vedere [Azure Cosmos DB operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Prevenzione delle modifiche da Cosmos SDK

> [!WARNING]
> L'abilitazione di questa funzionalità può avere un effetto pericoloso sull'applicazione. Prima di abilitare questa funzionalità, leggere attentamente.

Il provider di risorse Azure Cosmos DB può essere bloccato per evitare modifiche alle risorse effettuate da qualsiasi client che si connette usando le chiavi dell'account (ad esempio, le applicazioni che si connettono tramite Cosmos SDK). Sono incluse anche le modifiche apportate dal portale di Azure. Questo può essere utile per gli utenti che desiderano livelli superiori di controllo e governance per gli ambienti di produzione e abilitano funzionalità quali i blocchi delle risorse e abilitano anche i log di diagnostica per le operazioni del piano di controllo. I client che si connettono tramite Cosmos DB SDK non potranno modificare alcuna proprietà per gli account, i database, i contenitori e la velocità effettiva di Cosmos. Le operazioni che comportano la lettura e la scrittura dei dati nei contenitori Cosmos non sono interessate.

Quando è impostato, le modifiche apportate a qualsiasi risorsa possono essere effettuate solo da un utente con il ruolo controllo degli accessi in base al ruolo e Azure Active Directory credenziali, incluse le identità del servizio gestito

### <a name="check-list-before-enabling"></a>Elenco di controllo prima dell'abilitazione

Questa impostazione impedisce qualsiasi modifica a qualsiasi risorsa Cosmos da qualsiasi client che si connette usando chiavi dell'account, inclusi i Cosmos DB SDK, tutti gli strumenti che si connettono tramite chiavi dell'account o dal portale di Azure. Per evitare problemi o errori delle applicazioni dopo l'abilitazione di questa funzionalità, verificare se le applicazioni o portale di Azure utenti eseguono una delle azioni seguenti prima di abilitare questa funzionalità, tra cui:

- Qualsiasi modifica apportata all'account Cosmos, incluse le proprietà o l'aggiunta o la rimozione di aree.

- Creazione, eliminazione di risorse figlio quali database e contenitori. Sono incluse le risorse per altre API, ad esempio Cassandra, MongoDB, Gremlin e risorse tabella.

- Aggiornamento della velocità effettiva per le risorse di database o a livello di contenitore.

- Modifica delle proprietà del contenitore, tra cui criteri di indice, TTL e chiavi univoche.

- Modifica di stored procedure, trigger o funzioni definite dall'utente.

Se le applicazioni (o gli utenti tramite portale di Azure) eseguono una di queste azioni, sarà necessario eseguirne la migrazione per l'esecuzione tramite [modelli ARM](manage-sql-with-resource-manager.md), [PowerShell](manage-with-powershell.md), l'interfaccia della riga di comando di [Azure](manage-with-cli.md), [Rest](/rest/api/cosmos-db-resource-provider/) o la [libreria di gestione di Azure](https://github.com/Azure-Samples/cosmos-management-net). Si noti che la gestione di Azure è disponibile in [più lingue](https://docs.microsoft.com/azure/?product=featured#languages-and-tools).

### <a name="set-via-arm-template"></a>Imposta tramite il modello ARM

Per impostare questa proprietà usando un modello di Azure Resource Manager, aggiornare il modello esistente o esportare un nuovo modello per la distribuzione corrente, quindi includere le nelle `"disableKeyBasedMetadataWriteAccess": true` proprietà per le risorse databaseAccounts. Di seguito è riportato un esempio di base di un modello di Azure Resource Manager con questa impostazione di proprietà.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
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

> [!IMPORTANT]
> Assicurarsi di includere le altre proprietà per l'account e le risorse figlio quando si redploying con questa proprietà. Non distribuire questo modello così come è o verrà reimpostato tutte le proprietà dell'account.

### <a name="set-via-azure-cli"></a>Imposta tramite l'interfaccia della riga di comando

Per abilitare l'uso dell'interfaccia della riga di comando di Azure usare il comando seguente:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Imposta tramite PowerShell

Per abilitare l'utilizzo di Azure PowerShell, utilizzare il comando seguente:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md)
- [Ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md)
- [Operazioni del provider di risorse Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
