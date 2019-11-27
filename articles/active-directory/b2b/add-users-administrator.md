---
title: Aggiungere utenti di collaborazione B2B nel portale di Azure-Azure AD
description: Questo articolo illustra in che modo un amministratore può aggiungere utenti guest alla propria directory da un'organizzazione partner usando la collaborazione B2B di Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7176101a1e6c1da54ddd8b5f22b61b91fbc5115a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267932"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure

Gli utenti a cui viene assegnato uno dei ruoli della directory amministratore limitati possono utilizzare il portale di Azure per invitare gli utenti di collaborazione B2B. È possibile invitare utenti guest nella directory, in un gruppo o in un'applicazione. Dopo aver invitato un utente tramite uno di questi metodi, l'account dell'utente invitato viene aggiunto ad Azure Active Directory (Azure AD) con il tipo di utente *Guest*. L'utente guest deve quindi riscattare il proprio invito per accedere alle risorse. Un invito di un utente non scade.

In seguito all'aggiunta di un utente guest alla directory, è possibile inviare all'utente guest un collegamento diretto a un'app condivisa oppure l'utente guest può selezionare l'URL di riscatto nel messaggio di posta elettronica di invito. Per informazioni sul processo di riscatto, vedere [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md).

> [!IMPORTANT]
> È necessario seguire i passaggi descritti in [Procedura: Aggiungere le informazioni sulla privacy della propria organizzazione in Azure Active Directory](https://aka.ms/adprivacystatement) per aggiungere l'URL dell'informativa sulla privacy della propria organizzazione. Come parte del primo processo riscatto per il primo invito, un utente invitato deve accettare le condizioni sulla privacy per continuare. 

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi che le impostazioni di collaborazione esterna dell'organizzazione siano configurate in modo che sia possibile invitare i guest. Per impostazione predefinita, tutti gli utenti e gli amministratori possono invitare i guest. Tuttavia, i criteri di collaborazione esterna dell'organizzazione potrebbero essere configurati in modo da impedire a determinati tipi di utenti o amministratori di invitare i guest. Per informazioni su come visualizzare e impostare questi criteri, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Aggiungere utenti guest alla directory

Per aggiungere utenti di Collaborazione B2B alla directory, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come un utente a cui è assegnato un ruolo della directory amministratore limitato o il ruolo di invito Guest.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti**.
4. Selezionare **Nuovo utente guest**.

   ![Mostra la posizione dell'opzione Nuovo utente guest nell'interfaccia utente](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > L'opzione **Nuovo utente guest** è disponibile anche nella pagina **Relazioni aziendali**. In **Azure Active Directory**, nell'area **Gestisci**, selezionare **Relazioni aziendali**.

5. Nella pagina **Nuovo utente** selezionare **Invita l'utente** e quindi aggiungere le informazioni dell'utente Guest. 

    > [!NOTE]
    > Gli indirizzi e-mail di gruppo non sono supportati; inserire l'indirizzo e-mail di una sola persona. Alcuni provider di posta elettronica consentono agli utenti di aggiungere il simbolo più (+) e il testo aggiuntivo ai relativi indirizzi e-mail per consentire operazioni come il filtraggio della posta in arrivo. Tuttavia, Azure AD attualmente non supporta più simboli in indirizzi di posta elettronica. Per evitare problemi di recapito, omettere il simbolo di segno più e tutti i caratteri seguenti fino al simbolo @.

   - **Nome.** Nome e cognome dell'utente guest.
   - **Indirizzo e-mail (obbligatorio)** . Indirizzo e-mail dell'utente guest.
   - **Messaggio personale (facoltativo)** .Includere un messaggio di benvenuto personale per l'utente guest.
   - **Gruppi**: è possibile aggiungere l'utente Guest a uno o più gruppi esistenti oppure è possibile eseguire questa operazione in un secondo momento.
   - **Ruolo della directory**: se è necessario Azure ad autorizzazioni amministrative per l'utente, è possibile aggiungerle a un ruolo di Azure ad. 

7. Selezionare **Invita** per inviare automaticamente l'invito all'utente guest. 
 
Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.


![Mostra l'utente B2B con Guest come tipo di utente](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Aggiungere utenti guest a un gruppo
Se è necessario aggiungere manualmente gli utenti di collaborazione B2B a un gruppo, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Gruppi**.
4. Selezionare un gruppo o fare clic su **Nuovo gruppo** per crearne uno nuovo. È consigliabile indicare nella descrizione del gruppo che il gruppo contiene utenti guest B2B.
5. Selezionare **Membri**. 
6. Eseguire una delle operazioni seguenti:
   - Se l'utente guest esiste già nella directory, cercare l'utente B2B. Selezionare l'utente e quindi fare clic su **Seleziona** per aggiungerlo al gruppo.
   - Se l'utente guest non esiste già nella directory, invitarlo nel gruppo immettendo l'indirizzo di posta elettronica nella casella di ricerca, digitando un messaggio personale facoltativo e quindi facendo clic su **Seleziona**. L'invito viene inviato automaticamente all'utente invitato.
     
     ![Pulsante Invita per aggiungere membri guest](./media/add-users-administrator/GroupInvite.png)
   
È anche possibile usare gruppi dinamici con la collaborazione B2B di Azure AD. Per altre informazioni, vedere [Gruppi dinamici e Collaborazione B2B di Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Aggiungere utenti guest a un'applicazione

Per aggiungere utenti di collaborazione B2B a un'applicazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
4. Selezionare l'applicazione a cui aggiungere gli utenti guest.
5. Nella dashboard applicazione selezionare **Totale utenti** per aprire il riquadro **Utenti e gruppi**.

    ![Pulsante Totale utenti per aggiungere utenti e gruppi aperti](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selezionare **Aggiungi utente**.
7. In **Aggiungi assegnazione** selezionare **Utenti e gruppi**.
8. Eseguire una delle operazioni seguenti:
   - Se l'utente guest esiste già nella directory, cercare l'utente B2B. Selezionare l'utente, fare clic su **Seleziona** e quindi fare clic su **Assegna** per aggiungere l'utente all'app.
   - Se l'utente guest non è già presente nella directory, in **Selezionare un membro o invitare un utente esterno** digitare l'indirizzo di posta elettronica dell'utente. Nella finestra di messaggio digitare un messaggio personale facoltativo. Nella finestra di messaggio fare clic su **Invita**.
           
       ![Pulsante Invita per aggiungere membri guest](./media/add-users-administrator/AppInviteUsers.png)
   
      Fare clic su **Seleziona** e quindi fare clic su **Assegna** per aggiungere l'utente all'app. L'invito viene inviato automaticamente all'utente invitato.

9. L'utente guest viene visualizzato nell'elenco **Utenti e gruppi** dell'applicazione con il ruolo assegnato di **Accesso predefinito**. Per modificare il ruolo, eseguire le operazioni seguenti:
   - Selezionare l'utente guest e quindi selezionare **Modifica**. 
   - In **Modifica assegnazione**, fare clic su **Seleziona ruolo** e selezionare il ruolo da assegnare all'utente selezionato.
   - Fare clic su **Seleziona**.
   - Fare clic su **Assegna**.
 
## <a name="resend-invitations-to-guest-users"></a>Inviare di nuovo gli inviti agli utenti guest

Se un utente guest non ha ancora riscattato il proprio invito, è possibile inviare di nuovo messaggio di posta elettronica di invito.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. In **Gestisci** selezionare **Utenti**.
5. Selezionare l'account utente.
6. In **Gestisci** selezionare **Profilo**.
7. Se l'utente non ha ancora accettato l'invito, è disponibile l'opzione **Invia di nuovo l'invito**. Selezionare questo pulsante per inviare di nuovo l'invito.

   ![Opzione Invia di nuovo l'invito nel profilo utente](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Se si invia di nuovo un invito che originariamente indirizzava l'utente a un'app specifica, tenere presente che il collegamento nel nuovo invito indirizza invece l'utente al pannello di accesso di primo livello.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gli amministratori non Azure Active Directory possono aggiungere utenti guest B2B, vedere [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](add-users-information-worker.md).
- Per informazioni sul messaggio di posta elettronica di invito, vedere [Elementi del messaggio di posta elettronica di invito per la collaborazione B2B](invitation-email-elements.md).

