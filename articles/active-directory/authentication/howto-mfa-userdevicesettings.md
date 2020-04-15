---
title: Gestire le impostazioni utente per Azure Multi-Factor Authentication - Azure Active DirectoryManage user settings for Azure Multi-Factor Authentication - Azure Active Directory
description: Informazioni su come configurare le impostazioni utente di Azure Active Directory per Azure Multi-Factor Authentication
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
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309764"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gestire le impostazioni utente per Azure Multi-Factor AuthenticationManage user settings for Azure Multi-Factor Authentication

Per semplificare la gestione degli utenti di Azure Multi-Factor Authentication, è possibile richiedere agli utenti di reimpostare la password, registrarsi nuovamente per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti. Per gli utenti che hanno definito le password delle app, è anche possibile scegliere di eliminare queste password, causando l'esito negativo dell'autenticazione legacy in tali applicazioni. Queste azioni possono essere necessarie se è necessario fornire assistenza a un utente o si desidera reimpostare lo stato di sicurezza.

## <a name="manage-user-authentication-options"></a>Gestire le opzioni di autenticazione degli utenti

Se viene assegnato il ruolo *di amministratore* di autenticazione è possibile richiedere agli utenti di reimpostare la password, registrarsi nuovamente per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente. La procedura seguente illustra come gestire le impostazioni utente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory** > **Users** > **All Users**.
1. Scegliere l'utente su cui si desidera eseguire un'azione e selezionare **Metodi di autenticazione**. Nella parte superiore della finestra, quindi scegli una delle seguenti opzioni per l'utente:
   - **Reimposta password** reimposta la password dell'utente e assegna una password temporanea che deve essere modificata al successivo accesso.
   - **Richiedi nuovamente registrazione dell'autenticazione** a più fattori rende in modo che quando l'utente accede la prossima volta, viene richiesto di impostare un nuovo metodo di autenticazione MFA.
   - **Revoca sessioni di autenticazione a** più fattori cancella le sessioni di autenticazione a più fattori memorizzate dell'utente e richiede l'esecuzione dell'autenticazione a più fattori la volta successiva che richiesto dai criteri nel dispositivo.

   ![Gestire i metodi di autenticazione dal portale di AzureManage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Eliminare le password per le app esistenti degli utenti

Se necessario, è possibile eliminare tutte le password dell'app create da un utente. Le app non basate su browser associate a tali password dell'app non funzioneranno più fino a quando non verrà creata una nuova password dell'app. Per eseguire questa azione sono necessarie autorizzazioni *di amministratore globale.*

La procedura seguente illustra come eliminare le password dell'app di un utente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Sul lato sinistro selezionare **Azure Active Directory** > **Users** > **All users**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare questa opzione di menu. Selezionare la schermata di esempio seguente per visualizzare la finestra completa del portale di Azure e la posizione del menu:Select the example screenshot below to see the full Azure portal window and menu location:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selezionare Multi-Factor Authentication nella finestra Utenti in Azure ADSelect Multi-Factor Authentication from the Users window in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
1. Selezionare **Gestisci impostazioni utente**, quindi selezionare la casella Elimina tutte le password delle app esistenti generate dagli utenti **selezionati,** come illustrato nell'esempio seguente: ![Elimina tutte le password dell'app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selezionare **Salva**, quindi **chiudere**.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha aiutato a configurare le impostazioni dei singoli utenti. Per configurare le impostazioni del servizio Azure Multi-Factor Authentication, vedere [Configure Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md)

Se gli utenti necessitano di assistenza, vedere la Guida per l'utente [per Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
