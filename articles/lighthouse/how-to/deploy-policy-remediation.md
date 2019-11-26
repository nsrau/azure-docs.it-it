---
title: Distribuire un criterio che è possibile correggere
description: Informazioni su come eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure, consentendo l'accesso e la gestione delle risorse tramite il proprio tenant.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: 4522c9ebad741f5ec0cb7e56e68467312ef8f037
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463886"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata

[Azure Lighthouse](../overview.md) consente ai provider di servizi di creare e modificare le definizioni dei criteri in una sottoscrizione delegata. Tuttavia, per distribuire i criteri che usano una [attività di correzione](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (ovvero criteri con effetto [deployIfNotExists ](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) o [modify](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)), è necessario creare una [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nel tenant del cliente. Questa identità gestita può essere usata da Criteri di Azure per distribuire il modello all'interno del criterio. Per abilitare questo scenario sono necessari alcuni passaggi, sia quando si esegue l'onboarding del cliente per la gestione delle risorse delegate di Azure, sia quando si distribuisce il criterio stesso.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Creare un utente che può assegnare ruoli a un'identità gestita nel tenant del cliente

Quando si esegue l'onboarding di un cliente per la gestione delle risorse delegate di Azure, si usa un [modello di Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) insieme a un file di parametri che definisce gli utenti, i gruppi di utenti e le entità servizio nel tenant di gestione che riusciranno ad accedere alle risorse delegate nel tenant del cliente. Nel file dei parametri, a ognuno di questi utenti (**principalId** ) viene assegnato un ruolo [predefinito](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId**) che definisce il livello di accesso.

Per consentire a un **principalId**  di creare un'identità gestita nel tenant del cliente, è necessario impostare il relativo **roleDefinitionId** su **Amministratore accessi utente**. Anche se questo ruolo non è generalmente supportato, può essere usato in questo scenario specifico, consentendo agli utenti con questa autorizzazione di assegnare uno o più ruoli predefiniti specifici alle identità gestite. Questi ruoli sono definiti nella proprietà **delegatedRoleDefinitionIds**. È possibile includere qualsiasi ruolo predefinito, ad eccezione del Proprietario o Amministratore Accesso utenti.

Dopo aver eseguito l'onboarding del cliente, il **principalId** creato in questa autorizzazione potrà assegnare questi ruoli predefiniti alle identità gestite nel tenant del cliente. Tuttavia, nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà assegnata a questo cliente.

L'esempio seguente illustra un **principalId** che avrà il ruolo di Amministratore accesso utenti. This user will be able to assign two built-in roles to managed identities in the customer tenant: Contributor and Log Analytics Contributor.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Distribuire criteri che è possibile correggere

Dopo averlo creato come descritto in precedenza, l'utente con le autorizzazioni necessarie può distribuire nel tenant del cliente criteri che usano le attività di correzione.

Si immagini, ad esempio, di voler abilitare la diagnostica sulle risorse di Azure Key Vault nel tenant del cliente, come illustrato in questo [esempio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). Un utente nel tenant di gestione con le autorizzazioni appropriate (come descritto in precedenza) distribuirà un [modello di Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) per abilitare questo scenario.

Si noti che la creazione dell'assegnazione di criteri da usare con una sottoscrizione delegata deve essere eseguita tramite le API, non nel portale di Azure. Quando si esegue questa operazione, è necessario impostare **apiVersion** su **2019-04-01-Preview**, che include la nuova proprietà **delegatedManagedIdentityResourceId**. Questa proprietà consente di includere un'identità gestita che risiede nel tenant del cliente (in una sottoscrizione o in un gruppo di risorse di cui è stato eseguito l'onboarding in Gestione risorse delegate di Azure).

L'esempio seguente illustra un'assegnazione di ruolo con un **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> È disponibile un [esempio simile](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) che dimostra come distribuire un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/).
- Informazioni sulle [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
