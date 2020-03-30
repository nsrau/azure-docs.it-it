---
title: Ruoli personalizzati per le risorse di Azure | Microsoft Docs
description: Informazioni su come creare i ruoli personalizzati con il controllo degli accessi in base al ruolo per la gestione degli accessi specifica per le risorse di Azure.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062180"
---
# <a name="custom-roles-for-azure-resources"></a>Ruoli personalizzati per le risorse di Azure

> [!IMPORTANT]
> L'aggiunta di `AssignableScopes` un gruppo di gestione è attualmente in anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti per](built-in-roles.md) le risorse di Azure non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio negli ambiti dei gruppi di gestione, della sottoscrizione e del gruppo di risorse.

I ruoli personalizzati possono essere condivisi tra sottoscrizioni che considerano attendibile la stessa directory di Azure AD. Esiste un limite di **5.000** ruoli personalizzati per directory. Per Azure Germania e Azure China 21Vianet, il limite è 2.000 ruoli personalizzati. È possibile creare ruoli personalizzati usando il portale di Azure (anteprima), Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="custom-role-example"></a>Esempio di ruolo personalizzato

Di seguito è riportato come viene visualizzato un ruolo personalizzato in formato JSON. Questo ruolo personalizzato può essere usato per il monitoraggio e il riavvio di macchine virtuali.

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

Al termine della creazione, il ruolo personalizzato viene visualizzato nel portale di Azure con un'icona di risorsa arancione.

![Icona del ruolo personalizzato](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passaggi per la creazione di un ruolo personalizzato

1. Decidere come creare il ruolo personalizzatoDecide how you want to create the custom role

    È possibile creare ruoli personalizzati usando il [portale](custom-roles-portal.md) di Azure (anteprima), [Azure PowerShell,](custom-roles-powershell.md) [l'interfaccia della riga di comando di Azure](custom-roles-cli.md)o l'API [REST.](custom-roles-rest.md)

1. Determinare le autorizzazioni necessarie

    Quando si crea un ruolo personalizzato, occorre conoscere le operazioni del provider di risorse disponibili per definire le autorizzazioni. Per visualizzare l'elenco delle operazioni, vedere Operazioni del provider di risorse di [Azure Resource Manager](resource-provider-operations.md). Le operazioni verranno `Actions` aggiunte alle proprietà o `NotActions` della [definizione](role-definitions.md)di ruolo . Se si dispone di operazioni sui `DataActions` dati, verranno aggiunte alle proprietà o `NotDataActions` .

1. Creare il ruolo personalizzato

    In genere si parte da un ruolo predefinito esistente e lo si modifica in base alle esigenze. Si usa quindi il comando [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) o [az role definition create](/cli/azure/role/definition#az-role-definition-create) per creare il ruolo personalizzato. Per creare un ruolo personalizzato, occorre avere l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` su tutti i `AssignableScopes`, come [Proprietario](built-in-roles.md#owner) o [Amministratore accessi utente](built-in-roles.md#user-access-administrator).

1. Testare il ruolo personalizzato

    Una volta creato il ruolo personalizzato, è necessario testarlo per verificare che funzioni nel modo previsto. Se occorre apportare delle modifiche, è possibile aggiornare il ruolo personalizzato.

Per un'esercitazione dettagliata su come creare un ruolo personalizzato, vedere [Esercitazione: creare un ruolo personalizzato con Azure PowerShell](tutorial-custom-role-powershell.md) o [Esercitazione: creare un ruolo personalizzato con l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Proprietà del ruolo personalizzato

Un ruolo personalizzato ha le proprietà descritte di seguito.

| Proprietà | Obbligatoria | Type | Descrizione |
| --- | --- | --- | --- |
| `Name` | Sì | string | Nome visualizzato del ruolo personalizzato. Mentre una definizione di ruolo è un gruppo di gestione o una risorsa a livello di sottoscrizione, una definizione di ruolo può essere usata in più sottoscrizioni che condividono la stessa directory di Azure AD. Il nome visualizzato deve essere univoco nell'ambito della directory di Azure AD. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 128. |
| `Id` | Sì | string | ID univoco del ruolo personalizzato. Per Azure PowerShell e l'interfaccia della riga di comando di Azure questo ID viene generato automaticamente quando viene creato un nuovo ruolo. |
| `IsCustom` | Sì | string | Indica se questo è un ruolo personalizzato. Impostare su `true` per i ruoli personalizzati. |
| `Description` | Sì | string | Descrizione del ruolo personalizzato. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 1024. |
| `Actions` | Sì | String[] | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. Per altre informazioni, vedere [Azioni](role-definitions.md#actions). |
| `NotActions` | No | String[] | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `Actions` consentite. Per altre informazioni, vedere [notActions](role-definitions.md#notactions). |
| `DataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Se si crea un `DataActions`ruolo personalizzato con , tale ruolo non può essere assegnato nell'ambito del gruppo di gestione. Per ulteriori informazioni, vedere [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `DataActions` consentite. Per ulteriori informazioni, vedere [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sì | String[] | Matrice di stringhe che specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. È possibile definire un `AssignableScopes` solo gruppo di gestione in un ruolo personalizzato. L'aggiunta di `AssignableScopes` un gruppo di gestione è attualmente in anteprima. Per altre informazioni, vedere [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Chi può creare, eliminare, aggiornare o visualizzare un ruolo personalizzato

Come per i ruoli predefiniti, la proprietà `AssignableScopes` specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. La proprietà `AssignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, aggiornare o visualizzare il ruolo personalizzato.

| Attività | Operazione | Descrizione |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, [Proprietari](built-in-roles.md#owner) e [Amministratori accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Aggiornare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono aggiornare i ruoli personalizzati in tali ambiti. Ad esempio, [Proprietari](built-in-roles.md#owner) e [Amministratori accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="custom-role-limits"></a>Limiti dei ruoli personalizzati

Nell'elenco seguente vengono descritti i limiti per i ruoli personalizzati.

- Ogni directory può avere fino a **5000** ruoli personalizzati.
- Azure Germania e Azure China 21Vianet possono avere fino a 2000 ruoli personalizzati per ogni directory.
- Non è `AssignableScopes` possibile impostare`"/"`l'ambito radice ( ).
- È possibile definire un `AssignableScopes` solo gruppo di gestione in un ruolo personalizzato. L'aggiunta di `AssignableScopes` un gruppo di gestione è attualmente in anteprima.
- I ruoli `DataActions` personalizzati con non possono essere assegnati nell'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione del ruolo.

Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere Organizzare le risorse con i gruppi di gestione di Azure.For more information about custom roles and management groups, see [Organize your resources with Azure management groups.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)

## <a name="next-steps"></a>Passaggi successivi
- [Creare o aggiornare ruoli personalizzati di Azure usando il portale di Azure (anteprima)Create or update Azure custom roles using the Azure portal (Preview)](custom-roles-portal.md)
- [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
