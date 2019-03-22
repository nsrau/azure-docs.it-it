---
title: 'Aggiungere o eliminare utenti: Azure Active Directory | Microsoft Docs'
description: Informazioni su come aggiungere nuovi utenti o eliminare utenti esistenti tramite Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8770648a3683c4f612536c9a04921682a01bcd0c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089812"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Aggiungere o eliminare utenti tramite Azure Active Directory
Aggiungere nuovi utenti o eliminare utenti esistenti dalla tenant di Azure Active Directory (Azure AD).

## <a name="add-a-new-user"></a>Aggiungere un nuovo utente
Con il portale Azure Active Directory è possibile creare un nuovo utente.

### <a name="to-add-a-new-user"></a>Per aggiungere un nuovo utente
1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale o amministratore utenti per la directory.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare **Nuovo utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Nuovo utente evidenziato](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Nella pagina **Utente** inserire le informazioni richieste.

    ![Aggiunta di un nuovo utente, pagina Utente con le informazioni sull'utente](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nome (obbligatorio).** Nome e cognome del nuovo utente. Ad esempio, Mary Parker.

   - **Nome utente (obbligatorio).** Nome utente del nuovo utente. Ad esempio: mary@contoso.com. 
    
       La parte relativa al dominio del nome utente deve usare il nome di dominio predefinito iniziale, <_nomedominio_>.onmicrosoft.com o un nome di dominio personalizzato, ad esempio contoso.com. Per altre informazioni su come creare un nome di dominio personalizzato, vedere [Come aggiungere un nome di dominio personalizzato ad Azure Active Directory](add-custom-domain.md).

   - **Profilo.** Facoltativamente, è possibile aggiungere altre informazioni sull'utente. È anche possibile aggiungere informazioni sull'utente in un secondo momento. Per altre informazioni sull'aggiunta di informazioni sull'utente, vedere [Come aggiungere o modificare informazioni di un profilo utente](active-directory-users-profile-azure-portal.md).

   - **Gruppi.** Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento. Per altre informazioni sull'aggiunta di utenti a gruppi, vedere [Come creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md).

   - **Ruolo directory.** Facoltativamente, è possibile aggiungere l'utente a un ruolo della directory. È possibile assegnare all'utente il ruolo di amministratore globale o uno o più degli altri ruoli di amministratore in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere [Come assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md).

4. Copiare la password generata automaticamente nella casella **Password**. È necessario fornire la password all'utente per la procedura di accesso iniziale.

5. Selezionare **Create**.

    L'utente viene creato e aggiunto al tenant di Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Aggiungere un nuovo utente all'interno di un ambiente ibrido
Se l'ambiente usa sia Azure Active Directory (cloud) che Windows Server Active Directory (locale), è possibile aggiungere nuovi utenti sincronizzando i dati degli account utente esistenti. Per altre informazioni sugli ambienti ibridi e sugli utenti, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminare un utente
Con il portale Azure Active Directory è possibile eliminare un utente esistente.

### <a name="to-delete-a-user"></a>Per eliminare un utente
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente che si vuole eliminare dal tenant di Azure AD, ad esempio, _Mary Parker_.

3. Selezionare **Elimina utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Elimina utente evidenziato](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    L'utente viene eliminato e non è più visualizzato nella pagina **Utenti - Tutti gli utenti**. L'utente può essere visualizzato nella pagina **Utenti eliminati** per i 30 giorni successivi e durante tale periodo può essere ripristinato. Per altre informazioni sul ripristino di un utente, vedere [Come ripristinare o rimuovere definitivamente un utente eliminato di recente](active-directory-users-restore.md).

    >[!Note]
    >Per aggiornare l'identità, le informazioni di contatto o le informazioni professionali per gli utenti la cui origine dell'autorità è Windows Server Active Directory, è necessario usare Windows Server Active Directory. Dopo aver completato l'aggiornamento, è necessario attendere il ciclo di sincronizzazione successivo prima di poter visualizzare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver aggiunto gli utenti, è possibile eseguire i processi di base seguenti:

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Utilizzare gruppi e utenti dinamici](../users-groups-roles/groups-create-rule.md)

In alternativa, è possibile eseguire altre attività di gestione degli utenti, ad esempio [aggiungere utenti guest da un'altra directory](../b2b/what-is-b2b.md) oppure [ripristinare un utente eliminato](active-directory-users-restore.md). Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).
