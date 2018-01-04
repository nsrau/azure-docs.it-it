---
title: Completare una verifica di accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione con Azure AD| Microsoft Docs
description: Informazioni su come completare una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione in Azure Active Directory.
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: de853d633aa65c9f08f5e28088d5240c2e4d7fa6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Completare una verifica di accesso dei membri di un gruppo o degli utenti che accedono a un'applicazione in Azure AD

Gli amministratori possono usare Azure Active Directory (Azure AD) per [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md) per gli utenti o i membri del gruppo assegnati a un'applicazione. Azure AD invia automaticamente ai revisori un messaggio di posta elettronica in cui viene chiesto di verificare l'accesso. Se un utente non ha ricevuto il messaggio di posta elettronica, è possibile inviargli le istruzioni su [come eseguire una verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md). Al termine del periodo della verifica di accesso o se l'amministratore interrompe la verifica, seguire la procedura descritta in questo articolo per esaminare e applicare i risultati.

## <a name="view-an-access-review-in-the-azure-portal"></a>Visualizzare una verifica di accesso nel portale di Azure

1. Accedere alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), selezionare **Programmi** e selezionare il programma che contiene il controllo della verifica di accesso.

2. Selezionare **Gestisci** e selezionare il controllo della verifica di accesso. Se sono presenti molti controlli nel programma, è possibile filtrare i controlli di un tipo specifico e ordinarli in base allo stato. È possibile anche cercare il nome del controllo della verifica di accesso o il nome visualizzato del proprietario che lo ha creato. 

## <a name="stop-a-review-that-hasnt-finished"></a>Interrompere una verifica non completata

Se la verifica non ha raggiunto la data di fine pianificata, un amministratore può selezionare **Arresta** per terminare in anticipo la verifica. Dopo avere interrotto la verifica, gli utenti non possono più essere verificati. Non è possibile riavviare una verifica dopo che è stata interrotta.

## <a name="apply-the-changes"></a>Applicare le modifiche. 

Al termine di una verifica di accesso, sia che abbia raggiunto la data di fine o che sia stata interrotta manualmente da un amministratore, selezionare **Applica**. Il risultato della verifica viene implementato aggiornando il gruppo o l'applicazione. Se l'accesso di un utente è stato negato durante la verifica, Azure AD rimuoverà l'assegnazione dell'applicazione o l'appartenenza quando l'amministratore seleziona questa opzione. 

Se si seleziona **Applica**, non si avrà alcun effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico. Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="download-the-results-of-the-review"></a>Scaricare i risultati della verifica

Per recuperare i risultati della verifica, selezionare **Approvazioni** e quindi selezionare **Scarica**. Il file CSV risultante può essere visualizzato in Excel o in altri programmi idonei.

## <a name="optional-delete-a-review"></a>Facoltativo: eliminare una verifica
È possibile eliminare una verifica che non è più utile. Selezionare **Eliminare** per rimuovere la verifica da Azure AD.

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
