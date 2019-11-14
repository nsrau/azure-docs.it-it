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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073492"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Aggiungere o eliminare utenti tramite Azure Active Directory

Aggiungere nuovi utenti o eliminare gli utenti esistenti dall'organizzazione Azure Active Directory (Azure AD). Per aggiungere o eliminare utenti, è necessario essere un amministratore utente o un amministratore globale.

## <a name="add-a-new-user"></a>Aggiungere un nuovo utente

Con il portale Azure Active Directory è possibile creare un nuovo utente.

Per aggiungere un nuovo utente, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'organizzazione.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina.

1. Selezionare **utenti**, quindi selezionare **nuovo utente**.

    ![Aggiungere un utente tramite utenti: tutti gli utenti in Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Nella pagina **utente** immettere le informazioni per l'utente:

   - **Nome**. Obbligatorio. Nome e cognome del nuovo utente. Ad esempio, *Mary Parker*.

   - **Nome utente**. Obbligatorio. Nome utente del nuovo utente. Ad esempio: `mary@contoso.com`.

     Per la parte del dominio del nome utente deve essere utilizzato il nome di dominio predefinito iniziale, *\<nomedominio >. onmicrosoft. com*o un nome di dominio personalizzato, ad esempio *contoso.com*. Per altre informazioni su come creare un nome di dominio personalizzato, vedere [aggiungere il nome di dominio personalizzato usando il portale di Azure Active Directory](add-custom-domain.md).

   - **Gruppi**. Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento. Per ulteriori informazioni sull'aggiunta di utenti ai gruppi, vedere [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Ruolo della directory**: se è necessario Azure ad autorizzazioni amministrative per l'utente, è possibile aggiungerle a un ruolo di Azure ad. È possibile assegnare all'utente un ruolo di amministratore globale o uno o più ruoli di amministratore limitati in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere [Come assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md).

   - **Informazioni sul processo**: è possibile aggiungere altre informazioni sull'utente oppure eseguirle in un secondo momento. Per altre informazioni sull'aggiunta di informazioni sull'utente, vedere [Come aggiungere o modificare informazioni di un profilo utente](active-directory-users-profile-azure-portal.md).

1. Copiare la password generata automaticamente specificata nella casella **password** . Sarà necessario assegnare questa password all'utente per accedere per la prima volta.

1. Selezionare **Create**.

L'utente viene creato e aggiunto all'organizzazione Azure AD.

## <a name="add-a-new-guest-user"></a>Aggiungere un nuovo utente Guest

È anche possibile invitare un nuovo utente Guest per collaborare con l'organizzazione selezionando **invita utente** nella pagina **nuovo utente** . Se vengono configurate le impostazioni di collaborazione esterna dell'organizzazione in modo che sia possibile invitare i guest, l'utente verrà inviato tramite posta elettronica a un invito che deve accettare per iniziare la collaborazione. Per ulteriori informazioni sull'invito di utenti di collaborazione B2B, vedere [invitare utenti B2B a Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Aggiungere un utente consumer

Potrebbero essere presenti scenari in cui si desidera creare manualmente gli account utente nella directory Azure Active Directory B2C (Azure AD B2C). Per ulteriori informazioni sulla creazione di account utente, vedere [creare ed eliminare utenti consumer in Azure ad B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Aggiungere un nuovo utente all'interno di un ambiente ibrido

Se l'ambiente usa sia Azure Active Directory (cloud) che Windows Server Active Directory (locale), è possibile aggiungere nuovi utenti sincronizzando i dati degli account utente esistenti. Per altre informazioni sugli ambienti ibridi e sugli utenti, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Eliminare un utente

Con il portale Azure Active Directory è possibile eliminare un utente esistente.

Per eliminare un utente, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore utente per l'organizzazione.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina.

1. Cercare e selezionare l'utente che si vuole eliminare dal tenant di Azure AD. Ad esempio, _Mary Parker_.

1. Selezionare **Elimina utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Elimina utente evidenziato](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

L'utente viene eliminato e non è più visualizzato nella pagina **Utenti - Tutti gli utenti**. L'utente può essere visualizzato nella pagina **Utenti eliminati** per i 30 giorni successivi e durante tale periodo può essere ripristinato. Per ulteriori informazioni sul ripristino di un utente, vedere [ripristinare o rimuovere un utente eliminato di recente utilizzando Azure Active Directory](active-directory-users-restore.md).

Quando un utente viene eliminato, le licenze utilizzate dall'utente vengono rese disponibili per altri utenti.

>[!Note]
>È necessario utilizzare Windows Server Active Directory per aggiornare l'identità, le informazioni di contatto o le informazioni sui processi per gli utenti la cui origine di autorità è Windows Server Active Directory. Dopo aver completato l'aggiornamento, è necessario attendere il ciclo di sincronizzazione successivo prima di poter visualizzare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Una volta aggiunti gli utenti, è possibile eseguire i processi di base seguenti:

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- [Utilizzare gruppi e utenti dinamici](../users-groups-roles/groups-create-rule.md)

In alternativa, è possibile eseguire altre attività di gestione degli utenti, ad esempio l' [aggiunta di utenti Guest da un'altra directory](../b2b/what-is-b2b.md) o [il ripristino di un utente eliminato](active-directory-users-restore.md). Per altre informazioni sulle altre azioni disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).
