---
title: Ruoli personalizzati in Azure | Microsoft Docs
description: Informazioni su come definire i ruoli personalizzati con il controllo degli accessi in base al ruolo di Azure per la gestione degli accessi specifica per le risorse in Azure.
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
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 446cb34f2de8d0de3ee52e23df6cd26644d31bba
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435971"
---
# <a name="custom-roles-in-azure"></a>Ruoli personalizzati in Azure

Se i [ruoli predefiniti](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare i ruoli personalizzati a utenti, gruppi ed entità servizio nell'ambito della sottoscrizione, del gruppo di risorse e della risorsa. I ruoli personalizzati vengono archiviati in un tenant di Azure Active Directory (Azure AD) e possono essere condivisi tra le sottoscrizioni. Ogni tenant può avere al massimo 2000 ruoli personalizzati. È possibile creare ruoli personalizzati usando Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="custom-role-example"></a>Esempio di ruolo personalizzato

L'esempio seguente mostra un ruolo personalizzato per il monitoraggio e il riavvio di macchine virtuali, come visualizzato tramite Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Dopo la creazione, il ruolo personalizzato viene visualizzato nel portale di Azure con un'icona di risorsa arancione.

![Icona del ruolo personalizzato](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Passaggi per la creazione di un ruolo personalizzato

1. Determinare le autorizzazioni necessarie

    Quando si crea un ruolo personalizzato, occorre conoscere le operazioni del provider di risorse disponibili per definire le autorizzazioni. Per visualizzare l'elenco delle operazioni, è possibile usare il comando [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) o [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Per specificare le autorizzazioni per il ruolo personalizzato, occorre aggiungere le operazioni alla proprietà `actions` o `notActions` della [definizione del ruolo](role-definitions.md). Se si tratta di operazioni sui dati, aggiungerle alla proprietà `dataActions` o `notDataActions`.

2. Creare il ruolo personalizzato

    Per creare il ruolo personalizzato è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure. In genere si parte da un ruolo predefinito esistente e lo si modifica in base alle esigenze. Si usa quindi il comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) o [az role definition create](/cli/azure/role/definition#az-role-definition-create) per creare il ruolo personalizzato. Per creare un ruolo personalizzato, occorre avere l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` su tutti i `assignableScopes`, come [Proprietario](built-in-roles.md#owner) o [Amministratore accessi utente](built-in-roles.md#user-access-administrator).

3. Testare il ruolo personalizzato

    Una volta creato il ruolo personalizzato, è necessario testarlo per verificare che funzioni nel modo previsto. Se occorre apportare delle modifiche, è possibile aggiornare il ruolo personalizzato.

## <a name="custom-role-properties"></a>Proprietà del ruolo personalizzato

Un ruolo personalizzato ha le proprietà descritte di seguito.

| Proprietà | Obbligatoria | type | DESCRIZIONE |
| --- | --- | --- | --- |
| `Name` | Sì | string | Nome visualizzato del ruolo personalizzato. Deve essere univoco del tenant. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 128. |
| `Id` | Sì | string | ID univoco del ruolo personalizzato. Per Azure PowerShell e l'interfaccia della riga di comando di Azure questo ID viene generato automaticamente quando viene creato un nuovo ruolo. |
| `IsCustom` | Sì | string | Indica se questo è un ruolo personalizzato. Impostare su `true` per i ruoli personalizzati. |
| `Description` | Sì | string | Descrizione del ruolo personalizzato. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 1024. |
| `Actions` | Sì | String[] | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. Per altre informazioni, vedere [actions](role-definitions.md#actions). |
| `NotActions` | No  | String[] | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `actions` consentite. Per altre informazioni, vedere [notActions](role-definitions.md#notactions). |
| `DataActions` | No  | String[] | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Per altre informazioni, vedere [dataActions (anteprima)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | No  | String[] | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `dataActions` consentite. Per altre informazioni, vedere [notDataActions (anteprima)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Sì | String[] | Matrice di stringhe che specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. Non può essere impostata sull'ambito radice (`"/"`). Per altre informazioni, vedere [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>assignableScopes per i ruoli personalizzati

Come per i ruoli predefiniti, la proprietà `assignableScopes` specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. Non è tuttavia possibile usare l'ambito radice (`"/"`) nei ruoli personalizzati. Se si esegue questo tentativo, si ottiene un errore di autorizzazione. La proprietà `assignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, modificare o visualizzare il ruolo personalizzato.

| Attività | Operazione | DESCRIZIONE |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Modificare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono modificare ruoli personalizzati in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="next-steps"></a>Passaggi successivi
- [Creare ruoli personalizzati con Azure PowerShell](custom-roles-powershell.md)
- [Creare ruoli personalizzati con l'interfaccia della riga di comando di Azure](custom-roles-cli.md)
- [Informazioni sulle definizioni del ruolo](role-definitions.md)
