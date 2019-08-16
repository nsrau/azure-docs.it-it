---
title: I criteri di base richiedono l'autenticazione a più fattori per gli amministratori-Azure Active Directory
description: Criteri di accesso condizionale per richiedere l'autenticazione a più fattori per gli amministratori
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b00b061b1763d4b4e7236d8dc9ac1eedf7f923bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533028"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Criteri di base: Richiedi autenticazione a più fattori per gli amministratori (anteprima)

Gli utenti con accesso ad account con privilegi hanno accesso illimitato all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma di verifica degli account più avanzata quando vengono usati per l'accesso. In Azure Active Directory è possibile richiedere l'autenticazione a più fattori (MFA) per ottenere una verifica degli account più avanzata.

Richiedi autenticazione a più fattori **per gli amministratori (anteprima)** è un [criterio di base](concept-baseline-protection.md) che richiede l'autenticazione a più fattori ogni volta che uno dei seguenti ruoli di amministratore con privilegi esegue l'accesso:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore accesso condizionale
* Amministratore della sicurezza
* Amministratore helpdesk/amministratore password
* Amministratore fatturazione
* Amministratore utenti

Quando si Abilita il criterio Richiedi autenticazione a più fattori per gli amministratori, verranno richiesti i nove ruoli di amministratore precedenti per la registrazione per l'autenticazione a più fattori tramite l'app Authenticator. Una volta completata la registrazione dell'autenticazione a più fattori, gli amministratori dovranno eseguire l'autenticazione a più fattori ogni volta che accedono.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché il criterio Richiedi autenticazione a più fattori **per amministratori (anteprima)** si applica a tutti gli amministratori critici, è necessario tenere presenti alcune considerazioni per garantire una distribuzione uniforme. Queste considerazioni includono l'identificazione degli utenti e dei principi di servizio in Azure AD che non possono o non devono eseguire l'autenticazione a più fattori, nonché le applicazioni e i client usati dall'organizzazione che non supportano l'autenticazione moderna.

### <a name="legacy-protocols"></a>Protocolli legacy

I protocolli di autenticazione legacy (IMAP, SMTP, POP3 e così via) vengono usati dai client di posta per eseguire richieste di autenticazione. Questi protocolli non supportano l'autenticazione a più fattori. La maggior parte dei compromessi per gli account visualizzati da Microsoft è causata da cattivi attori che eseguono attacchi contro i protocolli legacy che tentano di ignorare l'autenticazione a più fattori. Per assicurarsi che l'autenticazione a più fattori sia necessaria quando si esegue l'accesso a un account amministrativo e gli attori malintenzionati non sono in grado di ignorare l'autenticazione a più fattori, questo criterio blocca tutte le richieste di autenticazione effettuate agli account

> [!WARNING]
> Prima di abilitare questo criterio, assicurarsi che gli amministratori non utilizzino protocolli di autenticazione legacy. Vedere l'articolo [procedura: Per ulteriori informazioni, bloccare l'autenticazione legacy](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) per Azure ad con accesso condizionale.

## <a name="enable-the-baseline-policy"></a>Abilitare i criteri di base

Criteri di **base dei criteri: Richiedi autenticazione a più fattori per gli amministratori** (anteprima) è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello accesso condizionale in portale di Azure.

Per abilitare questo criterio e proteggere gli amministratori:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri selezionare **criteri di base: Richiedi l'autenticazione a più fattori per gli**amministratori (anteprima).
1. Impostare **Abilita criterio** per **usare immediatamente i criteri**.
1. Fare clic su **Save**.

> [!WARNING]
> Quando il criterio era in anteprima, era possibile **abilitare automaticamente i criteri in futuro** . Questa opzione è stata rimossa per ridurre al minimo l'effetto improvviso dell'utente. Se è stata selezionata questa opzione quando era disponibile, non **usare i criteri** è ora selezionata automaticamente. Per usare i criteri di base, vedere la procedura precedente per abilitarla.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione di base per l'accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
