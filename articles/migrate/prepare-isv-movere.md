---
title: Preparare Azure Migrate per lavorare con gli strumenti ISV/Mover
description: Preparare Azure Migrate per lavorare con gli strumenti ISV/Mover
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906982"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Prepararsi a usare gli strumenti ISV/Mover

Se è stato aggiunto uno [strumento ISV](migrate-services-overview.md#isv-integration), o Mover, a un progetto di Azure migrate, è necessario preparare alcuni passaggi prima di collegare lo strumento e inviare i dati al Azure migrate. 

## <a name="check-azure-ad-permissions"></a>Controllare le autorizzazioni di Azure AD

L'account utente di Azure deve disporre di un paio di autorizzazioni:

- Autorizzazione per la registrazione di un'app Azure AD con il tenant di Azure.
- Autorizzazione per assegnare un ruolo all'app Azure AD a livello di sottoscrizione.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Impostare le autorizzazioni per la registrazione di un'app Azure AD

1. In Azure Active Directory controllare il ruolo per l'account. Se si dispone del ruolo utente, fare clic su **impostazioni utente** a sinistra e verificare se gli utenti possono registrare le applicazioni.
2. Se è impostato su **Sì**, tutti gli utenti nel tenant Azure ad possono registrare un'app. In caso affermativo, solo gli utenti amministratori possono registrare le app.
3. Se non si dispone delle autorizzazioni, un utente amministratore può fornire l'account utente con il ruolo di [amministratore dell'applicazione](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) , in modo che sia possibile registrare l'app.
4. Quando lo strumento viene collegato a Azure Migrate, l'amministratore può rimuovere il ruolo dall'account.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Impostare le autorizzazioni per assegnare un ruolo a un'app Azure AD
 
Nella sottoscrizione di Azure, l'account richiede **l'accesso a Microsoft. Authorization/*/Write**per assegnare un ruolo a un'app Azure ad. 

1. Per verificare le autorizzazioni della sottoscrizione, nella portale di Azure aprire **sottoscrizioni**.
2. Selezionare la sottoscrizione pertinente. Se non viene visualizzato, selezionare il **filtro sottoscrizioni globali**. 
3. Selezionare **Autorizzazioni personali**. Selezionare quindi **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.
4. Nella**visualizzazione** **assegnazioni** > di ruolo e controllare le autorizzazioni.
5. Se l'account non dispone di autorizzazioni, chiedere all'amministratore della sottoscrizione di aggiungere il ruolo [amministratore accesso utenti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) o il ruolo [proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Iniziare a usare lo strumento

1. Se non si dispone ancora di una licenza o di una versione di valutazione gratuita per lo strumento, nella voce dello strumento in Azure Migrate, in **registra**, fare clic su **altre informazioni**.
2. Nello strumento, seguire le istruzioni per collegare lo strumento al progetto Azure Migrate e inviare i dati Azure Migrate.

## <a name="next-steps"></a>Passaggi successivi

Seguire le istruzioni ISV o Mover per inviare i dati a Azure Migrate.

   
