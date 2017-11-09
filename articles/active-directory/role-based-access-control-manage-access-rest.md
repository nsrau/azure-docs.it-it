---
title: Controllo degli accessi in base al ruolo con REST - Azure AD | Microsoft Docs
description: Gestione del controllo degli accessi in base al ruolo con l'API REST
services: active-directory
documentationcenter: na
author: andredm7
manager: femila
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: andredm
ms.openlocfilehash: a5c19fd87ce1ae3e199bf1dfc8cf82f5653baac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Gestione del controllo degli accessi in base al ruolo con l'API REST
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Il controllo degli accessi in base al ruolo nel portale di Azure e nell'API di Azure Resource Manager aiuta a gestire l'accesso alla sottoscrizione e alle risorse a un livello estremamente specifico. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

## <a name="list-all-role-assignments"></a>Elencare tutte le assegnazioni di ruolo
Elenca tutte le assegnazioni di ruolo all'ambito specificato e agli ambiti secondari.

Per elencare le assegnazioni di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/read` nell'ambito. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **GET** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{api-version}* con 2015-07-01.
3. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco di assegnazioni di ruolo:

   * Elencare le assegnazioni di ruolo solo per l'ambito specificato, senza includere le assegnazioni di ruolo negli ambiti secondari: `atScope()`    
   * Elencare le assegnazioni di ruolo solo per l'utente, il gruppo o l'applicazione specifici: `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Elencare le assegnazioni di ruolo per un utente specifico, incluse quelle ereditate dai gruppi | `assignedTo('{objectId of user}')`

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Ottenere informazioni su un'assegnazione di ruolo
È possibile ottenere informazioni su una singola assegnazione di ruolo specificata dall'identificatore di assegnazione di ruolo.

Per ottenere informazioni su un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/read` . L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **GET** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-assignment-id}* con l'identificatore GUID dell'assegnazione di ruolo.
3. Sostituire *{api-version}* con 2015-07-01.

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo
È possibile creare un'assegnazione di ruolo nell'ambito specificato per l'entità specificata, concedendo il ruolo specificato.

Per creare un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/write` . Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **PUT** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito in cui creare le assegnazioni di ruolo. Quando si crea un'assegnazione di ruolo in un ambito padre, tutti gli ambiti figlio ereditano la stessa assegnazione di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1   
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-assignment-id}* con un nuovo GUID, che diventa l'identificatore GUID della nuova assegnazione di ruolo.
3. Sostituire *{api-version}* con 2015-07-01.

Per il corpo della richiesta, specificare i valori nel formato seguente:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome dell'elemento | Obbligatorio | Tipo | Descrizione |
| --- | --- | --- | --- |
| roleDefinitionId |Sì |String |Identificatore del ruolo. Il formato dell'identificatore è: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Sì |String |objectId dell'entità di Azure AD (utente, gruppo o entità servizio) a cui deve essere assegnato il ruolo. |

### <a name="response"></a>Response
Codice di stato: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Eliminare un'assegnazione di ruolo
È possibile eliminare un'assegnazione di ruolo nell'ambito specificato.

Per eliminare un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/delete` . Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **DELETE** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito in cui creare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-assignment-id}* con l'identificatore GUID dell'assegnazione di ruolo.
3. Sostituire *{api-version}* con 2015-07-01.

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Elencare tutti i ruoli
Elenca tutti i ruoli disponibili per l'assegnazione nell'ambito specificato.

Per elencare i ruoli, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read` nell'ambito. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **GET** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito per il quale elencare i ruoli. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{api-version}* con 2015-07-01.
3. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco di ruoli:

   * Elencare i ruoli disponibili per l'assegnazione nell'ambito specificato e in tutti i relativi ambiti figlio: `atScopeAndBelow()`
   * Cercare un ruolo usando l'esatto nome visualizzato: `roleName%20eq%20'{role-display-name}'`. Usare il form con codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Ottenere informazioni su un ruolo
È possibile ottenere informazioni su un singolo ruolo specificato dall'identificatore di definizione. Per ottenere informazioni su un singolo ruolo usando il relativo nome visualizzato, vedere [Elencare tutti i ruoli](role-based-access-control-manage-access-rest.md#list-all-roles).

Per ottenere informazioni su un ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read` . L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **GET** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-definition-id}* con l'identificatore GUID della definizione di ruolo.
3. Sostituire *{api-version}* con 2015-07-01.

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato
È possibile creare un ruolo personalizzato.

Per creare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti i `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **PUT** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con il primo *AssignableScope* del ruolo personalizzato. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-definition-id}* con un nuovo GUID, che diventa l'identificatore GUID del nuovo ruolo personalizzato.
3. Sostituire *{api-version}* con 2015-07-01.

Per il corpo della richiesta, specificare i valori nel formato seguente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome dell'elemento | Obbligatorio | Tipo | Descrizione |
| --- | --- | --- | --- |
| name |Sì |String |Identificatore GUID del ruolo personalizzato. |
| properties.roleName |Sì |String |Nome visualizzato del ruolo personalizzato. La dimensione massima è di 128 caratteri. |
| properties.description |No |String |Descrizione del ruolo personalizzato. La dimensione massima è di 1024 caratteri. |
| properties.type |Sì |String |Impostare su "CustomRole". |
| properties.permissions.actions |sì |String[] |Matrice di stringhe di azione che specifica le operazioni concesse dal ruolo personalizzato. |
| properties.permissions.notActions |No |String[] |Matrice di stringhe di azione che specifica le operazioni da escludere dalle operazioni concesse dal ruolo personalizzato. |
| properties.assignableScopes |Sì |String[] |Matrice di ambiti in cui il ruolo personalizzato può essere usato. |

### <a name="response"></a>Response
Codice di stato: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato
È possibile modificare un ruolo personalizzato.

Per modificare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti i `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **PUT** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con il primo *AssignableScope* del ruolo personalizzato. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-definition-id}* con l'identificatore GUID del ruolo personalizzato.
3. Sostituire *{api-version}* con 2015-07-01.

Per il corpo della richiesta, specificare i valori nel formato seguente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome dell'elemento | Obbligatorio | Tipo | Descrizione |
| --- | --- | --- | --- |
| name |Sì |String |Identificatore GUID del ruolo personalizzato. |
| properties.roleName |Sì |String |Nome visualizzato del ruolo personalizzato aggiornato. |
| properties.description |No |String |Descrizione del ruolo personalizzato aggiornato. |
| properties.type |Sì |String |Impostare su "CustomRole". |
| properties.permissions.actions |sì |String[] |Matrice di stringhe di azione che specifica le operazioni alle quali il ruolo personalizzato aggiornato concede l'accesso. |
| properties.permissions.notActions |No |String[] |Matrice di stringhe di azione che specifica le operazioni da escludere dalle operazione alle quali il ruolo personalizzato aggiornato concede l'accesso. |
| properties.assignableScopes |Sì |String[] |Matrice di ambiti in cui il ruolo personalizzato aggiornato può essere usato. |

### <a name="response"></a>Response
Codice di stato: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato
È possibile eliminare un ruolo personalizzato.

Per eliminare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/delete` in tutti i `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta
Usare il metodo **DELETE** con l'URI seguente:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

1. Sostituire *{scope}* con l'ambito in cui eliminare la definizione di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

   * Sottoscrizione: /subscriptions/{subscription-id}  
   * Gruppo di risorse: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Sostituire *{role-definition-id}* con l'identificatore GUID della definizione di ruolo del ruolo personalizzato.
3. Sostituire *{api-version}* con 2015-07-01.

### <a name="response"></a>Response
Codice di stato: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
