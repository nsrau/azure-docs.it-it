---
title: Completare una verifica di accesso di gruppi o applicazioni - Azure Active Directory | Microsoft Docs
description: Informazioni su come completare una verifica di accesso dei membri del gruppo o l'accesso alle applicazioni in Azure Active Directory le verifiche di accesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473361"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Completare una verifica di accesso dei gruppi o applicazioni in Azure AD le verifiche di accesso

Come amministratore, si [creare una verifica di accesso di gruppi o applicazioni](create-access-review.md) e i revisori [eseguire la verifica dell'accesso](perform-access-review.md). Questo articolo descrive come visualizzare i risultati della verifica di accesso e applicare i risultati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisiti

- Azure AD P2 Premium
- Lettura per la sicurezza, l'utente amministratore, amministratore della sicurezza o amministratore globale

Per altre informazioni, vedere [quali gli utenti devono avere le licenze?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Visualizzare una verifica di accesso

È possibile tenere traccia dello stato di avanzamento verifiche eseguite dai revisori loro.

1. Accedere al portale di Azure e aprire il [pagina di governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra, fare clic su **verifiche di accesso**.

1. Nell'elenco, fare clic su una verifica di accesso.

    Per visualizzare una serie di accesso recensioni, passare alla verifica di accesso e troverai le ricorrenze future nelle revisioni pianificato.

    Nel **Panoramica** pagina, è possibile visualizzare lo stato di avanzamento. Nessun diritto di accesso viene modificato nella directory fino a quando non viene completata la revisione.

    ![Lo stato di avanzamento delle verifiche di accesso](./media/complete-access-review/overview-progress.png)

1. Se si desidera interrompere una verifica di accesso in precedenza ha raggiunto la data di fine pianificata, scegliere il **arrestare** pulsante.

    Quando interrompere una verifica, i revisori non saranno in grado di fornire risposte. Non è possibile riavviare una verifica dopo che è stata interrotta.

1. Se si è non è più interessati durante la verifica di accesso, è possibile eliminare facendo il **eliminare** pulsante.

## <a name="apply-the-changes"></a>Applicare le modifiche.

Se **applica automaticamente i risultati alla risorsa** era abilitata e sulla base delle selezioni nella **impostazioni al completamento**, auto-applicare will da eseguire dopo la data di fine dell'analisi o quando si arresta manualmente la revisione.

Se **applica automaticamente i risultati alla risorsa** non è stato abilitato per la revisione, fare clic su **applica** applicare manualmente le modifiche. Se l'accesso un utente è stato negato durante la verifica, quando si fa clic **applica**, Azure AD rimuoverà l'assegnazione dell'applicazione o dell'appartenenza.

![Applicare le modifiche di verifica di accesso](./media/complete-access-review/apply-changes.png)

Lo stato della verifica passerà da **Completed** attraverso alcuni stati intermedi, ad esempio **applicazione** e infine allo stato **risultato è stato applicato**. È possibile che, dopo alcuni minuti, eventuali utenti rifiutati vengano rimossi dall'appartenenza al gruppo o dall'assegnazione dell'applicazione.

Se per una verifica si configura l'applicazione automatica o si seleziona **Applica**, questa operazione non avrà alcun effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico. Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="retrieve-the-results"></a>Recuperare i risultati

Per visualizzare i risultati di una verifica di accesso monouso, scegliere il **risultati** pagina. Per visualizzare solo un utente l'accesso, nella casella di ricerca, digitare il nome visualizzato o il nome dell'entità utente di un utente il cui accesso è stata rivista.

![Recuperare i risultati per una verifica di accesso](./media/complete-access-review/retrieve-results.png)

Per visualizzare lo stato di avanzamento di una verifica di accesso attivo che è ricorrente, fare clic sui **risultati** pagina.

Per visualizzare i risultati di un'istanza completata di una verifica di accesso che è ricorrente, fare clic su **esaminare la cronologia**, quindi selezionare l'istanza specifica nell'elenco di istanze di verifica di accesso completati, in base dell'istanza data di inizio e fine. I risultati di questa istanza possono essere ottenuti dal **risultati** pagina.

Per recuperare tutti i risultati di una verifica di accesso, fare clic sui **scaricare** pulsante. Il file CSV risultante può essere visualizzato in Excel o in altri programmi aperti in file CSV con codifica UTF-8.

## <a name="remove-users-from-an-access-review"></a>Rimuovere utenti da una verifica di accesso

 Per impostazione predefinita, un utente eliminato rimarrà comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario.  Dopo 30 giorni l'utente verrà eliminato definitivamente.  Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo.  Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle verifiche di accesso attive.  Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](manage-guest-access-with-access-reviews.md)
- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
