---
title: Esaminare l'accesso di un pacchetto di accesso nella gestione dei diritti di Azure ADReview access of an access package in Azure AD entitlement management
description: Informazioni su come completare una verifica di accesso dei pacchetti di accesso alla gestione dei diritti nelle verifiche di accesso ad Azure Active Directory (anteprima).
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
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968737"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Esaminare l'accesso di un pacchetto di accesso nella gestione dei diritti di Azure ADReview access of an access package in Azure AD entitlement management

La gestione dei diritti di Azure AD semplifica il modo in cui le aziende gestiscono l'accesso a gruppi, applicazioni e siti di SharePoint.Azure AD entitlement management simplifies how enterprises manage access to groups, applications, and SharePoint sites. In questo articolo viene descritto come eseguire revisioni di accesso per altri utenti assegnati a un pacchetto di accesso come revisore designato.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le assegnazioni dei pacchetti di accesso attivo degli utenti, è necessario soddisfare i prerequisiti per eseguire una verifica di accesso:To review users' active access package assignments, you must meet the prerequisites to do an access review:
- Azure AD Premium P2
- Amministratore globale
- Amministratore utente designato, proprietario del catalogo o gestore di pacchetti di Access

Per altre informazioni, vedere [Requisiti relativi alle licenze](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Aprire la verifica di accesso

Attenersi alla seguente procedura per trovare e aprire la verifica di accesso:

1. È possibile ricevere un messaggio di posta elettronica da Microsoft che chiede di controllare l'accesso. Individuare l'e-mail per aprire la verifica di accesso. Di seguito è riportato un esempio di posta elettronica per la revisione dell'accesso:Here is an example email to review access:
    
    ![Accedere alla posta elettronica del revisore](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Fare clic sul collegamento **Controlla accesso utente** per aprire la verifica di accesso. 

1. Se non si dispone dell'e-mail, è possibile trovare https://myaccess.microsoft.comle verifiche di accesso in sospeso passando direttamente a .  (Per il governo `https://myaccess.microsoft.us` degli Stati Uniti, utilizzare invece.)

1. Fare clic su **Revisioni** di accesso sulla barra di spostamento sinistra per visualizzare un elenco di verifiche di accesso in sospeso assegnate all'utente.
    
    ![Selezionare le verifiche di accesso in My Access](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Fai clic sulla recensione che desideri iniziare.
    
    ![Selezionare la verifica di accesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Eseguire la verifica di accesso

Una volta aperta la verifica di accesso, verranno visualizzati i nomi degli utenti per i quali è necessario esaminare. È possibile approvare o negare l'accesso in due modi:
- È possibile approvare o negare manualmente l'accesso per uno o più utenti
- È possibile accettare i consigli di sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Approvare o negare manualmente l'accesso a uno o più utenti
1. Esaminare l'elenco degli utenti e determinare quali utenti devono continuare ad avere accesso.

    ![Elenco di utenti da esaminare](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Per approvare o negare l'accesso, selezionare il pulsante di opzione a sinistra del nome dell'utente.

1. Selezionare **Approva** o **Nega** nella barra sopra i nomi utente.

    ![Selezionare l'utente](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. In caso di dubbi, è possibile fare clic sul pulsante **Non si conosce.**

    Se si effettua questa selezione, l'utente mantiene l'accesso e questa opzione viene effettuata nei registri di controllo. Il registro mostra tutti gli altri revisori che hai ancora completato la revisione.

1. Potrebbe esserti richiesto di fornire un motivo per la tua decisione. Digitare un motivo e fare clic su **Invia**.

    ![Approvare o negare l'accesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Puoi modificare la tua decisione in qualsiasi momento prima della fine della revisione. A tale scopo, selezionare l'utente dall'elenco e modificare la decisione. Ad esempio, è possibile approvare l'accesso per un utente precedentemente negato.

Se sono presenti più revisori, viene registrata l'ultima risposta inviata. Si consideri un esempio in cui un amministratore designa due revisori: Alice e Bob.Consider an example where an administrator designates two reviewers – Alice and Bob. Alice apre prima la revisione e approva l'accesso. Prima del termine della revisione, Bob apre la revisione e nega l'accesso. In questo caso, viene registrata l'ultima decisione di negazione dell'accesso.

>[!NOTE]
>Se a un utente viene negato l'accesso, non viene rimosso immediatamente dal pacchetto di accesso. L'utente verrà rimosso dal pacchetto di accesso al termine della revisione o un amministratore termina la revisione.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Approvare o negare l'accesso utilizzando i suggerimenti generati dal sistema

Per esaminare più rapidamente l'accesso per più utenti, è possibile utilizzare i consigli generati dal sistema, accettando i suggerimenti con un solo clic. I consigli vengono generati in base all'attività di accesso dell'utente.

1.  Nella barra nella parte superiore della pagina fare clic su **Accetta suggerimenti**.
    
    ![Selezionare Accetta raccomandazioni](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Verrà visualizzato un riepilogo delle azioni consigliate.

1.  Fare clic su **Invia** per accettare i suggerimenti.

## <a name="next-steps"></a>Passaggi successivi

- [Auto-revisione dei pacchetti di accesso](entitlement-management-access-reviews-self-review.md)