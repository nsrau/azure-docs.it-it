---
title: Informazioni sulle assegnazioni di rifiuto di Azure RBAC | Microsoft Docs
description: Informazioni sulle assegnazioni di rifiuto nel controllo degli accessi in base al ruolo (RBAC) per le risorse di Azure.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980169"
---
# <a name="understand-deny-assignments"></a>Informazioni sulle assegnazioni di rifiuto

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* associa un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Un'assegnazione di rifiuto può inoltre escludere entità e impedire l'ereditarietà per gli ambiti figlio, che si differenzia dalle assegnazioni di ruolo. Attualmente, le assegnazioni di rifiuto sono di **sola lettura** e possono essere impostate solo da Azure. Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

## <a name="deny-assignment-properties"></a>Proprietà dell'assegnazione di rifiuto

 Un'assegnazione di rifiuto ha le seguenti proprietà:

> [!div class="mx-tableFixed"]
> | Proprietà | Obbligatoria | type | DESCRIZIONE |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Yes | string | Il nome visualizzato dell'assegnazione di rifiuto. I nomi devono essere univoci per un determinato ambito. |
> | `Description` | No  | string | La descrizione dell'assegnazione di rifiuto. |
> | `Permissions.Actions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni di gestione a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotActions` | No  | String[] | Una matrice di stringhe che specifica le operazioni di gestione da escludere nell'assegnazione di rifiuto. |
> | `Permissions.DataActions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni dati a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotDataActions` | No  | String[] | Una matrice di stringhe che specifica le operazioni dati da escludere nell'assegnazione di rifiuto. |
> | `Scope` | No  | string | Una stringa che specifica l'ambito a cui si applica l'assegnazione di rifiuto. |
> | `DoNotApplyToChildScopes` | No  | boolean | Specifica se l'assegnazione di rifiuto è valida per gli ambiti figlio. Il valore predefinito è False. |
> | `Principals[i].Id` | Yes | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo o entità servizio) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutti gli utenti. |
> | `Principals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da Principals[i].Id. Impostarla su `Everyone` per rappresentare tutti gli utenti. |
> | `ExcludePrincipals[i].Id` | No  | String[] | Una matrice degli ID oggetto entità servizio di Azure AD (utente, gruppo o entità servizio) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No  | boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="everyone-principal"></a>Entità everyone

Per supportare tutte le assegnazioni di negazione è stata introdotta l'entità everyone. L'entità everyone rappresenta tutti gli utenti, gruppi e le entità servizio di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `Everyone`, l'entità rappresenta tutti gli utenti (everyone). L'entità everyone può essere combinata con `ExcludePrincipals` per rifiutare tutti, ad eccezione di alcuni utenti. L'entità everyone presenta i seguenti vincoli:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `Everyone`.

## <a name="next-steps"></a>Passaggi successivi

* [Elencare le assegnazioni di rifiuto usando RBAC e l'API REST](deny-assignments-rest.md)
* [Informazioni sulle definizioni del ruolo](role-definitions.md)
