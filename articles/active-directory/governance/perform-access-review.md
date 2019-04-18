---
title: Verificare l'accesso a gruppi o applicazioni in verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Informazioni su come verificare l'accesso dei membri del gruppo o l'accesso alle applicazioni in Azure Active Directory le verifiche di accesso.
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
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496028"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Verificare l'accesso a gruppi o applicazioni in Azure AD le verifiche di accesso

Azure Active Directory (Azure AD) semplifica le aziende gestiscono l'accesso a gruppi e applicazioni in Azure AD e vengono esaminati altri Microsoft Online Services con una funzionalità denominata accesso di Azure AD.

Questo articolo descrive come un revisore designato esegue una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso è per trovare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft in cui viene chiesto di verificare l'accesso. Di seguito è riportato un messaggio di esempio per verificare l'accesso per un gruppo.

    ![Messaggio di posta elettronica di verifica di accesso](./media/perform-access-review/access-review-email.png)

1. Scegliere il **avviare verifica** collegamento per aprire la verifica di accesso.

Se non si ha l'indirizzo di posta elettronica, è possibile trovare che l'accesso in sospeso esamina seguendo questa procedura.

1. Accedere al portale di MyApps all'indirizzo [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portale MyApps](./media/perform-access-review/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Scegliere il **verifiche di accesso** riquadro per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco delle verifiche di accesso](./media/perform-access-review/access-reviews-list.png)

1. Scegliere il **inizia la verifica** collegamento per la verifica di accesso da eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, verranno visualizzati i nomi degli utenti che devono essere esaminati.

Se la richiesta di verifica del proprio accesso, la pagina avrà un aspetto diversa. Per altre informazioni, vedere [verifica di accesso personali a gruppi o applicazioni](review-your-access.md).

![Eseguire una verifica di accesso](./media/perform-access-review/perform-access-review.png)

Esistono due modi, che è possibile approvare o negare l'accesso:

- È possibile approvare o rifiutare ogni richiesta singolarmente, o
- È possibile accettare i suggerimenti di sistema, che rappresenta il modo più semplice e rapido.

### <a name="approve-or-deny-access-for-each-request"></a>Approvare o negare l'accesso per ogni richiesta

1. Esaminare l'elenco di utenti di decidere se approvare o negare l'accesso continuo.

1. Per approvare o rifiutare ogni richiesta, fare clic sulla riga per aprire una finestra per specificare l'azione da intraprendere.

1. Fare clic su **approvare** oppure **negare**. Se si è certi, è possibile fare clic su **Don ' t know**. Questa operazione comporterà l'utente di mantenere l'accesso, ma la selezione si rifletteranno nel log di controllo.

    ![Eseguire una verifica di accesso](./media/perform-access-review/approve-deny.png)

1. Se necessario, immettere un motivo nel **motivo** casella.

    L'amministratore di verifica di accesso potrebbe richiedere che si specifichino un motivo per l'approvazione dell'accesso continuo o l'appartenenza al gruppo.

1. Dopo aver specificato l'azione da eseguire, fare clic su **salvare**.

    Se si desidera modificare la risposta, selezionare la riga e aggiornare la risposta. Ad esempio, è possibile approvare per un utente di accesso negato in precedenza o negare un accesso approvato in precedenza. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui l'amministratore definisce due revisori: Alice e Bob. Alice si apre innanzitutto la verifica di accesso e quindi approva l'accesso. Prima che termini la revisione, Bob apre la verifica di accesso, nega l'accesso. L'ultimo negare risposta è ciò che viene registrato.

    > [!NOTE]
    > Se un utente viene negato l'accesso, non vengono rimossi immediatamente. Questi vengono rimossi quando la verifica è stata completata o quando un amministratore interrompe la verifica.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approvare o negare l'accesso in base alle raccomandazioni

Per rendere più facile e veloce le verifiche di accesso per te, offriamo anche consigli che è possibile accettare con un solo clic. Le raccomandazioni vengono generate basato su attività di accesso dell'utente.

1. Nella barra blu nella parte inferiore della pagina, fare clic su **accettare le raccomandazioni**.

    ![Accetta le raccomandazioni](./media/perform-access-review/accept-recommendations.png)

    Viene visualizzato un riepilogo delle azioni consigliate.

    ![Accettare le raccomandazioni riepilogo](./media/perform-access-review/accept-recommendations-summary.png)

1. Fare clic su **accettabile** per accettare i suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso dei gruppi o applicazioni](complete-access-review.md)
