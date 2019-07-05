---
title: Criterio di base per richiedere il MFA per gli amministratori - Azure Active Directory
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560944"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Criterio di base: Richiedere l'autenticazione MFA per gli amministratori (anteprima)

Gli utenti con accesso ad account con privilegi hanno accesso illimitato all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma di verifica degli account più avanzata quando vengono usati per l'accesso. In Azure Active Directory è possibile richiedere l'autenticazione a più fattori (MFA) per ottenere una verifica degli account più avanzata.

**Richiedere l'autenticazione MFA per gli amministratori (anteprima)**  è un [criterio di base](concept-baseline-protection.md) che richiede l'autenticazione a più fattori ogni volta che uno dei seguenti ruoli con privilegi di amministratore accede:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza
* Amministratore supporto tecnico o amministratore Password
* Amministratore fatturazione
* Amministratore utenti

Con l'abilitazione dell'autenticazione a più fattori richiedono per criteri di gruppo admins, i ruoli di nove amministratore precedente saranno necessario effettuare la registrazione per MFA usando l'App Authenticator. Dopo aver completata la registrazione MFA, gli amministratori dovranno eseguire autenticazione a più fattori ogni volta che accedi.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché il **Richiedi autenticazione a più fattori per gli amministratori (anteprima)** criteri si applicano a tutti gli amministratori critico, è necessario apportare per garantire una regolare distribuzione diverse considerazioni. Queste considerazioni includono l'identificazione degli utenti e oggetti entità servizio in Azure AD che non è possibile o non devono eseguire MFA, nonché applicazioni e client usati dall'organizzazione che non supportano l'autenticazione moderna.

### <a name="legacy-protocols"></a>Protocolli legacy

Protocolli di autenticazione legacy (IMAP, SMTP, POP3, e così via) usati dai client di posta elettronica per effettuare richieste di autenticazione. Questi protocolli non supportano l'autenticazione a più fattori. La maggior parte dei rischi di compromissione di account rilevati da Microsoft sono causata da cattivi attori esegue gli attacchi contro i protocolli legacy, il tentativo di ignorare MFA. Per garantire che è richiesta la MFA quando accedono a un account amministrativo e cattivi attori non sono in grado di ignorare MFA, questo criterio blocca tutte le richieste di autenticazione apportate agli account amministratore da protocolli legacy.

> [!WARNING]
> Prima di abilitare questo criterio, assicurarsi che gli amministratori non usano protocolli di autenticazione legacy. Vedere l'articolo [come: L'autenticazione legacy di blocco con Azure AD con accesso condizionale](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) per altre informazioni.

## <a name="enable-the-baseline-policy"></a>Abilitare il criterio di base

I criteri **criterio di base: Richiedere l'autenticazione MFA per gli amministratori (anteprima)** è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello di accesso condizionale nel portale di Azure.

Per abilitare questo criterio e proteggere gli amministratori:

1. Accedi per il **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri, selezionare **criterio di base: Richiedere l'autenticazione MFA per gli amministratori (anteprima)** .
1. Impostare **abilitare i criteri** al **Usa i criteri immediatamente**.
1. Fare clic su **salvare**.

> [!WARNING]
> Si è verificato percorribile **abilita automaticamente i criteri in futuro** quando questo criterio è stato in fase di anteprima. È stata rimossa questa opzione per ridurre al minimo impatto sugli utenti improvvisi. Se si seleziona questa opzione quando era disponibile **non usare i criteri** viene automaticamente dopo aver selezionato. Se si vuole usare questo criterio di base, vedere i passaggi precedenti per abilitarlo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione della linea di base di accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
