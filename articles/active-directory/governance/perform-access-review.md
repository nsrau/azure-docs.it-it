---
title: Verificare l'accesso a gruppi o applicazioni nelle verifiche di accesso-Azure Active Directory | Microsoft Docs
description: Informazioni su come verificare l'accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
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
ms.openlocfilehash: e51ad8faecd935ea999b1287e542e4b14d55290a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948234"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Verificare l'accesso a gruppi o applicazioni in Azure AD verifiche di accesso

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso ai gruppi e alle applicazioni in Azure AD e in altri Microsoft Online Services con una funzionalità denominata verifiche di accesso Azure AD.

Questo articolo descrive il modo in cui un revisore designato esegue una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione.

## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Il primo passaggio per eseguire una verifica di accesso consiste nell'individuare e aprire la verifica di accesso.

1. Cercare un messaggio di posta elettronica da Microsoft che richiede di verificare l'accesso. Di seguito è riportato un esempio di messaggio di posta elettronica per esaminare l'accesso per un gruppo.

    ![Messaggio di posta elettronica di esempio da Microsoft per verificare l'accesso a un gruppo](./media/perform-access-review/access-review-email.png)

1. Fare clic sul collegamento **Avvia verifica** per aprire la verifica di accesso.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le verifiche di accesso in sospeso seguendo questa procedura.

1. Accedere al portale app Web in [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portale app Web che elenca le app a cui si hanno le autorizzazioni](./media/perform-access-review/myapps-access-panel.png)

1. Fare clic sul simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

1. Fare clic sul riquadro verifiche di **accesso** per visualizzare un elenco delle verifiche di accesso in sospeso.

    Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

    ![Elenco delle verifiche di accesso in sospeso per le app e i gruppi](./media/perform-access-review/access-reviews-list.png)

1. Fare clic sul collegamento **Begin Review** per la verifica di accesso che si desidera eseguire.

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Dopo aver aperto la verifica di accesso, verranno visualizzati i nomi degli utenti che devono essere esaminati.

Se la richiesta prevede di verificare il proprio accesso, l'aspetto della pagina sarà diverso. Per altre informazioni, vedere [verificare l'accesso per se stessi a gruppi o applicazioni](review-your-access.md).

![Aprire la verifica di accesso elencando gli utenti che devono essere esaminati](./media/perform-access-review/perform-access-review.png)

È possibile approvare o negare l'accesso in due modi:

- È possibile approvare o negare l'accesso per uno o più utenti o
- È possibile accettare le raccomandazioni di sistema, il modo più semplice e rapido.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Approva o nega l'accesso per uno o più utenti

1. Esaminare l'elenco di utenti per decidere se approvare o negare l'accesso continuo.

1. Per approvare o negare l'accesso per un singolo utente, fare clic sulla riga per aprire una finestra per specificare l'azione da eseguire. Per approvare o negare l'accesso per più utenti, aggiungere i segni di spunta accanto agli utenti e quindi fare clic sul pulsante **Verifica utente/i X** per aprire una finestra per specificare l'azione da eseguire.

1. Fare clic su **approva** o **Nega**. Se non si è certi, è possibile fare clic su **non so**. Questa operazione comporterà la gestione dell'accesso dell'utente, ma la selezione verrà applicata ai log di controllo.

    ![Finestra azione che include le opzioni approva, nega e non so](./media/perform-access-review/approve-deny.png)

1. Se necessario, immettere un motivo nella casella **motivo** .

    Per l'amministratore della verifica di accesso potrebbe essere necessario fornire un motivo per l'approvazione dell'accesso continuo o dell'appartenenza a un gruppo.

1. Dopo aver specificato l'azione da eseguire, fare clic su **Salva**.

    Se si desidera modificare la risposta, selezionare la riga e aggiornare la risposta. Ad esempio, è possibile approvare un utente negato in precedenza o negare un utente precedentemente approvato. È possibile modificare la risposta in qualsiasi momento fino al termine della verifica di accesso.

    Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob. Alice apre prima di tutto la verifica di accesso e approva l'accesso. Prima della fine della verifica, Bob apre la verifica di accesso e nega l'accesso. L'ultima risposta nega è ciò che viene registrato.

    > [!NOTE]
    > Se a un utente viene negato l'accesso, questi non vengono rimossi immediatamente. Vengono rimossi al termine della verifica o quando un amministratore interrompe la revisione.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approva o nega l'accesso in base alle raccomandazioni

Per semplificare e velocizzare le verifiche di accesso, sono disponibili anche consigli che è possibile accettare con un solo clic. Le indicazioni vengono generate in base all'attività di accesso dell'utente.

1. Nella barra blu nella parte inferiore della pagina fare clic su **accetta raccomandazioni**.

    ![Aprire l'elenco di controllo di accesso che mostra il pulsante accetta raccomandazioni](./media/perform-access-review/accept-recommendations.png)

    Viene visualizzato un riepilogo delle azioni consigliate.

    ![Finestra che visualizza un riepilogo delle azioni consigliate](./media/perform-access-review/accept-recommendations-summary.png)

1. Fare clic su **OK** per accettare le raccomandazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
