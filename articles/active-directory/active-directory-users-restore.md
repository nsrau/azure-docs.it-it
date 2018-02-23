---
title: Ripristinare o rimuovere definitivamente un utente eliminato di recente in Azure Active Directory | Microsoft Docs
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
ms.date: 01/12/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: d8a1850f8635097364268abdf77394ba592f761b
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Ripristinare un utente eliminato in Azure Active Directory

Questo articolo comprende le istruzioni per ripristinare o eliminare definitivamente un utente eliminato in precedenza. Quando si elimina un utente in Azure Active Directory (Azure AD), l'utente eliminato viene conservato per 30 giorni dalla data di eliminazione. Durante questo periodo, l'utente e le relative proprietà possono essere ripristinate. 


## <a name="how-to-restore-a-recently-deleted-user"></a>Come ripristinare un utente eliminato di recente
Se un utente è stato eliminato di recente, tutte le informazioni della directory sono conservate. Se l'utente viene ripristinato, vengono ripristinate anche queste informazioni.

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi** &gt; **Tutti gli utenti**. 
2. In **Mostra** filtrare la pagina per visualizzare gli **Utenti eliminati di recente**. 
3. Selezionare uno o più utenti eliminati di recente.
4. Selezionare **Ripristina utente**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Come rimuovere definitivamente un utente eliminato di recente

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi** &gt; **Tutti gli utenti**. 
2. In **Mostra** filtrare la pagina per visualizzare gli **Utenti eliminati di recente**. 
3. Selezionare uno o più utenti eliminati di recente.
4. Selezionare **Elimina definitivamente**.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per ripristinare un utente sono sufficienti le autorizzazioni seguenti.

Ruolo  | Autorizzazioni 
--------- | ---------
Amministratore società<p>Supporto partner - Livello 1<p>Supporto partner - Livello 2<p>Amministratore account utente | Può ripristinare gli utenti eliminati 
Amministratore società<p>Supporto partner - Livello 1<p>Supporto partner - Livello 2<p>Amministratore account utente | Può eliminare definitivamente gli utenti

## <a name="next-steps"></a>Passaggi successivi
Questi articoli contengono informazioni aggiuntive sulla gestione utenti di Azure Active Directory.

* [Avvio rapido: Aggiungere o eliminare utenti in Azure Active Directory](add-users-azure-active-directory.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Aggiungere utenti guest da un'altra directory](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)
