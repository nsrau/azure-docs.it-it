---
title: Creare un ruolo personalizzato di Azure usando un modello di Azure Resource Manager-RBAC di Azure
description: Informazioni su come creare un ruolo personalizzato di Azure usando modelli di Azure Resource Manager e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85397996"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Creare un ruolo personalizzato di Azure usando un modello di Azure Resource Manager

Se i [ruoli predefiniti di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati](custom-roles.md). Questo articolo descrive come creare un ruolo personalizzato usando un modello di Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Per creare un ruolo personalizzato, è necessario disporre di:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, è necessario specificare un nome di ruolo, le autorizzazioni e la posizione in cui è possibile utilizzare il ruolo. In questo articolo viene creato un ruolo denominato "Custom Role-RG Reader" con autorizzazioni per le risorse che possono essere assegnate a un ambito di sottoscrizione o a un livello inferiore.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo articolo è relativo ai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). Il modello include quattro parametri e una sezione Resources. I quattro parametri sono i seguenti:

- Matrice di azioni con un valore predefinito di ["Microsoft. resources/subscriptions/resourceGroups/Read"]
- Matrice di notacts con un valore predefinito vuoto
- Nome ruolo con un valore predefinito di "ruolo personalizzato-lettore RG"
- Descrizione del ruolo con un valore predefinito di "distribuzione a livello di sottoscrizione di una definizione di ruolo"

Nel modello è definita la risorsa seguente:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

L'ambito in cui è possibile assegnare questo ruolo personalizzato è impostato sulla sottoscrizione corrente.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello precedente, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire Azure Cloud Shell per PowerShell.

1. Copiare e incollare lo script seguente in Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Immettere un percorso per la distribuzione, ad esempio *centralus*.

1. Immettere un elenco di azioni per il ruolo personalizzato come un elenco delimitato da virgole *, ad esempio Microsoft. resources/Resources/Read, Microsoft. resources/subscriptions/resourceGroups/Read*.

1. Se necessario, premere INVIO per eseguire il comando New-AzDeployment.

    Il comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) distribuisce il modello per creare il ruolo personalizzato.

    L'output dovrebbe essere simile al seguente:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Attenersi alla seguente procedura per verificare che il ruolo personalizzato sia stato creato.

1. Eseguire il comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) per elencare il ruolo personalizzato.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    L'output dovrebbe essere simile al seguente:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Nella portale di Azure aprire la sottoscrizione.

1. Nel menu a sinistra fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **ruoli** .

1. Impostare l'elenco dei **tipi** su **CustomRole**.

1. Verificare che sia elencato il ruolo **personalizzato lettore RG** .

   ![Nuovo ruolo personalizzato in portale di Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il ruolo personalizzato, attenersi alla seguente procedura.

1. Eseguire il comando seguente per rimuovere il ruolo personalizzato.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Immettere **Y** per confermare che si desidera rimuovere il ruolo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md)
- [Avvio rapido: Aggiungere un'assegnazione di ruolo di Azure tramite un modello di Azure Resource Manager](quickstart-role-assignments-template.md)
- [Documentazione dei modelli di Resource Manager](../azure-resource-manager/templates/index.yml)
