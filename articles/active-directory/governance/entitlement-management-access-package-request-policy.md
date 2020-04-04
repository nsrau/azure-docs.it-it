---
title: Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso nella gestione dei diritti di Azure AD - Azure Active DirectoryChange request and approval settings for an access package in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to change request and approval settings for an access package in Azure Active Directory entitlement management.
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655949"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso nella gestione dei diritti di Azure ADChange request and approval settings for an access package in Azure AD entitlement management

In qualità di gestore di pacchetti di accesso, è possibile modificare gli utenti che possono richiedere un pacchetto di accesso in qualsiasi momento modificando il criterio o aggiungendo un nuovo criterio. È inoltre possibile modificare le impostazioni di approvazione.

In questo articolo viene descritto come modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso esistente.

## <a name="choose-between-one-or-multiple-polices"></a>Scegli tra una o più forze di polizia

Il modo in cui si specifica chi può richiedere un pacchetto di accesso è con un criterio. Quando si crea un pacchetto di accesso, si specifica l'impostazione di richiesta e approvazione che crea un criterio. La maggior parte dei pacchetti di accesso avrà un singolo criterio, ma un singolo pacchetto di accesso può avere più criteri. È necessario creare più criteri per un pacchetto di accesso se si desidera consentire a diversi set di utenti di ottenere assegnazioni con impostazioni di richiesta e approvazione diverse. Ad esempio, un singolo criterio non può essere utilizzato per assegnare utenti interni ed esterni allo stesso pacchetto di accesso. Tuttavia, è possibile creare due criteri nello stesso pacchetto di accesso, uno per gli utenti interni e uno per gli utenti esterni. Se sono presenti più criteri che si applicano a un utente, verrà richiesto al momento della richiesta di selezionare il criterio a cui desidera essere assegnati. Il diagramma seguente mostra un pacchetto di accesso con due criteri.

![Più criteri in un pacchetto di accessoMultiple policies in an access package](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scenario | Numero di politiche |
| --- | --- |
| Si desidera che tutti gli utenti della directory abbiano le stesse impostazioni di richiesta e approvazione per un pacchetto di accesso | Uno |
| Si desidera che tutti gli utenti di determinate organizzazioni connesse siano in grado di richiedere un pacchetto di accesso | Uno |
| Desidero consentire agli utenti nella mia directory e anche agli utenti esterni alla mia directory di richiedere un pacchetto di accesso | Multipli |
| Si desidera specificare impostazioni di approvazione diverse per alcuni utenti | Multipli |
| Desidero che alcuni utenti accedano alla scadenza delle assegnazioni dei pacchetti, mentre altri utenti possono estendere il loro accesso | Multipli |

Per informazioni sulla logica di priorità utilizzata quando si applicano più criteri, vedere [Più criteri](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Aprire un criterio esistente di impostazioni di richiesta e approvazione

Per modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Seguire questi passaggi per aprire le impostazioni di richiesta e approvazione per un pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Criteri** e quindi sul criterio che si desidera modificare.

    Il riquadro Dettagli criteri si apre nella parte inferiore della pagina.

    ![Pacchetto di accesso - Riquadro dei dettagli dei criteri](./media/entitlement-management-shared/policy-details.png)

1. Fare clic su **Modifica** per modificare il criterio.

    ![Pacchetto di accesso - Modifica criterio](./media/entitlement-management-shared/policy-edit.png)

1. Fare clic sulla scheda **Richieste** per aprire le impostazioni di richiesta e approvazione.

1. Eseguire i passaggi in una delle seguenti sezioni di richiesta.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Aggiungere un nuovo criterio di richiesta e approvazione delle impostazioni

Se si dispone di un set di utenti che devono avere impostazioni di richiesta e approvazione diverse, è probabile che sia necessario creare un nuovo criterio. Seguire questi passaggi per iniziare ad aggiungere un nuovo criterio a un pacchetto di accesso esistente.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Criteri** e quindi **su Aggiungi criteri**.

1. Digitare un nome e una descrizione per il criterio.

    ![Creare criteri con nome e descrizione](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Fare clic su **Avanti** per aprire la scheda **Richieste**.

1. Eseguire i passaggi in una delle seguenti sezioni di richiesta.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se si modifica un criterio, fare clic su **Aggiorna**. Se si aggiunge un nuovo criterio, fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni del ciclo di vita per un pacchetto di accesso](entitlement-management-access-package-lifecycle-policy.md)
- [Visualizzare le richieste per un pacchetto di accessoView requests for an access package](entitlement-management-access-package-requests.md)