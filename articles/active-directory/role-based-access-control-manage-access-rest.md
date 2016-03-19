<properties
	pageTitle="Gestione del controllo degli accessi in base al ruolo con l'API REST"
	description="Gestione del controllo degli accessi in base al ruolo con l'API REST"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Gestione del controllo degli accessi in base al ruolo con l'API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Elencare tutte le assegnazioni di ruolo

È possibile elenca tutte le assegnazioni di ruolo all'ambito specificato e agli ambiti secondari.

Per elencare le assegnazioni di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/read` nell'ambito. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **GET** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{api-version}* con 2015-07-01.

Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco di assegnazioni di ruolo. Sono supportate le condizioni seguenti.


| Condizione | *{Filter}* | Replace |
|-----------|------------|---------|
| Per elencare le assegnazioni di ruolo solo per l'ambito specificato, senza includere le assegnazioni di ruolo negli ambiti secondari. | `atScope()` | |
| Per elencare le assegnazioni di ruolo solo per l'utente, il gruppo o l'applicazione specifici. | `principalId%20eq%20'{objectId}'` | Sostituire *{objectId}* con l'objectId di Azure AD per l'utente, il gruppo o l'entità servizio. Ad esempio, `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| Per elencare le assegnazioni di ruolo solo per l'utente specifico, incluse quelle relative ai gruppi di cui l'utente è membro. | `assignedTo('{objectId}')` | Sostituire *{objectId}* con l'objectId di Azure AD per l'utente. Ad esempio, `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### Response

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

## Ottenere informazioni su un'assegnazione di ruolo

È possibile ottenere informazioni su una singola assegnazione di ruolo specificata dall'identificatore di assegnazione di ruolo.

Per ottenere informazioni su un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/read`. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **GET** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-assignment-id}* con l'identificatore GUID dell'assegnazione di ruolo.

Sostituire *{api-version}* con 2015-07-01.

### Response

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

## Creare un'assegnazione di ruolo

È possibile creare un'assegnazione di ruolo nell'ambito specificato per l'entità specificata, concedendo il ruolo specificato.

Per creare un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/write`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **PUT** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito in cui creare le assegnazioni di ruolo. Quando si crea un'assegnazione di ruolo in un ambito padre, tutti gli ambiti figlio ereditano la stessa assegnazione di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-assignment-id}* con un nuovo GUID. Questo verrà usato come identificatore GUID della nuova assegnazione di ruolo.

Sostituire *{api-version}* con 2015-07-01.

Per il corpo della richiesta, specificare i valori nel formato seguente:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sì | String | Identificatore del ruolo da assegnare. Il formato dell'identificatore è: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | Sì | String | objectId dell'entità di Azure AD (utente, gruppo o entità servizio) a cui deve essere assegnato il ruolo. |

### Response

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

## Eliminare un'assegnazione di ruolo

È possibile eliminare un'assegnazione di ruolo nell'ambito specificato.

Per eliminare un'assegnazione di ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/delete`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **DELETE** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito in cui creare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-assignment-id}* con l'identificatore GUID dell'assegnazione di ruolo.

Sostituire *{api-version}* con 2015-07-01.

### Response

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

## Elencare tutti i ruoli

È possibile elencare tutti i ruoli disponibili per l'assegnazione nell'ambito specificato.

Per elencare i ruoli, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read` nell'ambito. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **GET** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito per il quale elencare i ruoli. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{api-version}* con 2015-07-01.

Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco di ruoli. Sono supportate le condizioni seguenti.

| Condizione | *{Filter}* | Replace |
|-----------|------------|---------|
| Per elencare i ruoli disponibili per l'assegnazione nell'ambito specificato e in tutti i relativi ambiti figlio. | `atScopeAndBelow()` | |
| Per cercare un ruolo usando l'esatto nome visualizzato. | `roleName%20eq%20'{role-display-name}'` | Sostituire *{role-display-name}* con la codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### Response

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

## Ottenere informazioni su un ruolo

È possibile ottenere informazioni su un singolo ruolo specificato dall'identificatore di definizione. Per ottenere informazioni su un singolo ruolo usando il relativo nome visualizzato, vedere la sezione Elencare tutti i ruoli e il filtro roleName.

Per ottenere informazioni su un ruolo, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read`. L'accesso a questa operazione viene concesso a tutti i ruoli predefiniti. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **GET** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-definition-id}* con l'identificatore GUID della definizione di ruolo. Sostituire *{api-version}* con 2015-07-01.

### Response

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

## Creare un ruolo personalizzato
È possibile creare un ruolo personalizzato.

Per creare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti i relativi `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **PUT** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con il primo *AssignableScope* del ruolo personalizzato. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-definition-id}* con un nuovo GUID. Questo verrà usato come identificatore GUID del nuovo ruolo personalizzato.

Sostituire *{api-version}* con 2015-07-01.

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

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione |
|--------------|----------|------|-------------|
| name | Sì | String | Identificatore GUID del ruolo personalizzato. |
| properties.roleName | Sì | String | Nome visualizzato del ruolo personalizzato. La dimensione massima è di 128 caratteri. |
| properties.description | No | String | Descrizione del ruolo personalizzato. La dimensione massima è di 1024 caratteri. |
| properties.type | Sì | String | Impostare su "CustomRole". |
| properties.permissions.actions | Sì | String | Matrice di stringhe di azione che specifica le operazioni alle quali il ruolo personalizzato concede l'accesso. |
| properties.permissions.notActions | No | String | Matrice di stringhe di azione che specifica le operazioni escluse, alle quali il ruolo personalizzato non concede l'accesso. |
| properties.assignableScopes | Sì | String | Matrice di ambiti in cui il ruolo personalizzato può essere usato per la gestione degli accessi. |

### Response

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

## Aggiornare un ruolo personalizzato

È possibile modificare un ruolo personalizzato.

Per modificare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti i relativi `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **PUT** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con il primo *AssignableScope* del ruolo personalizzato. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-definition-id}* con l'identificatore GUID del ruolo personalizzato da aggiornare.

Sostituire *{api-version}* con 2015-07-01.

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

| Nome dell'elemento | Obbligatoria | Tipo | Descrizione |
|--------------|----------|------|-------------|
| name | Sì | String | Identificatore GUID del ruolo personalizzato da aggiornare. |
| properties.roleName | Sì | String | Nome visualizzato del ruolo personalizzato aggiornato. |
| properties.description | No | String | Descrizione del ruolo personalizzato aggiornato. |
| properties.type | Sì | String | Impostare su "CustomRole". |
| properties.permissions.actions | Sì | String | Matrice di stringhe di azione che specifica le operazioni alle quali il ruolo personalizzato aggiornato concede l'accesso. |
| properties.permissions.notActions | No | String | Matrice di stringhe di azione che specifica le operazioni escluse, alle quali il ruolo personalizzato aggiornato non concede l'accesso. |
| properties.assignableScopes | Sì | String | Matrice di ambiti in cui il ruolo personalizzato aggiornato può essere usato per la gestione degli accessi. |

### Response

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

## Eliminare un ruolo personalizzato

È possibile eliminare un ruolo personalizzato.

Per eliminare un ruolo personalizzato, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/delete` in tutti i relativi `AssignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli *Proprietario* e *Amministratore Accesso utenti*. Per altre informazioni sulle assegnazioni di ruolo e la gestione dell'accesso per le risorse di Azure, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

### Richiesta

Usare il metodo **DELETE** con l'URI seguente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno dell'URI, apportare le sostituzioni seguenti per personalizzare la richiesta:

Sostituire *{scope}* con l'ambito in cui eliminare la definizione di ruolo. Gli esempi seguenti illustrano come specificare l'ambito per livelli differenti:

| Level | *{Scope}* |
|-------|-----------|
| Sottoscrizione | /subscriptions/{subscription-id} |
| Gruppo di risorse | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Risorsa | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Sostituire *{role-definition-id}* con l'identificatore GUID della definizione di ruolo del ruolo personalizzato da eliminare.

Sostituire *{api-version}* con 2015-07-01.

### Response

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

<!---HONumber=AcomDC_0128_2016-->