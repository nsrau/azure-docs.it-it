---
title: Assegnare un utente ai ruoli di amministratore in anteprima di Azure Active Directory | Microsoft Docs
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
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 3778964172946fa928e2a908943f50897957eb42
ms.lasthandoff: 02/15/2017


---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Assegnare un utente ai ruoli di amministratore in anteprima di Azure Active Directory
Questo articolo illustra come assegnare un ruolo amministrativo a un utente in anteprima di Azure Active Directory (Azure AD). [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) Per informazioni sull'aggiunta di nuovi utenti nell'organizzazione, vedere [Aggiungere nuovi utenti ad Azure Active Directory](active-directory-users-create-azure-portal.md). Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura di Gestione utenti](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti gli utenti**.

   ![Apertura del pannello Tutti gli utenti](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Nel pannello **Utenti e gruppi - Tutti gli utenti** selezionare un utente nell'elenco.
5. Nel pannello per l'utente prescelto selezionare **Ruolo della directory** e quindi assegnare l'utente a un ruolo nell'elenco **Ruolo della directory**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

      ![Assegnazione di un utente a un ruolo](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere un utente](active-directory-users-create-azure-portal.md)
* [Reimpostare la password di un utente nel nuovo portale di Azure](active-directory-users-reset-password-azure-portal.md)
* [Modificare le informazioni di lavoro di un utente](active-directory-users-work-info-azure-portal.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)

