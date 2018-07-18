---
title: Recuperare i risultati della verifica di accesso di Azure AD| Microsoft Docs
description: Come recuperare i risultati delle verifiche di accesso di Azure Active Directory.
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
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 96676c3ec6d96fd6c6f78aa2dbed8cf4d0a6718d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295301"
---
# <a name="retrieve-access-review-results"></a>Recuperare i risultati della verifica di accesso

Gli amministratori possono usare Azure Active Directory (Azure AD) per [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md) per gli utenti o i membri del gruppo assegnati a un'applicazione.  Un utente che dispone del ruolo di **amministratore globale**, **amministratore account utente**, **amministratore della sicurezza** o del **ruolo con autorizzazioni di lettura per la sicurezza** può anche leggere i risultati di una verifica di accesso.  Al fine di assegnare utenti a uno di questi ruoli, un Amministratore dei ruoli con privilegi può usare Azure AD PIM per rendere idoneo un utente ad attivare il ruolo oppure un Amministratore globale può [assegnare un utente al ruolo](fundamentals/active-directory-users-assign-role-azure-portal.md) definitivamente.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Individuazione di una verifica di accesso

Se si conosce quale programma contiene la verifica di accesso, accedere alla pagina delle verifiche di accesso, scegliere **Programmi** e quindi selezionare il programma che contiene il controllo della verifica di accesso.  Scegliere quindi **Controlli** e selezionare il controllo della verifica di accesso. Se sono presenti molti controlli nel programma, è possibile filtrare i controlli di un tipo specifico e ordinarli in base allo stato. È possibile anche cercare il nome del controllo della verifica di accesso o il nome visualizzato del proprietario che lo ha creato. 

Se non si conosce quale programma contiene la verifica di accesso, accedere alla pagina delle verifiche di accesso e scegliere **Controlli**.  È possibile filtrare i controlli di un tipo specifico e ordinarli in base allo stato. È anche possibile cercare il nome del controllo della verifica di accesso o il nome visualizzato del proprietario che lo ha creato. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Recupero dei risultati di una verifica di accesso con ricorrenza singola

Se il tipo di ricorrenza della verifica è una sola volta, è possibile ottenere lo stato di avanzamento di una verifica di accesso attiva e i risultati di una verifica di accesso completata nella sezione **Risultati**.  È possibile digitare il nome visualizzato o il nome dell'entità utente di un utente di cui verificare l'accesso per visualizzare solo l'accesso di quell'utente specifico.  Per recuperare tutti i risultati di una verifica di accesso completata, fare clic sul pulsante **Download**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Recupero dei risultati per più istanze di una verifica di accesso ricorrente

Per visualizzare lo stato di avanzamento di una verifica di accesso attiva ricorrente, fare clic sulla sezione **Risultati**.  È possibile digitare il nome visualizzato o il nome dell'entità utente di un utente di cui verificare l'accesso.

Per visualizzare i risultati di un'istanza completata di una verifica di accesso ricorrente, scegliere **Cronologia delle revisioni**, quindi selezionare l'istanza specifica dall'elenco delle istanze delle verifiche di accesso completate, in base alla data di inizio e di fine dell'istanza.   I risultati di questa istanza possono essere visualizzati nella sezione **Risultati**.  È possibile digitare il nome visualizzato o il nome dell'entità utente di un utente di cui verificare l'accesso per visualizzare solo l'accesso di quell'utente specifico.  Per recuperare tutti i risultati di un'istanza completata di una verifica di accesso ricorrente, fare clic sul pulsante **Download**.


## <a name="removing-users-from-an-access-review"></a>Rimozione di utenti da una verifica di accesso

Per impostazione predefinita, un utente eliminato rimarrà comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario.  Dopo 30 giorni l'utente verrà eliminato definitivamente.  Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo.  Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle verifiche di accesso attive.  Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gestire i programmi e i controlli per le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](privileged-identity-management/pim-how-to-start-security-review.md)


