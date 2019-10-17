---
title: Impostazioni predefinite sicurezza Azure Active Directory
description: Criteri predefiniti di sicurezza progettati per proteggere le organizzazioni dagli attacchi comuni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453043"
---
# <a name="what-are-security-defaults"></a>Che cosa sono le impostazioni predefinite della sicurezza?

La gestione della sicurezza può essere difficile quando gli attacchi comuni correlati all'identità, ad esempio spray, riproduzione e phishing delle password, stanno diventando sempre più diffusi. La creazione di un modo più semplice per garantire che l'organizzazione sia più protetta da questi attacchi comuni è l'obiettivo delle impostazioni predefinite di sicurezza in Azure Active Directory (AD). Le impostazioni predefinite per la sicurezza semplificano la sicurezza e consentono di proteggere l'organizzazione dagli attacchi più comuni. I valori predefiniti per la sicurezza contengono impostazioni di sicurezza preconfigurate per questi attacchi comuni. Microsoft sta rendendo disponibili le impostazioni predefinite di sicurezza a tutti gli utenti. L'obiettivo è garantire che tutte le organizzazioni dispongano di un livello di sicurezza Basic abilitato senza costi aggiuntivi.

![Screenshot delle nuove impostazioni predefinite di sicurezza UX](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Nel tenant verranno attivate le seguenti configurazioni di sicurezza. 

## <a name="unified-mfa-registration"></a>Registrazione dell'autenticazione a più fattori unificata

A tutti gli utenti del tenant verrà richiesto di eseguire la registrazione per Azure Multi-Factor Authentication (multi-factor authentication). Gli utenti avranno 14 giorni per la registrazione per l'autenticazione a più fattori di Azure con l'app Microsoft Authenticator. Dopo la scadenza dei 14 giorni, l'utente non sarà in grado di accedere fino al completamento della registrazione dell'autenticazione a più fattori.

Sono consapevoli che alcuni utenti potrebbero non essere in ufficio e/o non effettueranno l'accesso durante i 14 giorni immediatamente successivi all'abilitazione delle impostazioni predefinite di sicurezza. Per assicurarsi che ogni utente disponga di un tempo sufficiente per la registrazione per l'autenticazione a più fattori, il periodo di 14 giorni è univoco per ogni utente. Il periodo di 14 giorni di un utente inizia dopo il primo accesso interattivo completato dopo l'abilitazione delle impostazioni predefinite di sicurezza.

## <a name="mfa-enforcement"></a>Imposizione dell'autenticazione a più fattori

### <a name="protecting-administrators"></a>Protezione degli amministratori

Gli utenti con accesso agli account con privilegi hanno un maggiore accesso all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma di verifica degli account più avanzata quando vengono usati per l'accesso. In Azure Active Directory, è possibile ottenere una verifica più avanzata dell'account richiedendo l'autenticazione a più fattori.

Una volta completata la registrazione dell'autenticazione a più fattori, verranno richiesti i nove ruoli di amministratore Azure AD seguenti per eseguire l'autenticazione a più fattori ogni volta che accedono.

- Amministratore globale
- Amministratore di SharePoint
- Amministratore di Exchange
- Amministratore di accesso condizionale
- Amministratore della sicurezza
- Amministratore helpdesk/amministratore password
- Amministratore fatturazione
- Amministratore utenti
- Amministratore dell'autenticazione

### <a name="protecting-all-users"></a>Protezione di tutti gli utenti

Si tende a pensare che gli account amministratore siano gli unici account che necessitano di protezione con multi-factor authentication. Gli amministratori hanno accesso esteso alle informazioni riservate e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Tuttavia, gli attori cattivi tendono a destinare gli utenti finali. Una volta effettuato l'accesso, questi attori malintenzionati possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale oppure scaricare l'intera directory per eseguire un attacco di phishing all'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account, ad esempio l'autenticazione a più fattori per tutti. Una volta completata la registrazione dell'autenticazione a più fattori, agli utenti verrà richiesto di eseguire l'autenticazione a più fattori quando necessario

### <a name="blocking-legacy-authentication"></a>Blocco dell'autenticazione legacy

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. L'autenticazione legacy è un termine che fa riferimento a una richiesta di autenticazione effettuata da:

- Client di Office meno recenti che non usano l'autenticazione moderna (ad esempio, Office 2010 client)
- Qualsiasi client che usa protocolli di posta legacy, ad esempio IMAP/SMTP/POP3

Attualmente, la maggior parte di tutti i tentativi di accesso compromessa provengono dall'autenticazione legacy. L'autenticazione legacy non supporta multi-factor authentication. Anche se nella directory sono abilitati criteri di autenticazione a più fattori, un attore malintenzionato può eseguire l'autenticazione usando un protocollo legacy e ignorare l'autenticazione a più fattori. Quando nel tenant sono abilitate le impostazioni predefinite per la sicurezza, tutte le richieste di autenticazione effettuate da un protocollo legacy a qualsiasi verranno bloccate. Le impostazioni predefinite di sicurezza non bloccano Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Protezione delle azioni con privilegi

Le organizzazioni usano un'ampia gamma di servizi di Azure gestiti tramite l'API Azure Resource Manager, tra cui:

- Portale di Azure 
- Azure PowerShell 
- Interfaccia della riga di comando di Azure

L'uso di Azure Resource Manager per gestire i servizi è un'azione con privilegi elevati. Azure Resource Manager possibile modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione. L'autenticazione a fattore singolo è vulnerabile a una serie di attacchi come phishing e spray per la password. È quindi importante verificare l'identità degli utenti che vogliono accedere alle configurazioni di Azure Resource Manager e aggiornamento, richiedendo l'autenticazione a più fattori prima di consentire l'accesso.

Quando nel tenant sono abilitati i valori predefiniti per la sicurezza, tutti gli utenti che accedono al portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure saranno necessari per completare l'autenticazione a più fattori. Questo criterio si applica a tutti gli utenti che accedono a Azure Resource Manager, indipendentemente dal fatto che si tratti di un amministratore o di un utente. Se l'utente non è registrato per l'autenticazione a più fattori, l'utente dovrà eseguire la registrazione usando l'app Microsoft Authenticator per continuare. Non verrà fornito alcun periodo di registrazione dell'autenticazione a più fattori di 14 giorni.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Di seguito sono riportate alcune considerazioni aggiuntive relative alla distribuzione delle impostazioni predefinite di sicurezza per il tenant.

### <a name="legacy-protocols"></a>Protocolli legacy

I protocolli di autenticazione legacy (IMAP, SMTP, POP3 e così via) vengono usati dai client di posta per eseguire richieste di autenticazione. Questi protocolli non supportano l'autenticazione a più fattori. La maggior parte dei compromessi per gli account visualizzati da Microsoft è causata da cattivi attori che eseguono attacchi contro i protocolli legacy che tentano di ignorare l'autenticazione a più fattori. Per assicurarsi che l'autenticazione a più fattori sia necessaria quando si esegue l'accesso a un account amministrativo e i cattivi attori non possono ignorare l'autenticazione a più fattori, le impostazioni predefinite di sicurezza bloccano tutte le richieste di autenticazione effettuate agli account amministratore

> [!WARNING]
> Prima di abilitare questa impostazione, assicurarsi che gli amministratori non utilizzino protocolli di autenticazione legacy. Per ulteriori informazioni, vedere l'articolo [come uscire dall'autenticazione legacy](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Accesso condizionale

L'accesso condizionale può essere usato per configurare i criteri che forniscono lo stesso comportamento abilitato dalle impostazioni predefinite di sicurezza. Se si usa l'accesso condizionale e i criteri di accesso condizionale sono abilitati nell'ambiente, le impostazioni predefinite di sicurezza non saranno disponibili. Se si dispone di una licenza che fornisce l'accesso condizionale, ma non sono abilitati criteri di accesso condizionale nell'ambiente, è possibile usare le impostazioni predefinite di sicurezza finché non si abilitano i criteri della CA.

![Impostazioni predefinite di sicurezza o accesso condizionale non entrambe](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Ecco le istruzioni dettagliate su come usare l'accesso condizionale per configurare criteri equivalenti:

- [Richiedi autenticazione a più fattori per gli amministratori](howto-conditional-access-policy-admin-mfa.md)
- [Richiedi autenticazione a più fattori per la gestione di Azure](howto-conditional-access-policy-azure-management.md)
- [Blocca autenticazione legacy](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Abilitazione delle impostazioni predefinite di sicurezza

Per abilitare le impostazioni predefinite di sicurezza nella directory:

1. Accedere al [portale di Azure](https://portal.azure.com)@no__t-aggiungere1come un amministratore della sicurezza, un amministratore dell'accesso condizionale o un amministratore globale.
1. Passare a **Azure Active Directory** **Proprietà**   @ no__t-2
1. Selezionare **Gestisci impostazioni predefinite sicurezza**
1. Impostare l' **opzione attiva impostazioni predefinite di sicurezza** su **Sì**.
1. Fare clic su Salva.

## <a name="next-steps"></a>Passaggi successivi

[Criteri di accesso condizionale comuni](concept-conditional-access-policy-common.md)

[Che cos'è l'accesso condizionale?](overview.md)
