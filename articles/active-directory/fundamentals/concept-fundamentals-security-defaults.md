---
title: Impostazioni predefinite di sicurezza di Azure Active DirectoryAzure Active Directory security defaults
description: Criteri predefiniti di sicurezza che consentono di proteggere le organizzazioni da attacchi comuni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248853"
---
# <a name="what-are-security-defaults"></a>Che cosa sono le impostazioni predefinite di sicurezza?

La gestione della sicurezza può essere difficile quando gli attacchi comuni relativi all'identità stanno diventando sempre più popolari. Questi attacchi includono password spray, replay, e phishing.

Le impostazioni predefinite per la sicurezza in Azure Active Directory (Azure AD) semplificano la sicurezza e proteggono l'organizzazione. Le impostazioni predefinite di sicurezza contengono impostazioni di sicurezza preconfigurate per gli attacchi comuni. 

Microsoft rende disponibili le impostazioni predefinite di sicurezza a tutti. L'obiettivo è garantire che tutte le organizzazioni abbiano un livello di sicurezza di base abilitato senza costi aggiuntivi. Attivare le impostazioni predefinite di sicurezza nel portale di Azure.You turn on security defaults in the Azure portal.

![Screenshot del portale di Azure con l'interruttore per abilitare le impostazioni predefinite di sicurezza](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Se il tenant è stato creato il 22 ottobre 2019 o dopo di esso, è possibile che si stia verificando il nuovo comportamento di sicurezza per impostazione predefinita e che nel tenant siano già abilitate le impostazioni predefinite di sicurezza. Nel tentativo di proteggere tutti i nostri utenti, le impostazioni predefinite di sicurezza vengono implementate in tutti i nuovi tenant creati.

Ulteriori dettagli sui motivi per cui le impostazioni predefinite di sicurezza vengono rese disponibili sono disponibili nel post di blog di Alex Weinert, [Introducing security defaults](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Registrazione unificata di Multi-Factor Authentication

Tutti gli utenti nel tenant devono registrarsi per l'autenticazione a più fattori (MFA) sotto forma di servizio Azure Multi-Factor Authentication. Gli utenti hanno 14 giorni di tempo per registrarsi per Multi-Factor Authentication utilizzando l'app Microsoft Authenticator. Dopo i 14 giorni, l'utente non sarà in grado di accedere fino al termine della registrazione di Multi-Factor Authentication.

Siamo consapevoli che alcuni utenti potrebbero essere fuori sede o non accedere durante i 14 giorni immediatamente dopo aver abilitato le impostazioni predefinite di sicurezza. Per garantire che ogni utente abbia tutto il tempo necessario per registrarsi per Multi-Factor Authentication, il periodo di 14 giorni è univoco per ogni utente. Il periodo di 14 giorni di un utente inizia dopo il primo accesso interattivo riuscito dopo l'abilitazione delle impostazioni predefinite di sicurezza.

## <a name="multi-factor-authentication-enforcement"></a>Applicazione dell'autenticazione a più fattori

### <a name="protecting-administrators"></a>Protezione degli amministratori

Gli utenti con accesso ad account con privilegi hanno aumentato l'accesso all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più avanzata di verifica dell'account per l'accesso. In Azure AD è possibile ottenere una verifica dell'account più avanzata richiedendo l'autenticazione a più fattori.

Al termine della registrazione con Multi-Factor Authentication, i cinque ruoli di amministratore di Azure AD seguenti dovranno eseguire un'autenticazione aggiuntiva ogni volta che accedono:

- Amministratore globale
- Amministratore di SharePoint
- Amministratore di Exchange
- Amministratore di accesso condizionale
- Amministratore della sicurezza
- Amministratore dell'helpdesk o amministratore della password
- Amministratore fatturazione
- Amministratore utenti
- Amministratore dell'autenticazione

### <a name="protecting-all-users"></a>Protezione di tutti gli utenti

Tendiamo a pensare che gli account amministratore sono gli unici che richiedono livelli aggiuntivi di autenticazione. Gli amministratori hanno un ampio accesso alle informazioni riservate e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Ma gli aggressori tendono a prendere di mira gli utenti finali. 

Dopo che questi utenti malintenzionati hanno ottenuto l'accesso, possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale. Possono anche scaricare l'intera directory per eseguire un attacco di phishing su tutta l'organizzazione. 

Un metodo comune per migliorare la protezione per tutti gli utenti consiste nel richiedere una forma più avanzata di verifica dell'account, ad esempio Multi-Factor Authentication, per tutti. Dopo che gli utenti hanno completato la registrazione di Multi-Factor Authentication, verrà richiesta un'ulteriore autenticazione quando necessario.

### <a name="blocking-legacy-authentication"></a>Blocco dell'autenticazione legacy

Per consentire agli utenti di accedere facilmente alle app cloud, Azure AD supporta un'ampia gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. *L'autenticazione legacy* è un termine che fa riferimento a una richiesta di autenticazione effettuata da:Legacy authentication is a term that refers to an authentication request made by:

- Client che non utilizzano l'autenticazione moderna (ad esempio, un client di Office 2010).
- Qualsiasi client che utilizza protocolli di posta precedenti, ad esempio IMAP, SMTP o POP3.

Oggi, la maggior parte dei tentativi di accesso compromettenti deriva dall'autenticazione legacy. L'autenticazione legacy non supporta l'autenticazione a più fattori. Anche se nella directory è abilitato un criterio di autenticazione a più fattori, un utente malintenzionato può eseguire l'autenticazione utilizzando un protocollo precedente e ignorare l'autenticazione a più fattori. 

Dopo aver abilitato le impostazioni predefinite di sicurezza nel tenant, tutte le richieste di autenticazione effettuate da un protocollo precedente verranno bloccate. Le impostazioni predefinite di protezione bloccano l'autenticazione di base di Exchange Active Sync.

> [!WARNING]
> Prima di abilitare le impostazioni predefinite di sicurezza, assicurarsi che gli amministratori non utilizzino protocolli di autenticazione meno recenti. Per ulteriori informazioni, vedere [Come spostarsi dall'autenticazione legacy](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Protezione delle azioni con privilegi

Le organizzazioni usano un'ampia gamma di servizi di Azure gestiti tramite l'API di Azure Resource Manager, tra cui:Organizations use a variety of Azure services managed through the Azure Resource Manager API, including:

- Portale di Azure 
- Azure PowerShell 
- Interfaccia della riga di comando di Azure

L'uso di Azure Resource Manager per gestire i servizi è un'azione con privilegi elevati. Azure Resource Manager può modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione. L'autenticazione a fattore singolo è vulnerabile a una varietà di attacchi come phishing e password spray. 

È importante verificare l'identità degli utenti che desiderano accedere alle configurazioni di aggiornamento e di Azure Resource Manager.It's important to verify the identity of users who want to access Azure Resource Manager and update configurations. Verificare la propria identità richiedendo l'autenticazione aggiuntiva prima di consentire l'accesso.

Dopo aver abilitato le impostazioni predefinite di sicurezza nel tenant, qualsiasi utente che accede al portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure dovrà completare l'autenticazione aggiuntiva. Questo criterio si applica a tutti gli utenti che accedono ad Azure Resource Manager, sia che si tratti di un amministratore o di un utente. 

Se l'utente non è registrato per Multi-Factor Authentication, l'utente dovrà registrarsi utilizzando l'app Microsoft Authenticator per procedere. Non verrà fornito alcun periodo di registrazione di 14 giorni di autenticazione a più fattori.

> [!NOTE]
> I tenant di Exchange Online pre-2017 hanno l'autenticazione moderna disabilitata per impostazione predefinita. Per evitare la possibilità di un ciclo di accesso durante l'autenticazione tramite questi tenant, è necessario [abilitare l'autenticazione moderna.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> L'account di sincronizzazione di Azure AD Connect è escluso dalle impostazioni predefinite di sicurezza e non verrà richiesto di registrarsi o eseguire l'autenticazione a più fattori. Le organizzazioni non devono utilizzare questo account per altri scopi.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Le considerazioni aggiuntive seguenti sono correlate alla distribuzione delle impostazioni predefinite di sicurezza per il tenant.

### <a name="authentication-methods"></a>Metodi di autenticazione

Le impostazioni predefinite di sicurezza consentono la registrazione e l'uso di Azure Multi-Factor Authentication **usando solo l'app Microsoft Authenticator tramite notifiche.** L'accesso condizionale consente l'utilizzo di qualsiasi metodo di autenticazione scelto dall'amministratore.

|   | Impostazioni predefinite di sicurezza | Accesso condizionale |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |   | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |
| Password dell'app |   | X |

Le password delle app sono disponibili solo nell'autenticazione a più fattori per utente con scenari di autenticazione legacy solo se abilitate dagli amministratori.

### <a name="conditional-access"></a>Accesso condizionale

È possibile utilizzare l'accesso condizionale per configurare criteri simili alle impostazioni predefinite di sicurezza, ma con una maggiore granularità, incluse le esclusioni utente, che non sono disponibili nelle impostazioni predefinite di sicurezza. Se si usa l'accesso condizionale e i criteri di accesso condizionale sono abilitati nell'ambiente, le impostazioni predefinite di sicurezza non saranno disponibili. Se si dispone di una licenza che fornisce l'accesso condizionale ma non sono abilitati criteri di accesso condizionale nell'ambiente, è possibile utilizzare le impostazioni predefinite di sicurezza fino a quando non si abilitano i criteri di accesso condizionale. Ulteriori informazioni sulle licenze di Azure AD sono disponibili nella pagina dei prezzi di [Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

![Messaggio di avviso che è possibile avere impostazioni predefinite di sicurezza o Accesso condizionale non sia](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Ecco le guide dettagliate su come utilizzare l'accesso condizionale per configurare criteri equivalenti:

- [Richiedere l'autenticazione a più fattori per gli amministratori](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Richiedere l'autenticazione a più fattori per la gestione di Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloccare l'autenticazione legacy](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Richiedere l'autenticazione a più fattori per tutti gli utenti](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Richiedere la registrazione di Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Richiede Azure AD Identity ProtectionRequire Azure MFA registration - Requires Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>Abilitazione delle impostazioni predefinite di sicurezza

Per abilitare le impostazioni predefinite di sicurezza nella directory:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare alle **proprietà**di **Azure Active Directory** >.
1. Selezionare **Gestisci impostazioni predefinite di sicurezza**.
1. Impostare l'opzione **Abilita impostazioni predefinite di sicurezza** su **Sì**.
1. Selezionare **Salva**.

## <a name="disabling-security-defaults"></a>Disabilitazione delle impostazioni predefinite di sicurezza

Le organizzazioni che scelgono di implementare criteri di accesso condizionale che sostituiscono le impostazioni predefinite di sicurezza devono disabilitare le impostazioni predefinite di sicurezza. 

![Messaggio di avviso che disabilita le impostazioni predefinite di sicurezza per abilitare l'accesso condizionaleWarning message disable security defaults to enable Conditional Access](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Per disabilitare le impostazioni predefinite di protezione nella directory:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare alle **proprietà**di **Azure Active Directory** >.
1. Selezionare **Gestisci impostazioni predefinite di sicurezza**.
1. Impostare l'interruttore **Abilita impostazioni predefinite di sicurezza** su **No**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](../conditional-access/concept-conditional-access-policy-common.md)
