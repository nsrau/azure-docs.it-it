---
title: Esaminare l'accesso ai gruppi & alle app nelle verifiche di accesso - Azure ADReview your access your access to groups & apps in access reviews - Azure AD
description: Informazioni su come esaminare il proprio accesso a gruppi o applicazioni nelle verifiche di accesso di Azure Active Directory.Learn how to review your own access to groups or applications in Azure Active Directory access reviews.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422411"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Esaminare l'accesso manualmente a gruppi o applicazioni nelle verifiche di accesso di Azure ADReview for yourself to groups or applications in Azure AD access reviews

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso a gruppi o applicazioni in Azure AD e altri Microsoft Online Services con una funzionalità denominata Recensioni di accesso di Azure AD.

In questo articolo viene descritto come esaminare il proprio accesso a un gruppo o a un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso consiste nel trovare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft che chiede di controllare l'accesso. Ecco un'email di esempio per esaminare l'accesso a un gruppo.

    ![Esempio di posta elettronica di Microsoft per esaminare l'accesso a un gruppo](./media/review-your-access/access-review-email.png)

1. Fare clic sul collegamento **Verifica accesso** per aprire la verifica di accesso.

Se non si dispone dell'e-mail, è possibile trovare le verifiche di accesso in sospeso attenendosi alla procedura seguente.

1. Accedere al portale MyApps [https://myapps.microsoft.com](https://myapps.microsoft.com)all'indirizzo .

    ![Portale MyApps che elenca le app per le quali si dispone delle autorizzazioni](./media/review-your-access/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Sul lato destro della pagina fare clic sul riquadro **Revisioni** di accesso per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco di verifiche di accesso in sospeso per app e gruppi](./media/review-your-access/access-reviews-list.png)

1. Fare clic sul collegamento **Avvia revisione** per la verifica di accesso che si desidera eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Una volta aperta la verifica di accesso, è possibile visualizzare l'accesso.

1. Rivedi il tuo accesso e decidi se hai ancora bisogno di accedere.

    Se la richiesta consiste nel rivedere l'accesso per altri utenti, la pagina avrà un aspetto diverso. Per ulteriori informazioni, consultate [Esaminare l'accesso a gruppi o applicazioni.](perform-access-review.md)

    ![Verifica di accesso aperto in cui viene chiesto se è ancora necessario accedere a un gruppo](./media/review-your-access/perform-access-review.png)

1. Fare clic su **Sì** per mantenere l'accesso o su **No** per rimuoverlo.

1. Se si fa clic su **Sì**, potrebbe essere necessario specificare una giustificazione nella casella **Motivo.**

    ![Revisione dell'accesso completata in cui viene chiesto se è ancora necessario accedere a un gruppo](./media/review-your-access/perform-access-review-submit.png)

1. Fare clic su **Submit**.

    La selezione viene inviata e si torna al portale MyApps.

    Se si desidera modificare la risposta, riaprire la pagina delle verifiche di accesso e aggiornare la risposta. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    > [!NOTE]
    > Se hai indicato che non è più necessario accedere, non ti verrà rimosso immediatamente. La revisione viene rimossa al termine della revisione o quando un amministratore interrompe la revisione.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
