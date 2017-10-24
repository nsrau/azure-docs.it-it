---
title: Approfondimento di sicurezza sull'autenticazione pass-through di Azure Active Directory | Microsoft Docs
description: Questo articolo descrive come l'autenticazione pass-through di Azure Active Directory, ovvero Azure AD, consenta di proteggere gli account locali.
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 7a886cdb0c36008bdb66592a8d3428889739627e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Approfondimento di sicurezza sull'autenticazione pass-through di Azure Active Directory

Questo articolo offre una descrizione più dettagliata sul funzionamento dell'autenticazione pass-through. Approfondisce maggiormente gli aspetti di sicurezza della funzionalità. Questo argomento sarà di particolare interesse per gli amministratori IT e della sicurezza, i responsabili della conformità e della sicurezza e altri professionisti IT responsabili della sicurezza IT e della conformità in piccole e medie organizzazioni o in grandi aziende.

Gli argomenti trattati includono:
- Informazioni tecniche dettagliate sulle modalità di installazione e registrazione degli agenti per l'autenticazione.
- Informazioni tecniche dettagliate sulla crittografia delle password durante l'accesso dell'utente.
- Sicurezza dei canali tra agenti di autenticazione locali e Azure Active Directory.
- Informazioni tecniche dettagliate sulle sicurezza operativa degli agenti per l'autenticazione.
- Altri argomenti sulla sicurezza.

## <a name="key-security-capabilities"></a>Principali funzionalità di sicurezza

Di seguito sono elencati gli aspetti principali realtivi alla sicurezza di questa funzionalità:
- È stata progettata su un'architettura sicura con multi-tenant che offre l'isolamento delle richieste di accesso tra tenant.
- Le password locali non vengono mai archiviate nel cloud in alcuna forma.
- Gli agenti di autenticazione locale, che restano in ascolto e rispondono alle richieste di convalida delle password, creano solo connessioni in uscita dalla rete. Non viene richiesto di installare gli agenti di autenticazione in una rete perimetrale.
- Solo le porte standard, ovvero le porte 80 e 443, vengono usate per le comunicazioni in uscita dagli agenti di autenticazione ad Azure AD. Nessuna porta in ingresso deve essere aperta sul firewall. 
  - La porta 443 è usata per tutte le comunicazioni in uscita autenticate
  - La porta 80 viene usata solo per il download di elenchi di revoche di certificati per verificare che nessun certificato usato dalla funzionalità sia stato revocato.
  - Vedere [qui](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) per l'elenco completo dei requisiti di rete.
- Le password fornite dall'utente durante l'accesso vengono crittografate nel cloud prima di essere accettate dagli agenti di autenticazione locale per la convalida con Active Directory.
- Il canale HTTPS tra Azure AD e un agente di autenticazione locale viene protetto mediante l'autenticazione reciproca.
- Si integra perfettamente con le funzionalità di protezione cloud di Azure AD, ad esempio i criteri di accesso condizionale, che includono l'autenticazione a più fattori, la protezione dell'identità e il blocco intelligente.

## <a name="components-involved"></a>Componenti coinvolti

Per informazioni generali sulla sicurezza dei dati, del servizio e operativa di Azure AD, vedere il [Centro protezione](https://azure.microsoft.com/support/trust-center/). Quando viene usata l'autenticazione pass-through per l'accesso utente, sono coinvolti i componenti seguenti:
- **STS di Azure AD**: un servizio token di sicurezza senza stato che elabora le richieste di accesso e rilascia i token di sicurezza nei browser, nei client o nei servizi degli utenti in base alle esigenze.
- Il **bus di servizio di Azure**: offre comunicazione abilitata per il cloud con messaggistica aziendale e inoltra la comunicazione per la connessione delle soluzioni locali con il cloud.
- **Agenti di autenticazione di Azure AD Connect**: un componente locale che resta in ascolto e risponde alle richieste di convalida delle password.
- **Database SQL di Azure**: contiene informazioni sugli agenti di autenticazione del tenant, tra cui le chiavi di crittografia e i metadati.
- **Active Directory**: l'Active Directory locale, in cui sono archiviati gli account utente e le relative password.

## <a name="installation-and-registration-of-authentication-agents"></a>Installazione e registrazione degli agenti di autenticazione

Gli agenti di autenticazione vengono installati e registrati con Azure AD quando si [abilita l'autenticazione pass-through con Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) o quando si [aggiungono altri agenti di autenticazione per garantire un'elevata disponibilità delle richieste di accesso](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Il funzionamento di un agente di autenticazione prevede tre fasi principali:

- Installazione dell'agente di autenticazione
- Registrazione dell'agente di autenticazione
- Inizializzazione dell'agente di autenticazione

Ognuna di queste fasi verrà illustrata in dettaglio negli argomenti seguenti.

### <a name="authentication-agent-installation"></a>Installazione dell'agente di autenticazione

Solo gli amministratori globali possono installare un agente di autenticazione, usando Azure AD Connect o in modo autonomo, in un server locale. L'installazione aggiunge queste due nuove voci all'elenco **Pannello di controllo -> Programmi -> Programmi e funzionalità**:
- L'applicazione dell'agente di autenticazione, che viene eseguita con i privilegi del [servizio di rete](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- L'applicazione di aggiornamento usata per aggiornare automaticamente l'agente di autenticazione. Questa viene eseguita con i privilegi del [sistema locale](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).


### <a name="authentication-agent-registration"></a>Registrazione dell'agente di autenticazione

Dopo aver installato l'agente di autenticazione, è necessario registrarlo con Azure AD. A tale scopo, Azure AD assegna a ogni agente di autenticazione un certificato di identità digitale univoco che può essere usato per proteggere le comunicazioni con Azure AD.

La procedura di registrazione associa anche l'agente di autenticazione con il tenant in modo che Azure AD sappia che questo agente di autenticazione specifico è l'unico autorizzato alla gestione delle richieste di convalida delle password per il tenant. Questa procedura viene ripetuta per ogni nuovo agente di autenticazione registrato.

Ecco come gli agenti di autenticazione effettuano la registrazione con Azure AD:

![Registrazione dell'agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD richiede innanzitutto all'amministratore globale di accedere ad Azure AD con le proprie credenziali. Durante l'accesso, l'agente di autenticazione acquisisce un token di accesso che può usare per conto dell'amministratore globale.
2. L'agente di autenticazione genera quindi una coppia di chiavi: una chiave pubblica e una chiave privata.
    - Questa coppia di chiavi viene generata usando la crittografia standard **RSA a 2048 bit**.
    - La chiave privata non lascia mai il server locale in cui è stato installato l'agente di autenticazione.
3.  L'agente di autenticazione effettua una richiesta di "registrazione" ad Azure AD su HTTPS, includendo i componenti seguenti nella richiesta:
    - Il token di accesso acquisito nel passaggio 1.
    - La chiave pubblica generata nel passaggio 2.
    - Una **richiesta di firma del certificato**, definita anche CSR o richiesta di certificato. Questa si applica a un certificato di identità digitale, con Azure AD come autorità di certificazione.
4. Azure AD convalida il token di accesso nella richiesta di registrazione e verifica che la richiesta provenga da un amministratore globale.
5. Azure AD quindi firma e rilascia un certificato di identità digitale all'agente di autenticazione.
    - Il certificato viene firmato tramite l'**autorità di certificazione radice di Azure AD**. Si noti che questa autorità di certificazione radice _non_ si trova nell'archivio delle **autorità di certificazione radice attendibili** di Windows.
    - Il soggetto del certificato, il **nome distinto o DN**, è impostato sul proprio **ID tenant**. Si tratta di un GUID che identifica il tenant in modo univoco. Viene definito solo l'ambito del certificato da usare con il tenant.
6. Azure AD archivia la chiave pubblica dell'agente di autenticazione in un database SQL di Azure, a cui ha accesso solo Azure AD.
7. Il certificato rilasciato nel passaggio 5 viene archiviato nel server locale dell'**archivio certificati Windows**, in particolare al percorso **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**, e viene usato sia dall'agente di autenticazione che dalle applicazioni per l'aggiornamento.

### <a name="authentication-agent-initialization"></a>Inizializzazione dell'agente di autenticazione

Quando viene avviato l'agente di autenticazione, per la prima volta dopo la registrazione o dopo il riavvio di un server, è necessario un modo per comunicare con il servizio di Azure AD e iniziare ad accettare le richieste di convalida delle password in modo sicuro.

![Inizializzazione dell'agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Di seguito viene descritto come vengono inizializzati gli agenti di autenticazione:



1. L'agente di autenticazione viene avviato eseguendo una richiesta di "avvio" in uscita ad Azure AD. 
    - La richiesta di avvio viene eseguita sulla porta 443 e si trova su un canale HTTPS autenticato reciprocamente, con lo stesso certificato emesso durante la registrazione dell'agente di autenticazione.
2. Azure AD risponde alla richiesta di avvio indicando una **chiave di accesso** a una coda del bus di servizio di Azure univoca per il tenant, identificata dall'ID del tenant.
3. L'agente di autenticazione stabilisce una connessione HTTPS in uscita permanente alla coda sulla porta 443. 
    - A questo punto è pronto per recuperare e gestire le richieste di convalida delle password.

Se nel tenant sono registrati più agenti di autenticazione, la procedura di inizializzazione assicura che ognuno di essi si connetta alla stessa coda del bus di servizio di Azure. 

## <a name="processing-sign-in-requests"></a>Elaborazione delle richieste di accesso 

Il diagramma seguente mostra come l'autenticazione pass-through elabori le richieste di accesso utente.

![Elaborazione dell'accesso](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

L'autenticazione pass-through gestisce una richiesta di accesso utente nel seguente modo: 

1. Un utente tenta di accedere a un'applicazione, ad esempio a Outlook Web App, [https://outlook.office365.com/owa](https://outlook.office365.com/owa).
2. Se l'utente non ha ancora eseguito l'accesso, l'applicazione reindirizza il browser alla pagina di accesso di Azure AD.
3. Il servizio STS di Azure AD risponde con la pagina di accesso utente.
4. L'utente immette il nome utente e la password nella pagina di accesso di Azure AD e fa clic sul pulsante "Accedi".
5. Il nome utente e la password vengono inviati a STS di Azure AD in una richiesta HTTPS POST.
6. STS di Azure AD recupera le chiavi pubbliche per tutti gli agenti di autenticazione registrati nel tenant dal database SQL di Azure e con queste esegue la crittografa della password. 
    - Produce "n" valori della password crittografata per "n" agenti di autenticazione registrati nel tenant.
7. STS di Azure AD inserisce la richiesta di convalida della password, ovvero i valori di nome utente e della password crittografata, nella coda del bus di servizio di Azure specifico per il tenant.
8. Poiché gli agenti di autenticazione inizializzati sono connessi in modo permanente alla coda del bus di servizio di Azure, uno degli agenti di autenticazione disponibili recupera la richiesta di convalida della password.
9. L'agente di autenticazione individua il valore della password crittografata specifico per la chiave pubblica usando un identificatore e lo decrittografa con la chiave privata.
10. L'agente di autenticazione tenta di convalidare il nome utente e la password per Active Directory locale usando l'**[API LogonUser Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)** con il paramento **dwLogonType** impostato su **LOGON32_LOGON_NETWORK** 
    - Si tratta della stessa API usata da Active Directory Federation Services per consentire agli utenti di effettuare l'accesso in uno scenario di accesso federato.
11. L'agente di autenticazione riceve il risultato da Active Directory, ovvero risultati come operazione riuscita, nome utente o password errata, password scaduta, utente bloccato e così via.
12. L'agente di autenticazione inoltra il risultato a STS di Azure AD su un canale HTTPS autenticato reciprocamente in uscita sulla porta 443. L'autenticazione reciproca usa lo stesso certificato emesso in precedenza per l'agente di autenticazione durante la registrazione.
13. STS di Azure AD verifica che questo risultato si metta in correlazione con la richiesta di accesso specifica per il tenant.
14. STS di Azure AD continua la procedura di accesso, come configurato. Ad esempio, se la convalida della password ha esito positivo, all'utente potrebbe essere richiesto di effettuare l'autenticazione a più fattori oppure viene reindirizzato all'applicazione.

## <a name="operational-security-of-authentication-agents"></a>Sicurezza operativa degli agenti di autenticazione

Per assicurarsi che l'autenticazione pass-through risulti protetta dal punto di vista operativo, Azure AD rinnova periodicamente i certificati. Questi rinnovi vengono attivati da Azure AD e non dipendono dagli agenti di autenticazione.

![Sicurezza operativa](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Ecco come un agente di autenticazione rinnova l'attendibilità con Azure AD:

1. L'agente di autenticazione esegue periodicamente il ping di Azure AD, a intervalli di poche ore, per verificare se è necessario rinnovare il certificato. 
    - Questa verifica viene eseguita in un canale HTTPS autenticato reciprocamente con lo stesso certificato emesso durante la registrazione.
2. Se il servizio indica che è necessario eseguire il rinnovo, l'agente di autenticazione genera una nuova coppia di chiavi: una chiave pubblica e una chiave privata.
    - Queste chiavi vengono generate usando la crittografia standard **RSA a 2048 bit**.
    - La chiave privata non abbandona mai il server locale.
3. L'agente di autenticazione effettua quindi una richiesta di "rinnovo del certificato" ad Azure AD su HTTPS, includendo i componenti seguenti nella richiesta:
    - Il certificato esistente, recuperato dal percorso **CERT_SYSTEM_STORE_LOCAL_MACHINE** dell'archivio certificati di Windows. Questa procedura non prevede alcun amministratore globale, pertanto non è necessario alcun token di accesso per conto dell'amministratore globale.
    - La chiave pubblica generata nel passaggio 2.
    - Una **richiesta di firma del certificato**, definita anche CSR o richiesta di certificato. Questa si applica a un nuovo certificato di identità digitale, con Azure AD come autorità di certificazione.
4. Azure AD convalida il certificato esistente nella richiesta di rinnovo del certificato. Verifica quindi che la richiesta provenga da un agente di autenticazione registrato nel tenant.
5. Se il certificato esistente è ancora valido, Azure AD firma un nuovo certificato di identità digitale e lo inoltra all'agente di autenticazione. 
6. Se il certificato esistente è scaduto, Azure AD elimina l'agente di autenticazione dall'elenco del tenant di agenti di autenticazione registrati. L'amministratore globale deve quindi installare e registrare manualmente un nuovo agente autenticazione.
    - Il certificato viene firmato tramite l'**autorità di certificazione radice di Azure AD**.
    - Il soggetto del certificato, il **nome distinto o DN**, è impostato sul proprio **ID tenant**, un GUID che identifica in modo univoco il tenant. Vale a dire che il certificato ha come ambito solo il tenant.
6. Azure AD archivia la "nuova" chiave pubblica dell'agente di autenticazione in un database SQL di Azure, a cui ha accesso solo AD Azure, e invalida la chiave pubblica "precedente" associata all'agente di autenticazione.
7. Il nuovo certificato, generato nel passaggio 5, verrà quindi archiviato nel server dell'**archivio certificati Windows**, in particolare al percorso **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**.
    - Poiché la procedura di rinnovo dell'attendibilità si verifica in modo non interattivo, senza la presenza di un amministratore globale, l'agente di autenticazione non avrà più accesso per aggiornare il certificato esistente al percorso **CERT_SYSTEM_STORE_LOCAL_MACHINE**. Si noti che il certificato stesso al percorso **CERT_SYSTEM_STORE_LOCAL_MACHINE** non deve essere rimosso durante questa procedura.
8. Il nuovo certificato viene usato per l'autenticazione da questo punto in poi. Ogni successivo rinnovo del certificato sostituisce il certificato che si trova al percorso **CERT_SYSTEM_STORE_LOCAL_MACHINE**.

## <a name="auto-update-of-authentication-agents"></a>Aggiornamento automatico degli agenti di autenticazione

L'applicazione di aggiornamento aggiorna automaticamente l'agente di autenticazione quando viene rilasciata una nuova versione. Non gestisce le richieste di convalida della password per il tenant. 

Azure AD ospita la nuova versione del software come un **pacchetto di Windows Installer** firmato. Il pacchetto di Windows Installer viene firmato usando [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) con **SHA256** come algoritmo di digest. 

![Aggiornamento automatico](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Ecco come un agente di autenticazione si aggiorna automaticamente:

1. Lo strumento di aggiornamento esegue periodicamente il ping di Azure AD, ovvero ogni ora, per verificare l'eventuale presenza di una nuova versione dell'agente di autenticazione. 
    - Questa verifica viene eseguita in un canale HTTPS autenticato reciprocamente con lo stesso certificato emesso durante la registrazione. L'agente di autenticazione e lo strumento di aggiornamento condividono il certificato archiviato nel server.
2. Se è disponibile una nuova versione, Azure AD restituisce il pacchetto di Windows Installer firmato allo strumento di aggiornamento.
3. Lo strumento di aggiornamento verifica che il pacchetto di Windows Installer sia firmato da Microsoft.
4. Lo strumento di aggiornamento esegue il pacchetto di Windows Installer. Questa azione esegue i passaggi seguenti. Si noti che lo strumento di aggiornamento viene eseguito con i privilegi del [sistema locale](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx):
    - Arresta il servizio agente di autenticazione.
    - Installa la nuova versione dell'agente di autenticazione nel server.
    - Riavvia il servizio agente di autenticazione.

>[!NOTE]
>Se più agenti di autenticazione sono registrati nel tenant, Azure AD non rinnova i certificati né li aggiorna contemporaneamente. Invece esegue questa operazione in modo graduale per assicurare la disponibilità elevata delle richieste di accesso.


## <a name="next-steps"></a>Passaggi successivi
- [**Limitazioni correnti**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): informazioni su quali scenari sono supportati e quali non lo sono.
- [**Guida introduttiva**](active-directory-aadconnect-pass-through-authentication-quick-start.md): avvio ed esecuzione dell'autenticazione pass-through di Azure AD.
- [**Blocco intelligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configurare la funzionalità Blocco intelligente nel tenant per proteggere gli account utente.
- [**Come funziona**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): nozioni di base sul funzionamento dell'autenticazione pass-through di Azure AD.
- [**Domande frequenti**](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Seamless Single Sign-On di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.

