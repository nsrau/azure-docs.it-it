---
title: Informazioni sulle assegnazioni di rifiuto per le risorse di Azure | Microsoft Docs
description: Informazioni sulle assegnazioni di rifiuto nel controllo degli accessi in base al ruolo per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479357"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Informazioni sulle assegnazioni di rifiuto per le risorse di Azure

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* allega un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso.

Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

## <a name="how-deny-assignments-are-created"></a>How deny assignments are created

Deny assignments are created and managed by Azure to protect resources. Azure Blueprints and Azure managed apps use deny assignments to protect system-managed resources. Azure Blueprints and Azure managed apps are the only way that deny assignments can be created. You can't directly create your own deny assignments.  Per altre informazioni, vedere [Proteggere le nuove risorse con blocchi delle risorse in Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

> [!NOTE]
> You can't directly create your own deny assignments.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare role assignments and deny assignments

Deny assignments follow a similar pattern as role assignments, but also have some differences.

| Funzionalità | Assegnazione di ruolo | Deny assignment |
| --- | --- | --- |
| Concedere l'accesso | :heavy_check_mark: |  |
| Rifiutare l'accesso |  | :heavy_check_mark: |
| Can be directly created | :heavy_check_mark: |  |
| Apply at a scope | :heavy_check_mark: | :heavy_check_mark: |
| Exclude principals |  | :heavy_check_mark: |
| Prevent inheritance to child scopes |  | :heavy_check_mark: |
| Apply to [classic subscription administrator](rbac-and-directory-admin-roles.md) assignments |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Proprietà dell'assegnazione di rifiuto

 Un'assegnazione di rifiuto ha le seguenti proprietà:

> [!div class="mx-tableFixed"]
> | Proprietà | Obbligatoria | Type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | SÌ | Stringa | Il nome visualizzato dell'assegnazione di rifiuto. I nomi devono essere univoci per un determinato ambito. |
> | `Description` | No | Stringa | La descrizione dell'assegnazione di rifiuto. |
> | `Permissions.Actions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni di gestione a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotActions` | No | String[] | Una matrice di stringhe che specifica le operazioni di gestione da escludere nell'assegnazione di rifiuto. |
> | `Permissions.DataActions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni dati a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotDataActions` | No | String[] | Una matrice di stringhe che specifica le operazioni dati da escludere nell'assegnazione di rifiuto. |
> | `Scope` | No | Stringa | Una stringa che specifica l'ambito a cui si applica l'assegnazione di rifiuto. |
> | `DoNotApplyToChildScopes` | No | boolean | Specifica se l'assegnazione di rifiuto è valida per gli ambiti figlio. Il valore predefinito è False. |
> | `Principals[i].Id` | SÌ | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutte le entità. |
> | `Principals[i].Type` | No | String[] | An array of object types represented by Principals[i].Id. Set to `SystemDefined` to represent all principals. |
> | `ExcludePrincipals[i].Id` | No | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No | boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="the-all-principals-principal"></a>The All Principals principal

To support deny assignments, a system-defined principal named *All Principals* has been introduced. Quest'entità rappresenta tutti gli utenti, gruppi, entità servizio e identità gestite in una directory di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `SystemDefined`, l'entità rappresenta tutte le entità. In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals can be combined with `ExcludePrincipals` to deny all principals except some users. All Principals has the following constraints:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `SystemDefined`.

## <a name="next-steps"></a>Passaggi successivi

* [List deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
* [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md)
