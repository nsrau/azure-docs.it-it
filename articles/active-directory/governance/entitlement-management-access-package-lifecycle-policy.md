---
title: Modificare le impostazioni del ciclo di vita per un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come modificare le informazioni del richiedente & impostazioni del ciclo di vita per un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980120"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni del ciclo di vita per un pacchetto di accesso in Azure AD gestione dei diritti

Come gestione pacchetti di accesso, è possibile modificare le impostazioni del ciclo di vita per un pacchetto di accesso in qualsiasi momento modificando un criterio esistente. Se si modifica la data di scadenza di un criterio, la data di scadenza per le richieste che si trovano già in uno stato di approvazione in sospeso o approvato non verrà modificata.

Questo articolo descrive come modificare le impostazioni del ciclo di vita per un pacchetto di accesso esistente.

## <a name="open-requestor-information"></a>Apri informazioni sul richiedente
Per assicurarsi che gli utenti dispongano del diritto di accesso a un pacchetto di accesso, è possibile configurare domande personalizzate per chiedere agli utenti di richiedere l'accesso a determinati pacchetti di accesso. Le opzioni di configurazione includono: localizzazione, obbligatorio/facoltativo e formati di risposta di testo/più scelte. I richiedenti vedranno le domande quando richiedono che il pacchetto e i responsabili approvazione visualizzino le risposte alle domande per aiutarli a prendere la decisione. Usare la procedura seguente per configurare le domande in un pacchetto di Access:

## <a name="open-lifecycle-settings"></a>Impostazioni del ciclo di vita aperte

Per modificare le impostazioni del ciclo di vita per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Per aprire le impostazioni del ciclo di vita per un pacchetto di accesso, seguire questa procedura.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

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