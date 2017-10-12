---
title: Completare una verifica di accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione con Azure AD| Microsoft Docs
description: Informazioni su come completare una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione in Azure Active Directory.
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Completare una verifica di accesso dei membri di un gruppo o degli utenti che accedono a un'applicazione in Azure AD

Gli amministratori possono richiedere una verifica dei membri di un gruppo o degli utenti assegnati a un'applicazione [creando una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md). Azure AD invia automaticamente ai revisori un messaggio di posta elettronica in cui viene chiesto di verificare l'accesso. Se un utente non ha ricevuto il messaggio di posta elettronica, è possibile inviargli le istruzioni su [come eseguire una verifica dell'accesso](active-directory-azure-ad-controls-perform-access-review.md).  Al termine della verifica di accesso o se l'amministratore ha interrotto la verifica, seguire la procedura descritta in questo articolo per esaminare e applicare i risultati.

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Visualizzazione di una verifica di accesso nel portale di Azure

1. Accedere alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), passare alla scheda **Programmi** e selezionare il programma che contiene il controllo della verifica di accesso.
2. Fare clic sull'opzione di gestione e selezionare il controllo della verifica di accesso.  Se nel programma sono presenti molti controlli, è possibile filtrare i controlli di un tipo specifico, eseguire l'ordinamento in base allo stato e la ricerca in base al nome della verifica di accesso o al nome visualizzato del proprietario che l'ha creata. 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>Interruzione di una verifica non ancora completata

Se la verifica non ha ancora raggiunto la data di fine pianificata, un amministratore può farla terminare in anticipo facendo clic sul pulsante **Interrompi**.  Eventuali utenti non sottoposti a verifica fino a questo momento, non potranno essere controllati dopo l'interruzione della verifica. Non è possibile riavviare una verifica dopo che è stata interrotta.

## <a name="applying-the-changes"></a>Applicazione delle modifiche 

Al termine di una verifica di accesso in corrispondenza della data di fine o in caso di interruzione manuale da parte di un amministratore, il pulsante **Applica** implementa il risultato della verifica aggiornando il gruppo o l'applicazione. Se l'accesso di un utente è stato negato durante la verifica, Azure AD rimuoverà l'assegnazione dell'applicazione o l'appartenenza quando l'amministratore fa clic su questo pulsante. 

Facendo clic sul pulsante Applica non si avrà alcun effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico.  Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="downloading-the-results-of-the-review"></a>Scaricare i risultati della verifica

Per recuperare i risultati della verifica, passare alla scheda Approvazioni e scegliere il pulsante **Scarica**.  Il file CSV risultante può essere visualizzato in Excel o in altri programmi idonei.

## <a name="optional-deleting-a-review"></a>Facoltativo: eliminazione di una verifica
Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica da Azure AD.

> [!IMPORTANT]
> Non verrà visualizzato un avviso prima dell'eliminazione ed è quindi necessario assicurarsi di voler eseguire questa operazione.
> 
> 

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gestire i programmi e i controlli per le verifiche di accesso di Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
