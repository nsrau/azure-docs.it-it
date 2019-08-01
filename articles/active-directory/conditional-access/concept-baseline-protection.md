---
title: Criteri di protezione di base per l'accesso condizionale-Azure Active Directory
description: Criteri di accesso condizionale di base per proteggere le organizzazioni dagli attacchi comuni
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
ms.openlocfilehash: 819b2f3c54d97639eceae9aed60fdd5da2e7b3ef
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608179"
---
# <a name="what-are-baseline-policies"></a>Che cosa sono i criteri di base?

I criteri di base sono un set di criteri predefiniti che consentono di proteggere le organizzazioni da molti attacchi comuni. Questi attacchi comuni possono includere spray, riproduzione e phishing delle password. I criteri di base sono disponibili in tutte le edizioni di Azure AD. Microsoft sta rendendo disponibili i criteri di protezione di base a tutti perché gli attacchi basati sull'identità sono stati in crescita negli ultimi anni. L'obiettivo di questi quattro criteri è garantire che tutte le organizzazioni dispongano di un livello di sicurezza di base abilitato senza costi aggiuntivi.  

Per la gestione dei criteri di accesso condizionale personalizzati è necessaria una licenza Azure AD Premium.

## <a name="baseline-policies"></a>Criteri di base

![Criteri di base per l'accesso condizionale nella portale di Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Sono disponibili quattro criteri di base che le organizzazioni possono abilitare:

* [Richiedi autenticazione a più fattori per gli amministratori (anteprima)](howto-baseline-protect-administrators.md)
* [Protezione dell'utente finale (anteprima)](howto-baseline-protect-end-users.md)
* [Blocca autenticazione legacy (anteprima)](howto-baseline-protect-legacy-auth.md)
* [Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)](howto-baseline-protect-azure.md)

Tutti e quattro questi criteri influiscono sui flussi di autenticazione legacy, ad esempio POP, IMAP e client desktop di Office meno recenti.

### <a name="require-mfa-for-admins-preview"></a>Richiedi autenticazione a più fattori per gli amministratori (anteprima)

A causa della potenza e dell'accesso degli account amministratore, è consigliabile gestirli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più avanzata di verifica dell'account quando vengono usati per eseguire l'accesso. In Azure Active Directory, è possibile ottenere una verifica più avanzata degli account richiedendo agli amministratori di registrarsi e usare Azure Multifactor Authentication.

[Richiedi autenticazione a più fattori per gli amministratori (anteprima)](howto-baseline-protect-administrators.md) è un criterio di base che richiede l'autenticazione a più fattori per i ruoli della directory seguenti, considerati come i ruoli Azure ad più privilegiati:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore accesso condizionale
* Amministratore della sicurezza
* Amministratore helpdesk/amministratore password
* Amministratore fatturazione
* Amministratore utenti

Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Protezione dell'utente finale (anteprima)

Gli amministratori con privilegi elevati non sono gli unici destinati ad attacchi. Gli attori cattivi tendono a raggiungere utenti normali. Una volta effettuato l'accesso, questi attori malintenzionati possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale oppure scaricare l'intera directory ed eseguire un attacco di phishing all'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account quando viene rilevato un accesso rischioso.

La **protezione dell'utente finale (anteprima)** è un criterio di base che protegge tutti gli utenti di una directory. L'abilitazione di questo criterio richiede a tutti gli utenti di registrarsi per l'autenticazione a più fattori di Azure entro 14 giorni. Una volta effettuata la registrazione, agli utenti verrà richiesto di eseguire l'autenticazione a più fattori solo durante i tentativi di accesso rischioso. Gli account utente compromessi vengono bloccati fino alla reimpostazione della password e al rischio. 

[!NOTE]
Tutti gli utenti contrassegnati in precedenza per il rischio vengono bloccati fino alla reimpostazione della password e alla dismissione del rischio dopo l'attivazione dei criteri.

### <a name="block-legacy-authentication-preview"></a>Blocca autenticazione legacy (anteprima)

Protocolli di autenticazione legacy (ad esempio: IMAP, SMTP, POP3 sono i protocolli usati normalmente dai client di posta meno recenti per l'autenticazione. I protocolli legacy non supportano l'autenticazione a più fattori. Anche se si dispone di criteri che richiedono l'autenticazione a più fattori per la directory, un attore malintenzionato può eseguire l'autenticazione usando uno di questi protocolli legacy e ignorare la funzionalità di autenticazione a più fattori.

Il modo migliore per proteggere l'account da richieste di autenticazione dannose effettuate da protocolli legacy consiste nel bloccarle.

I criteri di base **blocca autenticazione legacy (anteprima)** bloccano le richieste di autenticazione effettuate usando protocolli legacy. Per eseguire correttamente l'accesso a tutti gli utenti, è necessario usare l'autenticazione moderna. Usato in combinazione con gli altri criteri di base, le richieste provenienti da protocolli legacy verranno bloccate. Inoltre, tutti gli utenti saranno necessari per l'autenticazione a più fattori ogni volta che è necessario. Questo criterio non blocca Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)

Le organizzazioni usano un'ampia gamma di servizi di Azure e li gestiscono da strumenti Azure Resource Manager basati su, ad esempio:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Usare uno di questi strumenti per eseguire la gestione delle risorse è un'azione con privilegi elevati. Questi strumenti possono modificare le configurazioni a livello di sottoscrizione, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione.

Per proteggere le azioni con privilegi, è necessario che l'autenticazione a più fattori per i criteri di **gestione dei servizi (anteprima)** richieda l'autenticazione a più fattori per ogni utente che accede a portale di Azure, Azure PowerShell o l'interfaccia della riga di comando

## <a name="enable-a-baseline-policy"></a>Abilitare un criterio di base

Per abilitare un criterio di base:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **accesso condizionale**.
1. Nell'elenco dei criteri selezionare un criterio di base che si vuole abilitare.
1. Impostare **Abilita criterio** **su on**.
1. Fare clic su Salva.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
* [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md)
* [Richiedi autenticazione a più fattori per gli amministratori (anteprima)](howto-baseline-protect-administrators.md)
* [Protezione dell'utente finale (anteprima)](howto-baseline-protect-end-users.md)
* [Blocca autenticazione legacy (anteprima)](howto-baseline-protect-legacy-auth.md)
* [Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)](howto-baseline-protect-azure.md)
