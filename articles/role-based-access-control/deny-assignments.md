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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: fa1a979c01999bd79c45d24e4c7771edaf346dd8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632416"
---
# <a name="understand-deny-assignments"></a>Informazioni sulle assegnazioni di rifiuto

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* allega un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche, anche se un'assegnazione di ruolo concede loro l'accesso. Alcuni provider di risorse di Azure includono ora le assegnazioni di rifiuto. Attualmente le assegnazioni di rifiuto sono di **sola lettura** e possono essere impostate solo da Azure.

Per certi versi, le assegnazioni di rifiuto sono diverse dalle assegnazioni di ruolo. Le assegnazioni di rifiuto possono escludere le entità di sicurezza e impedire l'ereditarietà per gli ambiti figlio. Le assegnazioni di rifiuto si applicano anche alle assegnazioni [amministratore della sottoscrizione classica](rbac-and-directory-admin-roles.md).

Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

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
> | `Principals[i].Id` | Yes | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutte le entità. |
> | `Principals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da Principals[i].Id. Impostare `SystemDefined` per rappresentare tutte le entità. |
> | `ExcludePrincipals[i].Id` | No  | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No  | boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="system-defined-principal"></a>Entità definite dal sistema

Per supportare tutte le assegnazioni di negazione è stata introdotta l'**entità definita dal sistema**. Quest'entità rappresenta tutti gli utenti, gruppi, entità servizio e identità gestite in una directory di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `SystemDefined`, l'entità rappresenta tutte le entità. `SystemDefined` può essere combinato con `ExcludePrincipals` per negare tutte le entità, ad eccezione di alcuni utenti. `SystemDefined` presenta i vincoli seguenti:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `SystemDefined`.

## <a name="next-steps"></a>Passaggi successivi

* [Elencare le assegnazioni di rifiuto usando RBAC e l'API REST](deny-assignments-rest.md)
* [Informazioni sulle definizioni del ruolo](role-definitions.md)
