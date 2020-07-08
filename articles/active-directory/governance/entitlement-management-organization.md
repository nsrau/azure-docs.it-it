---
title: Aggiungere un'organizzazione connessa in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come consentire agli utenti esterni all'organizzazione di richiedere pacchetti di accesso per poter collaborare ai progetti.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8928e7293f184e8eb366df6a29e50cbea6a7c93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078194"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Aggiungere un'organizzazione connessa in Azure AD gestione dei diritti

Con la gestione dei diritti di Azure Active Directory (Azure AD), è possibile collaborare con utenti esterni all'organizzazione. Se si collabora spesso con gli utenti in una directory o un dominio di Azure AD esterno, è possibile aggiungerli come organizzazione connessa. Questo articolo descrive come aggiungere un'organizzazione connessa in modo che sia possibile consentire agli utenti esterni all'organizzazione di richiedere risorse nella directory.

## <a name="what-is-a-connected-organization"></a>Che cos'è un'organizzazione connessa?

Un'organizzazione connessa è una directory o un dominio di Azure AD esterno a cui è associata una relazione.

Si supponga, ad esempio, di lavorare presso la Woodgrove Bank e di voler collaborare con due organizzazioni esterne. Queste due organizzazioni hanno configurazioni diverse:

- Graphic Design Institute USA Azure AD e i relativi utenti hanno un nome dell'entità utente che termina con *graphicdesigninstitute.com*.
- Contoso non usa ancora Azure AD. Gli utenti di Contoso hanno un nome dell'entità utente che termina con *contoso.com*.

In questo caso, è possibile configurare due organizzazioni connesse. Si crea un'organizzazione connessa per Graphic Design Institute e una per contoso. Se successivamente si aggiungono le due organizzazioni connesse a un criterio, gli utenti di ogni organizzazione con un nome dell'entità utente corrispondente ai criteri possono richiedere pacchetti di accesso. Gli utenti con un nome dell'entità utente che ha un dominio di *graphicdesigninstitute.com* corrispondono all'organizzazione connessa a Graphic Design Institute e possono inviare richieste. Gli utenti con un nome dell'entità utente che ha un dominio di *contoso.com* corrispondono all'organizzazione connessa a Contoso e possono anche richiedere pacchetti. Inoltre, poiché Graphic Design Institute USA Azure AD, tutti gli utenti con un nome di entità corrispondente a un [dominio verificato](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) aggiunto al tenant, ad *esempio graphicdesigninstitute. example*, potranno anche richiedere pacchetti di accesso usando lo stesso criterio.

![Esempio di organizzazione connessa](./media/entitlement-management-organization/connected-organization-example.png)

Il modo in cui gli utenti della directory Azure AD o del dominio si autenticano dipende dal tipo di autenticazione. I tipi di autenticazione per le organizzazioni connesse sono:

- Azure AD
- [Federazione diretta](../b2b/direct-federation.md)
- [Un codice di](../b2b/one-time-passcode.md) accesso monouso (dominio)

Per una dimostrazione di come aggiungere un'organizzazione connessa, guardare il video seguente:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Aggiungere un'organizzazione connessa

Per aggiungere una directory o un dominio di Azure AD esterno come organizzazione connessa, seguire le istruzioni riportate in questa sezione.

**Ruolo prerequisito**: *amministratore globale*, *amministratore utente*o *invitatore Guest*

1. Nella portale di Azure selezionare **Azure Active Directory**e quindi selezionare **governance identità**.

1. Nel riquadro sinistro selezionare **organizzazioni connesse**e quindi selezionare **Aggiungi organizzazione connessa**.

    ![Pulsante "Aggiungi organizzazione connessa"](./media/entitlement-management-organization/connected-organization.png)

1. Selezionare la scheda **nozioni di base** , quindi immettere un nome visualizzato e una descrizione per l'organizzazione.

    ![Riquadro di base "Aggiungi organizzazione connessa"](./media/entitlement-management-organization/organization-basics.png)

1. Selezionare la scheda **Directory + dominio** , quindi selezionare **Aggiungi directory + dominio**.

    Viene visualizzato il riquadro **Seleziona Directory + domini** .

1. Nella casella di ricerca immettere un nome di dominio per cercare la directory Azure AD o il dominio. Assicurarsi di immettere l'intero nome di dominio.

1. Verificare che il nome dell'organizzazione e il tipo di autenticazione siano corretti. Il modo in cui gli utenti accede dipendono dal tipo di autenticazione.

    ![Riquadro "Seleziona Directory + domini"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selezionare **Aggiungi** per aggiungere la directory Azure ad o il dominio. Attualmente, è possibile aggiungere una sola directory Azure AD o dominio per ogni organizzazione connessa.

    > [!NOTE]
    > Tutti gli utenti della directory Azure AD o del dominio saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD da tutti i sottodomini associati alla directory, a meno che tali domini non siano bloccati dal Azure AD elenco Consenti o nega business to business (B2B). Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../b2b/allow-deny-list.md).

1. Dopo aver aggiunto la directory Azure AD o il dominio, selezionare **Seleziona**.

    L'organizzazione viene visualizzata nell'elenco.

    ![Riquadro "directory + dominio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selezionare la scheda **sponsor** , quindi aggiungere sponsor facoltativi per l'organizzazione connessa.

    Gli sponsor sono utenti interni o esterni già presenti nella directory che rappresentano il punto di contatto per la relazione con l'organizzazione connessa. Gli sponsor interni sono utenti membri della directory. Gli sponsor esterni sono utenti Guest dell'organizzazione connessa che in precedenza erano invitati e sono già presenti nella directory. Gli sponsor possono essere utilizzati come responsabili approvazione quando gli utenti di questa organizzazione connessa richiedono l'accesso a questo pacchetto di accesso. Per informazioni su come invitare un utente guest nella directory, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../b2b/add-users-administrator.md).

    Quando si seleziona **Aggiungi/Rimuovi**, viene aperto un riquadro in cui è possibile scegliere sponsor interni o esterni. Il riquadro Visualizza un elenco non filtrato di utenti e gruppi nella directory.

    ![Riquadro sponsor](./media/entitlement-management-organization/organization-sponsors.png)

1. Selezionare la scheda **Verifica + crea** , verificare le impostazioni dell'organizzazione e quindi selezionare **Crea**.

    ![Riquadro "verifica e creazione"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aggiornare un'organizzazione connessa 

Se l'organizzazione connessa passa a un dominio diverso, il nome dell'organizzazione cambia o si vuole modificare gli sponsor, è possibile aggiornare l'organizzazione connessa seguendo le istruzioni riportate in questa sezione.

**Ruolo prerequisito**: *amministratore globale*, *amministratore utente*o *invitatore Guest*

1. Nella portale di Azure selezionare **Azure Active Directory**e quindi selezionare **governance identità**.

1. Nel riquadro sinistro selezionare **organizzazioni connesse**e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro Panoramica dell'organizzazione connessa selezionare **modifica** per modificare il nome dell'organizzazione o la descrizione.  

1. Nel riquadro **Directory + dominio** selezionare **Aggiorna directory + dominio** per passare a una directory o a un dominio diverso.

1. Nel riquadro **sponsor** selezionare **Aggiungi sponsor interni** o Aggiungi sponsor **esterni** per aggiungere un utente come sponsor. Per rimuovere uno sponsor, selezionare lo sponsor e, nel riquadro destro, selezionare **Elimina**.


## <a name="delete-a-connected-organization"></a>Eliminare un'organizzazione connessa

Se non si ha più una relazione con una directory o un dominio di Azure AD esterno, è possibile eliminare l'organizzazione connessa.

**Ruolo prerequisito**: *amministratore globale*, *amministratore utente*o *invitatore Guest*

1. Nella portale di Azure selezionare **Azure Active Directory**e quindi selezionare **governance identità**.

1. Nel riquadro sinistro selezionare **organizzazioni connesse**e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro Panoramica dell'organizzazione connessa selezionare **Elimina** per eliminarlo.

    Attualmente, è possibile eliminare un'organizzazione connessa solo se non sono presenti utenti connessi.

    ![Pulsante Elimina organizzazione connessa](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso per gli utenti esterni](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Governare l'accesso per gli utenti che non si trovino nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
