---
title: Informazioni sulle assegnazioni di rifiuto per le risorse di Azure
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372477"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Informazioni sulle assegnazioni di rifiuto per le risorse di Azure

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* allega un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso.

Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

## <a name="how-deny-assignments-are-created"></a>Come vengono create le assegnazioni di negazione

Negare le assegnazioni vengono create e gestite da Azure per proteggere le risorse. Blueprint di Azure e le app gestite di Azure usano negare le assegnazioni per proteggere le risorse gestite dal sistema. I blueprint di Azure e le app gestite di Azure sono l'unico modo per negare le assegnazioni. Non è possibile creare direttamente le proprie assegnazioni di negazione. Per altre informazioni su come Blueprints usa le assegnazioni di negazione per bloccare le risorse, vedere Informazioni sul blocco delle risorse in Blueprint di Azure.For more information about how Blueprints uses deny assignments to lock resources, see [Understand resource locking in Azure Blueprints](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Non è possibile creare direttamente le proprie assegnazioni di negazione.

## <a name="compare-role-assignments-and-deny-assignments"></a>Confrontare le assegnazioni di ruolo e negare le assegnazioni

Le assegnazioni di negazione seguono un modello simile a quello delle assegnazioni di ruolo, ma presentano anche alcune differenze.

| Funzionalità | Assegnazione di ruolo | Nega assegnazione |
| --- | --- | --- |
| Concedere l'accesso | :heavy_check_mark: |  |
| Rifiutare l'accesso |  | :heavy_check_mark: |
| Può essere creato direttamente | :heavy_check_mark: |  |
| Applicare in un ambito | :heavy_check_mark: | :heavy_check_mark: |
| Escludi principi |  | :heavy_check_mark: |
| Impedire l'ereditarietà agli ambiti figlioPrevent inheritance to child scopes |  | :heavy_check_mark: |
| Applica alle assegnazioni [di amministratori di sottoscrizione classica](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Proprietà dell'assegnazione di rifiuto

 Un'assegnazione di rifiuto ha le seguenti proprietà:

> [!div class="mx-tableFixed"]
> | Proprietà | Obbligatoria | Type | Descrizione |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sì | string | Il nome visualizzato dell'assegnazione di rifiuto. I nomi devono essere univoci per un determinato ambito. |
> | `Description` | No | string | La descrizione dell'assegnazione di rifiuto. |
> | `Permissions.Actions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni di gestione a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotActions` | No | String[] | Una matrice di stringhe che specifica le operazioni di gestione da escludere nell'assegnazione di rifiuto. |
> | `Permissions.DataActions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni dati a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotDataActions` | No | String[] | Una matrice di stringhe che specifica le operazioni dati da escludere nell'assegnazione di rifiuto. |
> | `Scope` | No | string | Una stringa che specifica l'ambito a cui si applica l'assegnazione di rifiuto. |
> | `DoNotApplyToChildScopes` | No | Boolean | Specifica se l'assegnazione di rifiuto è valida per gli ambiti figlio. Il valore predefinito è False. |
> | `Principals[i].Id` | Sì | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutte le entità. |
> | `Principals[i].Type` | No | String[] | Matrice di tipi di oggetto rappresentati da Principals[i].Id. Impostare per `SystemDefined` rappresentare tutte le entità. |
> | `ExcludePrincipals[i].Id` | No | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No | Boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="the-all-principals-principal"></a>Il principale All Principals

Per supportare le assegnazioni di negazione, è stata introdotta un'entità definita dal sistema denominata All Principals.To support deny *assignments,* a system-defined principal named All Principals has been introduced. Quest'entità rappresenta tutti gli utenti, gruppi, entità servizio e identità gestite in una directory di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `SystemDefined`, l'entità rappresenta tutte le entità. Nell'output di Azure PowerShell tutte le entità è simile al seguente:In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Tutti i principi possono `ExcludePrincipals` essere combinati con per negare tutte le entità ad eccezione di alcuni utenti. Tutte le Entità Ha i seguenti vincoli:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `SystemDefined`.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Proteggere nuove risorse con i blocchi delle risorse di Azure BlueprintsTutorial: Protect new resources with Azure Blueprints resource locks](../governance/blueprints/tutorials/protect-new-resources.md)
* [Elencare le assegnazioni di negazione per le risorse di Azure usando il portale di AzureList deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
