---
title: Verifica di accesso personali a gruppi o applicazioni in verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Informazioni su come verificare il proprio accesso a gruppi o applicazioni in Azure Active Directory le verifiche di accesso.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fe2013ff84dd0451fed7d108539606520cb9403
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384513"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Verifica di accesso personali a gruppi o applicazioni in Azure AD le verifiche di accesso

Azure Active Directory (Azure AD) semplifica le aziende gestiscono l'accesso alle applicazioni o i gruppi in Azure AD e vengono esaminati altri Microsoft Online Services con una funzionalità denominata accesso di Azure AD.

Questo articolo descrive come verificare il proprio accesso a un gruppo o un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso è per trovare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft in cui viene chiesto di verificare l'accesso. Di seguito è riportato un messaggio di esempio per verificare l'accesso a un gruppo.

    ![Messaggio di posta elettronica di verifica di accesso](./media/review-your-access/access-review-email.png)

1. Scegliere il **verificare l'accesso** collegamento per aprire la verifica di accesso.

Se non si ha l'indirizzo di posta elettronica, è possibile trovare che l'accesso in sospeso esamina seguendo questa procedura.

1. Accedere al portale di MyApps all'indirizzo [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portale MyApps](./media/review-your-access/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Sul lato destro della pagina, scegliere il **verifiche di accesso** riquadro per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco delle verifiche di accesso](./media/review-your-access/access-reviews-list.png)

1. Scegliere il **inizia la verifica** collegamento per la verifica di accesso da eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, è possibile visualizzare l'accesso.

1. Verificare l'accesso e decidere se è ancora necessario l'accesso.

    Se la richiesta è di verificare l'accesso ad altri utenti, la pagina avrà un aspetto diversa. Per altre informazioni, vedere [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

    ![Eseguire una verifica di accesso](./media/review-your-access/perform-access-review.png)

1. Fare clic su **Yes** mantenere l'accesso o fare clic su **No** di rimuovere l'accesso.

1. Se si fa clic **Yes**, potrebbe essere necessario specificare una giustificazione nel **motivo** casella.

    ![Eseguire una verifica di accesso](./media/review-your-access/perform-access-review-submit.png)

1. Fare clic su **Submit**.

    Viene inviata la selezione e il portale MyApps di nuovo.

    Se si desidera modificare la risposta, aprire nuovamente la pagina di verifiche di accesso e la risposta all'aggiornamento. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    > [!NOTE]
    > Se è indicato che non è più necessario l'accesso, non vengono rimossi immediatamente. Vengono rimossi quando la verifica è stata completata o quando un amministratore interrompe la verifica.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso dei gruppi o applicazioni](complete-access-review.md)
