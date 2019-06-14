---
title: Linea di base protezione Criteri di accesso condizionale - Azure Active Directory
description: Criteri di accesso condizionale della linea di base per le organizzazioni di proteggere da attacchi comuni
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
ms.openlocfilehash: 39a591a335d022ef7b2b99fdec930ddf0496cd47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112579"
---
# <a name="what-are-baseline-policies"></a>Quali sono i criteri di base?

I criteri di base sono un set di criteri predefiniti che consentono di proteggere da attacchi comuni molte organizzazioni. Questi attacchi comuni possono includere phishing, riproduzione e spray password. I criteri di base sono disponibili in tutte le edizioni di Azure Active Directory. Microsoft sta rendendo questi criteri di protezione della linea di base disponibili per tutti gli utenti perché è in aumento negli ultimi anni sono state attacchi basati su identità. L'obiettivo di questi quattro criteri è per garantire che tutte le organizzazioni hanno un livello linea di base di sicurezza abilitato senza costi aggiuntivi.  

La gestione di criteri di accesso condizionale personalizzati richiede una licenza Azure AD Premium.

## <a name="baseline-policies"></a>Criteri di base

![Criteri di base di accesso condizionale nel portale di Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Esistono quattro criteri di base che le organizzazioni possono abilitare:

* [Richiedere l'autenticazione MFA per gli amministratori](howto-baseline-protect-administrators.md)
* [Protezione dell'utente finale (anteprima)](howto-baseline-protect-end-users.md)
* [Autenticazione legacy di blocco (anteprima)](howto-baseline-protect-legacy-auth.md)
* [Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)](howto-baseline-protect-azure.md)

Tutti e quattro di questi criteri interesserà i flussi di autenticazione legacy come POP, IMAP e client desktop di Office meno recenti.

### <a name="require-mfa-for-admins"></a>Richiedere l'autenticazione a più fattori per gli amministratori

A causa la potenza e l'accesso degli account amministratore, è necessario considerarle con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più avanzata di verifica dell'account quando vengono usati per accedere. In Azure Active Directory, è possibile ottenere una maggiore verifica dell'account, richiedendo agli amministratori di richiedere e usare Azure multi-Factor Authentication.

[Richiedere l'autenticazione MFA per gli amministratori](howto-baseline-protect-administrators.md) è un criterio di base che richiede l'autenticazione a più fattori (MFA) per i ruoli della directory seguenti, considerati i ruoli di Azure AD con più privilegi:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza
* Amministratore supporto tecnico o amministratore Password
* Amministratore fatturazione
* Amministratore utenti

Se l'organizzazione dispone di questi account in uso nel codice o gli script, è consigliabile sostituirli con [gestito identità](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere account utente specifici dai criteri di base.

### <a name="end-user-protection-preview"></a>Protezione dell'utente finale (anteprima)

Gli amministratori con privilegi elevati non sono gli unici negli attacchi di destinazione. Cattivi attori tendono a destinate agli utenti normali. Una volta ottenuto l'accesso, questi cattivi attori può richiedere l'accesso a informazioni con privilegi per conto il titolare dell'account originale o scaricare l'intera directory ed eseguire un attacco di phishing per l'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti consiste nel richiedere una forma più avanzata di verifica dell'account quando viene rilevato un accesso rischioso.

**Protezione dell'utente finale (anteprima)** è un criterio di base che consente di proteggere tutti gli utenti in una directory. Abilita questo criterio richiede tutti gli utenti di registrarsi ad Azure multi-Factor Authentication entro 14 giorni. Una volta registrato, verranno richiesto agli utenti per MFA solo durante i tentativi di accesso rischiosi. Account utente compromessi vengono bloccate finché non reimpostare e rischio licenziamento password.

### <a name="block-legacy-authentication-preview"></a>Autenticazione legacy di blocco (anteprima)

Protocolli di autenticazione legacy (ad esempio: IMAP, SMTP, POP3) sono protocolli utilizzati normalmente da client di posta elettronica meno recenti per l'autenticazione. I protocolli legacy non supportano multi-factor authentication. Anche se si dispone di un criterio che richiede l'autenticazione a più fattori per la directory, un attore malintenzionato potrebbe può eseguire l'autenticazione tramite uno di questi protocolli legacy e bypass di multi-factor authentication.

Il modo migliore per proteggere l'account da richieste di autenticazione dannoso eseguite dai protocolli legacy è per bloccarli.

Il **autenticazione legacy di blocco (anteprima)** criteri base bloccano le richieste di autenticazione vengono effettuate tramite i protocolli legacy. L'autenticazione moderna deve essere usato per accedere correttamente per tutti gli utenti. Usato in combinazione con altri criteri di base, le richieste provenienti dai protocolli legacy verranno bloccate. Inoltre, tutti gli utenti dovranno MFA quando necessario. Questo criterio non blocca Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)

Le organizzazioni usano un'ampia gamma di servizi di Azure e gestirli da strumenti basati su Azure Resource Manager, ad esempio:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Utilizza uno di questi strumenti per eseguire la gestione delle risorse è un'azione con privilegiata elevati. Questi strumenti possono modificare le configurazioni a livello di sottoscrizione, ad esempio le impostazioni del servizio e la fatturazione di sottoscrizione.

Per proteggere le azioni con privilegi, ciò **Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)** criteri richiederanno multi-factor authentication per qualsiasi utente che accede al portale di Azure, Azure PowerShell o CLI di Azure.

## <a name="enable-a-baseline-policy"></a>Abilitare un criterio di base

Per abilitare un criterio di base:

1. Accedi per il **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri, selezionare un criterio di base che si vuole abilitare.
1. Impostare **abilitare i criteri** al **su**.
1. Fare clic su Salva.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/azure-ad-secure-steps.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
* [Richiedere l'autenticazione MFA per gli amministratori](howto-baseline-protect-administrators.md)
* [Protezione dell'utente finale (anteprima)](howto-baseline-protect-end-users.md)
* [Autenticazione legacy di blocco (anteprima)](howto-baseline-protect-legacy-auth.md)
* [Richiedere l'autenticazione MFA per la gestione dei servizi (anteprima)](howto-baseline-protect-azure.md)