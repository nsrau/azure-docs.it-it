---
title: 'Azure AD Connect: Autenticazione pass-through - Domande frequenti | Microsoft Docs'
description: Questo articolo risponde ad alcune domande frequenti relative all'autenticazione pass-through di Azure Active Directory.
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: b8a08eb8fd036ad07ee6ce4cf624e8b5bc4c3ddc
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---

# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticazione pass-through di Azure Active Directory: domande frequenti

Questo articolo risponde ad alcune domande frequenti relative all'autenticazione pass-through di Azure Active Directory (Azure AD). Visitare questa pagina regolarmente per nuovi contenuti.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Quale dei metodi di accesso di Azure AD, tra l'autenticazione pass-through, la sincronizzazione dell'hash delle password e Active Directory Federation Services (AD FS), è preferibile usare?

Dipende dall'ambiente locale e dai requisiti dell'organizzazione. Esaminare questo articolo per un [confronto tra i vari metodi di accesso di AD Azure](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>L'autenticazione pass-through è una funzionalità gratuita?

L'autenticazione pass-through è una funzionalità gratuita e non serve acquistare edizioni a pagamento di Azure AD per usarla. Rimane gratuita quando la funzionalità raggiunge la disponibilità generale.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L'autenticazione pass-through è disponibile in [Microsoft Cloud per la Germania](http://www.microsoft.de/cloud-deutschland) e [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?

No, l'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello mondiale.

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>L'[accesso condizionale](../active-directory-conditional-access.md) funziona con l'autenticazione pass-through?

Sì, tutte le funzionalità di accesso condizionale, incluso Azure Multi-Factor Authentication, funzionano con l'autenticazione pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L'autenticazione pass-through supporta "Alternate ID" come nome utente, al posto di "userPrincipalName"?

Sì. L'autenticazione pass-through supporta `Alternate ID` come nome utente quando è configurata in Azure AD Connect come mostrato [qui](active-directory-aadconnect-get-started-custom.md). Non tutte le applicazioni di Office 365 supportano `Alternate ID`. Fare riferimento alla documentazione dell'applicazione specifica per sapere se è supportato.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>La sincronizzazione dell'hash delle password agisce da fallback per l'autenticazione pass-through?

No, la sincronizzazione dell'hash delle password non è un fallback generico per l'autenticazione pass-through. Agisce da fallback solo in [scenari che l'autenticazione pass-through attualmente non supporta](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Per evitare errori di accesso dell'utente, è consigliabile configurare l'autenticazione pass-through per la [disponibilità elevata](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>È possibile installare un connettore del [proxy di applicazione di Azure AD](../active-directory-application-proxy-get-started.md) nello stesso server in cui è presente un agente di autenticazione pass-through?

Sì, questa configurazione è supportata con le versioni dell'agente di autenticazione pass-through ridenominate (versioni 1.5.193.0 o successive).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quali versioni di Azure AD Connect e dell'agente di autenticazione pass-through sono necessarie?

Per usare questa funzionalità, è necessario disporre della versione 1.1.486.0 o versioni successive per Azure AD Connect e della versione 1.5.58.0 o versioni successive per l'agente di autenticazione pass-through. Tutto il software deve essere installato in server con Windows Server 2012 R2 o versioni successive.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>Cosa accade se la password dell'utente è scaduta e l'utente tenta di accedere usando l'autenticazione pass-through?

Se è stato configurato il [writeback delle password](../active-directory-passwords-update-your-own-password.md) per un utente specifico e se l'utente esegue l'accesso usando l'autenticazione pass-through, questi può modificare o reimpostare la password. Le password vengono riscritte in Active Directory locale come previsto.

Se invece il writeback delle password non è configurato o se l'utente non dispone di una licenza di Azure AD valida, l'utente non può aggiornare la propria password nel cloud, neanche se la password è scaduta. L'utente vedrà un messaggio simile a questo: "L'organizzazione non consente l'aggiornamento della password in questo sito. Aggiornare la password usando il metodo consigliato dall'organizzazione oppure contattare l'amministratore, se è necessaria assistenza". L'utente o l'amministratore deve reimpostare la password in Active Directory locale.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>La modalità autenticazione pass-through è una protezione dagli attacchi di forza bruta alle password?

Per maggiori informazioni, leggere [questo articolo](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Cosa comunicano gli agenti di autenticazione pass-through sulle porte 80 e 443?

- Gli agenti di autenticazione inviano le richieste HTTPS sulla porta 443 per tutte le operazioni di funzionalità, ad esempio l'abilitazione della funzionalità, l'elaborazione di tutte le richieste di accesso degli utenti e altro ancora.
- Gli agenti di autenticazione inviano le richieste HTTP sulla porta 80 per scaricare gli elenchi di revoche di certificati SSL.

     >[!NOTE]
     >Con gli ultimi aggiornamenti è stato ridotto il numero di porte necessarie agli agenti di autenticazione per comunicare con Azure AD. Se si eseguono versioni precedenti di Azure AD Connect e/o di agenti di autenticazione autonomi, è consigliabile continuare a mantenere aperte queste porte aggiuntive (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120).

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Gli agenti di autenticazione pass-through possono comunicare su un server proxy Web in uscita?

Sì. Se il servizio WPAD (Web Proxy Auto-Discovery) è abilitato nell'ambiente locale, gli agenti di autenticazione tentano automaticamente di individuare e usare un server proxy Web nella rete.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>È possibile installare due o più agenti di autenticazione pass-through nello stesso server?

No, è possibile installare solo un agente di autenticazione pass-through in un singolo server. Se si desidera configurare l'autenticazione pass-through per la disponibilità elevata, seguire invece le istruzioni in questo [articolo](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability).

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Si usa già Active Directory Federation Services (ADFS) per l'accesso ad AD Azure. Come si passa all'autenticazione pass-through?

Se ADFS è stato configurato come metodo di accesso usando la procedura guidata Azure AD Connect, cambiare il metodo di accesso utente nell'autenticazione pass-through. Questa modifica abilita l'autenticazione pass-through nel tenant e converte _tutti_ i domini federati in domini gestiti. Tutte le successive richieste di accesso nel tenant verranno gestite mediante l'autenticazione pass-through. Non esiste al momento un modo supportato all'interno di Azure AD Connect per usare una combinazione di AD FS e autenticazione pass-through in domini diversi.

Se ADFS è stato configurato come metodo di accesso _esternamente_ alla procedura guidata Azure AD Connect, cambiare il metodo di accesso utente nell'autenticazione pass-through (dall'opzione "Non configurare"). Questa modifica abilita l'autenticazione pass-through nel tenant. Tutti i domini federati continuano tuttavia a usare ADFS per l'accesso. Usare PowerShell per convertire manualmente alcuni o tutti questi domini federati in domini gestiti. Tutte le richieste di accesso nei domini gestiti (_solo_) verranno quindi gestite mediante l'autenticazione pass-through.

>[!IMPORTANT]
>L'autenticazione pass-through non gestisce l'accesso per gli utenti di Active Directory solo cloud.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>È possibile usare l'autenticazione pass-through in un ambiente Active Directory a più foreste?

Sì. Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste AD e se il routing del suffisso del nome è configurato correttamente.

## <a name="do-pass-through-authentication-agents-provide-load-balancing-capability"></a>Gli agenti di autenticazione pass-through includono la funzionalità di bilanciamento del carico?

No, l'installazione di più agenti di autenticazione pass-through assicura la [disponibilità elevata](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability) ma non il bilanciamento del carico. Uno o due degli agenti di autenticazione possono finire per gestire la maggior parte delle richieste di accesso.

Le richieste di convalida delle password che gli agenti di autenticazione devono gestire sono leggere. I carichi medio e di punta per la maggior parte dei clienti sono pertanto facilmente gestibili con due o tre agenti di autenticazione in totale.

È consigliabile installare gli agenti di autenticazione vicino al controller di dominio per migliorare la latenza di accesso.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>È possibile installare il primo agente di autenticazione pass-through in un server diverso da quello che esegue Azure AD Connect?

No, questo scenario _non_ è supportato.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Quanti agenti di autenticazione pass-through è consigliabile installare?

È consigliabile:

- Installare due o tre agenti di autenticazione in totale. Saranno sufficienti per la maggior parte dei clienti.
- Installare gli agenti di autenticazione sui controller di dominio (o il più vicino possibile) per migliorare la latenza di accesso.
- Assicurarsi che i server (in cui sono installati gli agenti di autenticazione) vengano aggiunti alla stessa foresta Active Directory degli utenti le cui password devono essere convalidate.

Si noti che esiste un limite di sistema di 12 agenti di autenticazione per ogni tenant.

## <a name="how-can-i-disable-pass-through-authentication"></a>Come si disabilita l'autenticazione pass-through?

Eseguire nuovamente la procedura guidata Azure AD Connect e scegliere un metodo di accesso utente diverso da Autenticazione pass-through. Questa modifica disabilita l'autenticazione pass-through nel tenant e disinstalla l'agente di autenticazione nel server. Gli agenti di autenticazione negli altri server devono essere disinstallati manualmente.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Cosa accade quando si disinstalla un agente di autenticazione pass-through?

Se si disinstalla un agente di autenticazione pass-through in un server, il server non accetta più richieste di accesso. Prima di eseguire questa operazione, verificare che un altro agente di autenticazione sia in esecuzione per evitare di interrompere l'accesso utente nel tenant.

## <a name="next-steps"></a>Passaggi successivi
- [**Current limitations**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) (Limitazioni correnti): questa funzionalità è attualmente in anteprima. Informazioni su quali scenari sono supportati e quali non lo sono.
- [**Guida introduttiva**](active-directory-aadconnect-pass-through-authentication-quick-start.md): informazioni per configurare e usare l'autenticazione pass-through di Azure AD.
- [**Approfondimento tecnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Seamless Single Sign-On di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

