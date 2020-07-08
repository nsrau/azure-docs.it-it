---
title: Gestire le impostazioni utente per Azure Multi-Factor Authentication-Azure Active Directory
description: Informazioni su come configurare Azure Active Directory impostazioni utente per Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295738ee5943a6cf54bc7e1e3ce4bba621dbe29f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658676"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gestire le impostazioni utente per Azure Multi-Factor Authentication

Per semplificare la gestione degli utenti di Azure Multi-Factor Authentication, è possibile richiedere agli utenti di reimpostare la password, ripetere la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti. Per gli utenti che hanno definito password di app, è anche possibile scegliere di eliminare queste password, causando un errore di autenticazione legacy in tali applicazioni. Queste azioni potrebbero essere necessarie se è necessario fornire assistenza a un utente o se si desidera reimpostare lo stato di sicurezza.

## <a name="manage-user-authentication-options"></a>Gestisci opzioni di autenticazione utente

Se viene assegnato il ruolo di *amministratore dell'autenticazione* , è possibile richiedere agli utenti di reimpostare la password, ripetere la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente. Per gestire le impostazioni utente, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory**  >  **utenti**  >  **tutti gli utenti**.
1. Scegliere l'utente su cui si vuole eseguire un'azione e selezionare **metodi di autenticazione**. Nella parte superiore della finestra, scegliere una delle opzioni seguenti per l'utente:
   - **Reset password Reimposta** la password dell'utente e assegna una password temporanea che deve essere modificata al successivo accesso.
   - È **necessario ripetere la registrazione** dell'autenticazione a più fattori, in modo che quando l'utente esegue l'accesso alla prossima volta, viene richiesto di configurare un nuovo metodo di autenticazione a più fattori.
   
      > [!NOTE]
      > I metodi di autenticazione attualmente registrati dell'utente non vengono eliminati quando un amministratore richiede la ripetizione della registrazione per l'autenticazione a più fattori. Quando un utente esegue nuovamente la registrazione per l'autenticazione a più fattori, è consigliabile esaminare le informazioni di sicurezza ed eliminare eventuali metodi di autenticazione precedentemente registrati che non sono più utilizzabili.
   
   - **Revoca le sessioni** di autenticazione a più fattori consente di cancellare le sessioni di autenticazione a più fattori memorizzate dall'utente e le richiede di eseguire l'autenticazione a più fattori la volta successiva che è richiesta dal criterio sul dispositivo.

   ![Gestire i metodi di autenticazione dal portale di Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Se necessario, è possibile eliminare tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app. Per eseguire questa azione, sono necessarie le autorizzazioni di *amministratore globale* .

Per eliminare le password dell'app di un utente, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Sul lato sinistro selezionare **Azure Active Directory**  >  **utenti**  >  **tutti gli utenti**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare l'opzione del menu. Selezionare lo screenshot di esempio seguente per visualizzare la finestra completa del portale di Azure e la posizione del menu: [![](media/howto-mfa-userstates/selectmfa-cropped.png "Selezionare Multi-Factor Authentication dalla finestra Utenti in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
1. Selezionare **Gestisci impostazioni utente**, quindi selezionare la casella **Elimina tutte le password dell'app esistenti generate dagli utenti selezionati**, come illustrato nell'esempio seguente: ![ eliminare tutte le password dell'app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selezionare **Save (Salva**) e quindi **Close (Chiudi**).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha consentito di configurare le singole impostazioni utente. Per configurare le impostazioni del servizio Multi-Factor Authentication di Azure, vedere [configurare le impostazioni di azure multi-factor authentication](howto-mfa-mfasettings.md)

Se gli utenti necessitano di assistenza, vedere il [manuale dell'utente per Azure multi-factor authentication](../user-help/multi-factor-authentication-end-user.md).
