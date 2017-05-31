---
title: 'Azure AD Connect: Risolvere i problemi di autenticazione pass-through | Microsoft Docs'
description: Questo articolo descrive come risolvere i problemi di autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Risolvere i problemi di autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Come risolvere i problemi di autenticazione pass-through di Azure Active Directory

Questo articolo contiene informazioni per la risoluzione dei problemi comuni che si riscontrano durante l'installazione, la registrazione o la disinstallazione dei connettori di autenticazione pass-through (tramite Azure AD Connect o autonomi) e durante l'abilitazione e il funzionamento della funzione di autenticazione pass-through di Azure Active Directory (Azure AD) del tenant.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemi durante l'installazione dei connettori (tramite Azure AD Connect o autonomi)

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Un connettore del proxy di applicazione di Azure AD esiste già

Un connettore di autenticazione pass-through non può essere installato nello stesso server di un connettore [proxy dell'applicazione AD Azure](../../active-directory/active-directory-application-proxy-get-started.md). È necessario installare il connettore di autenticazione pass-through in un server diverso.

### <a name="an-unexpected-error-occured"></a>Si è verificato un errore imprevisto

[Raccogliere i log connettore](#collecting-pass-through-authentication-connector-logs) dal server e contattare il supporto Microsoft per risolvere il problema.

## <a name="issues-during-registration-of-connectors"></a>Problemi durante la registrazione dei connettori

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>La registrazione del connettore non è riuscita a causa di porte bloccate

Verificare che il server in cui è installato il connettore possa comunicare con gli URL del nostro servizio e le porte elencate [qui](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>La registrazione del connettore non è riuscita a causa di errori di autorizzazione dell'account o del token

Assicurarsi di usare un account di amministratore globale solo cloud per tutte le operazioni di installazione e registrazione del connettore autonomo o di Azure AD Connect. Esiste un problema noto con gli account amministratore globale con autenticazione MFA abilitata: disattivare l'autenticazione MFA temporaneamente (solo per completare le operazioni) come soluzione alternativa.

### <a name="an-unexpected-error-occurred"></a>Si è verificato un errore imprevisto

[Raccogliere i log connettore](#collecting-pass-through-authentication-connector-logs) dal server e contattare il supporto Microsoft per risolvere il problema.

## <a name="issues-during-uninstallation-of-connectors"></a>Problemi durante la disinstallazione dei connettori

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Messaggio di avviso quando si disinstalla Azure AD Connect

Se l'autenticazione pass-through è abilitata nel tenant e si tenta di disinstallare Azure AD Connect, viene visualizzato il messaggio di avviso: "Gli utenti potranno accedere ad Azure AD solo se sono installati altri agenti di autenticazione pass-through su altri server".

È necessario che sia stata configurata la [disponibilità elevata](active-directory-aadconnect-pass-through-authentication.md) prima di disinstallare Azure AD Connect per evitare l'interruzione degli accessi utente.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemi con l'abilitazione della funzionalità di autenticazione pass-through

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>L'abilitazione della funzionalità non è riuscita perché non c'erano connettori disponibili

È necessario disporre di almeno un connettore attivo per poter abilitare l'autenticazione pass-through nel tenant. È possibile installare un connettore installando Azure AD Connect o un connettore autonomo.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>L'abilitazione della funzionalità non è riuscita a causa di porte bloccate

Assicurasi che il server in cui è installato Azure AD Connect possa comunicare con gli URL del nostro servizio e le porte elencate [qui](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>L'abilitazione della funzionalità non è riuscita a causa di errori di autorizzazione dell'account o del token

Assicurarsi di usare un account amministratore globale solo cloud quando si abilita la funzionalità. Esiste un problema noto con gli account amministratore globale con autenticazione MFA abilitata: disattivare l'autenticazione MFA temporaneamente (solo per completare le operazioni) come soluzione alternativa.

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemi con l'utilizzo della funzionalità di autenticazione pass-through

### <a name="user-facing-sign-in-errors"></a>Errori esposti all'utente in fase di accesso

La funzionalità segnala gli errori seguenti esposti all'utente nella schermata di accesso di Azure AD. Sono descritti in dettaglio di seguito con i passaggi appropriati per la risoluzione.

|Errore|Description|Risoluzione
| --- | --- | ---
|AADSTS80001|Impossibile connettersi ad Active Directory|Assicurarsi che i server del connettore siano membri della stessa foresta AD degli utenti le cui password devono essere convalidate e siano in grado di connettersi ad Active Directory.  
|AADSTS8002|Si è verificato un timeout di connessione ad Active Directory|Verificare che Active Directory sia disponibile e risponda alle richieste dei connettori.
|AADSTS80004|Il nome utente trasmesso al connettore non era valido|Assicurarsi che l'utente stia tentando di accedere con il nome utente corretto.
|AADSTS80005|La convalida ha rilevato un errore WebException imprevedibile|Si tratta probabilmente di un errore temporaneo. ripetere la richiesta. Se il problema persiste, contattare il supporto Microsoft.
|AADSTS80007|Errore durante la comunicazione con Active Directory|Controllare i log del connettore per ulteriori informazioni e verificare che Active Directory funzioni come previsto.

## <a name="collecting-pass-through-authentication-connector-logs"></a>Raccolta dei log connettore dell'autenticazione pass-through

A seconda del tipo di problema, i log connettore dell'autenticazione pass-through vanno cercati in posizioni diverse.

### <a name="connector-event-logs"></a>Registri eventi del connettore

Per gli errori correlati al connettore aprire l'applicazione Visualizzatore eventi sul server e controllare in **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Per log dettagliati di analisi e debug è possibile abilitare il log "Sessione". Non eseguire il connettore con questo log abilitato durante le normali operazioni; utilizzarlo solo per la risoluzione dei problemi. Si noti che il contenuto del log è visibile solo dopo che il log è stato nuovamente disabilitato.

### <a name="detailed-trace-logs"></a>Log di traccia dettagliati

Per risolvere gli errori di accesso utente, esaminare i log di traccia in **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Questi log includono i motivi per cui l'accesso di un utente specifico non è riuscito tramite la funzionalità di autenticazione pass-through. Di seguito è riportato un esempio di voce del log:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

È possibile ottenere una descrizione dettagliata dell'errore ("1328" nell'esempio precedente) aprendo il prompt dei comandi ed eseguendo il comando seguente. Nota: è necessario sostituire "1328" con il numero di errore effettivo riportato nei log.

`Net helpmsg 1328`

Il risultato dovrebbe essere simile a questo:

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Log del controller di dominio

Se la registrazione di controllo è abilitata, sono disponibili informazioni aggiuntive nei log di sicurezza dei controller di dominio. Un modo semplice per eseguire query sulle richieste di accesso inviate dai connettori di autenticazione pass-through è il seguente:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

