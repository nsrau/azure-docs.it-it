---
title: App gestita con identità gestita
description: Configurare l'applicazione gestita con identità gestita per il collegamento a risorse esistenti, la gestione delle risorse di Azure e la fornitura dell'identità operativa per il log attività.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651657"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure Managed Application with Managed Identity

> [!NOTE]
> Il supporto dell'identità gestita per le applicazioni gestite è attualmente in anteprima. Utilizzare la versione api 2018-09-01-preview per utilizzare Managed Identity.

Informazioni su come configurare un'applicazione gestita per contenere un'identità gestita. L'identità gestita può essere utilizzata per consentire al cliente di concedere all'applicazione gestita l'accesso ad altre risorse esistenti. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in Azure Active Directory (AAD), vedere Identità gestite per le risorse di Azure.For more about managed identities in Azure Active Directory (AAD), see [Managed identities for Azure resources.](../../active-directory/managed-identities-azure-resources/overview.md)

All'applicazione possono essere concessi due tipi di identità:

- Un'**identità assegnata dal sistema** viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema.
- **Un'identità assegnata dall'utente** è una risorsa autonoma di Azure che può essere assegnata all'app. Un'app può avere più identità assegnate dall'utente.

## <a name="how-to-use-managed-identity"></a>Come usare l'identità gestitaHow to use Managed Identity

Managed Identity abilita molti scenari per le applicazioni gestite. Alcuni scenari comuni che possono essere risolti sono:Some common scenarios that can be solved are:

- Distribuzione di un'applicazione gestita collegata alle risorse di Azure esistenti. Un esempio è la distribuzione di una macchina virtuale (VM) di Azure all'interno dell'applicazione gestita collegata a [un'interfaccia](../../virtual-network/virtual-network-network-interface-vm.md)di rete esistente.
- Concessione all'applicazione gestita e all'accesso dell'editore alle risorse di Azure all'esterno del **gruppo di risorse gestite.**
- Fornire un'identità operativa di applicazioni gestite per il log attività e altri servizi all'interno di Azure.Providing an operational identity of Managed Applications for Activity Log and other services within Azure.

## <a name="adding-managed-identity"></a>Aggiunta di identità gestita

La creazione di un'applicazione gestita con un'identità gestita richiede l'impostazione di una proprietà aggiuntiva nella risorsa di Azure.Creating a Managed Application with a Managed Identity requires an additional property to be set on the Azure resource. Nell'esempio seguente viene illustrata una proprietà **di identità** di esempio:The following example shows a sample identity property:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Esistono due modi comuni per creare un'applicazione gestita con **identità:** [CreateUIDefinition.json](./create-uidefinition-overview.md) e i modelli di [Azure Resource Manager](../templates/template-syntax.md). Per semplici scenari di creazione singola, CreateUIDefinition deve essere usato per abilitare l'identità gestita, perché offre un'esperienza più completa. Tuttavia, quando si gestiscono sistemi avanzati o complessi che richiedono distribuzioni automatizzate o più applicazioni gestite, è possibile utilizzare modelli.

### <a name="using-createuidefinition"></a>Uso di CreateUIDefinitionUsing CreateUIDefinition

Un'applicazione gestita può essere configurata con identità gestita tramite [CreateUIDefinition.json](./create-uidefinition-overview.md). Nella [sezione output](./create-uidefinition-overview.md#outputs), `managedIdentity` la chiave può essere utilizzata per eseguire l'override della proprietà identity del modello Applicazione gestita. Il pulsante di sottocontrollo dell'esempio consentirà l'identità **assegnata dal sistema** nell'applicazione gestita. Gli oggetti Identity più complessi possono essere formati usando gli elementi CreateUIDefinition per richiedere gli input al consumer. Questi input possono essere utilizzati per costruire applicazioni gestite con **identità assegnata dall'utente**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando usare CreateUIDefinition per l'identità gestitaWhen to use CreateUIDefinition for Managed Identity

Di seguito sono riportati alcuni suggerimenti su quando usare CreateUIDefinition per abilitare l'identità gestita nelle applicazioni gestite.

- La creazione dell'applicazione gestita passa attraverso il portale o il marketplace di Azure.The Managed Application creation goes through the Azure portal or marketplace.
- L'identità gestita richiede un input gestito complesso.
- L'identità gestita è necessaria per la creazione dell'applicazione gestita.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Oggetto CreateUIDefinition di base che abilita l'identità SystemAssigned per l'applicazione gestita.

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

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Oggetto CreateUIDefinition di base che accetta una risorsa **di identità assegnata dall'utente** come input e abilita l'identità UserAssigned per l'applicazione gestita.

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

CreateUIDefinition.json in precedenza genera un'esperienza utente di creazione che dispone di una casella di testo per un consumer per immettere l'ID di risorsa di **Azure identità assegnata dall'utente.** L'esperienza generata sarebbe simile alla seguente:The generated experience would look like:

![Esempio di identità assegnata dall'utente CreateUIDefinitionSample user-assigned identity CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

> [!NOTE]
> I modelli di applicazione gestita del Marketplace vengono generati automaticamente per i clienti che passano attraverso il portale di Azure per creare esperienza.
> Per questi scenari, la `managedIdentity` chiave di output in CreateUIDefinition deve essere utilizzata per abilitare l'identità.

L'identità gestita può essere abilitata anche tramite i modelli di Azure Resource Manager.The Managed Identity can also be enabled through Azure Resource Manager templates. Il pulsante di sottocontrollo dell'esempio consentirà l'identità **assegnata dal sistema** nell'applicazione gestita. Oggetti di identità più complessi possono essere creati usando i parametri di modello di Azure Resource Manager per fornire input. Questi input possono essere utilizzati per costruire applicazioni gestite con **identità assegnata dall'utente**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usare i modelli di Azure Resource Manager per l'identità gestitaWhen to use Azure Resource Manager templates for Managed Identity

Di seguito sono riportati alcuni consigli su quando usare i modelli di Azure Resource Manager per abilitare l'identità gestita nelle applicazioni gestite.

- Le applicazioni gestite possono essere distribuite a livello di codice in base a un modello.
- Le assegnazioni di ruolo personalizzate per l'identità gestita sono necessarie per eseguire il provisioning dell'applicazione gestita.
- L'applicazione gestita non richiede il flusso di creazione del portale e del marketplace di Azure.The Managed Application does not need the Azure portal and marketplace creation flow.

#### <a name="systemassigned-template"></a>Modello SystemAssigned

Modello di base di Azure Resource Manager che distribuisce un'applicazione gestita con identità **assegnata dal sistema.**

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

Modello di base di Azure Resource Manager che distribuisce un'applicazione gestita con **un'identità assegnata dall'utente.**

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

## <a name="granting-access-to-azure-resources"></a>Concessione dell'accesso alle risorse di AzureGranting access to Azure resources

Dopo che a un'applicazione gestita viene concessa un'identità, è possibile concedere l'accesso alle risorse di Azure esistenti. Questo processo può essere eseguito tramite l'interfaccia di controllo di accesso (IAM) nel portale di Azure.This process can be done through the Access control (IAM) interface in the Azure portal. È possibile cercare il nome dell'applicazione gestita o **dell'identità assegnata dall'utente** per aggiungere un'assegnazione di ruolo.

![Aggiungere l'assegnazione di ruolo per l'applicazione gestitaAdd role assignment for Managed Application](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Collegamento delle risorse di Azure esistenti

> [!NOTE]
> Prima di distribuire l'applicazione gestita, è necessario [configurare](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) **un'identità assegnata dall'utente.** Inoltre, la distribuzione di risorse collegate di applicazioni gestite è supportata solo per il tipo di **marketplace.**

L'identità gestita può essere utilizzata anche per distribuire un'applicazione gestita che richiede l'accesso alle risorse esistenti durante la distribuzione. Quando viene eseguito il provisioning dell'applicazione gestita dal cliente, è possibile aggiungere identità assegnate dall'utente per fornire autorizzazioni aggiuntive alla distribuzione **di mainTemplate.When** the Managed Application is provisioned by the **customer, user-assigned identities** can be added to provide additional authorizations to the mainTemplate deployment.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Creazione di CreateUIDefinition con una risorsa collegata

Quando si collega la distribuzione dell'applicazione gestita alle risorse esistenti, è necessario specificare sia la risorsa di Azure esistente che **un'identità assegnata dall'utente** con l'assegnazione di ruolo applicabile a tale risorsa.

 Un esempio CreateUIDefinition che richiede due input: un ID di risorsa dell'interfaccia di rete e un ID risorsa di identità assegnato dall'utente.

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

CreateUIDefinition.json genera un'esperienza utente di creazione con due campi. Il primo campo consente all'utente di immettere l'ID risorsa di Azure per la risorsa collegata alla distribuzione dell'applicazione gestita. Il secondo è che un consumer immetta l'ID risorsa di Azure **dell'identità assegnata dall'utente,** che ha accesso alla risorsa di Azure collegata. L'esperienza generata sarebbe simile alla seguente:The generated experience would look like:

![Esempio CreateUIDefinition con due input: un ID di risorsa dell'interfaccia di rete e un ID risorsa di identità assegnato dall'utenteSample CreateUIDefinition with two inputs: a network interface resource ID and a user assigned identity resource ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Creazione del mainTemplate con una risorsa collegata

Oltre ad aggiornare CreateUIDefinition, il modello principale deve anche essere aggiornato per accettare l'ID risorsa collegata passato. Il modello principale può essere aggiornato per accettare il nuovo output aggiungendo un nuovo parametro. Poiché `managedIdentity` l'output esegue l'override del valore nel modello di applicazione gestita generato, non viene passato al modello principale e non deve essere incluso nella sezione dei parametri.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Utilizzo dell'applicazione gestita con una risorsa collegataConsuming the Managed Application with a linked resource

Dopo aver creato il pacchetto dell'applicazione gestita, l'applicazione gestita può essere utilizzata tramite il portale di Azure.Once the Managed Application package is created, the Managed Application can be consumed through the Azure portal. Prima che possa essere utilizzato, sono disponibili diversi passaggi preliminari.

- È necessario creare un'istanza della risorsa di Azure collegata richiesta.
- **L'identità assegnata dall'utente** deve essere [creata e assegnare le assegnazioni](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) di ruolo alla risorsa collegata.
- L'ID risorsa collegata esistente e l'ID **identità assegnato dall'utente** vengono forniti a CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Accesso al token di identità gestitaAccessing the Managed Identity token

È ora possibile accedere al token `listTokens` dell'applicazione gestita tramite l'API dal tenant dell'editore. Una richiesta di esempio potrebbe essere simile alla seguente:An example request might look like:

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

Parametro | Obbligatoria | Descrizione
---|---|---
authorizationAudience | *No* | URI ID app della risorsa di destinazione. È anche `aud` l'affermazione (pubblico) del token emesso. Il valore predefinitohttps://management.azure.com/è " "
userAssignedIdentities | *No* | Elenco di identità gestite assegnate dall'utente per cui recuperare un token. Se non `listTokens` specificato, restituirà il token per l'identità gestita assegnata dal sistema.


Una risposta di esempio potrebbe essere simile alla seguente:A sample response might look like:

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

La risposta conterrà una matrice `value` di token nella proprietà:

Parametro | Descrizione
---|---
access_token | Token di accesso richiesto.
expires_in | Numero di secondi per cui il token di accesso sarà valido.
expires_on | Intervallo di tempo in cui il token di accesso scade. Questo è rappresentato come il numero di secondi dall'epoca.
not_before | Intervallo di tempo in cui il token di accesso diventa effettivo. Questo è rappresentato come il numero di secondi dall'epoca.
authorizationAudience | Il `aud` (gruppo di destinatari) per il cui token di accesso era richiesto. Questo è lo stesso di `listTokens` quanto è stato fornito nella richiesta.
resourceId | ID risorsa di Azure per il token rilasciato. Si tratta dell'ID applicazione gestita o dell'ID identità assegnato dall'utente.
token_type | Tipo di token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come configurare un'applicazione gestita con un provider personalizzatoHow to configure a Managed Application with a Custom Provider](../custom-providers/overview.md)
