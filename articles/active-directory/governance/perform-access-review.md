---
title: Esaminare l'accesso ai gruppi & alle applicazioni nelle verifiche di accesso - Azure ADReview access to groups & applications in access reviews - Azure AD
description: Informazioni su come esaminare l'accesso dei membri del gruppo o dell'accesso alle applicazioni nelle verifiche di accesso ad Azure Active Directory.Learn how to review access of group members or application access in Azure Active Directory access reviews.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128459"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Esaminare l'accesso a gruppi e applicazioni nelle verifiche di accesso di Azure ADReview access to groups and applications in Azure AD access reviews

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso a gruppi e applicazioni in Azure AD e altri Microsoft Online Services con una funzionalità denominata Recensioni di accesso di Azure AD.

In questo articolo viene descritto come un revisore designato esegue una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso consiste nel trovare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft che chiede di controllare l'accesso. Di seguito è riportato un messaggio di posta elettronica di esempio per esaminare l'accesso per un gruppo.

    ![Esempio di posta elettronica da Microsoft per esaminare l'accesso a un gruppo](./media/perform-access-review/access-review-email.png)

1. Fare clic sul collegamento **Avvia revisione** per aprire la verifica di accesso.

Se non si dispone dell'e-mail, è possibile trovare le verifiche di accesso in sospeso attenendosi alla procedura seguente.

1. Accedere al portale MyApps [https://myapps.microsoft.com](https://myapps.microsoft.com)all'indirizzo .

    ![Portale MyApps che elenca le app per le quali si dispone delle autorizzazioni](./media/perform-access-review/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Fare clic sul riquadro **Revisioni** di accesso per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco di verifiche di accesso in sospeso per app e gruppi](./media/perform-access-review/access-reviews-list.png)

1. Fare clic sul collegamento **Avvia revisione** per la verifica di accesso che si desidera eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, vengono visualizzati i nomi degli utenti che devono essere esaminati.

Se la richiesta consiste nel rivedere il proprio accesso, la pagina avrà un aspetto diverso. Per ulteriori informazioni, consultate [Esaminare l'accesso a gruppi o applicazioni.](review-your-access.md)

![Revisione di accesso aperto che elenca gli utenti che devono essere esaminati](./media/perform-access-review/perform-access-review.png)

È possibile approvare o negare l'accesso in due modi:

- È possibile approvare o negare l'accesso a uno o più utenti oppure
- È possibile accettare i consigli di sistema, che è il modo più semplice e veloce.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Approvare o negare l'accesso a uno o più utenti

1. Esaminare l'elenco di utenti per decidere se approvare o negare l'accesso continuo.

1. Per approvare o negare l'accesso a un singolo utente, fare clic sulla riga per aprire una finestra per specificare l'azione da eseguire. Per approvare o negare l'accesso a più utenti, aggiungere segni di spunta accanto agli utenti, quindi fare clic sul pulsante **Controlla X utenti** per aprire una finestra per specificare l'azione da eseguire.

1. Fare clic su **Approva** o **Nega**. In caso di dubbi, è possibile fare clic su **Non si conosce**. In questo modo l'utente manterrà l'accesso, ma la selezione si rifletterà nei registri di controllo.

    ![Finestra Azione che include le opzioni Approva, Nega e Non conosci](./media/perform-access-review/approve-deny.png)

1. Se necessario, immettere un motivo nella casella **Motivo.**

    L'amministratore della verifica dell'accesso potrebbe richiedere di fornire un motivo per l'approvazione dell'accesso continuo o dell'appartenenza al gruppo.

1. Dopo aver specificato l'azione da eseguire, fare clic su **Salva**.

    Se si desidera modificare la risposta, selezionare la riga e aggiornare la risposta. Ad esempio, è possibile approvare un utente negato in precedenza o negare un utente approvato in precedenza. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob.Consider an example where an administrator designates two reviewers – Alice and Bob. Alice apre prima la verifica di accesso e approva l'accesso. Prima del termine della revisione, Bob apre la verifica di accesso e nega l'accesso. L'ultima risposta di negazione è ciò che viene registrato.

    > [!NOTE]
    > Se a un utente viene negato l'accesso, non viene rimosso immediatamente. Vengono rimossi quando la revisione è terminata o quando un amministratore interrompe la revisione.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approvare o rifiutare l'accesso in base ai suggerimenti

Per rendere le recensioni di accesso più facili e veloci per te, ti forniamo anche consigli che puoi accettare con un solo clic. I consigli vengono generati in base all'attività di accesso dell'utente.

1. Nella barra blu nella parte inferiore della pagina fare clic su **Accetta suggerimenti**.

    ![Aprire l'elenco dei controlli di accesso con il pulsante Accetta consigli](./media/perform-access-review/accept-recommendations.png)

    Viene visualizzato un riepilogo delle azioni consigliate.

    ![Finestra che visualizza un riepilogo delle azioni consigliate](./media/perform-access-review/accept-recommendations-summary.png)

1. Fare clic **su OK** per accettare i suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
