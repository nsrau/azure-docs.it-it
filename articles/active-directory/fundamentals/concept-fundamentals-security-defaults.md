---
title: Impostazioni predefinite sicurezza Azure Active Directory
description: Criteri predefiniti di sicurezza che consentono di proteggere le organizzazioni dagli attacchi comuni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb6170aa54c286a5a2d8353c1dd951859fdf8a0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024586"
---
# <a name="what-are-security-defaults"></a>Che cosa sono le impostazioni predefinite della sicurezza?

La gestione della sicurezza può essere difficile quando gli attacchi comuni correlati all'identità sono sempre più diffusi. Questi attacchi includono spray, riproduzione e phishing delle password.

Le impostazioni predefinite di sicurezza in Azure Active Directory (Azure AD) semplificano la sicurezza e proteggono la propria organizzazione. I valori predefiniti per la sicurezza contengono impostazioni di sicurezza preconfigurate per gli attacchi comuni. 

Microsoft sta rendendo disponibili le impostazioni predefinite di sicurezza a tutti gli utenti. L'obiettivo è garantire che tutte le organizzazioni dispongano di un livello di sicurezza Basic abilitato senza costi aggiuntivi. Si attivano le impostazioni predefinite di sicurezza nel portale di Azure.

![Screenshot della portale di Azure con l'interruttore per abilitare le impostazioni predefinite di sicurezza](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Se il tenant è stato creato il 22 ottobre 2019, è possibile che si stia riscontrando il nuovo comportamento protetto per impostazione predefinita e che nel tenant siano già abilitati i valori predefiniti di sicurezza. Per garantire la protezione di tutti gli utenti, vengono implementate le impostazioni predefinite di sicurezza per tutti i nuovi tenant creati.

Altre informazioni sui motivi per cui vengono rese disponibili le impostazioni predefinite di sicurezza sono reperibili nel post di Blog di Alex Weinert, che [introduce le impostazioni predefinite di sicurezza](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Registrazione Multi-Factor Authentication unificata

Tutti gli utenti nel tenant devono registrarsi per l'autenticazione a più fattori sotto forma di servizio Azure Multi-Factor Authentication. Gli utenti hanno 14 giorni per la registrazione Multi-Factor Authentication usando l'app Microsoft Authenticator. Dopo la scadenza dei 14 giorni, l'utente non sarà in grado di accedere fino a quando non viene completata Multi-Factor Authentication registrazione.

Si è consapevoli che alcuni utenti potrebbero non essere in ufficio oppure non potranno accedere entro 14 giorni immediatamente dopo l'abilitazione delle impostazioni predefinite di sicurezza. Per assicurarsi che ogni utente abbia tempo sufficiente per la registrazione Multi-Factor Authentication, il periodo di 14 giorni è univoco per ogni utente. Il periodo di 14 giorni di un utente inizia dopo il primo accesso interattivo completato dopo l'abilitazione delle impostazioni predefinite di sicurezza.

## <a name="multi-factor-authentication-enforcement"></a>Imposizione di Multi-Factor Authentication

### <a name="protecting-administrators"></a>Protezione degli amministratori

Gli utenti con accesso agli account con privilegi hanno un maggiore accesso all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più avanzata di verifica dell'account per l'accesso. In Azure AD, è possibile ottenere una verifica più avanzata degli account richiedendo Multi-Factor Authentication.

Al termine della registrazione con Multi-Factor Authentication, verranno richiesti i nove ruoli di amministratore Azure AD seguenti per eseguire un'autenticazione aggiuntiva ogni volta che accedono:

- Amministratore globale
- Amministratore di SharePoint
- Amministratore di Exchange
- Amministratore di accesso condizionale
- Amministratore della sicurezza
- Amministratore helpdesk o amministratore password
- Amministratore fatturazione
- Amministratore utenti
- Amministratore autenticazione

### <a name="protecting-all-users"></a>Protezione di tutti gli utenti

Si tende a pensare che gli account amministratore siano gli unici account che richiedono livelli aggiuntivi di autenticazione. Gli amministratori hanno accesso esteso alle informazioni riservate e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Ma gli utenti malintenzionati tendono a destinare gli utenti finali. 

Una volta che gli utenti malintenzionati ottengono l'accesso, possono richiedere l'accesso alle informazioni privilegiate per conto del titolare dell'account originale. Possono anche scaricare l'intera directory per eseguire un attacco di phishing all'intera organizzazione. 

Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account, ad esempio Multi-Factor Authentication, per tutti. Al termine della registrazione Multi-Factor Authentication, gli utenti riceveranno la richiesta di autenticazione aggiuntiva ogni volta che è necessario.

### <a name="blocking-legacy-authentication"></a>Blocco dell'autenticazione legacy

Per consentire agli utenti di accedere facilmente alle app Cloud, Azure AD supporta diversi protocolli di autenticazione, inclusa l'autenticazione legacy. *L'autenticazione legacy* è un termine che fa riferimento a una richiesta di autenticazione effettuata da:

- Client di Office meno recenti che non usano l'autenticazione moderna (ad esempio, un client Office 2010).
- Qualsiasi client che utilizza protocolli di posta elettronica precedenti, ad esempio IMAP, SMTP o POP3.

Attualmente, la maggior parte dei compromessi per i tentativi di accesso proviene dall'autenticazione legacy. L'autenticazione legacy non supporta Multi-Factor Authentication. Anche se nella directory è abilitato un criterio di Multi-Factor Authentication, un utente malintenzionato può eseguire l'autenticazione usando un protocollo precedente e ignorare Multi-Factor Authentication. 

Una volta abilitate le impostazioni predefinite di sicurezza nel tenant, tutte le richieste di autenticazione effettuate da un protocollo precedente verranno bloccate. Le impostazioni predefinite di sicurezza non bloccano Exchange ActiveSync.

> [!WARNING]
> Prima di abilitare le impostazioni predefinite di sicurezza, assicurarsi che gli amministratori non utilizzino protocolli di autenticazione meno recenti. Per ulteriori informazioni, vedere [come uscire dall'autenticazione legacy](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Protezione delle azioni con privilegi

Le organizzazioni usano un'ampia gamma di servizi di Azure gestiti tramite l'API Azure Resource Manager, tra cui:

- Portale di Azure 
- Azure PowerShell 
- Interfaccia della riga di comando di Azure

L'uso di Azure Resource Manager per gestire i servizi è un'azione con privilegi elevati. Azure Resource Manager possibile modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione. L'autenticazione a fattore singolo è vulnerabile a una serie di attacchi come phishing e spray per la password. 

È importante verificare l'identità degli utenti che vogliono accedere alle configurazioni di Azure Resource Manager e aggiornamento. Per verificare la propria identità, è necessario eseguire un'autenticazione aggiuntiva prima di consentire l'accesso.

Dopo aver abilitato le impostazioni predefinite di sicurezza nel tenant, gli utenti che accedono al portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure dovranno completare l'autenticazione aggiuntiva. Questo criterio si applica a tutti gli utenti che accedono a Azure Resource Manager, sia che si tratti di un amministratore o di un utente. 

Se l'utente non è registrato per Multi-Factor Authentication, l'utente dovrà eseguire la registrazione usando l'app Microsoft Authenticator per continuare. Non verrà fornito alcun periodo di registrazione di 14 giorni Multi-Factor Authentication.

Per i tenant di Exchange Online pre-2017 l'autenticazione moderna è disabilitata per impostazione predefinita. Per evitare la possibilità di un ciclo di accesso durante l'autenticazione tramite questi tenant, è necessario [abilitare l'autenticazione moderna](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> L'account di sincronizzazione Azure AD Connect viene escluso dalle impostazioni predefinite di sicurezza e non verrà richiesto di eseguire la registrazione o l'esecuzione di autenticazione a più fattori. Le organizzazioni non devono usare questo account per altri scopi.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Di seguito sono riportate alcune considerazioni aggiuntive relative alla distribuzione delle impostazioni predefinite di sicurezza per il tenant.

### <a name="authentication-methods"></a>Metodi di autenticazione

Le impostazioni predefinite della sicurezza consentono la registrazione e l'uso di Multi-Factor Authentication di Azure **usando solo l'app Microsoft Authenticator con le notifiche**. L'accesso condizionale consente di usare qualsiasi metodo di autenticazione che l'amministratore sceglie di abilitare.

|   | Impostazioni predefinite di sicurezza | Accesso condizionale |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |   | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |
| Password app |   | X * * |

\* * Le password dell'app sono disponibili solo nell'autenticazione a più fattori per utente con scenari di autenticazione legacy solo se abilitati dagli amministratori.

### <a name="conditional-access"></a>Accesso condizionale

È possibile usare l'accesso condizionale per configurare criteri simili alle impostazioni predefinite di sicurezza, ma con maggiore granularità, incluse le esclusioni degli utenti, che non sono disponibili nelle impostazioni predefinite di sicurezza. Se si usa l'accesso condizionale e i criteri di accesso condizionale sono abilitati nell'ambiente, le impostazioni predefinite di sicurezza non saranno disponibili. Se si dispone di una licenza che fornisce l'accesso condizionale, ma non sono abilitati criteri di accesso condizionale nell'ambiente in uso, è possibile usare le impostazioni predefinite di sicurezza finché non si abilitano i criteri di accesso condizionale. Ulteriori informazioni sulle licenze Azure AD sono disponibili nella pagina relativa ai [prezzi Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

![Messaggio di avviso indicante che è possibile avere impostazioni predefinite di sicurezza o l'accesso condizionale non entrambe](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Ecco alcune guide dettagliate su come usare l'accesso condizionale per configurare criteri equivalenti:

- [Richiedi autenticazione a più fattori per gli amministratori](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Richiedi autenticazione a più fattori per la gestione di Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blocca autenticazione legacy](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Richiedi autenticazione a più fattori per tutti gli utenti](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Richiedi registrazione](../identity-protection/howto-identity-protection-configure-mfa-policy.md) autenticazione a più fattori di Azure: richiede Azure ad Identity Protection

## <a name="enabling-security-defaults"></a>Abilitazione delle impostazioni predefinite di sicurezza

Per abilitare le impostazioni predefinite di sicurezza nella directory:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare a **Azure Active Directory** > **Proprietà**.
1. Selezionare **Gestisci impostazioni predefinite sicurezza**.
1. Impostare l' **opzione attiva impostazioni predefinite di sicurezza** su **Sì**.
1. Selezionare **Salva**.

## <a name="disabling-security-defaults"></a>Disabilitazione delle impostazioni predefinite di sicurezza

Le organizzazioni che scelgono di implementare criteri di accesso condizionale che sostituiscono le impostazioni predefinite di sicurezza devono disabilitare le impostazioni predefinite di sicurezza. 

![Messaggio di avviso disabilitare le impostazioni predefinite di sicurezza per abilitare l'accesso condizionale](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Per disabilitare le impostazioni predefinite di sicurezza nella directory:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare a **Azure Active Directory** > **Proprietà**.
1. Selezionare **Gestisci impostazioni predefinite sicurezza**.
1. Impostare l' **opzione attiva impostazioni predefinite di sicurezza** su **No**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri di accesso condizionale comuni](../conditional-access/concept-conditional-access-policy-common.md)
