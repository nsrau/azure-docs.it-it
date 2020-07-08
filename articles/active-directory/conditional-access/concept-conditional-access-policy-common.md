---
title: Criteri di accesso condizionale comuni-Azure Active Directory
description: Criteri di accesso condizionale usati comunemente per le organizzazioni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf4d7b9f622ff590eee401544bf62655fd8d096
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024384"
---
# <a name="common-conditional-access-policies"></a>Criteri comuni di accesso condizionale

Le [impostazioni predefinite](../fundamentals/concept-fundamentals-security-defaults.md) per la sicurezza sono ottime per alcune organizzazioni che necessitano di una maggiore flessibilità rispetto a quelle offerte. Molte organizzazioni, ad esempio, hanno la possibilità di escludere account specifici come l'accesso di emergenza o gli account di amministrazione break-Glass dai criteri di accesso condizionale che richiedono l'autenticazione a più fattori. Per tali organizzazioni, è possibile usare i criteri comuni a cui si fa riferimento in questo articolo.

![Criteri di accesso condizionale nella portale di Azure](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Altre informazioni sugli account di accesso di emergenza e sul motivo per cui sono importanti sono disponibili negli articoli seguenti: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Criteri tipici distribuiti dalle organizzazioni

* [Blocca autenticazione legacy](howto-conditional-access-policy-block-legacy.md)\*
* [Richiedi autenticazione a più fattori per gli amministratori](howto-conditional-access-policy-admin-mfa.md)\*
* [Richiedi autenticazione a più fattori per la gestione di Azure](howto-conditional-access-policy-azure-management.md)\*
* [Richiedi autenticazione a più fattori per tutti gli utenti](howto-conditional-access-policy-all-users-mfa.md)\*

\*Questi quattro criteri, quando configurati insieme, simulano la funzionalità abilitata dalle [impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Criteri aggiuntivi

* [Accesso condizionale basato sul rischio di accesso (richiede Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Accesso condizionale basato sul rischio utente (richiede Azure AD Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Richiedere una località attendibile per la registrazione dell'autenticazione a più fattori](howto-conditional-access-policy-registration.md)
* [Blocca l'accesso in base alla località](howto-conditional-access-policy-location.md)
* [Richiedere un dispositivo conforme](howto-conditional-access-policy-compliant-device.md)
* [Blocca l'accesso eccetto app specifiche](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Passaggi successivi

- [Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale.](troubleshoot-conditional-access-what-if.md)

- [Utilizzare la modalità solo report per l'accesso condizionale per determinare l'effetto delle nuove decisioni sui criteri.](concept-conditional-access-report-only.md)
