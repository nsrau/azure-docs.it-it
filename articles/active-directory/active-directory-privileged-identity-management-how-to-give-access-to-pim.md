---
title: Come concedere l'accesso per la gestione di Privileged Identity Management - Azure | Documentazione Microsoft
description: Informazioni su come aggiungere ruoli agli utenti con l'estensione Azure Active Directory Privileged Identity Management per consentire la gestione di PIM.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: aeaefb484b29da6e89c2c3c650a79a881b3fa5b6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Concedere l'accesso per la gestione di Azure AD Privileged Identity Management
L'amministratore globale che abilita Azure AD Privileged Identity Management (PIM) per un'organizzazione ottiene automaticamente le assegnazioni di ruolo e l'accesso a PIM. Per impostazione predefinita, nessun altro utente ottiene l'accesso in scrittura, inclusi gli altri amministratori globali. Gli altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza hanno l'accesso in sola lettura ad Azure AD PIM. Per concedere l'accesso a PIM, il primo utente può assegnare ad altri il ruolo di **amministratore dei ruoli con privilegi** . Questa assegnazione deve essere eseguita dall'interno PIM e non può essere modificata tramite PowerShell o altri portali.

> [!NOTE]
> Per la gestione di Azure AD PIM è necessario Azure Multi-Factor Authentication. Poiché gli account Microsoft non possono effettuare la registrazione per Azure MFA, gli utenti che eseguono l'accesso con un account Microsoft non possono accedere a Azure AD PIM.
> 
> 

Assicurarsi che almeno due utenti abbiano sempre il ruolo di amministratore dei ruoli con privilegi, per l'eventualità in cui uno di questi venga bloccato o il suo account venga eliminato.

## <a name="give-another-user-access-to-manage-pim"></a>Concedere a un altro utente l'accesso per la gestione di PIM
1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'app **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare **Gestione dei ruoli con privilegi** > **Amministratore dei ruoli con privilegi** > **Aggiungi**.
   
    ![Schermata Aggiungi amministratori dei ruoli con privilegi][1]
3. Nel pannello Aggiungi utenti gestiti il passaggio 1 è già stato completato. Selezionare il passaggio 2, **Seleziona utenti** e ricercare l'utente da aggiungere.
   
    ![Schermata Seleziona gli utenti][2]
4. Selezionare l'utente nell'elenco dei risultati della ricerca e fare clic su **Fine**.
5. Fare clic su **OK** per salvare la selezione. L'utente selezionato verrà visualizzato nell'elenco degli amministratori dei ruoli con privilegi.
   
   * Ogni volta che si assegna un nuovo ruolo a un utente, questo viene configurato automaticamente come idoneo per attivare il ruolo. Se si vuole rendere l'utente permanente nel ruolo, fare clic sull'utente nell'elenco. Scegliere **Rendi permanente** dal menu delle informazioni dell'utente.
6. Inviare all'utente un collegamento a [Introduzione ad Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Rimuovere i diritti di accesso di un altro utente per la gestione di PIM
Prima di rimuovere un utente dal ruolo di amministratore dei ruoli con privilegi, assicurarsi che siano sempre presenti due utenti assegnati al ruolo.

1. Nel dashboard di PIM fare clic sul ruolo di **amministratore dei ruoli con privilegi**.  Verrà visualizzato l'elenco degli utenti attualmente assegnati al ruolo.
2. Selezionare l'utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**.  Verrà visualizzato un messaggio di richiesta di conferma.
4. Fare clic su **Sì** per rimuovere l'utente dal ruolo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
