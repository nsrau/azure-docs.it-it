---
title: Creare una verifica di accesso di un pacchetto di accesso in Azure AD gestione dei diritti
description: Informazioni su come creare criteri di verifica di accesso per i pacchetti di accesso alla gestione dei diritti in Azure Active Directory le verifiche di accesso (anteprima).
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
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608839"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Creare una verifica di accesso di un pacchetto di accesso in Azure AD gestione dei diritti

Per ridurre il rischio di accesso non aggiornato, è necessario abilitare le verifiche periodiche degli utenti che hanno assegnazioni attive a un pacchetto di accesso in Azure AD gestione dei diritti. È possibile abilitare le revisioni quando si crea un nuovo pacchetto di accesso o si modifica un pacchetto di accesso esistente. Questo articolo descrive come abilitare le verifiche di accesso dei pacchetti di Access.

## <a name="prerequisites"></a>Prerequisiti

Per abilitare le revisioni dei pacchetti di accesso, è necessario soddisfare i prerequisiti per la creazione di un pacchetto di accesso:
- Azure AD P2 Premium
- Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

Per ulteriori informazioni, vedere [requisiti di licenza](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Creare una verifica di accesso di un pacchetto di Access

È possibile abilitare le verifiche di accesso quando si [Crea un nuovo pacchetto di accesso](entitlement-management-access-package-create.md) o si [modifica un criterio esistente del pacchetto di accesso](entitlement-management-access-package-lifecycle-policy.md) . Per abilitare le verifiche di accesso di un pacchetto di Access, attenersi alla procedura seguente:

1. Aprire la scheda **ciclo** di vita per un pacchetto di accesso e scorrere verso il basso fino a verifiche di **accesso**.

1. Spostare l'interruttore Richiedi verifiche di **accesso** su **Sì**.

    ![Aggiungere la verifica di accesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Specificare la data in cui le verifiche inizieranno accanto a a **partire**da.

1. Successivamente, impostare la **frequenza di revisione** su **annualmente**, **bi-annuale**, **trimestrale** o **mensile**.
Questa impostazione determina la frequenza con cui si verificheranno le verifiche di accesso.

1. Impostare la **durata** per definire il numero di giorni per cui ogni revisione della serie ricorrente verrà aperta per l'input dai revisori. Ad esempio, è possibile pianificare una revisione annuale che inizia il 1 ° gennaio ed è aperta per la revisione per 30 giorni, in modo che i revisori abbiano fino alla fine del mese per rispondere.

1. Accanto a **revisori**selezionare **autoverifica** se si vuole che gli utenti eseguano la propria verifica di accesso o selezionare **revisori specifici** se si vuole designare un revisore.

    ![Selezionare Aggiungi revisori](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se è stato selezionato un **revisore specifico**, specificare quali utenti eseguiranno la verifica di accesso:
    1. Selezionare **Aggiungi revisori**.
    1. Nel riquadro **Seleziona revisori** cercare e selezionare gli utenti di cui si vuole essere un revisore.
    1. Dopo aver selezionato i revisori, fare clic sul pulsante **Seleziona** .

    ![Specificare i revisori](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Fare clic su **Verifica + crea** se si sta creando un nuovo pacchetto di accesso o un **aggiornamento** se si sta modificando un pacchetto di accesso, nella parte inferiore della pagina.

## <a name="view-the-status-of-the-access-review"></a>Visualizzare lo stato della verifica di accesso

Dopo la data di inizio, una verifica di accesso viene elencata nella sezione verifiche di **accesso** . Per visualizzare lo stato di una verifica di accesso, seguire questa procedura:

1. In **governance delle identità**fare clic su **pacchetti** di accesso e quindi selezionare il pacchetto di accesso con lo stato di verifica dell'accesso da controllare.   

1. Quando si è pronti alla panoramica di Access Package, fare clic su verifiche di **accesso** nel menu a sinistra.
    
    ![Selezionare le verifiche di accesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Verrà visualizzato un elenco contenente tutti i criteri a cui sono associate le verifiche di accesso. Fare clic su verifica per visualizzare il report.

    ![Elenco di verifiche di accesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando si Visualizza il report, viene visualizzato il numero di utenti esaminati e le azioni intraprese dal revisore.

    ![Visualizza lo stato di Revisione](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Revisioni di accesso notifiche di posta elettronica
È possibile designare i revisori oppure gli utenti possono verificarne l'accesso. Per impostazione predefinita, Azure AD invierà un messaggio di posta elettronica ai revisori o ai revisori poco dopo l'avvio della verifica.

Il messaggio di posta elettronica includerà istruzioni su come verificare l'accesso ai pacchetti di Access. Se la verifica è destinata agli utenti a verificare l'accesso, visualizzare le istruzioni su come eseguire una verifica automatica dei pacchetti di accesso.
  
Se gli utenti Guest sono stati assegnati come revisori e non hanno accettato l'invito Guest Azure AD, non riceveranno messaggi di posta elettronica dalle verifiche di accesso Azure AD. Prima di poter ricevere i messaggi di posta elettronica, è necessario accettare l'invito e creare un account con Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- [Verifica l'accesso ai pacchetti di accesso](entitlement-management-access-reviews-review-access.md)
- [Revisione automatica dei pacchetti di accesso](entitlement-management-access-reviews-self-review.md)
