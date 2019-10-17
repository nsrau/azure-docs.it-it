---
title: Modificare le impostazioni del ciclo di vita per un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come modificare le impostazioni del ciclo di vita per un pacchetto di accesso in Azure Active Directory gestione dei diritti (anteprima).
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
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392345"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Modificare le impostazioni del ciclo di vita per un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Come gestione pacchetti di accesso, è possibile modificare le impostazioni del ciclo di vita per un pacchetto di accesso in qualsiasi momento modificando un criterio esistente. Se si modifica la data di scadenza di un criterio, la data di scadenza per le richieste che si trovano già in uno stato di approvazione in sospeso o approvato non verrà modificata.

Questo articolo descrive come modificare le impostazioni del ciclo di vita per un pacchetto di accesso esistente.

## <a name="open-lifecycle-settings"></a>Impostazioni del ciclo di vita aperte

Per modificare le impostazioni del ciclo di vita per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Per aprire le impostazioni del ciclo di vita per un pacchetto di accesso, seguire questa procedura.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** e quindi fare clic sui criteri con le impostazioni del ciclo di vita che si desidera modificare.

    Verrà visualizzato il riquadro dettagli criteri nella parte inferiore della pagina.

    ![Pacchetto di accesso-riquadro dei dettagli dei criteri](./media/entitlement-management-shared/policy-details.png)

1. Fare clic su **modifica** per modificare il criterio.

    ![Accedi ai criteri di modifica del pacchetto](./media/entitlement-management-shared/policy-edit.png)

1. Fare clic sulla scheda **ciclo** di vita per aprire le impostazioni del ciclo di vita.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md)