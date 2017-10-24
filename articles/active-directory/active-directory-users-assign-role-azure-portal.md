---
title: Assegnare un utente ai ruoli di amministratore in Azure Active Directory | Microsoft Docs
description: Illustra come cambiare le informazioni amministrative in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 7574fdf8787e799e4d73fe32df9c1ad5be93abf1
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Assegnare un utente ai ruoli di amministratore in Azure Active Directory
Questo articolo descrive come assegnare un ruolo di amministratore a un utente in Azure Active Directory (Azure AD). Per informazioni sull'aggiunta di nuovi utenti nell'organizzazione, vedere [Aggiungere nuovi utenti ad Azure Active Directory](active-directory-users-create-azure-portal.md). Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente
1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Utenti e gruppi**.

   ![Apertura di Gestione utenti](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Selezionare **Tutti gli utenti**.

   ![Apertura del gruppo Tutti gli utenti](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. Selezionare un utente dall'elenco.
2. Per l'utente scelto selezionare **Ruolo della directory** e quindi assegnare l'utente a un ruolo nell'elenco **Ruolo della directory**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

      ![Assegnazione di un utente a un ruolo](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere un utente](active-directory-users-create-azure-portal.md)
* [Reimpostare la password di un utente nel nuovo portale di Azure](active-directory-users-reset-password-azure-portal.md)
* [Modificare le informazioni di lavoro di un utente](active-directory-users-work-info-azure-portal.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)
