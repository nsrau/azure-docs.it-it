---
title: Gestire l'accesso utente con le verifiche di accesso di Azure AD | Microsoft Docs
description: Informazioni su come gestire l'accesso degli utenti come appartenenza a un gruppo o assegnazione a un'applicazione con le verifiche di accesso di Azure Active Directory
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 6a4d25b2eb228cafab48419a0d0eda92bba9f1ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gestire l'accesso utente con le verifiche di accesso di Azure AD

Con Azure Active Directory (Azure AD), è possibile verificare facilmente che gli utenti abbiano l'accesso appropriato. Si può chiedere agli utenti stessi o a un decision maker di partecipare a una verifica di accesso e certificare di nuovo (o "attestare") l'accesso degli utenti. I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso, è possibile apportare modifiche e rimuovere l'accesso agli utenti per i quali non è più necessario.

> [!NOTE]
> Se si vuole verificare solo l'accesso degli utenti guest e non di tutti i tipi di utenti, vedere l'articolo su come [gestire l'accesso degli utenti guest con le verifiche di accesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). Se si vuole verificare l'appartenenza degli utenti a ruoli amministrativi come amministratore globale, vedere l'articolo su come [avviare una verifica di accesso in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>prerequisiti 

Le verifiche di accesso sono disponibili con Azure AD edizione Premium P2, incluso in Microsoft Enterprise Mobility + Security E5. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md). Ogni utente che interagisce con questa funzionalità per accedere a una verifica oppure crearne o applicarne una necessita di una licenza.


## <a name="create-and-perform-an-access-review"></a>Creare ed eseguire una verifica di accesso

A una verifica di accesso possono partecipare uno o più revisori.  

1. Selezionare un gruppo con uno o più membri in Azure AD. In alternativa, selezionare un'applicazione connessa ad Azure AD a cui sono assegnati uno o più utenti. 

2. Decidere se ogni utente dovrà verificare il proprio accesso o se uno o più utenti dovranno verificare l'accesso di tutti.

3. Abilitare la visualizzazione delle verifiche di accesso nei pannelli di accesso dei revisori. Come amministratore globale, passare alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Avviare la verifica di accesso. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

5. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceve un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso per [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).

6. Se i revisori non hanno espresso il proprio giudizio, è possibile chiedere ad Azure AD di inviare loro un promemoria. Per impostazione predefinita, Azure AD invia automaticamente un promemoria ai revisori che non hanno ancora risposto a metà periodo rispetto alla data di scadenza.

7. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Passaggi successivi

[Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)




