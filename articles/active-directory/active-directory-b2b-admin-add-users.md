---
title: Aggiungere utenti di Collaborazione B2B nel portale di Azure - Azure Active Directory | Microsoft Docs
description: Questo articolo illustra in che modo un amministratore può aggiungere utenti guest alla propria directory da un'organizzazione partner usando la collaborazione B2B di Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure

Un amministratore globale o un utente a cui è assegnato uno dei ruoli della directory di amministratore con autorizzazioni limitate può usare il portale di Azure per invitare utenti di Collaborazione B2B. È possibile invitare utenti guest nella directory, in un gruppo o in un'applicazione. Dopo aver invitato un utente tramite uno di questi metodi, l'account dell'utente invitato viene aggiunto ad Azure Active Directory (Azure AD) con il tipo di utente *Guest*. L'utente guest deve quindi riscattare il proprio invito per accedere alle risorse.

## <a name="add-guest-users-to-the-directory"></a>Aggiungere utenti guest alla directory

Per aggiungere utenti di Collaborazione B2B alla directory, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti e gruppi** > **Tutti gli utenti**.
4. Selezionare **Nuovo utente guest**.

   ![Mostra la posizione dell'opzione Nuovo utente guest nell'interfaccia utente](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. In **Invitare un utente guest** immettere l'indirizzo di posta elettronica dell'utente esterno. Se si vuole, includere un messaggio di benvenuto. Ad esempio: 

   ![Mostra la posizione dell'opzione Nuovo utente guest nell'interfaccia utente](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Selezionare **Invita** per inviare automaticamente l'invito all'utente guest. Nell'area **Notifica** cercare il messaggio **L'utente è stato invitato**. 
 
Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.


![Mostra l'utente B2B con Guest come tipo di utente](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Aggiungere utenti guest a un gruppo
Se è necessario aggiungere manualmente utenti di Collaborazione B2B a un gruppo come amministratore di AD Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti e gruppi** > **Tutti i gruppi**.
4. Selezionare un gruppo o fare clic su **Nuovo gruppo** per crearne uno nuovo. È consigliabile indicare nella descrizione del gruppo che il gruppo contiene utenti guest B2B.
5. Selezionare **Membri** > **Aggiungi membri**. 
6. Eseguire una di queste operazioni:
   - Se l'utente guest esiste già nella directory, cercare l'utente B2B. Selezionare l'utente e fare clic su **Seleziona** per aggiungerlo al gruppo.
   - Se l'utente guest non esiste già nella directory, selezionare **Invita**.
   ![Pulsante Invita per aggiungere membri guest](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      In **Invitare un utente guest** immettere l'indirizzo di posta elettronica e un messaggio personale facoltativo, quindi selezionare **Invita**. Fare clic su **Seleziona** per aggiungere l'utente al gruppo.

      L'invito viene inviato automaticamente all'utente invitato. Nell'area **Notifica** cercare il messaggio **L'utente è stato invitato** che indica che l'invito è stato inviato. 

È anche possibile usare gruppi dinamici con la collaborazione B2B di Azure AD. Per altre informazioni, vedere [Gruppi dinamici e Collaborazione B2B di Azure Active Directory](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Aggiungere utenti guest a un'applicazione

Per aggiungere utenti di Collaborazione B2B a un'applicazione come amministratore di Azure AD, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
4. Selezionare l'applicazione a cui aggiungere gli utenti guest.
5. In **Gestisci**selezionare **Utenti e gruppi**.
6. Selezionare **Aggiungi utente**.
7. In **Aggiungi assegnazione** selezionare **Utenti e gruppi**.
8. Eseguire una di queste operazioni:
   - Se l'utente guest esiste già nella directory, cercare l'utente B2B. Selezionare l'utente e quindi fare clic su **Seleziona** per aggiungerlo all'app.
   - Se l'utente guest non esiste già nella directory, selezionare **Invita**.
   ![Pulsante Invita per aggiungere membri guest](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      In **Invitare un utente guest** immettere l'indirizzo di posta elettronica e un messaggio personale facoltativo, quindi selezionare **Invita**. Fare clic su **Seleziona** per aggiungere l'utente all'app.

      L'invito viene inviato automaticamente all'utente invitato. Nell'area **Notifica** cercare il messaggio **L'utente è stato invitato** che indica che l'invito è stato inviato.

9. In **Aggiungi assegnazione** fare clic su **Selezionare un ruolo**, selezionare un ruolo da applicare all'utente selezionato (se applicabile), quindi scegliere **OK**.
10. Fare clic su **Assegna**.
 
## <a name="resend-invitations-to-guest-users"></a>Inviare di nuovo gli inviti agli utenti guest

Se un utente guest non ha ancora riscattato il proprio invito, è possibile inviare di nuovo l'invito.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci**selezionare **Utenti e gruppi**.
4. Selezionare **Tutti gli utenti**.
5. Selezionare l'account utente.
6. In **Gestisci** selezionare **Profilo**.
7. Se l'utente non ha ancora accettato l'invito, è disponibile l'opzione **Invia di nuovo l'invito**. Selezionare questo pulsante per inviare di nuovo l'invito.

   ![Opzione Invia di nuovo l'invito nel profilo utente](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> Se si invia di nuovo un invito che originariamente indirizzava l'utente a un'app specifica, tenere presente che il collegamento nel nuovo invito indirizza invece l'utente al pannello di accesso di primo livello.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gli amministratori non Azure Active Directory possono aggiungere utenti guest B2B, vedere [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md).
- Per informazioni sul messaggio di posta elettronica di invito, vedere [Elementi del messaggio di posta elettronica di invito per la collaborazione B2B](active-directory-b2b-invitation-email.md).
- Per informazioni sul processo di riscatto dell'invito, vedere [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md).


