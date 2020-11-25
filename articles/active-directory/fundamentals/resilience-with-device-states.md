---
title: Resilienza della compilazione usando gli Stati del dispositivo in Azure Active Directory
description: Guida per architetti e amministratori IT per la creazione di resilienza usando gli Stati dei dispositivi
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4075cb71cb4dae7ac506c16e3987070cfa10bb09
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919553"
---
# <a name="build-resilience-with-device-states"></a>Resilienza di compilazione con gli Stati dei dispositivi

Abilitando [gli Stati dei dispositivi](../devices/overview.md) con Azure ad, gli amministratori possono creare [criteri di accesso condizionale](../conditional-access/overview.md) che controllano l'accesso alle applicazioni in base allo stato del dispositivo. Il vantaggio aggiuntivo dei dispositivi è che soddisfa i requisiti di autenticazione avanzata per l'accesso alle risorse, riducendo così le richieste di autenticazione a più fattori e migliorando la resilienza. 

Il diagramma di flusso seguente illustra i diversi modi per eseguire l'onboarding dei dispositivi in Azure AD che abilitano gli Stati dei dispositivi. È possibile usare più di un utente nell'organizzazione.

![diagramma di flusso per la scelta degli Stati del dispositivo](./media/resilience-with-device-states/admin-resilience-devices.png)

Quando si usano [gli Stati del dispositivo](../devices/overview.md), gli utenti nella maggior parte dei casi possono Single Sign-on alle risorse tramite un [token di aggiornamento primario](../devices/concept-primary-refresh-token.md) (PRT). Il PRT contiene attestazioni relative all'utente e al dispositivo e può essere usato per ottenere i token di autenticazione per accedere alle applicazioni dal dispositivo. Il PRT è valido per 14 giorni e viene rinnovato continuamente fino a quando l'utente usa attivamente il dispositivo, offrendo agli utenti un'esperienza resiliente. Un PRT può anche ottenere un'attestazione di autenticazione a più fattori in diversi modi. Per altre informazioni, vedere [quando una PRT riceve un'attestazione](../devices/concept-primary-refresh-token.md)di autenticazione a più fattori.

## <a name="how-do-device-states-help"></a>In che modo gli Stati dei dispositivi sono utili?

Quando si usa un PRT per richiedere l'accesso a un'applicazione, le attestazioni relative a dispositivi, sessioni e autenticazione a più fattori sono considerate attendibili dal Azure AD. Quando gli amministratori creano criteri che richiedono un controllo basato su dispositivo o un controllo di autenticazione a più fattori, il requisito dei criteri può essere soddisfatto tramite lo stato del dispositivo senza tentare l'autenticazione a più fattori. Gli utenti non vedranno ulteriori richieste di autenticazione a più fattori sullo stesso dispositivo. In questo modo si aumenta la resilienza a un'ininterrotta del Servizio autenticazione a più fattori di Azure o delle relative dipendenze come i provider di telecomunicazioni

## <a name="how-do-i-implement-device-states"></a>Ricerca per categorie implementare gli Stati dei dispositivi?

* Abilitare l'aggiunta di [Azure ad ibride](../devices/hybrid-azuread-join-plan.md) e [Azure ad join](../devices/azureadjoin-plan.md) per i dispositivi Windows di proprietà dell'azienda e richiedere che siano unite in join, se possibile. Se non è possibile, richiedere che siano registrate.

  Se nell'organizzazione sono presenti versioni precedenti di Windows, aggiornare i dispositivi per l'uso di Windows 10.

* Standardizzare l'accesso del browser utente per usare [Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-security-identity) o Google Chrome con le [estensioni](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) [supportate](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) che hanno abilitato l'accesso Single Sign-on facile per le applicazioni Web che usano il PRT.

* Per i dispositivi iOS e Android di proprietà dell'azienda o personali, distribuire l' [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md). Oltre alle funzionalità di accesso con autenticazione a più fattori e senza password, l'app Microsoft Authenticator consentirà l'accesso Single Sign-on all'applicazione nativa tramite [l'autenticazione negoziata](../develop/brokered-auth.md) con un minor numero di richieste di autenticazione per gli utenti finali.

* Per i dispositivi iOS e Android personali o di proprietà dell'azienda usare la [gestione di applicazioni mobili](https://docs.microsoft.com/mem/intune/apps/app-management.md) per accedere in modo sicuro alle risorse aziendali con meno richieste di autenticazione. 

* [Usare il plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)](../develop/apple-sso-plugin.md). In questo modo viene registrato il dispositivo e viene fornito l'accesso SSO tra browser e applicazioni Azure AD native. 

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)


Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
