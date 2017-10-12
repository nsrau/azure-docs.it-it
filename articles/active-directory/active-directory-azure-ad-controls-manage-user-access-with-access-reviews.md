---
title: Gestire l'accesso utente con le verifiche di accesso di Azure AD | Microsoft Docs
description: Informazioni su come gestire l'accesso utente come appartenenza di un gruppo o assegnazione a un'applicazione con verifiche di accesso di Azure Active Directory
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gestire l'accesso utente con le verifiche di accesso di Azure AD

Con Azure Active Directory è possibile verificare facilmente che gli utenti dispongano dell'accesso appropriato chiedendo agli utenti stessi, o a un responsabile delle decisioni, di partecipare a una verifica di accesso e ricertificare (o "attestare") l'accesso dell'utente.  I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso, è possibile apportare modifiche e rimuovere l'accesso agli utenti per i quali non è più necessario.

> [!NOTE]
> Se si vuole esaminare solo l'accesso degli utenti guest e non di tutti i tipi di utenti, vedere l'articolo sulla [gestione dell'accesso degli utenti guest con verifiche di accesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).  Se invece si vuole verificare l'appartenenza dell'utente a ruoli amministrativi come amministratore globale, vedere [Come avviare una verifica dell'accesso in Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Prerequisiti 

Le verifiche di accesso sono disponibili con Azure Active Directory edizione Premium P2, incluso in EMS E5. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).  Ogni utente che interagisce con questa funzionalità per creare una verifica, eseguire una verifica di accesso o applicare una verifica richiede una licenza.


## <a name="creating-and-performing-an-access-review"></a>Creazione ed esecuzione di una verifica di accesso

A una verifica di accesso possono partecipare uno o più revisori.  

1. Selezionare un gruppo in Azure Active Directory contenente uno o più membri o un'applicazione connessa ad Azure Active Directory a cui sono assegnati uno o più utenti. 
2. Decidere se ogni utente dovrà verificare il proprio accesso o se uno o più utenti dovranno verificare l'accesso di tutti gli altri utenti.
3. Abilitare la visualizzazione delle verifiche di accesso nei pannelli di accesso dei revisori.  Come amministratore globale, passare alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Avviare la verifica di accesso. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
5. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceverà un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso in cui potranno [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).
6. Se i revisori non hanno fornito l'input, è possibile chiedere ad Azure AD di inviare loro un promemoria.  Per impostazione predefinita, Azure AD invierà automaticamente un promemoria a metà periodo rispetto alla data di scadenza ai revisori che non hanno ancora risposto.
7. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)




