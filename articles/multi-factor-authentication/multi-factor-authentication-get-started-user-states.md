---
title: Stati utente in Microsoft Azure Multi-Factor Authentication
description: Informazioni sugli stati utente in Azure MFA.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Stati utente in Azure Multi-Factor Authentication
Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Stato | Descrizione | App interessate non basate su browser | 
|:---:|:---:|:---:|
| Disabled |Lo stato predefinito per un nuovo utente non registrato alla modalità Multi-Factor Authentication (MFA). |No |
| Enabled |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Verrà richiesto di eseguire la registrazione al successivo accesso. |No.  Continuano a funzionare fino al completamento della registrazione. |
| Enforced |L'utente è stato iscritto e ha completato il processo di registrazione per Azure MFA. |Sì.  Le app richiedono password per le app. |

## <a name="changing-a-user-state"></a>Modifica dello stato utente
Lo stato dell'utente indica se un amministratore ha eseguito la relativa iscrizione in Azure MFA e se l'utente ha completato il processo di registrazione.

Tutti gli utenti iniziano con *disabilitato*. Quando si registrano gli utenti in Azure MFA, il relativo stato cambia in *abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *applicato*.  

### <a name="view-user-states"></a>Visualizzare gli stati utente

Per accedere alla pagina in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory relativa all'utente da visualizzare.
   ![Selezionare la directory - screenshot](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Selezionare **Utenti**.
5. Nella parte inferiore della pagina selezionare **Gestisci Multi-Factor Auth**. 
   ![Selezionare Gestisci Multi-Factor Auth - screenshot](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Verrà visualizzata una nuova scheda in cui sono visualizzati gli stati utente.
   ![Stati utente in Microsoft Azure Multi-Factor Authentication - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>Cambiare lo stato da disabilitato ad abilitato

1. Usare la procedura precedente per visualizzare la pagina Utenti Multi-Factor Authentication. 
2. Trovare l'utente che si vuole abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **disabilitato**.
   ![Trova utente - screenshot](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, sotto Azioni rapide, fare clic su **Abilita**.
   ![Abilitare l'utente selezionato - screenshot](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. Selezionare **Abilita Multi-Factor Auth**.
   ![Abilita Multi-Factor Auth - screenshot](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. Si noti che lo stato dell'utente è cambiato da **disabilitato** in **abilitato**.
   ![Verificare che l'utente è ora abilitato - screenshot](./media/multi-factor-authentication-get-started-cloud/user.png)

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Indicare anche che verrà richiesto di eseguire la registrazione al successivo accesso e che alcune app non basate su browser potrebbero non funzionare con la verifica in due passaggi. È possibile anche includere un collegamento alla [guida dell'utente finale ad Azure MFA](./end-user/multi-factor-authentication-end-user.md) con informazioni utili per iniziare. 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Per modificare lo stato da abilitato/applicato a disabilitato

1. Usare la procedura per [visualizzare gli stati utente](#view-user-states) per visualizzare la pagina Utenti Multi-Factor Authentication.
6. Cercare l'utente da disabilitare. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **abilitato** o **applicato**.
7. Selezionare la casella accanto al nome.
8. A destra, sotto Azioni rapide, fare clic su **Disabilita**.
   ![Disabilita utente - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. Verrà chiesto di confermare l'azione. Fare clic su **Sì**.
10. Se l'utente è stato disabilitato, viene visualizzato un messaggio che conferma l'operazione. Fare clic su **Close**.


