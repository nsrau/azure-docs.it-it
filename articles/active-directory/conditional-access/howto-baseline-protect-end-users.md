---
title: Criterio di base per la protezione dell'utente finale (anteprima) - Azure Active Directory
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
ms.openlocfilehash: 35cd52fb82e5e4cce759be5dfdd8872f64802459
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003162"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Criterio di base: Protezione dell'utente finale (anteprima)

Tendiamo a pensare che gli account degli amministratori sono gli unici account che richiedono protezione multi-factor Authentication (MFA). Gli amministratori hanno ampio accesso a informazioni riservate e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Tuttavia, gli attori dannosi tendono agli utenti finali di destinazione. Una volta ottenuto l'accesso, questi cattivi attori può richiedere l'accesso a informazioni con privilegi per conto il titolare dell'account originale o scaricare l'intera directory per eseguire un attacco di phishing per l'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti consiste nel richiedere una forma più avanzata di verifica dell'account, ad esempio multi-factor authentication (MFA).

Per raggiungere un equilibrio ragionevole di sicurezza e facilità di utilizzo, agli utenti non devono essere richiesto ogni volta che accedi. Le richieste di autenticazione che riflettano il comportamento di utente normale, ad esempio l'accesso dallo stesso dispositivo dallo stesso percorso, hanno una bassa probabilità di compromissione. Solo accessi considerati rischiosi e mostrano le caratteristiche di un attore malintenzionato potrebbe devono essere richiesto con le verifiche MFA.

![Richiedere l'autenticazione MFA per gli utenti](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

Protezione dell'utente finale è un MFA basato sui rischi [criterio di base](concept-baseline-protection.md) che protegge tutti gli utenti in una directory, inclusi tutti i ruoli di amministratore. Abilita questo criterio richiede tutti gli utenti di effettuare la registrazione per MFA usando l'App Authenticator. Gli utenti possono ignorare la richiesta di registrazione MFA per 14 giorni, dopodiché verranno bloccati dall'accesso fino a quando non si registrano per MFA. Una volta registrato per MFA, verranno richiesto agli utenti per MFA solo durante i tentativi di accesso rischiosi. Account utente compromessi vengono bloccate finché non viene reimpostata la password e gli eventi di rischio sono stati ignorati.

> [!NOTE]
> Questo criterio si applica a tutti gli utenti, inclusi gli account guest e verrà valutato durante l'accesso a tutte le applicazioni.

## <a name="recovering-compromised-accounts"></a>Il ripristino di account compromessi

Per proteggere i clienti, il servizio credenziali perse di Microsoft consente di trovare coppie di nome utente/password disponibile pubblicamente. Se uno dei nostri utenti corrispondono, contribuiamo alla protezione immediatamente tale account. Gli utenti identificati come avente una credenziale persa vengono confermati compromesso. Questi utenti non potranno accedere fino a quando non viene reimpostato la propria password.

Gli utenti assegnati una licenza di Azure AD Premium possono ripristinare l'accesso tramite la reimpostazione della password self-service (SSPR) se è abilitata la funzionalità nella propria directory. Gli utenti senza licenza premium che diventano bloccati devono contattare un amministratore per eseguire una reimpostazione manuale della password e chiudere l'evento di rischio utente contrassegnato.

### <a name="steps-to-unblock-a-user"></a>Procedura per sbloccare un utente

Verificare che l'utente è stata bloccata dai criteri, esaminare i log di accesso dell'utente.

1. Un amministratore deve eseguire l'accesso per il **portale di Azure** e passare alla **Azure Active Directory** > **utenti** > fare clic sul nome dell'utente e passare agli accessi.
1. Per avviare la reimpostazione su un utente bloccato della password, un amministratore deve passare a **Azure Active Directory** > **utenti contrassegnati per il rischio**
1. Fare clic sull'utente il cui account viene bloccato per visualizzare informazioni sull'attività dell'utente recente accesso.
1. Fare clic su Reimposta Password per assegnare una password temporanea che deve essere modificata dall'account di accesso successivo.
1. Fare clic su Elimina tutti gli eventi per reimpostare il punteggio di rischio dell'utente.

L'utente può accedere a questo punto, reimpostare la password e accedere all'applicazione.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Poiché il **protezione degli utenti finali** criteri si applicano a tutti gli utenti nella directory, è necessario apportare per garantire una regolare distribuzione diverse considerazioni. Queste considerazioni includono l'identificazione degli utenti e oggetti entità servizio in Azure AD che non è possibile o non devono eseguire MFA, nonché applicazioni e client usati dall'organizzazione che non supportano l'autenticazione moderna.

### <a name="legacy-protocols"></a>Protocolli legacy

Protocolli di autenticazione legacy (IMAP, SMTP, POP3, e così via) usati dai client di posta elettronica per effettuare richieste di autenticazione. Questi protocolli non supportano l'autenticazione a più fattori.  La maggior parte dei rischi di compromissione di account rilevati da Microsoft sono causata da cattivi attori esegue gli attacchi contro i protocolli legacy, il tentativo di ignorare MFA. Per garantire che è richiesta la MFA quando accedono a un account e cattivi attori non sono in grado di ignorare MFA, questo criterio blocca tutte le richieste di autenticazione apportate agli account amministratore da protocolli legacy.

> [!WARNING]
> Prima di abilitare questo criterio, assicurarsi che gli utenti non usano protocolli di autenticazione legacy. Vedere l'articolo [come: L'autenticazione legacy di blocco con Azure AD con accesso condizionale](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) per altre informazioni.

### <a name="user-exclusions"></a>Esclusioni di utente

Questo criterio di base offre la possibilità di escludere gli utenti. Prima di abilitare i criteri per il tenant, è consigliabile escludere i seguenti account:

* **Accesso di emergenza** oppure **emergenza** gli account per evitare il blocco degli account a livello di tenant. Nello scenario improbabile che tutti gli amministratori sono bloccati fuori dal tenant, l'account amministrativo di accesso di emergenza è utilizzabile per accedere alla procedura per ripristinare l'accesso ed esami del tenant.
   * Altre informazioni sono reperibili nell'articolo [gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Gli account del servizio** e **principi del servizio**, ad esempio la sincronizzazione Account connettere Azure AD. Account del servizio sono account non interattivi che non sono associate ad alcun utente specifico. In genere vengono usati dai servizi back-end e consentire l'accesso a livello di codice alle applicazioni. Gli account del servizio devono essere escluse perché Impossibile completare l'autenticazione a più fattori a livello di codice.
   * Se l'organizzazione dispone di questi account in uso nel codice o gli script, è consigliabile sostituirli con [gestito identità](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.
* Utenti che non dispongono o non sarà in grado di usare uno Smartphone.
   * Questo criterio richiede agli utenti di registrarsi per MFA usando l'app Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Abilitare il criterio di base

I criteri **criterio di base: Protezione dell'utente finale (anteprima)** è preconfigurata e verrà visualizzata nella parte superiore quando si passa al pannello di accesso condizionale nel portale di Azure.

Per abilitare questo criterio e proteggere gli amministratori:

1. Accedi per il **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri, selezionare **criterio di base: Protezione dell'utente finale (anteprima)**.
1. Impostare **abilitare i criteri** al **Usa i criteri immediatamente**.
1. Aggiungere eventuali esclusioni utente facendo clic su **gli utenti** > **selezionare gli utenti esclusi** e scegliendo gli utenti che devono essere escluse. Fare clic su **selezionate** quindi **eseguita**.
1. Fare clic su **salvare**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Criteri di protezione della linea di base di accesso condizionale](concept-baseline-protection.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
