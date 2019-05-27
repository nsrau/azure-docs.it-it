---
title: Applicazione gestita di Azure con identità gestita
description: Informazioni su come configurare un'applicazione gestita con un'identità gestita. Identità gestita può essere utilizzata per distribuire le applicazioni gestite di collegato alle risorse esistenti, concedere le applicazioni gestite per gestire le risorse di Azure all'esterno del gruppo di risorse gestite e fornire un'identità operativa delle applicazioni gestite per il Log attività e altri servizi all'interno di Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003447"
---
# <a name="azure-managed-application-with-managed-identity"></a>Applicazione gestita di Azure con identità gestita

> [!NOTE]
> Supporto di identità gestita per le applicazioni gestite è attualmente in anteprima. Usare la versione dell'api 2018-09-01-preview di utilizzare identità gestita.

Informazioni su come configurare un'applicazione gestita per contenere un'identità gestita. Identità gestita può essere utilizzata per consentire al cliente di concedere l'accesso di un'applicazione gestita a ulteriori risorse esistenti. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in Azure Active Directory (AAD), vedere [gestite le identità per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- Un'**identità assegnata dal sistema** viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema.
- Oggetto **identità assegnata dall'utente** è una risorsa di Azure che può essere assegnato all'App autonoma. Un'app può avere più identità assegnate dall'utente.

## <a name="how-to-use-managed-identity"></a>Come usare l'identità gestita

Managed Identity consente molti scenari per le applicazioni gestite. Alcuni scenari comuni che possono essere risolti sono:

- Distribuzione di un'applicazione gestita collegato alle risorse di Azure esistente. Un esempio è la distribuzione di una macchina virtuale di Azure (VM) all'interno dell'applicazione gestita che è collegato a un [interfaccia di rete esistente](../virtual-network/virtual-network-network-interface-vm.md).
- Concede l'accesso di un'applicazione gestita e server di pubblicazione per le risorse di Azure di fuori di **gruppo di risorse gestite**.
- Fornendo un'identità operativa delle applicazioni gestite per Log attività e altri servizi all'interno di Azure.

## <a name="adding-managed-identity"></a>Aggiunta di identità gestita

Creazione di un'applicazione gestita con un'identità gestita richiede una proprietà aggiuntiva da impostare per la risorsa di Azure. L'esempio seguente mostra un campione **identità** proprietà:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Esistono due modi comuni per creare un'applicazione gestita con **identità**: [Createuidefinition. JSON](./create-uidefinition-overview.md) e [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Per un singolo semplice creare scenari, CreateUIDefinition deve essere utilizzato per abilitare l'identità gestita, in quanto fornisce un'esperienza più completa. Tuttavia, quando si lavora con avanzato o complessi automatizzata di sistemi che richiedono o più distribuzioni di applicazioni gestite, i modelli possono essere utilizzate.

### <a name="using-createuidefinition"></a>Uso di CreateUIDefinition

Un'applicazione gestita può essere configurata con identità gestite tramite il [createuidefinition. JSON](./create-uidefinition-overview.md). Nel [sezione outputs](./create-uidefinition-overview.md#outputs), la chiave `managedIdentity` può essere utilizzato per ignorare la proprietà identity del modello di applicazione gestita. La campana di esempio abiliterà **assegnato dal sistema** identità dell'applicazione gestita. Oggetti identity più complessi possono essere creati usando gli elementi di CreateUIDefinition per chiedere il consumer per gli input. I dati di input può essere utilizzati per costruire le applicazioni gestite con **identità assegnata dall'utente**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quando utilizzare CreateUIDefinition per identità gestita

Di seguito sono riportati alcuni consigli su quando usare CreateUIDefinition per l'abilitazione di identità gestite in applicazioni gestite.

- La creazione di un'applicazione gestita passa attraverso il portale di Azure o il marketplace.
- L'identità gestita richiede l'input di consumer complesse.
- È necessaria l'identità gestita al momento della creazione dell'applicazione gestita.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

CreateUIDefinition base che abilita l'identità SystemAssigned per l'applicazione gestita.

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

CreateUIDefinition base che accetta un **identità assegnata dall'utente** risorse come input e abilita l'identità UserAssigned per l'applicazione gestita.

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

Di createuidefinition. JSON precedente genera un'esperienza utente crea una casella di testo per un utente di immettere il **identità assegnata dall'utente** ID risorsa di Azure. L'esperienza generato è simile:

![Esempio identità assegnata dall'utente CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

> [!NOTE]
> Modelli di applicazione gestita di Marketplace vengono generati automaticamente per i clienti di passare attraverso il portale di Azure crea l'esperienza.
> Per questi scenari, il `managedIdentity` chiave output nella finestra di CreateUIDefinition deve essere usata di abilitare l'identità.

L'identità gestita può essere abilitata anche tramite modelli Azure Resource Manager. La campana di esempio abiliterà **assegnato dal sistema** identità dell'applicazione gestita. Oggetti identity più complessi possono essere creati usando i parametri di modello di Azure Resource Manager per l'invio degli input. I dati di input può essere utilizzati per costruire le applicazioni gestite con **identità assegnata dall'utente**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quando usare i modelli Azure Resource Manager per identità gestita

Di seguito sono riportati alcuni consigli su quando usare i modelli Azure Resource Manager per l'abilitazione di identità gestite in applicazioni gestite.

- Le applicazioni gestite possono essere distribuite a livello di programmazione basato su un modello.
- Sono necessarie assegnazioni di ruolo personalizzate per l'identità gestita per il provisioning dell'applicazione gestita.
- L'applicazione gestita non è necessario che il flusso di creazione marketplace e portale di Azure.

#### <a name="systemassigned-template"></a>Modello SystemAssigned

Un modello di base di Azure Resource Manager che distribuisce un'applicazione gestita con **assegnato dal sistema** identità.

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

Un modello di base di Azure Resource Manager che distribuisce un'applicazione gestita con un **identità assegnata dall'utente**.

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

Una volta che un'applicazione gestita viene concessa un'identità, è possibile concedere l'accesso alle risorse di azure esistente. Questo processo può essere eseguito tramite l'interfaccia di accesso (IAM) di controllo nel portale di Azure. Il nome dell'applicazione gestita o **identità assegnata dall'utente** possono essere cercati per aggiungere un'assegnazione di ruolo.

![Aggiungere un'assegnazione di ruolo per un'applicazione gestita](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Collegamento di risorse di Azure esistente

> [!NOTE]
> Oggetto **identità assegnata dall'utente** deve essere [configurato](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) prima di distribuire l'applicazione gestita. Inoltre, risorsa collegata la distribuzione di applicazioni gestite è supportata solo per i **marketplace** tipo.

Managed Identity è anche utilizzabile per distribuire un'applicazione gestita che richiede l'accesso alle risorse esistenti durante la distribuzione. Quando viene eseguito il provisioning di un'applicazione gestita dal cliente, **identità assegnata dall'utente** possono essere aggiunti per fornire autorizzazioni aggiuntive per il **mainTemplate** distribuzione.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Creazione con una risorsa collegata di CreateUIDefinition

Quando si collegano la distribuzione dell'applicazione gestita le risorse esistenti, sia le risorse di Azure esistente e un **identità assegnata dall'utente** con il ruolo applicabile è necessario specificare assegnazioni su tale risorsa.

 Un esempio di CreateUIDefinition che richiede due input: un ID di risorsa interfaccia di rete e un id di risorsa identità assegnata dall'utente.

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

Questo createuidefinition. JSON genera un'esperienza utente di creazione che dispone di due campi. Il primo campo consente all'utente di immettere l'ID risorsa di Azure per la risorsa da collegare alla distribuzione di un'applicazione gestita. La seconda è per un utente di immettere il **identità assegnata dall'utente** ID risorsa di Azure, che può accedere alla risorsa di Azure collegata. L'esperienza generato è simile:

![CreateUIDefinition di esempio con due input: un'interfaccia di rete, ID di risorsa e un ID di risorsa identità assegnata dall'utente](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Creazione mainTemplate con una risorsa collegata

Oltre all'aggiornamento di CreateUIDefinition, il modello principale inoltre deve essere aggiornata per accettare l'ID della risorsa collegata. passato. Il modello principale può essere aggiornato per accettare il nuovo output aggiungendo un nuovo parametro. Poiché il `managedIdentity` output sostituisce il valore nel modello generato che un'applicazione gestita, non viene passato al modello principale e non devono essere inclusi nella sezione dei parametri.

Un modello principale di esempio che consente di impostare il profilo di rete a un'interfaccia di rete esistente fornita da di CreateUIDefinition.

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

Dopo aver creato il pacchetto di applicazione gestita, l'applicazione gestita possono essere utilizzato tramite il portale di Azure. Prima che possa essere utilizzata, sono necessari passaggi diversi.

- È necessario creare un'istanza di tale risorsa Azure collegata.
- Il **identità assegnata dall'utente** deve essere [creato e assegnato le assegnazioni di ruolo](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) della risorsa collegata.
- ID risorsa collegati esistenti e il **identità assegnata dall'utente** ID forniti a di CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>L'accesso al token dell'identità gestita

Il token dell'applicazione gestita è ora possibile accedere tramite il `listTokens` api dal tenant di server di pubblicazione. Un esempio di richiesta potrebbe essere simile:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Una risposta di esempio potrebbe essere simile:

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come configurare un'applicazione gestita con un Provider personalizzato](./custom-providers-overview.md)