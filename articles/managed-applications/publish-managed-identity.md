---
title: Applicazione gestita di Azure con identità gestita
description: Configurare un'applicazione gestita con identità gestita per il collegamento a risorse esistenti, la gestione delle risorse di Azure e la fornitura di identità operativa per il log attività.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 9e1f5072921104c749a0acef95b7da09f1cbb662
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330234"
---
# <a name="azure-managed-application-with-managed-identity"></a>Applicazione gestita di Azure con identità gestita

> [!NOTE]
> Il supporto per le identità gestite per le applicazioni gestite è attualmente in anteprima. Usare la versione API 2018-09-01-Preview per usare l'identità gestita.

Informazioni su come configurare un'applicazione gestita in modo che contenga un'identità gestita. L'identità gestita può essere usata per consentire al cliente di concedere l'accesso alle applicazioni gestite a risorse esistenti aggiuntive. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in Azure Active Directory (AAD), vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- Un'**identità assegnata dal sistema** viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema.
- Un' **identità assegnata dall'utente** è una risorsa di Azure autonoma che può essere assegnata all'app. Un'app può avere più identità assegnate dall'utente.

## <a name="how-to-use-managed-identity"></a>Come usare l'identità gestita

L'identità gestita consente molti scenari per le applicazioni gestite. Di seguito sono riportati alcuni scenari comuni che possono essere risolti:

- Distribuzione di un'applicazione gestita collegata a risorse di Azure esistenti. Un esempio è la distribuzione di una macchina virtuale (VM) di Azure all'interno dell'applicazione gestita collegata a un' [interfaccia di rete esistente](../virtual-network/virtual-network-network-interface-vm.md).
- Concessione dell'accesso all'applicazione gestita e al server di pubblicazione alle risorse di Azure all'esterno del **gruppo di risorse gestite**.
- Fornire un'identità operativa delle applicazioni gestite per il log attività e altri servizi in Azure.

## <a name="adding-managed-identity"></a>Aggiunta dell'identità gestita

Per la creazione di un'applicazione gestita con un'identità gestita è necessario impostare una proprietà aggiuntiva nella risorsa di Azure. Nell'esempio seguente viene illustrata una proprietà **Identity** di esempio:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Esistono due modi comuni per creare un'applicazione gestita con **identità**: [CreateUIDefinition. JSON](./create-uidefinition-overview.md) e [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Per gli scenari di creazione singola semplice, CreateUIDefinition deve essere usato per abilitare l'identità gestita, perché offre un'esperienza più completa. Tuttavia, quando si gestiscono sistemi avanzati o complessi che necessitano di distribuzioni di applicazioni gestite o automatizzate, è possibile usare i modelli.

### <a name="using-createuidefinition"></a>Uso di CreateUIDefinition

Un'applicazione gestita può essere configurata con l'identità gestita tramite [CreateUIDefinition. JSON](./create-uidefinition-overview.md). Nella [sezione Outputs](./create-uidefinition-overview.md#outputs), la chiave `managedIdentity` può essere usata per eseguire l'override della proprietà Identity del modello di applicazione gestita. Il muggito di esempio consentirà di abilitare l'identità **assegnata dal sistema** nell'applicazione gestita. È possibile formare oggetti Identity più complessi usando gli elementi CreateUIDefinition per chiedere al consumer gli input. Questi input possono essere usati per costruire applicazioni gestite con **identità assegnata dall'utente**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando usare CreateUIDefinition per l'identità gestita

Di seguito sono riportate alcune indicazioni su quando usare CreateUIDefinition per abilitare l'identità gestita nelle applicazioni gestite.

- La creazione di applicazioni gestite passa attraverso il portale di Azure o il Marketplace.
- L'identità gestita richiede un input del consumer complesso.
- L'identità gestita è necessaria per la creazione dell'applicazione gestita.

#### <a name="systemassigned-createuidefinition"></a>CreateUIDefinition SystemAssigned

CreateUIDefinition di base che Abilita l'identità SystemAssigned per l'applicazione gestita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>CreateUIDefinition UserAssigned

Un CreateUIDefinition di base che accetta come input una risorsa di **identità assegnata dall'utente** e Abilita l'identità UserAssigned per l'applicazione gestita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Il file CreateUIDefinition. JSON precedente genera un'esperienza utente per la creazione di una casella di testo che consente a un consumer di immettere l'ID risorsa di Azure dell' **identità assegnata dall'utente** . L'esperienza generata sarà simile alla seguente:

![Esempio di identità assegnata dall'utente CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

> [!NOTE]
> I modelli di applicazione gestita del Marketplace vengono generati automaticamente per i clienti che passano attraverso il portale di Azure creare un'esperienza.
> Per questi scenari, è necessario usare la chiave di output `managedIdentity` in CreateUIDefinition per abilitare l'identità.

L'identità gestita può anche essere abilitata tramite modelli Azure Resource Manager. Il muggito di esempio consentirà di abilitare l'identità **assegnata dal sistema** nell'applicazione gestita. È possibile formare oggetti Identity più complessi usando Azure Resource Manager parametri di modello per fornire input. Questi input possono essere usati per costruire applicazioni gestite con **identità assegnata dall'utente**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usare Azure Resource Manager modelli per l'identità gestita

Di seguito sono riportate alcune indicazioni su quando usare i modelli Azure Resource Manager per abilitare l'identità gestita nelle applicazioni gestite.

- Le applicazioni gestite possono essere distribuite a livello di codice in base a un modello.
- Le assegnazioni di ruolo personalizzate per l'identità gestita sono necessarie per eseguire il provisioning dell'applicazione gestita.
- L'applicazione gestita non necessita del flusso di creazione portale di Azure e del Marketplace.

#### <a name="systemassigned-template"></a>Modello SystemAssigned

Modello di Azure Resource Manager di base che distribuisce un'applicazione gestita con identità **assegnata dal sistema** .

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Modello UserAssigned

Modello di Azure Resource Manager di base che distribuisce un'applicazione gestita con un' **identità assegnata dall'utente**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Concessione dell'accesso alle risorse di Azure

Una volta che a un'applicazione gestita viene concessa un'identità, è possibile concedere l'accesso alle risorse di Azure esistenti. Questo processo può essere eseguito tramite l'interfaccia di controllo di accesso (IAM) nel portale di Azure. È possibile cercare il nome dell'applicazione gestita o l' **identità assegnata dall'utente** per aggiungere un'assegnazione di ruolo.

![Aggiunta dell'assegnazione di ruolo per l'applicazione gestita](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Collegamento di risorse di Azure esistenti

> [!NOTE]
> Prima di distribuire l'applicazione gestita, è necessario [configurare](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) un' **identità assegnata dall'utente** . Inoltre, la distribuzione delle risorse collegate delle applicazioni gestite è supportata solo per il tipo di **Marketplace** .

L'identità gestita può essere usata anche per distribuire un'applicazione gestita che richiede l'accesso alle risorse esistenti durante la distribuzione. Quando viene effettuato il provisioning dell'applicazione gestita da parte del cliente, è possibile aggiungere **identità assegnate dall'utente** per fornire ulteriori autorizzazioni alla distribuzione di **mainTemplate** .

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Creazione di CreateUIDefinition con una risorsa collegata

Quando si collega la distribuzione dell'applicazione gestita a risorse esistenti, è necessario fornire sia la risorsa di Azure esistente che un' **identità assegnata dall'utente** con l'assegnazione di ruolo applicabile su tale risorsa.

 Un CreateUIDefinition di esempio che richiede due input: un ID risorsa dell'interfaccia di rete e un ID risorsa di identità assegnato dall'utente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Questo CreateUIDefinition. JSON genera un'esperienza utente di creazione con due campi. Il primo campo consente all'utente di immettere l'ID risorsa di Azure per la risorsa collegata alla distribuzione di applicazioni gestite. Il secondo è che un utente deve immettere l'ID risorsa di Azure **Identity assegnato dall'utente** , che ha accesso alla risorsa di Azure collegata. L'esperienza generata sarà simile alla seguente:

![CreateUIDefinition di esempio con due input: un ID risorsa dell'interfaccia di rete e un ID risorsa di identità assegnato dall'utente](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Creazione di mainTemplate con una risorsa collegata

Oltre ad aggiornare il CreateUIDefinition, è necessario aggiornare anche il modello principale in modo da accettare l'ID della risorsa collegata passata. Il modello principale può essere aggiornato in modo da accettare il nuovo output aggiungendo un nuovo parametro. Poiché l'output `managedIdentity` sostituisce il valore nel modello di applicazione gestita generato, non viene passato al modello principale e non deve essere incluso nella sezione Parameters.

Modello principale di esempio che imposta il profilo di rete su un'interfaccia di rete esistente fornita da CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Utilizzo dell'applicazione gestita con una risorsa collegata

Una volta creato il pacchetto dell'applicazione gestita, l'applicazione gestita può essere utilizzata tramite il portale di Azure. Per poter essere utilizzato, è necessario eseguire diversi passaggi dei prerequisiti.

- È necessario creare un'istanza della risorsa di Azure collegata necessaria.
- È necessario creare l' **identità assegnata dall'utente** [e assegnare le assegnazioni di ruolo](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) alla risorsa collegata.
- L'ID di risorsa collegato esistente e l'ID **identità assegnato dall'utente** vengono forniti a CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Accesso al token di identità gestito

È ora possibile accedere al token dell'applicazione gestita tramite l'API `listTokens` dal tenant del server di pubblicazione. Una richiesta di esempio può essere simile alla seguente:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametri del corpo della richiesta:

Parametro | Obbligatoria | Description
---|---|---
authorizationAudience | *no* | URI ID app della risorsa di destinazione. È anche l'attestazione `aud` (audience) del token emesso. Il valore predefinito è "https://management.azure.com/".
userAssignedIdentities | *no* | Elenco di identità gestite assegnate dall'utente per cui recuperare un token. Se non specificato, `listTokens` restituirà il token per l'identità gestita assegnata dal sistema.


Una risposta di esempio può essere simile alla seguente:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

La risposta conterrà una matrice di token sotto la proprietà `value`:

Parametro | Description
---|---
access_token | Token di accesso richiesto.
expires_in | Numero di secondi per cui il token di accesso sarà valido.
expires_on | Intervallo di tempo in cui il token di accesso scade. Questa operazione viene rappresentata come numero di secondi da Epoch.
not_before | TimeSpan quando viene applicato il token di accesso. Questa operazione viene rappresentata come numero di secondi da Epoch.
authorizationAudience | Il `aud` (audience) per cui è stato richiesto il token di accesso. Corrisponde a quanto specificato nella richiesta `listTokens`.
ResourceId | ID risorsa di Azure per il token emesso. Si tratta dell'ID dell'applicazione gestita o dell'ID identità assegnato dall'utente.
token_type | Tipo del token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come configurare un'applicazione gestita con un provider personalizzato](./custom-providers-overview.md)
