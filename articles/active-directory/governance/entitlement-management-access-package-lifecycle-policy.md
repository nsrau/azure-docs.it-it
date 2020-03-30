---
title: Modificare le impostazioni del ciclo di vita per un pacchetto di accesso nella gestione dei diritti di Azure AD - Azure Active DirectoryChange lifecycle settings for an access package in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come modificare le impostazioni del ciclo di vita per un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to change lifecycle settings for an access package in Azure Active Directory entitlement management.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261983"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni del ciclo di vita per un pacchetto di accesso nella gestione dei diritti di Azure ADChange lifecycle settings for an access package in Azure AD entitlement management

In qualità di gestore di pacchetti di accesso, è possibile modificare le impostazioni del ciclo di vita per un pacchetto di accesso in qualsiasi momento modificando un criterio esistente. Se si modifica la data di scadenza di un criterio, la data di scadenza per le richieste che si trovano già in uno stato di approvazione o approvazione in sospeso non verrà modificata.

In questo articolo viene descritto come modificare le impostazioni del ciclo di vita per un pacchetto di accesso esistente.

## <a name="open-lifecycle-settings"></a>Aprire le impostazioni del ciclo di vita

Per modificare le impostazioni del ciclo di vita per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Seguire questi passaggi per aprire le impostazioni del ciclo di vita per un pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Criteri** e quindi sul criterio contenente le impostazioni del ciclo di vita che si desidera modificare.

    Il riquadro Dettagli criteri si apre nella parte inferiore della pagina.

    ![Pacchetto di accesso - Riquadro dei dettagli dei criteri](./media/entitlement-management-shared/policy-details.png)

1. Fare clic su **Modifica** per modificare il criterio.

    ![Pacchetto di accesso - Modifica criterio](./media/entitlement-management-shared/policy-edit.png)

1. Fare clic sulla scheda **Ciclo di vita** per aprire le impostazioni del ciclo di vita.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accessoChange request and approval settings for an access package](entitlement-management-access-package-request-policy.md)