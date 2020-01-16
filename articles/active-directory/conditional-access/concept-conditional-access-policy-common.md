---
title: Criteri di accesso condizionale comuni-Azure Active Directory
description: Criteri di accesso condizionale usati comunemente per le organizzazioni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5cfe6b790d5eb2877e46845317779c89cf33884
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978010"
---
# <a name="common-conditional-access-policies"></a>Criteri comuni di accesso condizionale

Le [impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) per la sicurezza sono ottime per alcune organizzazioni che necessitano di una maggiore flessibilità rispetto a quelle offerte. Molti hanno ad esempio la possibilità di escludere account specifici come l'accesso di emergenza o gli account di amministrazione break-Glass dai criteri di accesso condizionale che richiedono l'autenticazione a più fattori. Per tali organizzazioni, è possibile usare i criteri comuni a cui si fa riferimento in questo articolo.

![Criteri di accesso condizionale nella portale di Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Altre informazioni sugli account di accesso di emergenza e sul motivo per cui sono importanti sono disponibili negli articoli seguenti: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione del controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Criteri tipici distribuiti dalle organizzazioni

* [Richiedi](howto-conditional-access-policy-admin-mfa.md) l'autenticazione a più fattori per gli amministratori\*
* [Richiedi autenticazione a più fattori per la gestione di Azure](howto-conditional-access-policy-azure-management.md)\*
* [Richiedi autenticazione a più fattori per tutti gli utenti](howto-conditional-access-policy-all-users-mfa.md)\*
* [Blocca l'autenticazione legacy](howto-conditional-access-policy-block-legacy.md)\*
* [Accesso condizionale basato sul rischio (richiede Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Richiedi percorso attendibile per la registrazione con autenticazione a più fattori](howto-conditional-access-policy-registration.md)
* [Blocca l'accesso in base alla località](howto-conditional-access-policy-location.md)
* [Richiedi un dispositivo conforme](howto-conditional-access-policy-compliant-device.md)

\* questi quattro criteri una volta configurati insieme, simula la funzionalità abilitata dalle [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="next-steps"></a>Passaggi successivi

- [Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale.](troubleshoot-conditional-access-what-if.md)
- [Utilizzare la modalità solo report per l'accesso condizionale per determinare l'effetto delle nuove decisioni sui criteri.](concept-conditional-access-report-only.md)
