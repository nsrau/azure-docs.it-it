---
title: Verificare l'accesso di un pacchetto di accesso in Azure AD gestione dei diritti
description: Informazioni su come completare una verifica di accesso dei pacchetti di accesso alla gestione dei diritti in Azure Active Directory le verifiche di accesso (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78a69301ab2ae637531f8643d9d57a8b44b563f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608930"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Verificare l'accesso di un pacchetto di accesso in Azure AD gestione dei diritti

Azure AD gestione dei diritti semplifica il modo in cui le aziende gestiscono l'accesso a gruppi, applicazioni e siti di SharePoint. Questo articolo descrive come eseguire verifiche di accesso per altri utenti assegnati a un pacchetto di accesso come revisore designato.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le assegnazioni dei pacchetti di accesso attivi degli utenti, è necessario soddisfare i prerequisiti per eseguire una verifica di accesso:
- Azure AD P2 Premium
- Amministratore globale
- Amministratore utente designato, proprietario del catalogo o gestione pacchetti di accesso

Per ulteriori informazioni, vedere [requisiti di licenza](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Per trovare e aprire la verifica di accesso, attenersi alla procedura seguente:

1. Il licenziatario potrà ricevere un messaggio di posta elettronica da Microsoft che richiede di verificare l'accesso. Individuare il messaggio di posta elettronica per aprire la verifica di accesso. Di seguito è riportato un messaggio di posta elettronica di esempio per verificare l'accesso:
    
    ![Indirizzo di posta elettronica del revisore della verifica di accesso](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Fare clic sul collegamento **Verifica accesso utente** per aprire la verifica di accesso. 

1. Se non si dispone del messaggio di posta elettronica, è possibile trovare le verifiche di accesso in sospeso passando direttamente a https://myaccess.microsoft.com.

1. Fare clic su verifiche di **accesso** sulla barra di spostamento a sinistra per visualizzare un elenco delle verifiche di accesso in sospeso assegnate all'utente.
    
    ![Selezionare le verifiche di accesso per l'accesso](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Fare clic sulla revisione che si desidera avviare.
    
    ![Selezionare la verifica di accesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Una volta aperta la verifica di accesso, vengono visualizzati i nomi degli utenti per i quali è necessario esaminarli. È possibile approvare o negare l'accesso in due modi:
- È possibile approvare o negare manualmente l'accesso per uno o più utenti
- È possibile accettare le raccomandazioni di sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Approva o nega manualmente l'accesso per uno o più utenti
1. Esaminare l'elenco degli utenti e determinare gli utenti che devono continuare ad accedere.

    ![Elenco di utenti da rivedere](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Per approvare o negare l'accesso, selezionare il pulsante di opzione a sinistra del nome dell'utente.

1. Selezionare **approva** o **Nega** nella barra sopra i nomi utente.

    ![Selezionare l'utente](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Se non si è certi, è possibile fare clic sul pulsante **non so** .

    Se si effettua questa selezione, l'utente mantiene l'accesso e questa selezione viene inserita nei log di controllo. Il log Mostra gli altri revisori ancora completati per la revisione.

1. Potrebbe essere necessario fornire un motivo per la decisione. Digitare un motivo e fare clic su **Invia**.

    ![Approvare o negare l'accesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. È possibile modificare la decisione in qualsiasi momento prima della fine della verifica. A tale scopo, selezionare l'utente dall'elenco e modificare la decisione. Ad esempio, è possibile approvare l'accesso per un utente negato in precedenza.

Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob. Alice apre prima di tutto la revisione e approva l'accesso. Prima della fine della verifica, Bob apre la revisione e nega l'accesso. In questo caso, viene registrata l'ultima decisione di negazione dell'accesso.

>[!NOTE]
>Se a un utente viene negato l'accesso, questi non vengono rimossi immediatamente dal pacchetto di accesso. L'utente verrà rimosso dal pacchetto di accesso al termine della verifica oppure un amministratore conclude la revisione.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Approva o nega l'accesso usando le raccomandazioni generate dal sistema

Per esaminare più rapidamente l'accesso per più utenti, è possibile usare le raccomandazioni generate dal sistema, accettando le raccomandazioni con un solo clic. Le indicazioni vengono generate in base all'attività di accesso dell'utente.

1.  Nella barra nella parte superiore della pagina fare clic su **accetta raccomandazioni**.
    
    ![Selezionare accetta raccomandazioni](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Verrà visualizzato un riepilogo delle azioni consigliate.

1.  Fare clic su **Submit (Invia** ) per accettare le raccomandazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Revisione automatica dei pacchetti di accesso](entitlement-management-access-reviews-self-review.md)