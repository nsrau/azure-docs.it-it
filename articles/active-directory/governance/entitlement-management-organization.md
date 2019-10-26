---
title: Aggiungere un'organizzazione connessa in Azure AD gestione diritti (anteprima)-Azure Active Directory
description: Informazioni su come consentire agli utenti esterni all'organizzazione di richiedere pacchetti di accesso per poter collaborare ai progetti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d45db1f62b370a2692ed932572e16c247b8903
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952485"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management-preview"></a>Aggiungere un'organizzazione connessa in Azure AD gestione dei diritti (anteprima)

Azure AD la gestione dei diritti consente di collaborare con utenti esterni all'organizzazione. Se si collabora spesso con gli utenti in una directory o un dominio di Azure AD esterno, è possibile aggiungerli come organizzazione connessa. Questo articolo descrive come aggiungere un'organizzazione connessa in modo che sia possibile consentire agli utenti esterni all'organizzazione di richiedere risorse nella directory.

## <a name="what-is-a-connected-organization"></a>Che cos'è un'organizzazione connessa?

Un'organizzazione connessa è una directory o un dominio di Azure AD esterno a cui è associata una relazione.

Si supponga, ad esempio, di lavorare presso la Woodgrove Bank e di voler collaborare con due organizzazioni esterne: Graphic Design Institute e contoso. L'utente ha ricevuto il contatto presso Graphical Design Institute che usa Azure AD e che gli utenti dell'Istituto di progettazione grafica hanno un nome dell'entità utente che termina con `graphicdesigninstitute.com`. E il contatto presso Contoso è stato detto che non usano ancora Azure AD, ma che gli utenti di Contoso hanno un nome dell'entità utente che termina con `contoso.com`.

È possibile configurare due organizzazioni connesse, una per Graphic Design Institute con la `graphicdesigninstitute.com`di dominio e una per Contoso con il dominio `contoso.com`. Se si aggiungono queste due organizzazioni connesse a un criterio, gli utenti di ogni organizzazione che dispongono di un nome dell'entità utente che corrisponde al criterio possono richiedere pacchetti di accesso. Inoltre, poiché Graphic Design Institute è stato identificato come utilizzando Azure AD, se successivamente Graphic Design Institute dispone di sottodomini, ad esempio `graphicdesigninstitute.example`, gli utenti con il nome dell'entità utente saranno anche in grado di richiedere pacchetti di accesso tramite il stesso criterio.

![Esempio di organizzazione connessa](./media/entitlement-management-organization/connected-organization-example.png)

Il modo in cui gli utenti della directory Azure AD o del dominio si autenticano dipende dal tipo di autenticazione. I tipi di autenticazione per le organizzazioni connesse sono i seguenti:

| Tipo di autenticazione | Status |
| --- | --- |
| Azure AD | Supportato |
| [Federazione diretta](../b2b/direct-federation.md) | Preview |
| [Un codice di](../b2b/one-time-passcode.md) accesso monouso (dominio) | Preview |

## <a name="add-a-connected-organization"></a>Aggiungere un'organizzazione connessa

Seguire questa procedura per aggiungere una directory o un dominio di Azure AD esterno come organizzazione connessa.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o invito Guest

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **organizzazioni connesse** e quindi su **Aggiungi organizzazione connessa**.

    ![Governance delle identità-organizzazioni connesse-Aggiungi organizzazione connessa](./media/entitlement-management-organization/connected-organization.png)

1. Nella scheda informazioni di **base** immettere un nome visualizzato e una descrizione per l'organizzazione.

    ![Aggiungi organizzazione connessa-scheda nozioni di base](./media/entitlement-management-organization/organization-basics.png)

1. Nella scheda **Directory + dominio** fare clic su **Aggiungi directory + dominio** per aprire il riquadro selezionare le directory e i domini.

1. Digitare un nome di dominio per cercare la directory Azure AD o il dominio. È necessario digitare l'intero nome di dominio.

1. Verificare che sia l'organizzazione corretta in base al nome e al tipo di autenticazione forniti. Il modo in cui gli utenti effettueranno l'accesso dipende dal tipo di autenticazione.

    ![Aggiungi organizzazione connessa-Seleziona Directory + domini](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Fare clic su **Aggiungi** per aggiungere la directory Azure ad o il dominio. Attualmente, è possibile aggiungere una sola Azure AD directory o dominio per ogni organizzazione connessa.

    > [!NOTE]
    > Tutti gli utenti della directory Azure AD o del dominio saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD da tutti i sottodomini associati alla directory, a meno che tali domini non siano bloccati dall'elenco Consenti o nega di Azure B2B. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../b2b/allow-deny-list.md).

1. Una volta aggiunto il Azure AD directory o dominio, fare clic su **Seleziona**.

    L'organizzazione viene visualizzata nell'elenco.

    ![Aggiungi organizzazione connessa-scheda Directory](./media/entitlement-management-organization/organization-directory-domain.png)

1. Nella scheda **sponsor** aggiungere gli sponsor facoltativi per l'organizzazione connessa.

    Gli sponsor sono utenti interni o esterni già presenti nella directory che rappresentano il punto di contatto per la relazione con l'organizzazione connessa. Gli sponsor interni sono utenti membri della directory. Gli sponsor esterni sono utenti Guest dell'organizzazione connessa che in precedenza erano invitati e sono già presenti nella directory. Gli sponsor possono essere utilizzati come responsabili approvazione quando gli utenti di questa organizzazione connessa richiedono l'accesso a questo pacchetto di accesso. Per informazioni su come invitare un utente guest nella directory, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../b2b/add-users-administrator.md).

    Quando si fa clic su **Aggiungi/Rimuovi**, viene visualizzato un riquadro per selezionare gli sponsor interni o esterni. Il riquadro Visualizza un elenco non filtrato di utenti e gruppi nella directory.

    ![Accedere a Package-Policy-Aggiungi organizzazione connessa-scheda sponsor](./media/entitlement-management-organization/organization-sponsors.png)

1. Nella scheda **revisione e creazione** esaminare le impostazioni dell'organizzazione e quindi fare clic su **Crea**.

    ![Accedere a Package-Policy-Aggiungi organizzazione connessa-Revisione + creazione scheda](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Eliminare un'organizzazione connessa

Se non si ha più una relazione con una directory o un dominio di Azure AD esterno, è possibile eliminare l'organizzazione connessa.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o invito Guest

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **organizzazioni connesse** e quindi fare clic per aprire l'organizzazione connessa.

1. Nella pagina Panoramica fare clic su **Elimina** per eliminare l'organizzazione connessa.

    Attualmente, è possibile eliminare un'organizzazione connessa solo se non sono presenti utenti connessi.

    ![Governance delle identità-organizzazioni connesse-eliminare un'organizzazione connessa](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passaggi successivi

- [Governare l'accesso per gli utenti esterni](entitlement-management-organization.md)
- [Per gli utenti che non si trovino nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
