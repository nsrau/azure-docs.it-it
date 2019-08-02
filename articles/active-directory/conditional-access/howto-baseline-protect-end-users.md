---
title: Protezione dell'utente finale dei criteri di base (anteprima)-Azure Active Directory
description: Criteri di accesso condizionale per richiedere l'autenticazione a più fattori per gli utenti
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
ms.openlocfilehash: afcd9c9d3191caeabe182f499b5fd80cd8e1d8dd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608146"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Criteri di base: Protezione dell'utente finale (anteprima)

Si tende a pensare che gli account amministratore siano gli unici account che necessitano di protezione con multi-factor authentication. Gli amministratori hanno accesso esteso alle informazioni riservate e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Tuttavia, gli attori cattivi tendono a destinare gli utenti finali. Una volta effettuato l'accesso, questi attori malintenzionati possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale oppure scaricare l'intera directory per eseguire un attacco di phishing all'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account, ad esempio l'autenticazione a più fattori (multi-factor authentication).

Per ottenere un ragionevole equilibrio di sicurezza e usabilità, gli utenti non devono essere richiesti ogni volta che accedono. Le richieste di autenticazione che riflettono il normale comportamento dell'utente, ad esempio l'accesso dallo stesso dispositivo dalla stessa posizione, hanno una bassa probabilità di compromissione. Solo gli accessi che sono considerati rischiosi e mostrano le caratteristiche di un attore non valido dovrebbero essere richiesti con le difficoltà di autenticazione a più fattori.

La protezione degli utenti finali è un criterio di [base](concept-baseline-protection.md) dell'autenticazione a più fattori basato sul rischio che protegge tutti gli utenti in una directory, inclusi tutti i ruoli di amministratore. Per abilitare questo criterio è necessario che tutti gli utenti eseguano la registrazione per l'autenticazione a più fattori usando l'app Authenticator Gli utenti possono ignorare la richiesta di registrazione dell'autenticazione a più fattori per 14 giorni, dopo i quali verrà impedito l'accesso fino alla registrazione per l'autenticazione a più fattori. Una volta registrati per l'autenticazione a più fattori, agli utenti verrà richiesto di eseguire l'autenticazione a più fattori solo durante i tentativi di accesso rischioso. Gli account utente compromessi vengono bloccati finché la password non viene reimpostata e gli eventi di rischio sono stati rilasciati.

> [!NOTE]
> Questi criteri si applicano a tutti gli utenti, inclusi gli account Guest, e verranno valutati durante l'accesso a tutte le applicazioni.

## <a name="recovering-compromised-accounts"></a>Ripristino di account compromessi

Per garantire la protezione dei clienti, il servizio di credenziali perse trova le coppie nome utente/password disponibili pubblicamente. Se corrispondono a uno degli utenti, Microsoft contribuisce a proteggere immediatamente l'account. Gli utenti identificati con una credenziale persa vengono confermati compromessi. A questi utenti verrà impedito l'accesso fino alla reimpostazione della password.

Gli utenti assegnati a una licenza Azure AD Premium possono ripristinare l'accesso tramite la reimpostazione della password self-service (SSPR) se la funzionalità è abilitata nella propria directory. Gli utenti senza una licenza Premium che viene bloccata devono contattare un amministratore per eseguire una reimpostazione manuale della password e ignorare l'evento di rischio utente contrassegnato.

### <a name="steps-to-unblock-a-user"></a>Passaggi per sbloccare un utente

Verificare che l'utente sia stato bloccato dal criterio esaminando i log di accesso dell'utente.

1. Un amministratore deve accedere al **portale di Azure** e passare a **Azure Active Directory** > **utenti** > fare clic sul nome dell'utente e passare ad accessi.
1. Per avviare la reimpostazione della password per un utente bloccato, un amministratore deve passare a **Azure Active Directory** > **utenti contrassegnati per il rischio**
1. Fare clic sull'utente il cui account è bloccato per visualizzare le informazioni sull'attività di accesso recente dell'utente.
1. Fare clic su Reimposta password per assegnare una password temporanea che deve essere modificata al successivo accesso.
1. Fare clic su Ignora tutti gli eventi per reimpostare il Punteggio di rischio dell'utente.

L'utente può ora accedere, reimpostare la password e accedere all'applicazione.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché i criteri di **protezione dell'utente finale** si applicano a tutti gli utenti nella directory, è necessario considerare alcune considerazioni per garantire una distribuzione uniforme. Queste considerazioni includono l'identificazione degli utenti e dei principi di servizio in Azure AD che non possono o non devono eseguire l'autenticazione a più fattori, nonché le applicazioni e i client usati dall'organizzazione che non supportano l'autenticazione moderna.

### <a name="legacy-protocols"></a>Protocolli legacy

I protocolli di autenticazione legacy (IMAP, SMTP, POP3 e così via) vengono usati dai client di posta per eseguire richieste di autenticazione. Questi protocolli non supportano l'autenticazione a più fattori.  La maggior parte dei compromessi per gli account visualizzati da Microsoft è causata da cattivi attori che eseguono attacchi contro i protocolli legacy che tentano di ignorare l'autenticazione a più fattori. Per assicurarsi che l'autenticazione a più fattori sia necessaria quando si esegue l'accesso a un account e gli attori malintenzionati non sono in grado di ignorare l'autenticazione a più fattori, questo criterio blocca tutte le richieste di autenticazione effettuate agli account amministratore

> [!WARNING]
> Prima di abilitare questo criterio, verificare che gli utenti non utilizzino protocolli di autenticazione legacy. Vedere l'articolo [procedura: Per ulteriori informazioni, bloccare l'autenticazione legacy](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) per Azure ad con accesso condizionale.

## <a name="enable-the-baseline-policy"></a>Abilitare i criteri di base

Criteri di **base dei criteri: La protezione dell'utente finale (** anteprima) è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello accesso condizionale in portale di Azure.

Per abilitare questo criterio e proteggere gli utenti:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri selezionare **criteri di base: Protezione dell'utente finale (anteprima**).
1. Impostare **Abilita criterio** per **usare immediatamente i criteri**.
1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione di base per l'accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
