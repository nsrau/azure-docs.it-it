---
title: Assegnare ruoli della directory agli utenti - Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: e8646893d6dd57fd3f743f450f438cd962f02b36
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095121"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory
Se un utente nell'organizzazione richiede l'autorizzazione per gestire le risorse di Azure Active Directory (Azure AD), è necessario assegnare all'utente un ruolo appropriato in Azure AD, in base alle azioni per cui richiede l'autorizzazione.

Per altre informazioni sui ruoli disponibili, vedere [Assegnazione del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Per informazioni sull'aggiunta di nuovi utenti, vedere [Aggiungere nuovi utenti ad Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Assegnare ruoli
Un modo comune per assegnare ruoli di Azure AD a un utente è usare la pagina **Ruolo directory** per l'utente.

È anche possibile assegnare ruoli con Azure AD Privileged Identity Management (PIM). Per altre informazioni su come usare PIM, vedere [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Per assegnare un ruolo a un utente
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente a cui assegnare il ruolo. Ad esempio, _Alain Charon_.

3. Nella pagina **Alain Charon - Profilo** selezionare **Ruolo directory**.

    Viene visualizzata la pagina **Alain Charon - Ruolo directory**.

4. Selezionare **Aggiungi ruolo**, selezionare il ruolo da assegnare ad Alain (ad esempio, _Amministratore dell'applicazione_) e quindi scegliere **Seleziona**.

    ![Pagina dei ruoli della directory con il ruolo selezionato](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Il ruolo Amministratore dell'applicazione viene assegnato ad Alain Charon e viene visualizzato nella pagina **Alain Charon - Ruolo directory**.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo
Se è necessario rimuovere l'assegnazione di ruolo a un utente, è possibile farlo anche dalla pagina **Alain Charon - Ruolo directory**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Per rimuovere un'assegnazione di ruolo a un utente

1. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente per il quale si vuole rimuovere l'assegnazione del ruolo. Ad esempio, _Alain Charon_.

2. Selezionare **Ruolo directory**, **Amministratore dell'applicazione** e **Rimuovi ruolo**.

    ![Pagina dei ruoli della directory con il ruolo selezionato e l'opzione di rimozione visualizzati](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Il ruolo Amministratore dell'applicazione viene rimosso per Alain Charon e non viene più visualizzato nella pagina **Alain Charon - Ruolo directory**.

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti guest da un'altra directory](../b2b/what-is-b2b.md)

Oppure è possibile eseguire altre attività di gestione, ad esempio l'assegnazione di delegati, l'uso di criteri e la condivisione degli account utente. Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).


