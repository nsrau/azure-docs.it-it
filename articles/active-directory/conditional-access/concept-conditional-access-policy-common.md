---
title: Criteri di accesso condizionale comuni-Azure Active Directory
description: Criteri di accesso condizionale usati comunemente per le organizzazioni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcca0d2712a462e4d2ecf9c8023d0cb0e68ad6c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576680"
---
# <a name="common-conditional-access-policies"></a>Criteri di accesso condizionale comuni

I criteri di protezione di base sono eccezionali, ma molte organizzazioni necessitano di una maggiore flessibilità rispetto a quelle offerte. Molte organizzazioni, ad esempio, hanno la possibilità di escludere account specifici come l'accesso di emergenza o gli account di amministrazione break-Glass dai criteri di accesso condizionale che richiedono l'autenticazione a più fattori. Per tali organizzazioni, è possibile usare i criteri comuni a cui si fa riferimento in questo articolo.

![Criteri di accesso condizionale nella portale di Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Altre informazioni sugli account di accesso di emergenza e sul motivo per cui sono importanti sono disponibili negli articoli seguenti: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione del controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Criteri tipici distribuiti dalle organizzazioni

* [Richiedi autenticazione a più fattori per gli amministratori](howto-conditional-access-policy-admin-mfa.md)
* [Richiedi autenticazione a più fattori per la gestione di Azure](howto-conditional-access-policy-azure-management.md)
* [Blocca autenticazione legacy](howto-conditional-access-policy-block-legacy.md)
* [Accesso condizionale basato sul rischio (richiede Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Richiedi percorso attendibile per la registrazione con autenticazione a più fattori](howto-conditional-access-policy-registration.md)
* [Blocca l'accesso in base alla località](howto-conditional-access-policy-location.md)
* [Richiedi un dispositivo conforme](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Passaggi successivi

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
