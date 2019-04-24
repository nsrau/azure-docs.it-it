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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197137"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Informazioni sulle assegnazioni di rifiuto per le risorse di Azure

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* allega un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Alcuni provider di risorse di Azure includono ora le assegnazioni di rifiuto.

Per certi versi, le assegnazioni di rifiuto sono diverse dalle assegnazioni di ruolo. Le assegnazioni di rifiuto possono escludere le entità di sicurezza e impedire l'ereditarietà per gli ambiti figlio. Le assegnazioni di rifiuto si applicano anche alle assegnazioni [amministratore della sottoscrizione classica](rbac-and-directory-admin-roles.md).

Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

> [!NOTE]
> Al momento, l'unico modo per aggiungere le proprie assegnazioni di rifiuto è tramite Azure Blueprints. Per altre informazioni, vedere [Proteggere le nuove risorse con blocchi delle risorse in Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="deny-assignment-properties"></a>Proprietà dell'assegnazione di rifiuto

 Un'assegnazione di rifiuto ha le seguenti proprietà:

> [!div class="mx-tableFixed"]
> | Proprietà | Obbligatorio | Type | DESCRIZIONE |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sì | String | Il nome visualizzato dell'assegnazione di rifiuto. I nomi devono essere univoci per un determinato ambito. |
> | `Description` | No  | String | La descrizione dell'assegnazione di rifiuto. |
> | `Permissions.Actions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni di gestione a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotActions` | No  | String[] | Una matrice di stringhe che specifica le operazioni di gestione da escludere nell'assegnazione di rifiuto. |
> | `Permissions.DataActions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni dati a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotDataActions` | No  | String[] | Una matrice di stringhe che specifica le operazioni dati da escludere nell'assegnazione di rifiuto. |
> | `Scope` | No  | String | Una stringa che specifica l'ambito a cui si applica l'assegnazione di rifiuto. |
> | `DoNotApplyToChildScopes` | No  | Boolean | Specifica se l'assegnazione di rifiuto è valida per gli ambiti figlio. Il valore predefinito è False. |
> | `Principals[i].Id` | Sì | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutte le entità. |
> | `Principals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da Principals[i].Id. Impostare `SystemDefined` per rappresentare tutte le entità. |
> | `ExcludePrincipals[i].Id` | No  | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No  | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No  | Boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="system-defined-principal"></a>Entità definite dal sistema

Per supportare tutte le assegnazioni di negazione è stata introdotta l'**entità definita dal sistema**. Quest'entità rappresenta tutti gli utenti, gruppi, entità servizio e identità gestite in una directory di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `SystemDefined`, l'entità rappresenta tutte le entità. `SystemDefined` può essere combinato con `ExcludePrincipals` per negare tutte le entità, ad eccezione di alcuni utenti. `SystemDefined` presenta i vincoli seguenti:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `SystemDefined`.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzazione negare le assegnazioni per le risorse di Azure usando il portale di Azure](deny-assignments-portal.md)
* [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md)
