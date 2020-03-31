---
title: 'Aggiungere o eliminare utenti: Azure Active Directory | Microsoft Docs'
description: Informazioni su come aggiungere nuovi utenti o eliminare utenti esistenti tramite Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262113"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Aggiungere o eliminare utenti tramite Azure Active Directory

Aggiungere nuovi utenti o eliminare utenti esistenti dall'organizzazione di Azure Active Directory (Azure AD). Per aggiungere o eliminare utenti è necessario essere un amministratore utente o un amministratore globale.

## <a name="add-a-new-user"></a>Aggiungere un nuovo utente

Con il portale Azure Active Directory è possibile creare un nuovo utente.

Per aggiungere un nuovo utente, attenersi alla seguente procedura:

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore utente per l'organizzazione.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina.

1. Selezionare **Utenti**, quindi **Nuovo utente**.

    ![Aggiungere un utente tramite Utenti - Tutti gli utenti in Azure ADAdd a user through Users - All users in Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Nella pagina **Utente** immettere le informazioni per l'utente:

   - **Nome**. Obbligatorio. Nome e cognome del nuovo utente. Ad esempio, *Mary Parker*.

   - **Nome utente**. Obbligatorio. Nome utente del nuovo utente. Ad esempio: `mary@contoso.com`.

     La parte del dominio del nome utente deve utilizzare il nome di dominio predefinito iniziale, * \<il nome del dominio>.onmicrosoft.com*o un nome di dominio personalizzato, ad esempio *contoso.com*. Per altre informazioni su come creare un nome di dominio personalizzato, vedere Aggiungere il nome di dominio personalizzato usando il portale di Azure Active Directory.For more information about how to create a custom domain name, see [Add your custom domain name using the Azure Active Directory portal.](add-custom-domain.md)

   - **Gruppi**. Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento. Per altre informazioni sull'aggiunta di utenti ai gruppi, vedere [Creare un gruppo di base e aggiungere membri tramite Azure Active Directory.For](active-directory-groups-create-azure-portal.md)more information about adding users to groups, see Create a basic group and add members using Azure Active Directory .

   - **Ruolo directory:** se sono necessarie autorizzazioni amministrative di Azure AD per l'utente, è possibile aggiungerle a un ruolo di Azure AD. È possibile assegnare l'utente come amministratore globale o uno o più ruoli di amministratore limitati in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere [Come assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md).

   - **Informazioni sul lavoro**: Qui puoi aggiungere ulteriori informazioni sull'utente o farlo in un secondo momento. Per altre informazioni sull'aggiunta di informazioni sull'utente, vedere [Come aggiungere o modificare informazioni di un profilo utente](active-directory-users-profile-azure-portal.md).

1. Copiare la password generata automaticamente fornita nella casella **Password.** Dovrai fornire questa password all'utente per accedere per la prima volta.

1. Selezionare **Crea**.

L'utente viene creato e aggiunto all'organizzazione di Azure AD.

## <a name="add-a-new-guest-user"></a>Aggiungere un nuovo utente guest

È inoltre possibile invitare un nuovo utente guest a collaborare con l'organizzazione selezionando **Invita utente** dalla pagina **Nuovo utente.** Se le impostazioni di collaborazione esterna dell'organizzazione sono configurate in modo che l'utente possa invitare ospiti, all'utente verrà inviato un messaggio di posta elettronica con un invito che deve accettare per iniziare a collaborare. Per altre informazioni sull'invito di utenti di Collaborazione B2B, vedere [Invitare utenti B2B in Azure Active DirectoryFor](../b2b/add-users-administrator.md) more information about inviting B2B collaboration users, see Invite B2B users to Azure Active Directory

## <a name="add-a-consumer-user"></a>Aggiungere un utente consumer

Potrebbero esserci scenari in cui si desidera creare manualmente gli account consumer nella directory Azure Active Directory B2C (Azure AD B2C). Per altre informazioni sulla creazione di account consumer, vedere [Creare ed eliminare utenti consumer in Azure AD B2C.](../../active-directory-b2c/manage-users-portal.md)

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Aggiungere un nuovo utente all'interno di un ambiente ibrido

Se l'ambiente usa sia Azure Active Directory (cloud) che Windows Server Active Directory (locale), è possibile aggiungere nuovi utenti sincronizzando i dati degli account utente esistenti. Per altre informazioni sugli ambienti ibridi e sugli utenti, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminare un utente

Con il portale Azure Active Directory è possibile eliminare un utente esistente.

Per eliminare un utente, attenersi alla seguente procedura:

1. Accedere al [portale](https://portal.azure.com/) di Azure usando un account amministratore utente per l'organizzazione.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina.

1. Cercare e selezionare l'utente da eliminare dal tenant di Azure AD. Ad esempio, _Mary Parker_.

1. Selezionare **Elimina utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Elimina utente evidenziato](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

L'utente viene eliminato e non è più visualizzato nella pagina **Utenti - Tutti gli utenti**. L'utente può essere visualizzato nella pagina **Utenti eliminati** per i 30 giorni successivi e durante tale periodo può essere ripristinato. Per altre informazioni sul ripristino di un utente, vedere [Ripristinare o rimuovere un utente eliminato di recente tramite Azure Active Directory.](active-directory-users-restore.md)

Quando un utente viene eliminato, tutte le licenze utilizzate dall'utente vengono rese disponibili per altri utenti.

>[!Note]
>È necessario utilizzare Windows Server Active Directory per aggiornare l'identità, le informazioni di contatto o le informazioni sul processo per gli utenti la cui origine di autorità è Windows Server Active Directory. Dopo aver completato l'aggiornamento, è necessario attendere il ciclo di sincronizzazione successivo prima di poter visualizzare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto gli utenti, è possibile eseguire i processi di base seguenti:After you've added your users, you can do the following basic processes:

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Utilizzare gruppi e utenti dinamici](../users-groups-roles/groups-create-rule.md)

In alternativa, è possibile eseguire altre attività di gestione degli utenti, ad esempio [l'aggiunta di utenti guest da un'altra directory](../b2b/what-is-b2b.md) o il ripristino di [un utente eliminato.](active-directory-users-restore.md) Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).
