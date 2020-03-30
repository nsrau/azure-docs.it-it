---
title: Creare una verifica di accesso di un pacchetto di accesso nella gestione dei diritti di Azure ADCreate an access review of an access package in Azure AD entitlement management
description: Informazioni su come creare criteri di verifica dell'accesso per i pacchetti di accesso alla gestione dei diritti in Azure Active Directory Access Reviews (Preview).
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
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608839"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Creare una verifica di accesso di un pacchetto di accesso nella gestione dei diritti di Azure ADCreate an access review of an access package in Azure AD entitlement management

Per ridurre il rischio di accesso non aggiornato, è consigliabile abilitare revisioni periodiche degli utenti che dispongono di assegnazioni attive a un pacchetto di accesso nella gestione dei diritti di Azure AD. È possibile abilitare le revisioni quando si crea un nuovo pacchetto di accesso o si modifica un pacchetto di accesso esistente. In questo articolo viene descritto come abilitare le verifiche di accesso dei pacchetti di accesso.

## <a name="prerequisites"></a>Prerequisiti

Per abilitare le revisioni dei pacchetti di accesso, è necessario soddisfare i prerequisiti per la creazione di un pacchetto di accesso:To enable reviews of access packages, you must meet the prerequisites for creating an access package:
- Azure AD Premium P2
- amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

Per altre informazioni, vedere [Requisiti relativi alle licenze](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Creare una verifica di accesso di un pacchetto di accessoCreate an access review of an access package

È possibile abilitare le verifiche di accesso durante [la creazione di un nuovo pacchetto](entitlement-management-access-package-create.md) di accesso o la modifica di un criterio del pacchetto di accesso [esistente.](entitlement-management-access-package-lifecycle-policy.md) Seguire questi passaggi per abilitare le verifiche di accesso di un pacchetto di accesso:Follow these steps to enable access reviews of an access package:

1. Aprire la scheda **Ciclo di** vita di un pacchetto di accesso e scorrere verso il basso fino a **Recensioni di**accesso .

1. Spostare l'interruttore **Richiedi revisioni accesso** su **Sì**.

    ![Aggiungere la verifica di accesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Specificare la data di inizio delle revisioni accanto a **Inizia su**.

1. Impostare quindi la **frequenza** di revisione su **Annuale**, **Bietante**, **Trimestrale** o **Mensile**.
Questa impostazione determina la frequenza con cui si verificheranno le verifiche di accesso.

1. Impostare **Durata** per definire per quanti giorni ogni revisione della serie ricorrente sarà aperta per l'input dei revisori. Ad esempio, è possibile pianificare una revisione annuale che inizia il 1 gennaio ed è aperta per la revisione per 30 giorni in modo che i revisori abbiano tempo fino alla fine del mese per rispondere.

1. Accanto a **Revisori**, selezionare **Auto-revisione** se si desidera che gli utenti eseguano la propria verifica di accesso o selezionare **Revisori specifici** se si desidera designare un revisore.

    ![Selezionare Aggiungi revisori](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se è stata selezionata l'opzione **Revisori specifici,** specificare quali utenti eseguiranno la verifica dell'accesso:
    1. Selezionare **Aggiungi revisori**.
    1. Nel riquadro **Seleziona revisori** cercare e selezionare gli utenti che si desidera siano revisori.
    1. Dopo aver selezionato i revisori, fare clic sul pulsante **Seleziona.**

    ![Specificare i revisori](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Fare clic su **Revisione e creazione** se si sta creando un nuovo pacchetto di accesso o su **Aggiorna** se si sta modificando un pacchetto di accesso, nella parte inferiore della pagina.

## <a name="view-the-status-of-the-access-review"></a>Visualizzare lo stato della verifica di accesso

Dopo la data di inizio, una verifica di accesso verrà elencata nella sezione Verifiche di **accesso.** Attenersi alla seguente procedura per visualizzare lo stato di una verifica di accesso:

1. In **Identity Governance**fare clic su **Pacchetti** di accesso, quindi selezionare il pacchetto di accesso con lo stato di verifica dell'accesso che si desidera controllare.   

1. Una volta attivata la panoramica del pacchetto di accesso, fai clic su Recensioni di **accesso** nel menu a sinistra.
    
    ![Selezionare le verifiche di accesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Verrà visualizzato un elenco contenente tutti i criteri a cui sono associate verifiche di accesso. Fare clic sulla revisione per visualizzarne il report.

    ![Elenco delle verifiche di accesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando si visualizza il report, mostra il numero di utenti esaminati e le azioni eseguite dal revisore su di essi.

    ![Visualizzare lo stato della revisione](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Access esamina le notifiche email
È possibile designare i revisori o gli utenti possono esaminarne l'accesso. Per impostazione predefinita, Azure AD invierà un messaggio di posta elettronica ai revisori o agli autorevisori poco dopo l'avvio della revisione.

L'e-mail includerà istruzioni su come controllare l'accesso ai pacchetti di accesso. Se la revisione prevede la revisione dell'accesso da parte degli utenti, mostra loro le istruzioni su come eseguire un'auto-revisione dei pacchetti di accesso.
  
Se gli utenti guest sono stati assegnati come revisori e non hanno accettato l'invito guest di Azure AD, non riceveranno messaggi di posta elettronica dalle verifiche di accesso di Azure AD. Prima di poter ricevere i messaggi di posta elettronica, devono prima accettare l'invito e creare un account con Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare l'accesso ai pacchetti di accessoReview access of access packages](entitlement-management-access-reviews-review-access.md)
- [Auto-revisione dei pacchetti di accesso](entitlement-management-access-reviews-self-review.md)
