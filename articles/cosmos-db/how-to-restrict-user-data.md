---
title: Limitare l'accesso degli utenti alle operazioni sui dati solo con Azure Cosmos DBRestrict user access to data operations only with Azure Cosmos DB
description: Informazioni su come limitare l'accesso alle operazioni sui dati solo con Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980374"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Limitare l'accesso utente solo alle operazioni sui dati

In Azure Cosmos DB esistono due modi per autenticare le interazioni con il servizio di database:In Azure Cosmos DB, there are two ways to authenticate your interactions with the database service:
- utilizzando l'identità di Azure Active Directory durante l'interazione con il portale di Azure,
- uso di [chiavi](secure-access-to-data.md#master-keys) DB Cosmos di Azure o [di token](secure-access-to-data.md#resource-tokens) di risorse durante l'emissione di chiamate da API e SDK.

Ogni metodo di autenticazione consente di accedere ![a diversi set di operazioni, con alcune sovrapposizioni: divisione delle operazioni per tipo di autenticazioneEach authentication method gives access to different sets of operations, with some overlap: Split of operations per authentication type](./media/how-to-restrict-user-data/operations.png)

In alcuni scenari, è possibile limitare alcuni utenti dell'organizzazione per eseguire solo operazioni sui dati (ovvero richieste CRUD e query). Questo è in genere il caso per gli sviluppatori che non hanno bisogno di creare o eliminare risorse o modificare la velocità effettiva di provisioning dei contenitori su cui stanno lavorando.

È possibile limitare l'accesso applicando la procedura seguente:
1. Creazione di un ruolo di Azure Active Directory personalizzato per gli utenti a cui si vuole limitare l'accesso. Il ruolo di Active Directory personalizzato deve avere un livello di accesso granulare alle operazioni usando [le azioni granulari](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)di Azure Cosmos DB.
1. Non consentire l'esecuzione di operazioni non di dati con chiavi. È possibile ottenere questo risultato limitando queste operazioni solo alle chiamate di Azure Resource Manager.You can achieve this by restricting these operations to Azure Resource Manager calls only.

Nelle sezioni successive di questo articolo viene illustrato come eseguire questi passaggi.

> [!NOTE]
> Per eseguire i comandi nelle sezioni successive, è necessario installare Azure PowerShell Module 3.0.0 o versione successiva, nonché il [ruolo proprietario](../role-based-access-control/built-in-roles.md#owner) di Azure nella sottoscrizione che si sta tentando di modificare.

Nelle sezioni successive negli script di PowerShell sostituire i segnaposto seguenti con valori specifici per l'ambiente:
- `$MySubscriptionId`- L'ID sottoscrizione che contiene l'account Cosmos di Azure in cui si vogliono limitare le autorizzazioni.- The subscription ID that contains the Azure Cosmos account where you want to limit the permissions. Ad esempio `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Il gruppo di risorse contenente l'account Cosmos di Azure.- The resource group containing the Azure Cosmos account. Ad esempio `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Il nome dell'account Cosmos di Azure.- The name of your Azure Cosmos account. Ad esempio `mycosmosdbsaccount`.
- `$MyUserName`- Ilusername@domainlogin ( ) dell'utente per il quale si desidera limitare l'accesso. Ad esempio `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Selezionare la sottoscrizione ad Azure

I comandi di Azure PowerShell richiedono l'accesso e la selezione della sottoscrizione per l'esecuzione dei comandi:Azure PowerShell commands require you to login and select the subscription to execute the commands:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Creare il ruolo di Azure Active Directory personalizzatoCreate the custom Azure Active Directory role

Lo script seguente crea un'assegnazione di ruolo di Azure Active Directory con accesso "Solo chiave" per gli account Cosmos di Azure.The following script creates an Azure Active Directory role assignment with "Key Only" access for Azure Cosmos accounts. Il ruolo è basato sui [ruoli personalizzati per](../role-based-access-control/custom-roles.md) le risorse di Azure e sulle azioni [granulari per Azure Cosmos DB.](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) Questi ruoli e azioni `Microsoft.DocumentDB` fanno parte dello spazio dei nomi di Azure Active Directory.These roles and actions are part of the Azure Active Directory namespace.

1. Creare innanzitutto un `AzureCosmosKeyOnlyAccess.json` documento JSON denominato con il contenuto seguente:First, create a JSON document named with the following content:

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

## <a name="disallow-the-execution-of-non-data-operations"></a>Non consentire l'esecuzione di operazioni non relative ai dati

I seguenti comandi rimuovono la possibilità di utilizzare i tasti per:
- creare, modificare o eliminare risorse
- aggiornare le impostazioni del contenitore (inclusi i criteri di indicizzazione, la velocità effettiva e così via).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul [controllo degli accessi in base](role-based-access-control.md) al ruolo di Cosmos DB
- Ottieni una panoramica [dell'accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md)
