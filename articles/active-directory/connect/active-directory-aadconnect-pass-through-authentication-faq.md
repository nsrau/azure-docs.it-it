---
title: 'Azure AD Connect: Autenticazione pass-through - Domande frequenti | Microsoft Docs'
description: Fornisce le risponde alle domande frequenti sull'autenticazione pass-through di Azure Active Directory.
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: 077a60949b5eed24cb9a1c56008a0073693f121e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticazione pass-through di Azure Active Directory: domande frequenti

Questo articolo risponde alle domande frequenti sull'autenticazione pass-through di Azure Active Directory (Azure AD). Visitare questa pagina regolarmente per eventuali aggiornamenti.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Quale metodo di accesso a Azure AD è preferibile usare tra autenticazione pass-through, sincronizzazione dell'hash delle password e Active Directory Federation Services (AD FS)?

Dipende dall'ambiente locale e dai requisiti dell'organizzazione. Esaminare l'articolo [Opzioni di accesso utente di Azure AD Connect](active-directory-aadconnect-user-signin.md) per un confronto tra i vari metodi di accesso di Azure AD.

## <a name="is-pass-through-authentication-a-free-feature"></a>L'autenticazione pass-through è una funzionalità gratuita?

L'autenticazione pass-through è una funzionalità gratuita. Per usare Azure AD non sono necessarie edizioni a pagamento.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L'autenticazione pass-through è disponibile nel [cloud Microsoft Azure Germania](http://www.microsoft.de/cloud-deutschland) e nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/)?

di serie L'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello mondiale.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>L'[accesso condizionale](../active-directory-conditional-access-azure-portal.md) funziona con l'autenticazione pass-through?

Sì. Tutte le funzionalità di accesso condizionale, incluso Azure Multi-Factor Authentication, funzionano con l'autenticazione pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L'autenticazione pass-through supporta "Alternate ID" come nome utente, al posto di "userPrincipalName"?

Sì. L'autenticazione pass-through supporta `Alternate ID` come nome utente quando è configurata in Azure AD Connect. Per altre informazioni, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Non tutte le applicazioni di Office 365 supportano `Alternate ID`. Fare riferimento all'informativa sul supporto contenuta nella documentazione dell'applicazione specifica.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>La sincronizzazione dell'hash delle password agisce da fallback per l'autenticazione pass-through?

di serie L'autenticazione pass-through _non_ esegue automaticamente il failover sulla sincronizzazione dell'hash delle password. Agisce da fallback solo in [scenari che l'autenticazione pass-through attualmente non supporta](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Per evitare errori di accesso dell'utente, è consigliabile configurare l'autenticazione pass-through per la [disponibilità elevata](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>È possibile installare un connettore del [proxy di applicazione di Azure AD](../active-directory-application-proxy-get-started.md) nello stesso server in cui è presente un agente di autenticazione pass-through?

Sì. Questa configurazione è supportata nelle versioni ridenominate dell'agente di autenticazione pass-through (versione 1.5.193.0 o successive).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quali versioni di Azure AD Connect e dell'agente di autenticazione pass-through sono necessarie?

Per il corretto funzionamento di questa funzionalità è necessario disporre della versione 1.1.486.0 di Azure AD Connect (o versioni successive) e della versione 1.5.58.0 dell'agente di autenticazione pass-through (o versioni successive). Installare tutto il software in server Windows Server 2012 R2 o versioni successive.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Cosa accade se la password dell'utente è scaduta e l'utente prova ad accedere usando l'autenticazione pass-through?

Se è stato configurato il [writeback delle password](../active-directory-passwords-update-your-own-password.md) per un utente specifico e se l'utente esegue l'accesso usando l'autenticazione pass-through, la password può essere modificata o reimpostata. Le password vengono riscritte in Active Directory locale come previsto.

Se non è stato configurato il writeback delle password per un utente specifico o se l'utente non dispone di una licenza di Azure AD valida, la password non può essere aggiornata nel cloud neanche se è scaduta. L'utente vedrà un messaggio simile a questo: "L'organizzazione non consente l'aggiornamento della password in questo sito. Aggiornare la password usando il metodo consigliato dall'organizzazione oppure contattare l'amministratore per chiedere assistenza". L'utente o l'amministratore deve reimpostare la password in Active Directory locale.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>In che modo l'autenticazione pass-through protegge dagli attacchi di forza bruta contro le password?

Per altre informazioni, leggere [Autenticazione pass-through di Azure Active Directory: blocco smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Cosa comunicano gli agenti di autenticazione pass-through sulle porte 80 e 443?

- Gli agenti di autenticazione inviano le richieste HTTP sulla porta 443 per tutte le operazioni di funzionalità.
- Gli agenti di autenticazione inviano richieste HTTP sulla porta 80 per scaricare gli elenchi di revoche di certificati (CRL) SSL.

     >[!NOTE]
     >In aggiornamenti recenti è stato ridotto il numero di porte necessarie per la funzionalità. Se si dispone di versioni precedenti di Azure AD Connect o dell'agente di autenticazione, tenere aperte anche le porte seguenti: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Gli agenti di autenticazione pass-through possono comunicare su un server proxy Web in uscita?

Sì. Se Web Proxy Auto-Discovery (WPAD) è abilitato nell'ambiente locale, gli agenti di autenticazione provano automaticamente a individuare e usare un server proxy Web della rete.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>È possibile installare due o più agenti di autenticazione pass-through nello stesso server?

No, è possibile installare solo un agente di autenticazione pass-through in un singolo server. Se si vuole configurare l'autenticazione pass-through per la disponibilità elevata, seguire le istruzioni fornite in [Autenticazione pass-through di Azure Active Directory - Avvio rapido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Come rimuovere un agente autenticazione pass-through?

Fino a quando un agente autenticazione pass-through è in esecuzione, rimane attivo e continuamente gestisce le richieste di accesso utente. Se si desidera disinstallare un agente di autenticazione, passare a **Pannello di controllo -> programmi -> programmi e funzionalità** e disinstallare sia il **agente autenticazione di Microsoft Azure AD Connect** e il  **Aggiornamento di Microsoft Azure AD Connect agente** programmi.

Se si seleziona il pannello di autenticazione pass-through nel [centro di amministrazione di Azure Active Directory](https://aad.portal.azure.com) dopo aver completato il passaggio precedente, si noterà l'agente di autenticazione che mostra come **inattivo**. Questo è il comportamento _previsto_. L'agente di autenticazione viene eliminato automaticamente nell'elenco dopo alcuni giorni.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Si usa già AD FS per eseguire l'accesso a Azure AD. Come si passa all'autenticazione pass-through?

Se AD FS è stato configurato come metodo di accesso usando la procedura guidata di Azure AD Connect, impostare il metodo di accesso utente su Autenticazione pass-through. Questa modifica abilita l'autenticazione pass-through nel tenant e converte _tutti_ i domini federati in domini gestiti. Tutte le successive richieste di accesso al tenant vengono gestite tramite l'autenticazione pass-through. Non esiste al momento un modo supportato all'interno di Azure AD Connect per usare una combinazione di AD FS e autenticazione pass-through in domini diversi.

Se AD FS è stato configurato come metodo di accesso _fuori_ dalla procedura guidata di Azure AD Connect, impostare il metodo di accesso utente su Autenticazione pass-through. È possibile apportare questa modifica dall'opzione **Non configurare**. Questa modifica abilita l'autenticazione pass-through nel tenant ma tutti i domini federati, per eseguire l'accesso, continueranno a usare AD FS. Usare PowerShell per convertire manualmente tutti questi domini federati o solo alcuni in domini gestiti. Dopo aver apportato questa modifica, tutte le richieste di accesso ai domini gestiti saranno gestite *soltanto* tramite l'autenticazione pass-through.

>[!IMPORTANT]
>L'autenticazione pass-through non gestisce l'accesso per gli utenti di Azure AD solo cloud.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>È possibile usare l'autenticazione pass-through in un ambiente Active Directory a più foreste?

Sì. Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste di Active Directory e se il routing del suffisso del nome è configurato correttamente.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quanti agenti di autenticazione pass-through è necessario installare?

L'installazione di più agenti di autenticazione pass-through garantisce la [disponibilità elevata](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability), ma non offre un bilanciamento del carico deterministico tra gli agenti.

Considerare il carico massimo e medio di richieste di accesso previsto nel tenant. Come benchmark, un singolo agente di autenticazione può gestire da 300 a 400 autenticazioni al secondo in un server standard con CPU a 4 core e 16 GB di RAM.

Per stimare il traffico di rete, usare le indicazioni seguenti relative al dimensionamento:
- Ogni richiesta ha una dimensione di payload di (0.5K + 1K * num_of_agents) byte. Ad esempio, i dati da Azure AD per l'agente di autenticazione. In questo caso, "num_of_agents" indica il numero di agenti di autenticazione registrati nel tenant.
- Ogni risposta ha una dimensione di payload di 1 KB. Ad esempio, i dati dall'agente di autenticazione ad Azure AD.

Per la maggior parte dei clienti, un totale di due o tre agenti di autenticazione è sufficiente ai fini della capacità e della disponibilità elevata. È consigliabile installare gli agenti di autenticazione vicino ai controller di dominio per migliorare la latenza di accesso.

>[!NOTE]
>Esiste un limite di sistema di 12 agenti di autenticazione per ogni tenant.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>È possibile installare il primo agente di autenticazione pass-through in un server diverso da quello che esegue Azure AD Connect?

No, questo scenario _non_ è supportato.

## <a name="how-can-i-disable-pass-through-authentication"></a>Come si disabilita l'autenticazione pass-through?

Eseguire nuovamente la procedura guidata Azure AD Connect e scegliere un metodo di accesso utente diverso da Autenticazione pass-through. Questa modifica disabilita l'autenticazione pass-through nel tenant e disinstalla l'agente di autenticazione nel server. Gli agenti di autenticazione degli altri server devono essere disinstallati manualmente.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Cosa accade quando si disinstalla un agente di autenticazione pass-through?

Se si disinstalla un agente di autenticazione pass-through in un server, il server non accetta più richieste di accesso. Per evitare l'interruzione della funzionalità di accesso utente al tenant, verificare che sia in esecuzione un altro agente di autenticazione prima di disinstallare l'agente di autenticazione pass-through.

## <a name="next-steps"></a>Passaggi successivi
- [Limitazioni correnti](active-directory-aadconnect-pass-through-authentication-current-limitations.md): apprendere quali sono gli scenari supportati.
- [Avvio rapido](active-directory-aadconnect-pass-through-authentication-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Blocco smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): apprendere come configurare la funzionalità di blocco smart nel tenant per proteggere gli account utente.
- [Approfondimento tecnico](active-directory-aadconnect-pass-through-authentication-how-it-works.md): comprendere come funziona l'autenticazione pass-through.
- [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): ottenere informazioni tecniche approfondite sulla funzionalità di autenticazione pass-through.
- [Accesso Single Sign-On facile di Azure AD](active-directory-aadconnect-sso.md): ottenere altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.

