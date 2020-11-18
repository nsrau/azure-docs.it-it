---
title: Impostazioni predefinite per la sicurezza Azure Active Directory
description: Criteri di sicurezza predefiniti che consentono di proteggere le organizzazioni dagli attacchi comuni in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c26cbf55c1e3883605d4c65659511af20cf02c7f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836768"
---
# <a name="what-are-security-defaults"></a>Cosa sono le impostazioni predefinite per la sicurezza?

La gestione della sicurezza può essere difficile grazie a attacchi comuni correlati all'identità, ad esempio spray per la password, riproduzione e phishing che diventano sempre più diffusi. Le impostazioni predefinite per la sicurezza consentono di proteggere l'organizzazione da questi attacchi con impostazioni di sicurezza preconfigurate:

- Richiedere a tutti gli utenti di registrarsi per Azure AD Multi-Factor Authentication.
- Richiedendo agli amministratori di eseguire l'autenticazione a più fattori.
- Bloccando i protocolli di autenticazione legacy.
- Richiedendo agli utenti di eseguire l'autenticazione a più fattori quando necessario.
- Proteggendo attività con privilegi come l'accesso al portale di Azure.

![Screenshot del portale di Azure con le impostazioni predefinite per la sicurezza abilitate](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Per altre informazioni sui motivi per cui vengono rese disponibili le impostazioni predefinite per la sicurezza, vedere il post di blog di Alex Weinert [Introduzione alle impostazioni predefinite per la sicurezza](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Disponibilità

Microsoft sta rendendo disponibili le impostazioni predefinite per la sicurezza a tutti gli utenti. L'obiettivo consiste nel garantire che tutte le organizzazioni dispongano di un livello di sicurezza base abilitato senza costi aggiuntivi. Si attivano le impostazioni predefinite per la sicurezza nel portale di Azure. Se il tenant è stato creato a partire dal 22 ottobre 2019, è possibile che le impostazioni predefinite per la sicurezza siano già abilitate nel tenant. Per garantire la protezione di tutti gli utenti, le impostazioni predefinite per la sicurezza vengono implementate per tutti i nuovi tenant creati.

### <a name="whos-it-for"></a>A chi sono destinate le impostazioni predefinite per la sicurezza?

- Alle organizzazioni che vogliono aumentare il comportamento di sicurezza, ma non sanno come o dove iniziare.
- Alle organizzazioni che usano il livello gratuito della licenza Azure Active Directory.

### <a name="who-should-use-conditional-access"></a>A chi è rivolto l'accesso condizionale?

- Le impostazioni predefinite per la sicurezza probabilmente non sono adatte alle organizzazioni che usano criteri di accesso condizionale per mettere insieme i segnali, prendere decisioni e applicare criteri aziendali. 
- Le impostazioni predefinite per la sicurezza probabilmente non sono adatte alle organizzazioni con licenze Azure Active Directory Premium.
- Se l'organizzazione ha requisiti di sicurezza complessi, è consigliabile prendere in considerazione l'accesso condizionale.

## <a name="policies-enforced"></a>Criteri applicati

### <a name="unified-multi-factor-authentication-registration"></a>Registrazione per l'autenticazione a più fattori unificata

Tutti gli utenti nel tenant devono registrarsi per l'autenticazione a più fattori sotto forma di Azure AD Multi-Factor Authentication. Gli utenti hanno 14 giorni per la registrazione Azure AD Multi-Factor Authentication usando l'app Microsoft Authenticator. Dopo i 14 giorni, l'utente non sarà in grado di accedere fino al completamento della registrazione. Il periodo di 14 giorni di un utente inizia dopo il primo accesso interattivo completato dopo l'abilitazione delle impostazioni predefinite per la sicurezza.

### <a name="protecting-administrators"></a>Protezione degli amministratori

Gli utenti con accesso con privilegi hanno un accesso maggiore all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma di verifica degli account più avanzata per l'accesso. In Azure AD è possibile richiedere l'autenticazione a più fattori per ottenere una verifica degli account più avanzata.

Al termine della registrazione con Azure AD Multi-Factor Authentication, verranno richiesti i nove ruoli di amministratore Azure AD seguenti per eseguire un'autenticazione aggiuntiva ogni volta che accedono:

- Amministratore globale
- Amministratore di SharePoint
- Amministratore di Exchange
- Amministratore di accesso condizionale
- Amministratore della sicurezza
- Amministratore dell'help desk
- Amministratore fatturazione
- Amministratore utenti
- Amministratore dell'autenticazione

### <a name="protecting-all-users"></a>Protezione di tutti gli utenti

Si tende a pensare che gli account amministratore siano gli unici account che richiedono livelli aggiuntivi di autenticazione. Gli amministratori hanno accesso esteso alle informazioni sensibili e possono apportare modifiche alle impostazioni a livello di sottoscrizione. Ma gli utenti malintenzionati spesso hanno come obiettivo gli utenti finali. 

Una volta che gli utenti malintenzionati ottengono l'accesso, possono richiedere l'accesso alle informazioni con privilegi per conto del titolare dell'account originale. Possono anche scaricare l'intera directory per eseguire un attacco di phishing all'intera organizzazione. 

Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account, come Multi-Factor Authentication, per tutti. Al termine della registrazione a Multi-Factor Authentication, gli utenti riceveranno la richiesta di autenticazione aggiuntiva ogni volta che è necessario. Questa funzionalità protegge tutte le applicazioni registrate con Azure AD incluse le applicazioni SaaS.

### <a name="blocking-legacy-authentication"></a>Bloccare l'autenticazione legacy

Per consentire agli utenti di accedere facilmente alle app cloud, Azure AD supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. Con il termine *autenticazione legacy* si fa riferimento a una richiesta di autenticazione effettuata da:

- Client che non usano l'autenticazione moderna (ad esempio, un client Office 2010).
- Qualsiasi client che usi protocolli di posta elettronica precedenti come IMAP, SMTP o POP3.

Oggi la maggior parte dei tentativi di accesso che compromettono la sicurezza derivano dall'autenticazione legacy. L'autenticazione legacy non supporta Multi-Factor Authentication. Anche se nella directory è abilitato un criterio di Multi-Factor Authentication, un utente malintenzionato può eseguire l'autenticazione usando un protocollo precedente e ignorare Multi-Factor Authentication. 

Una volta abilitate le impostazioni predefinite per la sicurezza nel tenant, tutte le richieste di autenticazione effettuate da un protocollo precedente verranno bloccate. Le impostazioni predefinite per la sicurezza bloccano l'autenticazione di base Exchange Active Sync.

> [!WARNING]
> Prima di abilitare le impostazioni predefinite per la sicurezza, assicurarsi che gli amministratori non usino protocolli di autenticazione precedenti. Per altre informazioni, vedere [Come passare dall'autenticazione legacy a un'altra autenticazione](concept-fundamentals-block-legacy-authentication.md).

- [Come configurare un dispositivo multifunzione o un'applicazione per l'invio di messaggi di posta elettronica tramite Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Protezione delle azioni con privilegi

Le organizzazioni usano un'ampia gamma di servizi di Azure gestiti tramite l'API Azure Resource Manager, come:

- Portale di Azure 
- Azure PowerShell 
- Interfaccia della riga di comando di Azure

L'uso di Azure Resource Manager per gestire i servizi è un'azione con privilegi elevati. Azure Resource Manager può modificare le configurazioni a livello di tenant, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione. L'autenticazione a fattore singolo è vulnerabile a numerosi attacchi, come phishing e password spraying. 

È importante verificare l'identità degli utenti che vogliono accedere alle configurazioni di Azure Resource Manager e di aggiornamento. Per verificarne l'identità, è necessario eseguire un'autenticazione aggiuntiva prima di consentire l'accesso.

Dopo aver abilitato le impostazioni predefinite per la sicurezza nel tenant, gli utenti che accedono al portale di Azure, ad Azure PowerShell o all'interfaccia della riga di comando di Azure dovranno completare l'autenticazione aggiuntiva. Questo criterio si applica a tutti gli utenti che accedono ad Azure Resource Manager, che si tratti di un amministratore o di un utente. 

> [!NOTE]
> Per i tenant di Exchange Online precedenti al 2017, l'autenticazione moderna è disabilitata per impostazione predefinita. Per evitare la possibilità di un ciclo di accesso durante l'autenticazione tramite questi tenant, è necessario [abilitare l'autenticazione moderna](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> L'account di sincronizzazione Azure AD Connect viene escluso dalle impostazioni predefinite per la sicurezza e non verrà richiesto di eseguire la registrazione o l'autenticazione a più fattori. Le organizzazioni non devono usare questo account per altri scopi.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Di seguito sono riportate alcune considerazioni aggiuntive relative alla distribuzione delle impostazioni predefinite per la sicurezza.

### <a name="authentication-methods"></a>Metodi di autenticazione

Queste impostazioni predefinite di sicurezza gratuite consentono la registrazione e l'uso di Azure AD Multi-Factor Authentication **usando solo l'app Microsoft Authenticator usando le notifiche**. L'accesso condizionale consente di usare qualsiasi metodo di autenticazione che l'amministratore sceglie di abilitare.

| Metodo | Impostazioni predefinite di sicurezza | Accesso condizionale |
| --- | --- | --- |
| Notifica tramite app per dispositivi mobili | X | X |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware | X** | X |
| SMS al telefono |   | X |
| Chiamata al telefono |   | X |
| Password dell'app |   | X * * _ |

- _ * Gli utenti possono usare i codici di verifica dell'app Microsoft Authenticator ma possono registrarsi solo con l'opzione di notifica.
- * * _ Le password dell'app sono disponibili solo nell'autenticazione a più fattori per utente con scenari di autenticazione legacy solo se abilitati dagli amministratori.

### <a name="disabled-mfa-status"></a>Stato MFA disabilitato

Se l'organizzazione è un utente precedente di Azure AD Multi-Factor Authentication basato su utente, non è necessario che gli utenti in uno stato _ *abilitato** o **applicato** siano visualizzati nella pagina stato di autenticazione a più fattori. **Disabled** è lo stato appropriato per gli utenti che usano le impostazioni predefinite di sicurezza o l'accesso condizionale basato Azure ad multi-factor authentication.

### <a name="conditional-access"></a>Accesso condizionale

È possibile usare l'accesso condizionale per configurare criteri simili alle impostazioni predefinite per la sicurezza ma con maggiore granularità, incluse le esclusioni di utenti, che non sono disponibili nelle impostazioni predefinite per la sicurezza. Se si usa l'accesso condizionale e i criteri di accesso condizionale sono abilitati nell'ambiente, le impostazioni predefinite per la sicurezza non saranno disponibili. Se si dispone di una licenza che fornisce l'accesso condizionale, ma i criteri di accesso condizionale non sono abilitati nell'ambiente in uso, l'utente è invitato a usare le impostazioni predefinite per la sicurezza finché non si abilitano i criteri di accesso condizionale. Per altre informazioni sulle licenze di Azure AD, vedere la [pagina dei prezzi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Messaggio di avviso indicante che è possibile disporre di impostazioni predefinite per la sicurezza o dell'accesso condizionale, ma non di entrambi](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Di seguito sono riportate istruzioni dettagliate su come usare l'accesso condizionale per configurare criteri equivalenti per i criteri abilitati dalle impostazioni predefinite per la sicurezza:

- [Richiedere l'autenticazione a più fattori per gli amministratori](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Richiedere l'autenticazione a più fattori per la gestione di Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloccare l'autenticazione legacy](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Richiedere l'autenticazione a più fattori per tutti gli utenti](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Richiedi Azure ad registrazione](../identity-protection/howto-identity-protection-configure-mfa-policy.md) con autenticazione a più fattori: richiede Azure ad Identity Protection parte di Azure ad Premium P2.

## <a name="enabling-security-defaults"></a>Abilitazione delle impostazioni predefinite per la sicurezza

Per abilitare le impostazioni predefinite per la sicurezza nella directory:

1. Accedere al  [portale di Azure](https://portal.azure.com)  come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare ad **Azure Active Directory** > **Proprietà**.
1. Selezionare **Gestisci le impostazioni predefinite per la sicurezza**.
1. Impostare **Abilita le impostazioni predefinite per la sicurezza** su **Sì**.
1. Selezionare **Salva**.

## <a name="disabling-security-defaults"></a>Disabilitazione delle impostazioni predefinite per la sicurezza

Le organizzazioni che scelgono di implementare i criteri di accesso condizionale che sostituiscono le impostazioni predefinite per la sicurezza devono disabilitare le impostazioni predefinite per la sicurezza. 

![Messaggio di avviso indicante che le impostazioni predefinite per la sicurezza devono essere disabilitate per abilitare l'accesso condizionale](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Per disabilitare le impostazioni predefinite per la sicurezza nella directory:

1. Accedere al  [portale di Azure](https://portal.azure.com)  come amministratore della sicurezza, amministratore di accesso condizionale o amministratore globale.
1. Passare ad **Azure Active Directory** > **Proprietà**.
1. Selezionare **Gestisci le impostazioni predefinite per la sicurezza**.
1. Impostare **Abilita le impostazioni predefinite per la sicurezza** su **No**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](../conditional-access/concept-conditional-access-policy-common.md)