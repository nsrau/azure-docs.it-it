---
title: Aggiungere un'organizzazione connessa nella gestione dei diritti di Azure AD - Azure Active DirectoryAdd a connected organization in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come consentire alle persone esterne all'organizzazione di richiedere pacchetti di accesso in modo da poter collaborare ai progetti.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128601"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Aggiungere un'organizzazione connessa nella gestione dei diritti di Azure ADAdd a connected organization in Azure AD entitlement management

Con la gestione dei diritti di Azure Active Directory (Azure AD) è possibile collaborare con persone esterne all'organizzazione. Se si collabora spesso con utenti in una directory o un dominio di Azure AD esterno, è possibile aggiungerli come organizzazione connessa. In questo articolo viene descritto come aggiungere un'organizzazione connessa in modo da consentire agli utenti esterni all'organizzazione di richiedere risorse nella directory.

## <a name="what-is-a-connected-organization"></a>Che cos'è un'organizzazione connessa?

Un'organizzazione connessa è una directory o un dominio di Azure AD esterno con cui si ha una relazione.

Si supponga, ad esempio, di lavorare presso Woodgrove Bank e di voler collaborare con due organizzazioni esterne. Queste due organizzazioni hanno configurazioni diverse:

- Graphic Design Institute usa Azure AD e i relativi utenti hanno un nome dell'entità utente che termina con *graphicdesigninstitute.com*.
- Contoso non usa ancora Azure AD. Gli utenti Contoso dispongono di un nome dell'entità utente che termina con *contoso.com*.

In questo caso, è possibile configurare due organizzazioni connesse. Creare un'organizzazione connessa per Graphic Design Institute e uno per Contoso. Se quindi si aggiungono le due organizzazioni connesse a un criterio, gli utenti di ogni organizzazione con un nome dell'entità utente corrispondente al criterio possono richiedere pacchetti di accesso. Gli utenti con un nome principale utente con un dominio di *graphicdesigninstitute.com* corrisponderebbero all'organizzazione connessa a Graphic Design Institute e potranno inviare richieste. Gli utenti con un nome dell'entità utente con un dominio di *contoso.com* corrisponderebbero all'organizzazione connessa a Contoso e sarebbero inoltre autorizzati a richiedere pacchetti. Inoltre, poiché Graphic Design Institute usa Azure AD, tutti gli utenti con un nome principale corrispondente a un [dominio verificato](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) aggiunto al tenant, ad esempio *graphicdesigninstitute.example,* potrebbero anche richiedere pacchetti di accesso usando gli stessi criteri.

![Esempio di organizzazione connessa](./media/entitlement-management-organization/connected-organization-example.png)

La modalità di autenticazione degli utenti dalla directory o dal dominio di Azure AD dipende dal tipo di autenticazione. I tipi di autenticazione per le organizzazioni connesse sono:

- Azure AD
- [Federazione diretta](../b2b/direct-federation.md)
- [Passcode monouso](../b2b/one-time-passcode.md) (dominio)

Per una dimostrazione di come aggiungere un'organizzazione connessa, guardare il video seguente:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Aggiungere un'organizzazione connessa

Per aggiungere una directory o un dominio di Azure AD esterno come organizzazione connessa, seguire le istruzioni in questa sezione.

**Ruolo Prerequisito**: *Amministratore globale*, *Amministratore utente*o *Invitante ospite*

1. Nel portale di Azure selezionare **Azure Active Directory**e quindi Governance **dell'identità.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse**e quindi **Aggiungi organizzazione connessa**.

    ![Il pulsante "Aggiungi organizzazione connessa"](./media/entitlement-management-organization/connected-organization.png)

1. Selezionare la scheda **Nozioni di base** e quindi immettere un nome visualizzato e una descrizione per l'organizzazione.

    ![Riquadro Nozioni di base sull'organizzazione connessa](./media/entitlement-management-organization/organization-basics.png)

1. Selezionare la scheda **Directory e dominio,** quindi selezionare **Aggiungi directory e dominio**.

    Viene visualizzato il riquadro **Seleziona directory - domini.**

1. Nella casella di ricerca immettere un nome di dominio per cercare la directory o il dominio di Azure AD. Assicurati di inserire l'intero nome di dominio.

1. Verificare che il nome dell'organizzazione e il tipo di autenticazione siano corretti. La modalità di accesso degli utenti dipende dal tipo di autenticazione.

    ![Il riquadro "Seleziona directory e domini"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selezionare **Aggiungi** per aggiungere la directory o il dominio di Azure AD. Attualmente, è possibile aggiungere una sola directory o dominio di Azure AD per ogni organizzazione connessa.

    > [!NOTE]
    > Tutti gli utenti della directory o del dominio di Azure AD saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD da tutti i sottodomini associati alla directory, a meno che tali domini non siano bloccati dall'elenco di autorizzazione o rifiuto di Azure AD business to business (B2B). Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../b2b/allow-deny-list.md).

1. Dopo aver aggiunto la directory o il dominio di Azure AD, selezionare **Seleziona**.

    L'organizzazione viene visualizzata nell'elenco.

    ![Il riquadro "Directory e dominio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selezionare la scheda **Sponsor** e quindi aggiungere sponsor facoltativi per l'organizzazione connessa.

    Gli sponsor sono utenti interni o esterni già presenti nella directory che sono il punto di contatto per la relazione con questa organizzazione connessa. Gli sponsor interni sono utenti membri nella directory. Gli sponsor esterni sono utenti guest dell'organizzazione connessa che sono stati invitati in precedenza e sono già nella directory. Gli sponsor possono essere utilizzati come approvatori quando gli utenti di questa organizzazione connessa richiedono l'accesso a questo pacchetto di accesso. Per informazioni su come invitare un utente guest alla directory, vedere Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure.For information about how to invite a guest user to your directory, see [Add Azure Active Directory B2B collaboration users in the Azure portal.](../b2b/add-users-administrator.md)

    Quando si seleziona **Aggiungi/Rimuovi**, viene visualizzato un riquadro in cui è possibile scegliere sponsor interni o esterni. Nel riquadro viene visualizzato un elenco non filtrato di utenti e gruppi nella directory.

    ![Il riquadro Sponsor](./media/entitlement-management-organization/organization-sponsors.png)

1. Selezionare la scheda **Revisione e creazione,** rivedere le impostazioni dell'organizzazione e quindi selezionare **Crea**.

    ![Il riquadro "Revisione e creazione"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aggiornare un'organizzazione connessa 

Se l'organizzazione connessa passa a un dominio diverso, il nome dell'organizzazione cambia o si desidera modificare gli sponsor, è possibile aggiornare l'organizzazione connessa seguendo le istruzioni riportate in questa sezione.

**Ruolo Prerequisito**: *Amministratore globale*, *Amministratore utente*o *Invitante ospite*

1. Nel portale di Azure selezionare **Azure Active Directory**e quindi Governance **dell'identità.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse**e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro Panoramica dell'organizzazione connessa selezionare **Modifica** per modificare il nome o la descrizione dell'organizzazione.  

1. Nel riquadro **Directory e dominio** selezionare Aggiorna directory e **dominio** per passare a una directory o a un dominio diverso.

1. Nel riquadro **Sponsor** selezionare **Aggiungi sponsor interni** o Aggiungi sponsor **esterni** per aggiungere un utente come sponsor. Per rimuovere uno sponsor, selezionarlo e, nel riquadro destro, selezionare **Elimina**.


## <a name="delete-a-connected-organization"></a>Eliminare un'organizzazione connessa

Se non si ha più una relazione con una directory o un dominio di Azure AD esterno, è possibile eliminare l'organizzazione connessa.

**Ruolo Prerequisito**: *Amministratore globale*, *Amministratore utente*o *Invitante ospite*

1. Nel portale di Azure selezionare **Azure Active Directory**e quindi Governance **dell'identità.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse**e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro Panoramica dell'organizzazione connessa selezionare **Elimina** per eliminarla.

    Attualmente, è possibile eliminare un'organizzazione connessa solo se non sono presenti utenti connessi.

    ![Pulsante Elimina organizzazione connessa](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso per gli utenti esterni](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Gestire l'accesso per gli utenti non presente nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
