---
title: Ripristinare un utente eliminato in Azure Active Directory | Microsoft Docs
description: Come ripristinare un utente eliminato, visualizzare gli utenti ripristinabili ed eliminare definitivamente un utente in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c3b7550c2aea0e8bcb7998e0e8c732894b500403
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Ripristinare un utente eliminato in Azure Active Directory

Questo articolo comprende le istruzioni per ripristinare o eliminare definitivamente un utente eliminato in precedenza. Quando si elimina un utente in Azure Active Directory (Azure AD), l'utente eliminato viene conservato per 30 giorni dalla data di eliminazione. Durante questo periodo, l'utente e le relative proprietà possono essere ripristinate. 

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per ripristinare un utente sono sufficienti le autorizzazioni seguenti.

Ruolo  | Autorizzazioni 
--------- | ---------
Amministratore società<p>Supporto partner - Livello 1<p>Supporto partner - Livello 2<p>Amministratore account utente | Può ripristinare gli utenti eliminati 
Amministratore società<p>Supporto partner - Livello 1<p>Supporto partner - Livello 2<p>Amministratore account utente | Può eliminare definitivamente gli utenti

## <a name="how-to-restore-a-deleted-user"></a>Ripristinare un utente eliminato

Nel portale di Azure è possibile ripristinare un utente eliminato ed eliminare definitivamente un utente eliminato.

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi** &gt; **Tutti gli utenti**. 
2. In **Mostra** filtrare la pagina per visualizzare gli **Utenti eliminati di recente**. 
3. Selezionare uno o più utenti eliminati di recente.
4. Selezionare **Ripristina utente** o **Elimina definitivamente**.

## <a name="next-steps"></a>Passaggi successivi
Questi articoli contengono informazioni aggiuntive sulla gestione utenti di Azure Active Directory.

* [Avvio rapido: Aggiungere o eliminare utenti in Azure Active Directory](add-users-azure-active-directory.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Aggiungere utenti guest da un'altra directory](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)
