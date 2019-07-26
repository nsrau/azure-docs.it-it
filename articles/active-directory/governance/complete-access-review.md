---
title: Completare una verifica di accesso di gruppi o applicazioni-Azure Active Directory | Microsoft Docs
description: Informazioni su come completare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c405032368ffd06f5808bc4518302d2f6d66b9
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489152"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Completare una verifica di accesso di gruppi o applicazioni in Azure AD verifiche di accesso

In qualità di amministratore, si [Crea una verifica di accesso di gruppi o applicazioni](create-access-review.md) e i revisori [eseguono la verifica dell'accesso](perform-access-review.md). Questo articolo descrive come visualizzare i risultati della verifica di accesso e applicare i risultati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisiti

- Azure AD P2 Premium
- Amministratore globale, Amministratore utenti, amministratore della sicurezza o lettore sicurezza

Per altre informazioni, vedere [quali utenti devono avere licenze?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Visualizzare una verifica di accesso

È possibile tenere traccia dello stato di avanzamento dei revisori che completano le proprie revisioni.

1. Accedere alla portale di Azure e aprire la [pagina governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra fare clic su verifiche di **accesso**.

1. Nell'elenco fare clic su una verifica di accesso.

    Per visualizzare una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti nelle verifiche pianificate.

    Nella pagina **Panoramica** è possibile visualizzare lo stato di avanzamento. Nessun diritto di accesso viene modificato nella directory fino al completamento della verifica.

    ![Analisi di accesso-stato](./media/complete-access-review/overview-progress.png)

1. Se si desidera arrestare una verifica di accesso prima di aver raggiunto la data di fine pianificata, fare clic sul pulsante **Arresta** .

    Quando si arresta una revisione, i revisori non saranno più in grado di fornire risposte. Non è possibile riavviare una verifica dopo che è stata interrotta.

1. Se non si è più interessati alla verifica di accesso, è possibile eliminarlo facendo clic sul pulsante **Elimina** .

## <a name="apply-the-changes"></a>Applicare le modifiche.

Se l' **opzione applica automaticamente i risultati alla risorsa** è stata abilitata e si basa sulle selezioni effettuate in **al termine delle impostazioni**, l'applicazione automatica verrà eseguita dopo la data di fine della verifica o quando si arresta manualmente la revisione.

Se l' **opzione applica automaticamente i risultati alla risorsa** non è stata abilitata per la revisione, fare clic su **applica** per applicare manualmente le modifiche. Se l'accesso di un utente è stato negato durante la verifica, quando si fa clic su **applica**Azure ad rimuove l'assegnazione dell'applicazione o dell'appartenenza.

![Applicare le modifiche della verifica di accesso](./media/complete-access-review/apply-changes.png)

Lo stato della revisione passerà da **completato** a stati intermedi, ad esempio l' **applicazione** e infine il **risultato**di stato applicato. È possibile che, dopo alcuni minuti, eventuali utenti rifiutati vengano rimossi dall'appartenenza al gruppo o dall'assegnazione dell'applicazione.

Se per una verifica si configura l'applicazione automatica o si seleziona **Applica**, questa operazione non avrà alcun effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico. Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="retrieve-the-results"></a>Recuperare i risultati

Per visualizzare i risultati di una verifica di accesso monouso, fare clic sulla pagina **risultati** . Per visualizzare solo l'accesso di un utente, nella casella di ricerca digitare il nome visualizzato o il nome dell'entità utente di un utente il cui accesso è stato esaminato.

![Recuperare i risultati per una verifica di accesso](./media/complete-access-review/retrieve-results.png)

Per visualizzare lo stato di una verifica di accesso attivo ricorrente, fare clic sulla pagina **risultati** .

Per visualizzare i risultati di un'istanza completata di una verifica di accesso ricorrente, fare clic su **Verifica cronologia**, quindi selezionare l'istanza specifica dall'elenco di istanze di verifica di accesso completate in base alla data di inizio e di fine dell'istanza. I risultati di questa istanza possono essere ottenuti dalla pagina **risultati** .

Per recuperare tutti i risultati di una verifica di accesso, fare clic sul pulsante **download** . Il file CSV risultante può essere visualizzato in Excel o in altri programmi aperti in file CSV con codifica UTF-8.

## <a name="remove-users-from-an-access-review"></a>Rimuovere gli utenti da una verifica di accesso

 Per impostazione predefinita, un utente eliminato rimarrà comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario.  Dopo 30 giorni l'utente verrà eliminato definitivamente.  Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo.  Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle verifiche di accesso attive.  Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](manage-guest-access-with-access-reviews.md)
- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
