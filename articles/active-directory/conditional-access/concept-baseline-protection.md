---
title: Criteri di base di accesso condizionale - Azure Active DirectoryConditional Access baseline policies - Azure Active Directory
description: Criteri di accesso condizionale di base per proteggere le organizzazioni da attacchi comuni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767569"
---
# <a name="what-are-baseline-policies"></a>Cosa sono le politiche di base?

I criteri di base sono un insieme di criteri predefiniti che consentono di proteggere le organizzazioni da molti attacchi comuni. Questi attacchi comuni possono includere password spraying, riproduzione e phishing. I criteri di base sono disponibili in tutte le edizioni di Azure AD. A seguito dell'aumento degli attacchi basati sull'identità registrato negli ultimi anni, Microsoft li renderà presto disponibili a tutti gli utenti. L'obiettivo di questi quattro criteri è garantire che tutte le organizzazioni abbiano un livello di sicurezza di base abilitato senza costi aggiuntivi.

La gestione dei criteri di accesso condizionale personalizzati richiede una licenza di Azure AD Premium.Managing customized Conditional Access policies requires an Azure AD Premium license.

> [!IMPORTANT]
> I criteri di base sono deprecati. Per altre informazioni, vedere [Novità di Azure Active Directory.](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Criteri di base

![Criteri di base per l'accesso condizionale nel portale di AzureConditional Access baseline policies in the Azure portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Sono disponibili quattro criteri di base:

* Richiedi autenticazione a più fattori per gli amministratori (anteprima)Require MFA for admins (preview)
* Protezione dell'utente finale (anteprima)End user protection (preview)
* Bloccare l'autenticazione legacy (anteprima)Block legacy authentication (preview)
* Richiedere l'autenticazione a più fattori per la gestione dei servizi (anteprima)Require MFA for service management (preview)

Tutti e quattro questi criteri influiranno sui flussi di autenticazione legacy, ad esempio POP, IMAP e client desktop Office meno recenti.

### <a name="exclusions"></a>Esclusioni

Quando i criteri di base sono entrati nell'anteprima pubblica iniziale, era disponibile un'opzione per escludere gli utenti dai criteri. Questa funzionalità si è evoluta attraverso l'anteprima ed è stata rimossa nel luglio del 2019. Le organizzazioni che avevano già creato esclusioni sono state in grado di continuare a mantenere i nuovi utenti non sono state in grado di aggiungere esclusioni ai criteri.

### <a name="require-mfa-for-admins-preview"></a>Richiedi autenticazione a più fattori per gli amministratori (anteprima)Require MFA for admins (preview)

A causa della potenza e dell'accesso degli account amministratore, è necessario trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più forte di verifica dell'account quando vengono utilizzati per accedere. In Azure Active Directory, you can get a stronger account verification by requiring administrators to register for and use Azure Multi-Factor Authentication.

Richiedi autenticazione a più fattori per gli amministratori (anteprima) è un criterio di base che richiede l'autenticazione a più fattori (MFA) per i ruoli di directory seguenti, considerati i ruoli di Azure AD con privilegi più privilegi:Require MFA for admins (preview) is a baseline policy that requires multi-factor authentication (MFA) for the following directory roles, considered to be the most privileged Azure AD roles:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza
* Amministratore helpdesk/amministratore password
* Amministratore fatturazione
* Amministratore utenti

Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Protezione dell'utente finale (anteprima)End user protection (preview)

Gli amministratori con privilegi elevati non sono gli unici interessati agli attacchi. I cattivi attori tendono a indirizzare gli utenti normali. Dopo aver ottenuto l'accesso, questi malintenzionati possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale o scaricare l'intera directory ed eseguire un attacco di phishing sull'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti consiste nel richiedere una forma più avanzata di verifica dell'account quando viene rilevato un accesso rischioso.

**La protezione degli utenti finali (anteprima)** è un criterio di base che protegge tutti gli utenti in una directory. L'abilitazione di questo criterio richiede la registrazione di tutti gli utenti per Azure Multi-Factor Authentication entro 14 giorni. Al termine della registrazione, agli utenti verrà richiesto di eseguire l'autenticazione a più fattori solo durante i tentativi di accesso rischioso. Gli account utente compromessi vengono bloccati fino alla reimpostazione della password e alla rimozione del rischio. 

> [!NOTE]
> Tutti gli utenti contrassegnati in precedenza per il rischio vengono bloccati fino alla reimpostazione della password e al blocco del rischio in caso di attivazione dei criteri.

### <a name="block-legacy-authentication-preview"></a>Bloccare l'autenticazione legacy (anteprima)Block legacy authentication (preview)

I protocolli di autenticazione legacy (ad esempio IMAP, SMTP, POP3) sono protocolli normalmente utilizzati dai client di posta meno recenti per l'autenticazione. I protocolli legacy non supportano l'autenticazione a più fattori. Anche se si dispone di un criterio che richiede l'autenticazione a più fattori per la directory, un attore non valido può eseguire l'autenticazione utilizzando uno di questi protocolli legacy e ignorare l'autenticazione a più fattori.

Il modo migliore per proteggere il tuo account da richieste di autenticazione dannose effettuate da protocolli legacy è bloccarli.

Il criterio di base **Blocca autenticazione legacy (anteprima)** blocca le richieste di autenticazione effettuate utilizzando protocolli legacy. L'autenticazione moderna deve essere utilizzata per accedere correttamente a tutti gli utenti. Utilizzato in combinazione con gli altri criteri di base, le richieste provenienti da protocolli legacy verranno bloccate. Inoltre, tutti gli utenti saranno tenuti a MFA quando necessario. Questo criterio non blocca Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Richiedere l'autenticazione a più fattori per la gestione dei servizi (anteprima)Require MFA for service management (preview)

Le organizzazioni usano un'ampia gamma di servizi di Azure e li gestiscono da strumenti basati su Azure Resource Manager, ad esempio:Organizations use a variety of Azure services and manage them from Azure Resource Manager based tools like:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

L'utilizzo di uno di questi strumenti per eseguire la gestione delle risorse è un'azione con privilegi elevati. Questi strumenti possono modificare le configurazioni a livello di sottoscrizione, ad esempio le impostazioni del servizio e la fatturazione delle sottoscrizioni.

Per proteggere le azioni con privilegi, questo criterio Richiedi autenticazione a più fattori per la gestione del servizio (anteprima) richiederà l'autenticazione a più fattori per qualsiasi utente che accede al portale di Azure, ad Azure PowerShell o all'interfaccia della riga di comando di Azure.To protect privileged actions, this **Require MFA for service management (preview)** policy will require multi-factor authentication for any user accessing Azure portal, Azure PowerShell, or Azure CLI.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

* [Abilitazione delle impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md)
* [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
