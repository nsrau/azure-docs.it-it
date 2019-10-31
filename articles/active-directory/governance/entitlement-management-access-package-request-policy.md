---
title: Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 649a4a50766d26f73584dae4481652c8007b2e9e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174670"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso in Azure AD gestione dei diritti

Per accedere a gestione pacchetti, è possibile modificare gli utenti che possono richiedere un pacchetto di accesso in qualsiasi momento modificando il criterio o aggiungendo un nuovo criterio. È anche possibile modificare le impostazioni di approvazione.

Questo articolo descrive come modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso esistente.

## <a name="choose-between-one-or-multiple-polices"></a>Scegliere tra uno o più criteri

Il modo in cui si specificano gli utenti che possono richiedere un pacchetto di accesso è con un criterio. Quando si crea un pacchetto di accesso, si specificano le impostazioni di richiesta e approvazione che creano un criterio. La maggior parte dei pacchetti di accesso avrà un solo criterio, ma un singolo pacchetto di accesso può avere più criteri. È possibile creare più criteri per un pacchetto di accesso se si desidera consentire a diversi set di utenti di concedere le assegnazioni con diverse impostazioni di richiesta e approvazione. Ad esempio, non è possibile usare un singolo criterio per assegnare utenti interni ed esterni allo stesso pacchetto di accesso. Tuttavia, è possibile creare due criteri nello stesso pacchetto di accesso, uno per gli utenti interni e uno per gli utenti esterni. Se sono presenti più criteri che si applicano a un utente, verrà richiesto al momento della richiesta di selezionare i criteri a cui si desidera assegnare. Il diagramma seguente illustra un pacchetto di accesso con due criteri.

![Più criteri in un pacchetto di accesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Numero di criteri |
| --- | --- |
| Si desidera che tutti gli utenti della directory dispongano delle stesse impostazioni di richiesta e approvazione per un pacchetto di accesso | Uno |
| Desidero che tutti gli utenti di determinate organizzazioni connesse siano in grado di richiedere un pacchetto di accesso | Uno |
| Desidero consentire agli utenti della mia directory e anche a utenti esterni alla directory di richiedere un pacchetto di accesso | Multipli |
| Si desidera specificare impostazioni di approvazione diverse per alcuni utenti | Multipli |
| Si desidera che alcuni utenti accedano alle assegnazioni dei pacchetti per scadere mentre altri utenti possono estenderne l'accesso | Multipli |

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Apre un criterio esistente per le impostazioni di richiesta e approvazione

Per modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Attenersi alla seguente procedura per aprire le impostazioni di richiesta e approvazione per un pacchetto di accesso.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi selezionare il criterio che si desidera modificare.

    Verrà visualizzato il riquadro dettagli criteri nella parte inferiore della pagina.

    ![Pacchetto di accesso-riquadro dei dettagli dei criteri](./media/entitlement-management-shared/policy-details.png)

1. Fare clic su **modifica** per modificare il criterio.

    ![Accedi ai criteri di modifica del pacchetto](./media/entitlement-management-shared/policy-edit.png)

1. Fare clic sulla scheda **richieste** per aprire le impostazioni di richiesta e approvazione.

1. Eseguire i passaggi in una delle sezioni di richiesta seguenti.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Aggiungere un nuovo criterio di impostazioni di richiesta e approvazione

Se si dispone di un set di utenti che devono disporre di impostazioni di richiesta e approvazione diverse, è probabile che sia necessario creare un nuovo criterio. Attenersi alla seguente procedura per iniziare ad aggiungere un nuovo criterio a un pacchetto di accesso esistente.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi su **Aggiungi criterio**.

1. Digitare un nome e una descrizione per il criterio.

    ![Crea criterio con nome e descrizione](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Fare clic su **Avanti** per aprire la scheda **richieste** .

1. Eseguire i passaggi in una delle sezioni di richiesta seguenti.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se si sta modificando un criterio, fare clic su **Aggiorna**. Se si aggiunge un nuovo criterio, fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni del ciclo di vita per un pacchetto di accesso](entitlement-management-access-package-lifecycle-policy.md)
- [Visualizzare le richieste per un pacchetto di accesso](entitlement-management-access-package-requests.md)