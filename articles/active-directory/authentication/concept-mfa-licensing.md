---
title: Versioni dell'autenticazione a più fattori e piani di consumo di Azure-Azure Active Directory
description: Informazioni sul client Multi-Factor Authentication e sui diversi metodi e versioni disponibili.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61a2afad33aea7fa2a21135dca0995ad862a2258
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848664"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Come ottenere Azure Multi-Factor Authentication

Quando si tratta di proteggere gli account, la verifica in due passaggi dovrebbe essere lo standard in tutta l'organizzazione. Questa funzionalità è importante soprattutto per gli account dotati di accesso con privilegi alle risorse. Per questo motivo, Microsoft offre le funzionalità di base della verifica in due passaggi agli amministratori di Office 365 e di Active Directory (Azure AD) senza costi aggiuntivi. Per aggiornare le funzionalità per gli amministratori o estendere la verifica in due passaggi agli altri utenti, è possibile acquistare Azure Multi-Factor Authentication in molti modi.

> [!IMPORTANT]
> Questo articolo è una guida che spiega i diversi modi di acquistare Azure Multi-Factor Authentication. Per dettagli specifici sui prezzi e sulla fatturazione, è consigliabile vedere sempre la [pagina dei prezzi di Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication

La tabella seguente descrive le differenze tra le versioni di autenticazione a più fattori:

| Versione | Description |
| --- | --- |
| Opzione gratuita | I clienti che utilizzano i vantaggi gratuiti di Azure AD possono utilizzare le [impostazioni predefinite di sicurezza](../conditional-access/concept-conditional-access-security-defaults.md) per abilitare l'autenticazione a più fattori nel proprio ambiente. |
| Autenticazione a più fattori per Office 365 | Questa versione è gestita dal portale di Office 365 o Microsoft 365. Gli amministratori possono [proteggere le risorse di Office 365 con la verifica in due passaggi](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Questa versione è inclusa in una sottoscrizione di Office 365. |
| Multi-Factor Authentication per amministratori di Azure Active Directory | Gli utenti a cui è assegnato il ruolo di amministratori globali di tenant di Azure AD possono abilitare la verifica in due passaggi senza costi aggiuntivi. |
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication, definito spesso la versione "completa", offre la più ampia gamma di funzionalità. Offre opzioni di configurazione aggiuntive tramite il [portale di Azure](https://portal.azure.com), segnalazione avanzata e il supporto per una gamma di applicazioni locali e cloud. Azure Multi-Factor Authentication è una funzionalità di [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) e [Microsoft 365 business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> I nuovi clienti non possono più acquistare Azure Multi-Factor Authentication come offerta autonoma valida dal 1 ° settembre 2018. L'autenticazione a più fattori continuerà a essere disponibile come funzionalità in Azure AD Premium o Microsoft 365 Business licenze.

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni

La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.

> [!NOTE]
> Questa tabella di confronto descrive le funzionalità incluse in ogni versione di Multi-Factor Authentication. Se si ha il servizio Azure Multi-Factor Authentication completo, alcune funzionalità potrebbero non essere disponibili a seconda che si usi [MFA nel cloud o MFA in locale](concept-mfa-whichversion.md).
>

| Funzionalità | Autenticazione a più fattori per Office 365 | Multi-Factor Authentication per amministratori di Azure Active Directory | Azure Multi-Factor Authentication | Impostazioni predefinite di sicurezza | 
| --- |:---:|:---:|:---:|:---:|
| Protezione degli account amministratore di Azure Active Directory con MFA |● |● (solo per l'account amministratore globale di Azure Active Directory) |● |● |
| App per dispositivi mobili come secondo fattore |● |● |● |● |
| Chiamata telefonica come secondo fattore |● |● |● |   |
| SMS come secondo fattore |● |● |● |   |
| Password di app per i client che non supportano MFA |● |● |● |   |
| Controllo amministrativo sui metodi di verifica |● |● |● |   |
| Proteggere gli account non amministratore con MFA |● | |● |● |
| Modalità PIN | | |● |   |
| Avviso sulle frodi | | |● |   |
| Report MFA | | |● |   |
| Bypass monouso | | |● |   |
| Messaggi di saluto personalizzati per le telefonate | | |● |   |
| ID chiamante personalizzato per le telefonate | | |● |   |
| IP attendibili | | |● |   |
| Memorizzazione di MFA per dispositivi attendibili |● |● |● |   |
| MFA per applicazioni locali | | |● |   |

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni per le telefonate non saranno disponibili per gli utenti di multi-factor authentication e SSPR in un tenant gratuito/di valutazione Azure AD. Questa modifica non ha alcun effetto sui messaggi SMS. La telefonata continuerà a essere disponibile per gli utenti in tenant Azure AD a pagamento. Questa modifica ha effetto solo sui tenant di Azure AD gratuiti/di valutazione.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Come attivare Azure Multi-Factor Authentication per amministratori di Azure AD

Gli utenti a cui è assegnato il ruolo di amministratori globali di tenant di Azure AD possono abilitare la verifica in due passaggi per i loro account di amministratore globale di Azure AD senza costi aggiuntivi. Se si usa un Account Microsoft, è possibile registrarsi per l'autenticazione a più fattori usando le istruzioni disponibili nell'articolo del supporto tecnico sugli account Microsoft [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Se non si usa un account Microsoft, attivare l'autenticazione a più fattori per gli amministratori globali seguendo le istruzioni disponibili nell'articolo [Come richiedere la verifica in due passaggi per un utente o un gruppo](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Come acquistare Azure Multi-Factor Authentication

Acquistare le licenze che includono Azure Multi-Factor Authentication, ad esempio Azure Active Directory Premium, o un bundle di licenze che include Azure AD Premium o l'accesso condizionale e assegnarle agli utenti in Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licenze basate sul consumo

Le licenze basate sul consumo non sono più disponibili per i nuovi clienti a partire dal 1 ° settembre 2018.

A partire dal 1 ° settembre 2018 non è più possibile creare nuovi provider di autenticazione. I provider di autenticazione esistenti potranno continuare a essere usati e aggiornato. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:

1. **Per utente abilitato**: per le aziende che vogliono abilitare la verifica in due passaggi per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione. La fatturazione per utente si basa sul numero di utenti abilitati per MFA nel tenant di Azure AD e in Azure MFA Server. Se gli utenti sono abilitati per MFA sia in Azure AD che in Azure MFA Server e la sincronizzazione dei domini (Azure AD Connect) è abilitata, viene considerato il set di utenti più numeroso. Se la sincronizzazione dei domini non è abilitata, viene calcolata la somma di tutti gli utenti abilitati per MFA in Azure AD e in Azure MFA Server. La fatturazione viene ripartita e segnalata ogni giorno al sistema commerciale.

   > [!NOTE]
   > Esempio di fatturazione 1: oggi si hanno 5.000 utenti abilitati per l'autenticazione a più fattori. Il sistema MFA divide il numero per 31 e segnala 161,29 utenti per quel giorno. Il giorno dopo si abilitano altri 15 utenti, quindi il sistema MFA segnala 161,77 utenti per quel giorno. Alla fine del ciclo di fatturazione, il numero totale di utenti fatturati sulla base della sottoscrizione di Azure arriva a circa 5.000.
   >
   > Esempio di fatturazione 2: alcuni utenti hanno le licenze, altri invece no, quindi è disponibile un provider Azure MFA per utente per compensare la differenza. Nel tenant esistono 4.500 licenze Enterprise Mobility + Security, ma 5.000 utenti abilitati per MFA. Alla sottoscrizione di Azure vengono fatturati 500 utenti, ripartiti e segnalati ogni giorno come 16,13 utenti.
   >

1. **Per autenticazione**: per le aziende che vogliono abilitare la verifica in due passaggi per un gruppo esteso di utenti che non richiedono regolarmente l'autenticazione. La fatturazione si basa sul numero di richieste di verifica in due passaggi, indipendentemente dal fatto che tali verifiche riescano o vengano negate. Questa fatturazione viene visualizzata nel rendiconto sull'utilizzo di Azure in pacchetti di 10 autenticazioni e viene segnalata ogni giorno.

   > [!NOTE]
   > Esempio di fatturazione 3: oggi il servizio Azure MFA ha ricevuto 3.105 richieste di verifica in due passaggi. Alla sottoscrizione di Azure vengono fatturati 310,5 pacchetti di autenticazione.
   >

È importante notare che, anche con licenze, continua a essere fatturata la configurazione in base al consumo. Se si configura un provider di autenticazione a più fattori di Azure per autenticazione, vengono addebitate tutte le richieste di verifica in due passaggi, anche quelle eseguite dagli utenti che hanno licenze. Se si configura un provider Azure MFA per utente in un dominio non collegato al tenant di Azure AD, viene fatturato ogni utente abilitato anche se gli utenti hanno licenze in Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui prezzi, vedere [Multi-Factor Authentication Prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
