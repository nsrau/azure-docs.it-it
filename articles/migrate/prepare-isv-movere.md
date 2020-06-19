---
title: Preparare Azure Migrate per il funzionamento con uno strumento ISV e Movere
description: Questo articolo descrive come preparare Azure Migrate per il funzionamento con uno strumento ISV o con Movere e come iniziare a usare lo strumento.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682654"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Predisporre l'uso di uno strumento ISV o di Movere

Se è stato aggiunto uno [strumento ISV](migrate-services-overview.md#isv-integration) o Movere a un progetto Azure Migrate, è necessario eseguire alcuni passaggi prima di collegare lo strumento e inviare i dati ad Azure Migrate. 

## <a name="check-azure-ad-permissions"></a>Controllare le autorizzazioni di Azure AD

Per l'account utente di Azure sono necessarie le autorizzazioni seguenti:

- Autorizzazione per la registrazione di un'app Azure Active Directory (Azure AD) nel tenant di Azure
- Autorizzazione per allocare un ruolo all'app Azure AD a livello di sottoscrizione


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Impostare le autorizzazioni per registrare un'app Azure AD

1. In Azure AD controllare il ruolo per l'account.
2. Se si ha il ruolo utente, selezionare **Impostazioni utente** a sinistra e verificare se gli utenti possono registrare le applicazioni. Se è impostato su **Sì**, qualsiasi utente nel tenant di Azure AD può registrare un'app. Se è impostato su **No**, solo gli utenti amministratori possono registrare le app.   
3. Se non si hanno autorizzazioni, un utente amministratore può concedere all'account utente il ruolo di [Amministratore applicazione](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator), in modo che possa registrare l'app.
4. Quando lo strumento viene collegato a Azure Migrate, l'amministratore può rimuovere il ruolo dall'account.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Impostare le autorizzazioni per assegnare un ruolo a un'app Azure AD
 
Nella sottoscrizione di Azure l'account deve avere l'accesso a **Microsoft.Authorization/*/Write** per assegnare un ruolo a un'app Azure AD. 

1. Nel portale di Azure aprire **Sottoscrizioni**.
2. Selezionare la sottoscrizione pertinente. Se non è visualizzata, selezionare il **filtro per le sottoscrizioni globali**. 
3. Selezionare **Autorizzazioni personali**. Selezionare quindi **Fare clic qui per visualizzare i dettagli di accesso completi per questa compilazione**.
4. Verificare le autorizzazioni in **Assegnazioni di ruolo** > **Visualizzazione**. Se l'account non ha le autorizzazioni necessarie, chiedere all'amministratore della sottoscrizione di aggiungere l'utente al ruolo [Amministratore Accesso utenti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) o al ruolo [Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
 

## <a name="start-using-the-tool"></a>Iniziare a usare lo strumento

1. Se non si ha ancora una licenza o una versione di valutazione gratuita per lo strumento, nella voce dello strumento in Azure Migrate, in **Registra**, selezionare **Altre informazioni**.
2. Nello strumento seguire le istruzioni per collegare lo strumento al progetto Azure Migrate e per inviare i dati ad Azure Migrate.

## <a name="next-steps"></a>Passaggi successivi

Seguire le istruzioni dell'ISV o di Movere per inviare i dati ad Azure Migrate.

   
