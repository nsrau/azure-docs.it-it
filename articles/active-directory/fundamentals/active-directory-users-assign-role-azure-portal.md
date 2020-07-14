---
title: Assegnare ruoli Azure AD agli utenti-Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5dc42d8308b8e20a647f5f64867fd78a1a09c96
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224017"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory

In Azure Active Directory (Azure AD), se uno degli utenti necessita dell'autorizzazione per gestire le risorse Azure AD, è necessario assegnarle a un ruolo che fornisce le autorizzazioni necessarie. Per informazioni sui ruoli che gestiscono le risorse di Azure e sui ruoli che gestiscono Azure AD risorse, vedere [ruoli di amministratore della sottoscrizione classica, ruoli di Azure e ruoli di Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Per ulteriori informazioni sui ruoli di Azure AD disponibili, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Per aggiungere utenti, vedere [aggiungere nuovi utenti a Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Assegnare ruoli

Un modo comune per assegnare Azure AD ruoli a un utente è nella pagina **ruoli assegnati** per un utente. È anche possibile usare l'idoneità per l'elevazione JIT in un ruolo usando Privileged Identity Management (PIM). Per ulteriori informazioni sull'utilizzo di PIM, vedere [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

> [!Note]
> Se si dispone di un piano di licenza Azure AD Premium P2 e si utilizza già PIM, tutte le attività di gestione dei ruoli vengono eseguite nell' [esperienza Privileged Identity Management](../users-groups-roles/directory-manage-roles-portal.md).
>
> ![Azure AD ruoli gestiti in PIM per gli utenti che usano già PIM e hanno una licenza Premium P2](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente

1. Passare alla [portale di Azure](https://portal.azure.com/) e accedere con un account di amministratore globale per la directory.

2. Cercare e selezionare **Azure Active Directory**.

      ![Ricerca di Azure Active Directory nel portale di Azure](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selezionare **Utenti**.

4. Cercare e selezionare l'utente che sta ottenendo l'assegnazione di ruolo. Ad esempio, _Alain Charon_.

      ![Pagina tutti gli utenti: selezionare l'utente](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Nella pagina **Alain Charn-profile** selezionare **ruoli assegnati**.

    Viene visualizzata la pagina **Alain Caronte-administrative roles** .

6. Selezionare **Aggiungi assegnazioni**, selezionare il ruolo da assegnare a Alain, ad esempio _amministratore applicazione_, quindi scegliere **Seleziona**.

    ![Pagina ruoli assegnati-visualizzazione del ruolo selezionato](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Il ruolo di amministratore dell'applicazione viene assegnato a Alain Caronte e viene visualizzato nella pagina **Alain caronten-administrative roles** .

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Se è necessario rimuovere l'assegnazione di ruolo da un utente, è anche possibile eseguire questa operazione dalla pagina **Alain Charn-administrative roles** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Per rimuovere un'assegnazione di ruolo a un utente

1. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente per il quale si vuole rimuovere l'assegnazione del ruolo. Ad esempio, _Alain Charon_.

2. Selezionare **ruoli assegnati**, selezionare **amministratore applicazione**, quindi selezionare **Rimuovi assegnazione**.

    ![Pagina ruoli assegnati che mostra il ruolo selezionato e l'opzione Rimuovi](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Il ruolo di amministratore dell'applicazione viene rimosso da Alain Caronte e non viene più visualizzato nella pagina **Alain Caronte-administrative roles** .

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti guest da un'altra directory](../b2b/what-is-b2b.md)

Altre attività di gestione degli utenti che è possibile estrarre sono disponibili nella [documentazione relativa alla gestione degli utenti Azure Active Directory](../users-groups-roles/index.yml).
