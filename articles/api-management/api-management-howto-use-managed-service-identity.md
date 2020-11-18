---
title: Usare identità gestite in gestione API di Azure | Microsoft Docs
description: Informazioni su come creare identità assegnate dal sistema e assegnate dall'utente in gestione API usando il portale di Azure, PowerShell e un modello di Gestione risorse.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: db1a8238cf9ddae57d73438d43daa54294ce6860
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686226"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Usare identità gestite in gestione API di Azure

Questo articolo illustra come creare un'identità gestita per un'istanza di gestione API di Azure e come accedere ad altre risorse. Un'identità gestita generata da Azure Active Directory (Azure AD) consente all'istanza di gestione API di accedere in modo semplice e sicuro ad altre risorse protette da Azure AD, ad esempio Azure Key Vault. Azure gestisce questa identità, quindi non è necessario effettuare il provisioning o ruotare i segreti. Per altre informazioni sulle identità gestite, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

È possibile concedere due tipi di identità a un'istanza di gestione API:

- Un' *identità assegnata dal sistema* è associata al servizio e viene eliminata se il servizio viene eliminato. Il servizio può disporre di una sola identità assegnata dal sistema.
- Un' *identità assegnata dall'utente* è una risorsa di Azure autonoma che può essere assegnata al servizio. Il servizio può avere più identità assegnate dall'utente.

## <a name="create-a-system-assigned-managed-identity"></a>Creare un'identità gestita assegnata dal sistema

### <a name="azure-portal"></a>Portale di Azure

Per configurare un'identità gestita nel portale di Azure, creare prima di tutto un'istanza di gestione API e quindi abilitare la funzionalità.

1. Creare un'istanza di Gestione API nel portale come di consueto. Passare al portale.
2. Selezionare **identità gestite**.
3. Nella scheda **sistema assegnato** , impostare **stato** **su** attivato. Selezionare **Salva**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Selezioni per l'abilitazione di un'identità gestita assegnata dal sistema" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'istanza di gestione API e assegnarle un'identità usando Azure PowerShell. 

1. Se necessario, installare Azure PowerShell usando le istruzioni disponibili nella [guida Azure PowerShell](/powershell/azure/install-az-ps). Quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

2. Usare il codice seguente per creare l'istanza. Per altri esempi su come usare Azure PowerShell con un'istanza di gestione API, vedere [esempi di PowerShell per gestione API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Aggiornare un'istanza esistente per creare l'identità:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

È possibile creare un'istanza di Gestione API con un'identità includendo la proprietà seguente nella definizione della risorsa:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Questa proprietà indica ad Azure di creare e gestire l'identità per l'istanza di Gestione API.

Ad esempio, un modello completo di Azure Resource Manager può essere simile al seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Quando l'istanza viene creata, presenta le proprietà aggiuntive seguenti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

La `tenantId` proprietà identifica la Azure ad tenant a cui appartiene l'identità. La `principalId` proprietà è un identificatore univoco per la nuova identità dell'istanza. All'interno Azure AD, l'entità servizio ha lo stesso nome assegnato all'istanza di gestione API.


> [!NOTE]
> Un'istanza di gestione API può avere contemporaneamente sia le identità assegnate dal sistema che quelle assegnate dall'utente. In questo caso, la `type` proprietà sarà `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Scenari supportati che usano l'identità assegnata dal sistema

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Ottenere un certificato TLS/SSL personalizzato per l'istanza di gestione API da Azure Key Vault
È possibile usare l'identità assegnata dal sistema di un'istanza di gestione API per recuperare i certificati TLS/SSL personalizzati archiviati in Azure Key Vault. È quindi possibile assegnare questi certificati ai domini personalizzati nell'istanza di gestione API. Tenere presente quanto segue:

- Il tipo di contenuto del segreto deve essere *Application/x-PKCS12*.
- Usare l'endpoint del segreto del certificato Key Vault, che contiene il segreto.

> [!Important]
> Se non si specifica la versione dell'oggetto del certificato, gestione API otterrà automaticamente la versione più recente del certificato entro quattro ore dopo l'aggiornamento in Key Vault.

L'esempio seguente mostra un modello di Azure Resource Manager che contiene i passaggi seguenti:

1. Creare un'istanza di gestione API con un'identità gestita.
2. Aggiornare i criteri di accesso dell'istanza di Azure Key Vault e consentire all'istanza di Gestione API di ottenere i segreti da essa.
3. Aggiornare l'istanza di Gestione API impostando un nome di dominio personalizzato tramite un certificato ottenuto dall'istanza di Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Eseguire l'autenticazione al back-end usando un'identità di gestione API

È possibile usare l'identità assegnata dal sistema per autenticare il back-end tramite il criterio di [autenticazione gestito dall'identità](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

> [!NOTE]
> È possibile associare un'istanza di gestione API a un massimo di 10 identità gestite assegnate dall'utente.

### <a name="azure-portal"></a>Portale di Azure

Per configurare un'identità gestita nel portale, creare prima di tutto un'istanza di gestione API e quindi abilitare la funzionalità.

1. Creare un'istanza di Gestione API nel portale come di consueto. Passare al portale.
2. Selezionare **identità gestite**.
3. Nella scheda **assegnato dall'utente** selezionare **Aggiungi**.
4. Cercare l'identità creata in precedenza e selezionarla. Selezionare **Aggiungi**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Selezioni per l'abilitazione di un'identità gestita assegnata dall'utente" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'istanza di gestione API e assegnarle un'identità usando Azure PowerShell. 

1. Se necessario, installare il Azure PowerShell usando le istruzioni disponibili nella [guida Azure PowerShell](/powershell/azure/install-az-ps). Quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

2. Usare il codice seguente per creare l'istanza. Per altri esempi su come usare Azure PowerShell con un'istanza di gestione API, vedere [esempi di PowerShell per gestione API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Aggiornare un servizio esistente per assegnare un'identità al servizio:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

È possibile creare un'istanza di Gestione API con un'identità includendo la proprietà seguente nella definizione della risorsa:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

L'aggiunta del tipo assegnato dall'utente indica ad Azure di usare l'identità assegnata dall'utente specificata per l'istanza.

Ad esempio, un modello completo di Azure Resource Manager può essere simile al seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Quando il servizio viene creato, dispone delle proprietà aggiuntive seguenti:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

La `principalId` proprietà è un identificatore univoco per l'identità utilizzata per l'amministrazione Azure ad. La `clientId` proprietà è un identificatore univoco per la nuova identità dell'applicazione usata per specificare l'identità da usare durante le chiamate di Runtime.

> [!NOTE]
> Un'istanza di gestione API può avere contemporaneamente sia le identità assegnate dal sistema che quelle assegnate dall'utente. In questo caso, la `type` proprietà sarà `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Scenari supportati mediante identità gestita assegnata dall'utente

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Ottenere un certificato TLS/SSL personalizzato per l'istanza di gestione API da Azure Key Vault
È possibile usare qualsiasi identità assegnata dall'utente per stabilire una relazione di trust tra un'istanza di gestione API e l'insieme di credenziali delle credenziali. Questa relazione di trust può quindi essere usata per recuperare i certificati TLS/SSL personalizzati archiviati in Azure Key Vault. È quindi possibile assegnare questi certificati ai domini personalizzati nell'istanza di gestione API. 

Tenere presente quanto segue:

- Il tipo di contenuto del segreto deve essere *Application/x-PKCS12*.
- Usare l'endpoint del segreto del certificato Key Vault, che contiene il segreto.

> [!Important]
> Se non si specifica la versione dell'oggetto del certificato, gestione API otterrà automaticamente la versione più recente del certificato entro quattro ore dopo l'aggiornamento in Key Vault.

Per il modello completo, vedere [gestione API con l'autenticazione basata su Vault basata sull'identità utente assegnata dall'utente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

In questo modello, verrà distribuito quanto segue:

* Gestione API di Azure
* Identità assegnata dall'utente gestito di Azure
* Azure Vault per archiviare il certificato SSL/TLS

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Eseguire l'autenticazione al back-end usando un'identità assegnata dall'utente

È possibile usare l'identità assegnata dall'utente per eseguire l'autenticazione al back-end tramite il criterio di [autenticazione gestito dall'identità](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="remove-an-identity"></a><a name="remove"></a>Rimuovere un'identità

È possibile rimuovere un'identità assegnata dal sistema disabilitando la funzionalità tramite il portale o il modello di Azure Resource Manager nello stesso modo in cui è stato creato. Le identità assegnate dall'utente possono essere rimosse separatamente. Per rimuovere tutte le identità, impostare il tipo di identità su `"None"` .

La rimozione di un'identità assegnata dal sistema in questo modo ne comporta l'eliminazione anche da Azure AD. Anche le identità assegnate dal sistema vengono rimosse automaticamente da Azure AD quando viene eliminata l'istanza di gestione API.

Per rimuovere tutte le identità usando il modello di Azure Resource Manager, aggiornare questa sezione:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se un'istanza di gestione API è configurata con un certificato SSL personalizzato da Key Vault e si tenta di disabilitare un'identità gestita, la richiesta avrà esito negativo.
>
> È possibile sbloccare autonomamente passando da un certificato Azure Key Vault a un certificato codificato in linea e quindi disabilitando l'identità gestita. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato](configure-custom-domain.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle identità gestite per le risorse di Azure:

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelli di Gestione risorse di Azure](https://github.com/Azure/azure-quickstart-templates)
* [Eseguire l'autenticazione con un'identità gestita in un criterio](./api-management-authentication-policies.md#ManagedIdentity)
