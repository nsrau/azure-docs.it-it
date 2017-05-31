---

title: Aggiungere nuovi utenti ad Azure Active Directory | Documentazione Microsoft
description: Illustra come aggiungere nuovi utenti o modificare le informazioni sugli utenti in Azure Active Directory.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand;jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 5f1f28b6e0135fbd76fa5f6664f457f259dd9dd4
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="add-new-users-to-azure-active-directory"></a>Aggiungere nuovi utenti in Azure Active Directory
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-users-create-azure-portal.md)
> * [portale di Azure classico](active-directory-create-users.md)
>
>

Questo articolo illustra come aggiungere nuovi utenti all'organizzazione in Azure Active Directory, ovvero Azure AD. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura del pannello Utenti e gruppi](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti gli utenti** e quindi selezionare **Aggiungi**.

   ![Selezione del comando Aggiungi](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "foo.onmicrosoft.com" del nome di dominio predefinito iniziale o un nome di dominio verificato, non federato, ad esempio "contoso.com".
5. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
6. Facoltativamente, è possibile aprire e inserire le informazioni contenute nel pannello **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).
7. Nel pannello **Utente** selezionare **Crea**.
8. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

### <a name="next-steps"></a>Passaggi successivi
* [Aggiungere un utente esterno](active-directory-users-create-external-azure-portal.md)
* [Reimpostare la password di un utente nel nuovo portale di Azure](active-directory-users-reset-password-azure-portal.md)
* [Modificare le informazioni di lavoro di un utente](active-directory-users-work-info-azure-portal.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)
* [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)

