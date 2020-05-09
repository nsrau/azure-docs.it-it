---
title: Ruoli personalizzati di Azure-RBAC di Azure
description: Informazioni su come creare ruoli personalizzati di Azure con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per la gestione degli accessi con granularità fine delle risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a30ea70c623c8456ae97c8ca9475e4989784edf
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995843"
---
# <a name="azure-custom-roles"></a>Ruoli personalizzati di Azure

> [!IMPORTANT]
> L'aggiunta di un gruppo `AssignableScopes` di gestione a è attualmente in fase di anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio in ambito gruppo di gestione, sottoscrizione e gruppo di risorse.

I ruoli personalizzati possono essere condivisi tra sottoscrizioni che considerano attendibile la stessa directory Azure AD. È previsto un limite di **5.000** ruoli personalizzati per ogni directory. (Per Azure Germania e Azure Cina 21Vianet, il limite è 2.000 ruoli personalizzati). I ruoli personalizzati possono essere creati usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="custom-role-example"></a>Esempio di ruolo personalizzato

Di seguito viene illustrato il modo in cui un ruolo personalizzato appare come visualizzato con Azure PowerShell in formato JSON. Questo ruolo personalizzato può essere usato per il monitoraggio e il riavvio di macchine virtuali.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Di seguito è illustrato lo stesso ruolo personalizzato visualizzato con l'interfaccia della riga di comando di Azure.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Al termine della creazione, il ruolo personalizzato viene visualizzato nel portale di Azure con un'icona di risorsa arancione.

![Icona del ruolo personalizzato](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Proprietà del ruolo personalizzato

La tabella seguente descrive le proprietà del ruolo personalizzato.

| Proprietà | Obbligatoria | Type | Description |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Sì | string | Nome visualizzato del ruolo personalizzato. Mentre una definizione di ruolo è un gruppo di gestione o una risorsa a livello di sottoscrizione, è possibile utilizzare una definizione di ruolo in più sottoscrizioni che condividono la stessa directory Azure AD. Il nome visualizzato deve essere univoco nell'ambito della directory di Azure AD. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 128. |
| `Id`</br>`name` | Sì | string | ID univoco del ruolo personalizzato. Per Azure PowerShell e l'interfaccia della riga di comando di Azure questo ID viene generato automaticamente quando viene creato un nuovo ruolo. |
| `IsCustom`</br>`roleType` | Sì | string | Indica se questo è un ruolo personalizzato. Impostare su `true` o `CustomRole` per i ruoli personalizzati. Impostare su `false` o `BuiltInRole` per i ruoli predefiniti. |
| `Description`</br>`description` | Sì | string | Descrizione del ruolo personalizzato. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 1024. |
| `Actions`</br>`actions` | Sì | String[] | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. Per altre informazioni, vedere [Azioni](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | No | String[] | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `Actions` consentite. Per altre informazioni, vedere [notActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Se si crea un ruolo personalizzato con `DataActions`, tale ruolo non può essere assegnato all'ambito del gruppo di gestione. Per ulteriori informazioni, vedere [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `DataActions` consentite. Per ulteriori informazioni, vedere [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Sì | String[] | Matrice di stringhe che specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. È possibile definire un solo gruppo di gestione `AssignableScopes` in di un ruolo personalizzato. L'aggiunta di un gruppo `AssignableScopes` di gestione a è attualmente in fase di anteprima. Per altre informazioni, vedere [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="steps-to-create-a-custom-role"></a>Passaggi per la creazione di un ruolo personalizzato

Per creare un ruolo personalizzato, di seguito sono riportati i passaggi di base da seguire.

1. Decidere come si desidera creare il ruolo personalizzato.

    È possibile creare ruoli personalizzati usando portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

1. Determinare le autorizzazioni necessarie.

    Quando si crea un ruolo personalizzato, è necessario conoscerne le operazioni disponibili per definire le autorizzazioni. Per visualizzare l'elenco delle operazioni, vedere il [Azure Resource Manager operazioni del provider di risorse](resource-provider-operations.md). Le operazioni vengono aggiunte alle proprietà `Actions` o `NotActions` della definizione di [ruolo](role-definitions.md). Se si dispone di operazioni sui dati, sarà necessario aggiungerle `DataActions` alle `NotDataActions` proprietà o.

1. Creare il ruolo personalizzato.

    In genere si parte da un ruolo predefinito esistente e lo si modifica in base alle esigenze. Il modo più semplice consiste nell'usare il portale di Azure. Per i passaggi relativi alla creazione di un ruolo personalizzato usando il portale di Azure, vedere [creare o aggiornare i ruoli personalizzati di Azure usando il portale di Azure](custom-roles-portal.md).

1. Testare il ruolo personalizzato.

    Una volta creato il ruolo personalizzato, è necessario testarlo per verificare che funzioni nel modo previsto. Se occorre apportare delle modifiche, è possibile aggiornare il ruolo personalizzato.

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Chi può creare, eliminare, aggiornare o visualizzare un ruolo personalizzato

Come per i ruoli predefiniti, la proprietà `AssignableScopes` specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. La proprietà `AssignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, aggiornare o visualizzare il ruolo personalizzato.

| Attività | Operazione | Descrizione |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, [proprietari](built-in-roles.md#owner) e [amministratori accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Aggiornare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono aggiornare i ruoli personalizzati in tali ambiti. Ad esempio, [proprietari](built-in-roles.md#owner) e [amministratori accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="custom-role-limits"></a>Limiti dei ruoli personalizzati

Nell'elenco seguente vengono descritti i limiti per i ruoli personalizzati.

- Ogni directory può avere un massimo di **5000** ruoli personalizzati.
- Azure Germania e Azure Cina 21Vianet possono avere fino a 2000 ruoli personalizzati per ogni directory.
- Non è possibile `AssignableScopes` impostare sull'ambito radice (`"/"`).
- È possibile definire un solo gruppo di gestione `AssignableScopes` in di un ruolo personalizzato. L'aggiunta di un gruppo `AssignableScopes` di gestione a è attualmente in fase di anteprima.
- I ruoli personalizzati `DataActions` con non possono essere assegnati all'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione di ruolo.

Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere [organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Formati di input e output

Per creare un ruolo personalizzato usando la riga di comando, in genere si usa JSON per specificare le proprietà desiderate per il ruolo personalizzato. A seconda degli strumenti usati, i formati di input e di output appariranno leggermente diversi. In questa sezione sono elencati i formati di input e output in base allo strumento.

### <a name="azure-powershell"></a>Azure PowerShell

Per creare un ruolo personalizzato utilizzando Azure PowerShell, è necessario fornire l'input seguente.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Per aggiornare un ruolo personalizzato utilizzando Azure PowerShell, è necessario fornire l'input seguente. Si noti che `Id` la proprietà è stata aggiunta. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Di seguito viene illustrato un esempio di output quando si elenca un ruolo personalizzato usando Azure PowerShell e il comando [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) . 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per creare o aggiornare un ruolo personalizzato usando l'interfaccia della riga di comando di Azure, è necessario fornire l'input seguente. Questo formato è dello stesso formato quando si crea un ruolo personalizzato utilizzando Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Di seguito viene illustrato un esempio di output quando si elenca un ruolo personalizzato tramite l'interfaccia della riga di comando di Azure.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>API REST

Per creare o aggiornare un ruolo personalizzato usando l'API REST, è necessario fornire l'input seguente. Questo formato è dello stesso formato che viene generato quando si crea un ruolo personalizzato usando il portale di Azure.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Di seguito viene illustrato un esempio di output quando si elenca un ruolo personalizzato usando l'API REST.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato di Azure usando Azure PowerShell](tutorial-custom-role-powershell.md)
- [Esercitazione: creare un ruolo personalizzato di Azure con l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
