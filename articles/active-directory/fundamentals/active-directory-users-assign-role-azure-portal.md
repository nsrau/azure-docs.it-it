---
title: Assegnare ruoli della directory agli utenti - Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b063a4d51d5d5d1e3d7949be3754ccbe74acca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604115"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory
Se un utente nell'organizzazione richiede l'autorizzazione per gestire le risorse di Azure Active Directory (Azure AD), è necessario assegnare all'utente un ruolo appropriato in Azure AD, in base alle azioni per cui richiede l'autorizzazione.

Per ulteriori informazioni sui ruoli disponibili, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Per informazioni sull'aggiunta di nuovi utenti, vedere [Aggiungere nuovi utenti ad Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Assegnare ruoli
Un modo comune per assegnare ruoli di Azure AD a un utente è usare la pagina **Ruolo directory** per l'utente.

È anche possibile assegnare ruoli con Azure AD Privileged Identity Management (PIM). Per altre informazioni su come usare PIM, vedere [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Per assegnare un ruolo a un utente
1. Passare alla [portale di Azure](https://portal.azure.com/) e accedere con un account di amministratore globale per la directory. 

2. Cercare e selezionare **Azure Active Directory**.

      ![Ricerca di Azure Active Directory nel portale di Azure](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selezionare **utenti**.

4. Cercare e selezionare l'utente che sta ottenendo l'assegnazione di ruolo. Ad esempio, _Alain Charon_.

      ![Pagina tutti gli utenti: selezionare l'utente](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Nella pagina **Alain Charn-profile** selezionare **ruoli assegnati**.

    Viene visualizzata la pagina **Alain Charon - Ruolo directory**.

6. Selezionare **Aggiungi assegnazione**, selezionare il ruolo da assegnare a Alain, ad esempio _amministratore applicazione_, quindi scegliere **Seleziona**.

    ![Pagina ruoli assegnati-visualizzazione del ruolo selezionato](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Il ruolo Amministratore dell'applicazione viene assegnato ad Alain Charon e viene visualizzato nella pagina **Alain Charon - Ruolo directory**.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo
Se è necessario rimuovere l'assegnazione di ruolo a un utente, è possibile farlo anche dalla pagina **Alain Charon - Ruolo directory**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Per rimuovere un'assegnazione di ruolo a un utente

1. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente per il quale si vuole rimuovere l'assegnazione del ruolo. Ad esempio, _Alain Charon_.

2. Selezionare **ruoli assegnati**, selezionare **amministratore applicazione**, quindi selezionare **Rimuovi assegnazione**.

    ![Pagina ruoli assegnati che mostra il ruolo selezionato e l'opzione Rimuovi](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Il ruolo Amministratore dell'applicazione viene rimosso per Alain Charon e non viene più visualizzato nella pagina **Alain Charon - Ruolo directory**.

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti guest da un'altra directory](../b2b/what-is-b2b.md)

Oppure è possibile eseguire altre attività di gestione, ad esempio l'assegnazione di delegati, l'uso di criteri e la condivisione degli account utente. Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).


