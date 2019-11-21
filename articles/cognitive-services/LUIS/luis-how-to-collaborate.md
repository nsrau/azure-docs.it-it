---
title: Collaborate with others - LUIS
titleSuffix: Azure Cognitive Services
description: An app owner can add contributors to the authoring resource. These contributors can modify the model, train, and publish the app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: efeb8b28ef2b8eec480fcf5090e0173d6451deef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225521"
---
# <a name="add-contributors-to-your-app"></a>Add contributors to your app

An app owner can add contributors to apps. I collaboratori possono modificare il modello, eseguire il training e pubblicare l'app. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Add contributor to Azure authoring resource

The following procedure is for all users that have **migrated** to use the Azure authoring resource.

You have migrated if your LUIS authoring experience is tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

1. In the Azure portal, find the Language Understanding (LUIS) authoring resource. It has the type `LUIS.Authoring`.
1. On this resource's **Access Control (IAM)** page, select **+Add** then select **Add role assignment**.

    ![In Azure portal, add role assignment on authoring resource.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. In the **Add role assignment** window, select the **Role** of Contributor. In the **Assign access to** option, select **Azure AD user, group, or service principal**. In the **Select** option, enter the user's email address. If the user is known by more than 1 email address for the same domain, make sure the enter the _primary_ email account.

    ![Add user's email to the contributor role for Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    When the user's email is found, select the account and select **Save**. 

    If you have trouble with this role assignment, review [Azure role assignments](../../role-based-access-control/role-assignments-portal.md) and [Azure access control troubleshooting](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Add collaborator to LUIS app

The following procedure is for all users that have **not migrated** to use the Azure authoring resource.

You have not migrated if your LUIS authoring experience is not tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

Un'app ha un unico autore, il proprietario, ma può avere molti collaboratori. Per consentire ai collaboratori di modificare l'app LUIS, alll'elenco di collaboratori è necessario aggiungere l'indirizzo di posta elettronica che useranno per accedere al portale di LUIS. Una volta aggiunta, l'app appare nel loro portale di LUIS.

1. Selezionare **Gestisci** dal menu in alto a destra, quindi selezionare **Collaboratori** nel menu a sinistra.

1. Selezionare **Aggiungi collaboratore** nella barra degli strumenti.

1. Immettere l'indirizzo di posta elettronica che il collaboratore userà per accedere al portale di LUIS.

    ![Aggiungere l'indirizzo di posta elettronica del collaboratore](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Users with multiple emails 

If you add contributors/collaborators to a LUIS app, you are specifying the exact email address. While Azure Active Directory (Azure AD) allows a single user to have more than one email account used interchangeably, LUIS requires the user to sign in with the email address specified when adding the contributor/collaborator.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Risorse Azure Active Directory

Se all'interno della propria organizzazione si usa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD), Language Understanding (LUIS) richiede l'autorizzazione per le informazioni relative all'accesso degli utenti quando desiderano usare LUIS. Le risorse richieste da LUIS sono minime. 

La descrizione dettagliata viene visualizzata quando si tenta di effettuare l'iscrizione con un account che ha ottenuto il consenso da parte dell'amministratore o non richiede il consenso dello stesso, ad esempio il consenso amministratore:

* Consente di accedere all'app con l'account dell'organizzazione e consente all'app di leggere il profilo personale. Consente inoltre all'app di leggere le informazioni aziendali di base. Questo consente a LUIS di leggere i dati di profilo di base, come ad esempio l'ID utente, l'indirizzo di posta elettronica e il nome.
* Consente all'app di visualizzare e aggiornare i dati, anche quando non la si sta usando. L'autorizzazione è obbligatoria per aggiornare il token di accesso dell'utente.


### <a name="azure-active-directory-tenant-user"></a>Utente del tenant di Azure Active Directory

LUIS usa il flusso di consenso standard di Azure Active Directory (Azure AD). 

L'amministratore del tenant collabora direttamente con l'utente che necessita dell'accesso per poter usare LUIS in Azure AD. 

* Innanzitutto, l'utente accede a LUIS e visualizza la finestra popup che richiede l'approvazione dell'amministratore. L'utente contatta l'amministratore del tenant prima di continuare. 
* L'amministratore del tenant accede quindi a LUIS e visualizza una finestra popup del flusso di consenso. La finestra consente all'amministratore di concedere l'autorizzazione all'utente. Dopo che l'amministratore ha accettato l'autorizzazione, l'utente sarà in grado di continuare a usare LUIS. Se non accede a LUIS, l'amministratore del tenant può accedere al [consenso](https://account.activedirectory.windowsazure.com/r#/applications) per LUIS, come illustrato nello screenshot seguente. Si noti che l'elenco è filtrato in modo da visualizzare gli elementi che includono il nome `LUIS`.

![Autorizzazione di Azure Active Directory dal sito Web dell'app](./media/luis-how-to-collaborate/tenant-permissions.png)

Se l'amministratore del tenant vuole che solo determinati utenti usino LUIS, sono due le soluzioni possibili:
* Dare il "consenso amministratore" (consenso a tutti gli utenti di Azure AD), ma successivamente impostare su "Sì" l'opzione "Assegnazione utenti obbligatoria" nelle proprietà dell'applicazione aziendale e infine assegnare/aggiungere all'applicazione solo gli utenti desiderati. In questo modo l'amministratore dà comunque il "consenso amministratore" per l'applicazione, ma è possibile controllare gli utenti che possono accedervi.
* Una seconda soluzione consiste nell'usare l'[API Graph di Azure AD](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) per dare il consenso per ogni utente specifico. 

Altre informazioni sugli utenti di Azure Active Directory e sul consenso: 
* [Limitare l'app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a un set di utenti

## <a name="next-steps"></a>Passaggi successivi

* Learn [how to use versions](luis-how-to-manage-versions.md) to control your app life cycle.
* Understand the concepts including the [authoring resource](luis-concept-keys.md#authoring-key) and [contributors](luis-concept-keys.md#contributions-from-other-authors) on that resource.
* Learn [how to create](luis-how-to-azure-subscription.md) authoring and runtime resources
* Migrate to the new [authoring resource](luis-migration-authoring.md) 
