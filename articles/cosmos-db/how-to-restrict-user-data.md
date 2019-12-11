---
title: Limitare l'accesso degli utenti alle operazioni sui dati solo con Azure Cosmos DB
description: Informazioni su come limitare l'accesso alle operazioni sui dati solo con Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980374"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Limitazione dell'accesso utente solo alle operazioni sui dati

In Azure Cosmos DB, esistono due modi per autenticare le interazioni con il servizio di database:
- uso dell'identità del Azure Active Directory durante l'interazione con l'portale di Azure,
- uso di [chiavi](secure-access-to-data.md#master-keys) Azure Cosmos DB o [token di risorsa](secure-access-to-data.md#resource-tokens) quando si inviano chiamate da API e SDK.

Ogni metodo di autenticazione consente di accedere a diversi set di operazioni, con alcune sovrapposizioni: ![suddivisione delle operazioni per tipo di autenticazione](./media/how-to-restrict-user-data/operations.png)

In alcuni scenari può essere opportuno limitare l'esecuzione di operazioni sui dati (ovvero solo richieste e query CRUD) ad alcuni utenti dell'organizzazione. Questo è in genere il caso per gli sviluppatori che non hanno bisogno di creare o eliminare risorse o di modificare la velocità effettiva con provisioning dei contenitori su cui stanno lavorando.

È possibile limitare l'accesso applicando i passaggi seguenti:
1. Creazione di un ruolo di Azure Active Directory personalizzato per gli utenti a cui si vuole limitare l'accesso. Il ruolo di Active Directory personalizzato deve avere un livello di accesso con granularità fine alle operazioni usando le [azioni granulari](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)di Azure Cosmos DB.
1. Impedisce l'esecuzione di operazioni non dati con chiavi. È possibile ottenere questo risultato limitando queste operazioni a Azure Resource Manager solo le chiamate.

Le sezioni successive di questo articolo illustrano come eseguire questi passaggi.

> [!NOTE]
> Per eseguire i comandi nelle sezioni successive, è necessario installare Azure PowerShell Module 3.0.0 o versione successiva, nonché il [ruolo di proprietario di Azure](../role-based-access-control/built-in-roles.md#owner) nella sottoscrizione che si sta provando a modificare.

Negli script di PowerShell nelle sezioni successive sostituire i segnaposto seguenti con i valori specifici dell'ambiente:
- `$MySubscriptionId`: ID sottoscrizione che contiene l'account Azure Cosmos in cui si desidera limitare le autorizzazioni. Ad esempio: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`: il gruppo di risorse contenente l'account Azure Cosmos. Ad esempio: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`: il nome dell'account Azure Cosmos. Ad esempio: `mycosmosdbsaccount`.
- `$MyUserName`: l'account di accesso (username@domain) dell'utente per il quale si desidera limitare l'accesso. Ad esempio: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Selezionare la sottoscrizione ad Azure

Per Azure PowerShell comandi è necessario effettuare l'accesso e selezionare la sottoscrizione per l'esecuzione dei comandi:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Creare il ruolo di Azure Active Directory personalizzato

Lo script seguente crea un'assegnazione di ruolo Azure Active Directory con accesso "solo chiave" per gli account Azure Cosmos. Il ruolo è basato sui [ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md) e sulle [azioni granulari per Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Questi ruoli e azioni fanno parte dello spazio dei nomi `Microsoft.DocumentDB` Azure Active Directory.

1. Per prima cosa, creare un documento JSON denominato `AzureCosmosKeyOnlyAccess.json` con il contenuto seguente:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Eseguire i comandi seguenti per creare l'assegnazione di ruolo e assegnarla all'utente:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Non consentire l'esecuzione di operazioni non sui dati

I comandi seguenti consentono di rimuovere la possibilità di usare le chiavi per:
- creare, modificare o eliminare risorse
- aggiornare le impostazioni del contenitore, inclusi i criteri di indicizzazione, la velocità effettiva e così via.

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [controllo degli accessi in base al ruolo Cosmos DB](role-based-access-control.md)
- Ottenere una panoramica dell' [accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md)
