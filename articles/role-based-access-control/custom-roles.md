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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6307c57f32700c0c2dd2e5da15b98a2a54dbe9c4
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339329"
---
# <a name="custom-roles-for-azure-resources"></a>Ruoli personalizzati per le risorse di Azure

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare i ruoli personalizzati a utenti, gruppi ed entità servizio nell'ambito della sottoscrizione, del gruppo di risorse e della risorsa. I ruoli personalizzati vengono archiviati in una directory di Azure Active Directory (Azure AD) e possono essere condivisi tra le sottoscrizioni. Ogni directory può avere al massimo 2000 ruoli personalizzati. È possibile creare ruoli personalizzati usando Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

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
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Al termine della creazione, il ruolo personalizzato viene visualizzato nel portale di Azure con un'icona di risorsa arancione.

![Icona del ruolo personalizzato](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passaggi per la creazione di un ruolo personalizzato

1. Determinare le autorizzazioni necessarie

    Quando si crea un ruolo personalizzato, occorre conoscere le operazioni del provider di risorse disponibili per definire le autorizzazioni. Per visualizzare l'elenco delle operazioni, è possibile usare il comando [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) o [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Per specificare le autorizzazioni per il ruolo personalizzato, occorre aggiungere le operazioni alla proprietà `Actions` o `NotActions` della [definizione del ruolo](role-definitions.md). Se si tratta di operazioni sui dati, aggiungerle alla proprietà `DataActions` o `NotDataActions`.

2. Creare il ruolo personalizzato

    Per creare il ruolo personalizzato è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure. In genere si parte da un ruolo predefinito esistente e lo si modifica in base alle esigenze. Si usa quindi il comando [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) o [az role definition create](/cli/azure/role/definition#az-role-definition-create) per creare il ruolo personalizzato. Per creare un ruolo personalizzato, occorre avere l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` su tutti i `AssignableScopes`, come [Proprietario](built-in-roles.md#owner) o [Amministratore accessi utente](built-in-roles.md#user-access-administrator).

3. Testare il ruolo personalizzato

    Una volta creato il ruolo personalizzato, è necessario testarlo per verificare che funzioni nel modo previsto. Se occorre apportare delle modifiche, è possibile aggiornare il ruolo personalizzato.

Per un'esercitazione dettagliata su come creare un ruolo personalizzato, vedere [Esercitazione: Creare un ruolo personalizzato con Azure PowerShell](tutorial-custom-role-powershell.md) oppure [Esercitazione: Creare un ruolo personalizzato con l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Proprietà del ruolo personalizzato

Un ruolo personalizzato ha le proprietà descritte di seguito.

| Proprietà | Obbligatoria | Type | DESCRIZIONE |
| --- | --- | --- | --- |
| `Name` | Sì | string | Nome visualizzato del ruolo personalizzato. Mentre una definizione di ruolo è una risorsa a livello di sottoscrizione, una definizione di ruolo può essere usata in più sottoscrizioni che condividono la stessa directory di Azure AD. Il nome visualizzato deve essere univoco nell'ambito della directory di Azure AD. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 128. |
| `Id` | Sì | string | ID univoco del ruolo personalizzato. Per Azure PowerShell e l'interfaccia della riga di comando di Azure questo ID viene generato automaticamente quando viene creato un nuovo ruolo. |
| `IsCustom` | Sì | string | Indica se questo è un ruolo personalizzato. Impostare su `true` per i ruoli personalizzati. |
| `Description` | Sì | string | Descrizione del ruolo personalizzato. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 1024. |
| `Actions` | Sì | String[] | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. Per altre informazioni, vedere [Azioni](role-definitions.md#actions). |
| `NotActions` | No  | String[] | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `Actions` consentite. Per altre informazioni, vedere [notActions](role-definitions.md#notactions). |
| `DataActions` | No  | String[] | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Per altre informazioni, vedere [dataActions (anteprima)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | No  | String[] | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `DataActions` consentite. Per altre informazioni, vedere [notDataActions (anteprima)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Sì | String[] | Matrice di stringhe che specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. Attualmente non è possibile impostarlo sull'ambito radice (`"/"`) o su un ambito del gruppo di gestione. Per altre informazioni, vedere [AssignableScopes](role-definitions.md#assignablescopes) e [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Chi può creare, eliminare, aggiornare o visualizzare un ruolo personalizzato

Come per i ruoli predefiniti, la proprietà `AssignableScopes` specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. La proprietà `AssignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, aggiornare o visualizzare il ruolo personalizzato.

| Attività | Operazione | DESCRIZIONE |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Aggiornare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono aggiornare i ruoli personalizzati in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="next-steps"></a>Passaggi successivi
- [Creare ruoli personalizzati per le risorse di Azure tramite Azure PowerShell](custom-roles-powershell.md)
- [Creare ruoli personalizzati per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](custom-roles-cli.md)
- [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md)
- [Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure](troubleshooting.md)
