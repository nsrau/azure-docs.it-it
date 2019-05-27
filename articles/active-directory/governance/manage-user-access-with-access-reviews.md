---
title: Gestire l'accesso utente con le verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Informazioni su come gestire l'accesso degli utenti come appartenenza a un gruppo o assegnazione a un'applicazione con le verifiche di accesso di Azure Active Directory
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
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5efbc3b78800caa9d603e870b81705424c4d3fd6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113222"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gestire l'accesso utente con le verifiche di accesso di Azure AD

Con Azure Active Directory (Azure AD), è possibile verificare facilmente che gli utenti abbiano l'accesso appropriato. Si può chiedere agli utenti stessi o a un decision maker di partecipare a una verifica di accesso e certificare di nuovo (o "attestare") l'accesso degli utenti. I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso, è possibile apportare modifiche e rimuovere l'accesso agli utenti per i quali non è più necessario.

> [!NOTE]
> Se si vuole verificare solo l'accesso degli utenti guest e non di tutti i tipi di utenti, vedere l'articolo su come [gestire l'accesso degli utenti guest con le verifiche di accesso](manage-guest-access-with-access-reviews.md). Se si vuole verificare l'appartenenza degli utenti a ruoli amministrativi come amministratore globale, vedere l'articolo su come [avviare una verifica di accesso in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Prerequisiti

- Azure AD P2 Premium

Per altre informazioni, vedere [quali gli utenti devono avere le licenze?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review"></a>Creare ed eseguire una verifica di accesso

A una verifica di accesso possono partecipare uno o più revisori.  

1. Selezionare un gruppo con uno o più membri in Azure AD. In alternativa, selezionare un'applicazione connessa ad Azure AD a cui sono assegnati uno o più utenti. 

2. Decidere se ogni utente dovrà verificare il proprio accesso o se uno o più utenti dovranno verificare l'accesso di tutti.

3. Come amministratore globale o amministratore dell'utente, vedere la [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Creare la verifica di accesso. Per altre informazioni, vedere [creare una verifica di accesso di gruppi o applicazioni](create-access-review.md).

5. Quando la verifica di accesso viene avviata, chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ogni ricevono un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso, in cui essi [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

6. Se i revisori non hanno espresso il proprio giudizio, è possibile chiedere ad Azure AD di inviare loro un promemoria. Per impostazione predefinita, Azure AD invia automaticamente un promemoria ai revisori che non hanno ancora risposto a metà periodo rispetto alla data di scadenza.

7. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [completare una verifica di accesso dei gruppi o applicazioni](complete-access-review.md).


## <a name="next-steps"></a>Passaggi successivi

[Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)




