---
title: Approfondimento di sicurezza sull'autenticazione pass-through di Azure Active Directory | Microsoft Docs
description: Questo articolo descrive in che modo l'autenticazione pass-through di Azure Active Directory (Azure AD) consente di proteggere gli account locali
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: 84a5ef23739635ba4d2f0adc688c1b506f643a36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Approfondimento di sicurezza sull'autenticazione pass-through di Azure Active Directory

Questo articolo offre una descrizione più dettagliata del funzionamento dell'autenticazione pass-through di Azure Active Directory (Azure AD). Approfondisce gli aspetti di sicurezza della funzionalità. Questo articolo è rivolto agli amministratori IT e della sicurezza, i responsabili della conformità e della sicurezza e altri professionisti IT responsabili della sicurezza IT e della conformità in piccole e medie organizzazioni o in grandi aziende.

Gli argomenti trattati includono:
- Informazioni tecniche dettagliate sulle modalità di installazione e registrazione degli agenti per l'autenticazione.
- Informazioni tecniche dettagliate sulla crittografia delle password durante l'accesso dell'utente.
- Sicurezza dei canali tra agenti di autenticazione locali e Azure AD.
- Informazioni tecniche dettagliate su come mantenere la sicurezza operativa degli agenti per l'autenticazione.
- Altri argomenti sulla sicurezza.

## <a name="key-security-capabilities"></a>Principali funzionalità di sicurezza

Di seguito sono elencati gli aspetti principali realtivi alla sicurezza di questa funzionalità:
- È stata progettata su un'architettura sicura multi-tenant che offre l'isolamento delle richieste di accesso tra tenant.
- Le password locali non vengono mai archiviate nel cloud in alcuna forma.
- Gli agenti di autenticazione locale, che restano in ascolto e rispondono alle richieste di convalida delle password, creano solo connessioni in uscita dalla rete. Non viene richiesto di installare gli agenti di autenticazione in una rete perimetrale.
- Solo le porte standard, ovvero le porte 80 e 443, vengono usate per le comunicazioni in uscita dagli agenti di autenticazione ad Azure AD. Non è necessario aprire porte in ingresso nel firewall. 
  - La porta 443 viene usata per tutte le comunicazioni in uscita autenticate.
  - La porta 80 viene usata solo per il download degli elenchi di revoche di certificati per verificare che nessun certificato utilizzato dalla funzionalità sia stato revocato.
  - Per l'elenco completo dei requisiti di rete, vedere [Autenticazione pass-through di Azure Active Directory - Avvio rapido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Le password fornite dagli utenti durante l'accesso vengono crittografate nel cloud prima di essere accettate dagli agenti di autenticazione locale per la convalida con Active Directory.
- Il canale HTTPS tra Azure AD e un agente di autenticazione locale viene protetto mediante l'autenticazione reciproca.
- La funzionalità si integra perfettamente con le funzionalità di protezione cloud di Azure AD, ad esempio i criteri di accesso condizionale, che includono Azure Multi-Factor Authentication, la protezione dell'identità e il blocco intelligente.

## <a name="components-involved"></a>Componenti coinvolti

Per informazioni generali sulla sicurezza dei dati, del servizio e operativa di Azure AD, vedere il [Centro protezione](https://azure.microsoft.com/support/trust-center/). Quando viene usata l'autenticazione pass-through per l'accesso utente, sono coinvolti i componenti seguenti:
- **STS di Azure AD**: un servizio token di sicurezza senza stato che elabora le richieste di accesso e rilascia i token di sicurezza nei browser, nei client o nei servizi degli utenti in base alle esigenze.
- Il **bus di servizio di Azure**: offre comunicazione abilitata per il cloud con messaggistica aziendale e inoltra la comunicazione per la connessione delle soluzioni locali con il cloud.
- **Agenti di autenticazione di Azure AD Connect**: un componente locale che resta in ascolto e risponde alle richieste di convalida delle password.
- **Database SQL di Azure**: contiene informazioni sugli agenti di autenticazione del tenant, tra cui le loro chiavi di crittografia e i metadati.
- **Active Directory**: Active Directory locale, in cui sono archiviati gli account utente e le relative password.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installazione e registrazione degli agenti di autenticazione

Gli agenti di autenticazione vengono installati e registrati con Azure AD quando si esegue una delle seguenti operazioni:
   - [Si abilita l'autenticazione pass-through attraverso Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Si aggiungono altri agenti di autenticazione per garantire la disponibilità elevata delle richieste di accesso](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Il funzionamento di un agente di autenticazione prevede tre fasi principali:

1. Installazione dell'agente di autenticazione
2. Registrazione dell'agente di autenticazione
3. Inizializzazione dell'agente di autenticazione

Le sezioni seguenti illustrano queste fasi in modo dettagliato.

### <a name="authentication-agent-installation"></a>Installazione dell'agente di autenticazione

Solo gli amministratori globali possono installare un agente di autenticazione, usando Azure AD Connect o in modo autonomo, in un server locale. L'installazione aggiunge due nuove voci all'elenco **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**:
- L'applicazione dell'agente di autenticazione, Questa applicazione viene eseguita con privilegi [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- L'applicazione di aggiornamento utilizzata per aggiornare automaticamente l'agente di autenticazione. Questa applicazione viene eseguita con privilegi [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).

### <a name="authentication-agent-registration"></a>Registrazione dell'agente di autenticazione

Dopo aver installato l'agente di autenticazione, è necessario registrarlo con Azure AD. Azure AD assegna a ogni agente di autenticazione un certificato di identità digitale univoco che può essere usato per proteggere le comunicazioni con Azure AD.

La procedura di registrazione associa anche l'agente di autenticazione con il tenant. Questo assicura che Azure AD sappia che questo agente di autenticazione specifico è l'unico autorizzato alla gestione delle richieste di convalida delle password per il tenant. Questa procedura viene ripetuta per ogni nuovo agente di autenticazione registrato.

Gli agenti di autenticazione usano la procedura seguente per registrarsi con Azure AD:

![Registrazione dell'agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD richiede innanzitutto che un amministratore globale acceda ad Azure AD con le proprie credenziali. Durante l'accesso, l'agente di autenticazione acquisisce un token di accesso che può usare per conto dell'amministratore globale.
2. L'agente di autenticazione genera quindi una coppia di chiavi: una chiave pubblica e una chiave privata.
    - La coppia di chiavi viene generata mediante la crittografia standard RSA a 2048 bit.
    - La chiave privata rimane sul server locale in cui risiede l'agente di autenticazione.
3. L'agente di autenticazione effettua una richiesta di "registrazione" ad Azure AD su HTTPS, includendo i componenti seguenti nella richiesta:
    - Il token di accesso acquisito nel passaggio 1.
    - La chiave pubblica generata nel passaggio 2.
    - Una richiesta di firma del certificato, definita anche CSR o richiesta di certificato. Questa richiesta si applica per un certificato di identità digitale, con Azure AD come autorità di certificazione (CA).
4. Azure AD convalida il token di accesso nella richiesta di registrazione e verifica che la richiesta provenga da un amministratore globale.
5. Azure AD quindi firma e invia un certificato di identità digitale all'agente di autenticazione.
    - L'autorità di certificazione radice in Azure AD viene utilizzata per firmare il certificato. 

     >[!NOTE]
     > Questa autorità di certificazione radice _non_ si trova nell'archivio delle autorità di certificazione radice attendibili di Windows.
    - L'autorità di certificazione viene usata solo dalla funzionalità di autenticazione pass-through. L'autorità di certificazione viene usata solo per firmare i CSR durante la registrazione dell'agente di autenticazione.
    -  Nessuno degli altri servizi di Azure Active Directory usa questa autorità di certificazione.
    - Il soggetto del certificato, ovvero il nome distinto o DN, è impostato sul proprio ID tenant. Questo DN è un GUID che identifica il tenant in modo univoco. Questo DN stabilisce che l'ambito di utilizzo del certificato è il solo tenant.
6. Azure AD archivia la chiave pubblica dell'agente di autenticazione in un database SQL di Azure, a cui ha accesso solo Azure AD.
7. Il certificato, emesso al passaggio 5, viene archiviato sul server locale nell'archivio certificati Windows, in particolare nel percorso [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE). Viene usato sia dall'agente di autenticazione che dalle applicazioni di aggiornamento.

### <a name="authentication-agent-initialization"></a>Inizializzazione dell'agente di autenticazione

Quando l'agente di autenticazione viene avviato, che sia per la prima volta dopo la registrazione o dopo il riavvio di un server, ha bisogno di un modo per comunicare in maniera sicura con il servizio di Azure AD e iniziare ad accettare le richieste di convalida delle password.

![Inizializzazione dell'agente](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Di seguito è descritto come vengono inizializzati gli agenti di autenticazione:

1. L'agente di autenticazione esegue una richiesta di "avvio" in uscita ad Azure AD. 
    - Questa richiesta viene effettuata sulla porta 443 e utilizza un canale HTTPS autenticato reciprocamente. La richiesta usa lo stesso certificato emesso durante la registrazione dell'agente di autenticazione.
2. Azure AD risponde alla richiesta indicando una chiave di accesso a una coda del bus di servizio di Azure univoca per il tenant, identificata dall'ID del tenant.
3. L'agente di autenticazione stabilisce una connessione HTTPS in uscita permanente alla coda sulla porta 443. 
    - L'agente di autenticazione a questo punto è pronto per recuperare e gestire le richieste di convalida delle password.

Se nel tenant sono registrati più agenti di autenticazione, la procedura di inizializzazione assicura che ognuno di essi si connetta alla stessa coda del bus di servizio. 

## <a name="process-sign-in-requests"></a>Elaborare le richieste di accesso 

Il diagramma seguente mostra come l'autenticazione pass-through elabora le richieste di accesso utente.

![Elaborare l'accesso](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

L'autenticazione pass-through gestisce una richiesta di accesso utente nel seguente modo: 

1. Un utente tenta di accedere a un'applicazione, ad esempio [Outlook Web App](https://outlook.office365.com/owa).
2. Se l'utente non ha ancora eseguito l'accesso, l'applicazione reindirizza il browser alla pagina di accesso di Azure AD.
3. Il servizio STS di Azure AD risponde con la pagina **Accesso utente**.
4. L'utente immette il nome utente e la password nella pagina **Accesso utente** di Azure AD e seleziona il pulsante **Accedi**.
5. Il nome utente e la password vengono inviati a STS di Azure AD in una richiesta HTTPS POST.
6. STS di Azure AD recupera le chiavi pubbliche per tutti gli agenti di autenticazione registrati nel tenant dal database SQL di Azure e con queste esegue la crittografa della password. 
    - Produce "n" valori password crittografata per "n" agenti di autenticazione registrati nel tenant.
7. STS di Azure AD inserisce la richiesta di convalida della password, che consiste dei valori del nome utente e della password crittografata, nella coda del bus di servizio specifica per il tenant.
8. Poiché gli agenti di autenticazione inizializzati sono connessi in modo permanente alla coda del bus di servizio, uno degli agenti di autenticazione disponibili recupera la richiesta di convalida della password.
9. L'agente di autenticazione individua il valore password crittografata specifico per la chiave pubblica usando un identificatore e lo decrittografa con la chiave privata.
10. L'agente di autenticazione tenta di convalidare il nome utente e la password in Active Directory locale usando l'[API LogonUser Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) con il paramento **dwLogonType** impostato su **LOGON32_LOGON_NETWORK**. 
    - Si tratta della stessa API utilizzata da Active Directory Federation Services per consentire agli utenti di effettuare l'accesso in uno scenario di accesso federato.
    - Questa API fa affidamento sul processo di risoluzione standard in Windows Server per individuare il controller di dominio.
11. L'agente di autenticazione riceve il risultato da Active Directory, ad esempio risultati come operazione riuscita, nome utente o password errata o password scaduta.
12. L'agente di autenticazione inoltra il risultato a STS di Azure AD su un canale HTTPS autenticato reciprocamente in uscita sulla porta 443. L'autenticazione reciproca usa il certificato emesso in precedenza per l'agente di autenticazione durante la registrazione.
13. STS di Azure AD verifica che questo risultato si metta in correlazione con la richiesta di accesso specifica per il tenant.
14. STS di Azure AD continua la procedura di accesso, come configurato. Ad esempio, se la convalida della password ha esito positivo, all'utente potrebbe essere richiesto di effettuare l'autenticazione a più fattori oppure potrebbe essere reindirizzato all'applicazione.

## <a name="operational-security-of-the-authentication-agents"></a>Sicurezza operativa degli agenti di autenticazione

Per assicurarsi che l'autenticazione pass-through risulti protetta dal punto di vista operativo, Azure AD rinnova periodicamente i certificati degli agenti di autenticazione. Azure AD attiva il rinnovo. Questi rinnovi non dipendono dagli agenti di autenticazione.

![Sicurezza operativa](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Per rinnovare l'attendibilità di un agente di autenticazione con Azure AD:

1. L'agente di autenticazione esegue periodicamente il ping di Azure AD, a intervalli di poche ore, per verificare se è necessario rinnovare il certificato. 
    - Questa verifica viene eseguita in un canale HTTPS autenticato reciprocamente e utilizza lo stesso certificato emesso durante la registrazione.
2. Se il servizio indica che è necessario eseguire il rinnovo, l'agente di autenticazione genera una nuova coppia di chiavi: una chiave pubblica e una chiave privata.
    - Queste chiavi vengono generate mediante la crittografia standard RSA a 2048 bit.
    - La chiave privata non abbandona mai il server locale.
3. L'agente di autenticazione effettua quindi una richiesta di "rinnovo del certificato" ad Azure AD su HTTPS, includendo i componenti seguenti nella richiesta:
    - Il certificato esistente, recuperato dal percorso CERT_SYSTEM_STORE_LOCAL_MACHINE dell'archivio certificati di Windows. Questa procedura non prevede alcun amministratore globale, pertanto non è necessario alcun token di accesso per conto dell'amministratore globale.
    - La chiave pubblica generata nel passaggio 2.
    - Una richiesta di firma del certificato, definita anche CSR o richiesta di certificato. Questa richiesta chiede un nuovo certificato di identità digitale, con Azure AD come autorità di certificazione.
4. Azure AD convalida il certificato esistente nella richiesta di rinnovo del certificato. Verifica quindi che la richiesta provenga da un agente di autenticazione registrato nel tenant.
5. Se il certificato esistente è ancora valido, Azure AD firma un nuovo certificato di identità digitale e lo inoltra all'agente di autenticazione. 
6. Se il certificato esistente è scaduto, Azure AD elimina l'agente di autenticazione dall'elenco del tenant di agenti di autenticazione registrati. L'amministratore globale deve quindi installare e registrare manualmente un nuovo agente di autenticazione.
    - Usare l'autorità di certificazione radice di Azure AD per firmare il certificato.
    - Impostare il soggetto del certificato, ovvero il nome distinto o DN, sul proprio ID tenant, un GUID che identifica in modo univoco il tenant. Il DN stabilisce come ambito del certificato il solo tenant.
6. Azure AD archivia la "nuova" chiave pubblica dell'agente di autenticazione in un database SQL di Azure, a cui ha accesso solo AD Azure, e invalida la chiave pubblica "precedente" associata all'agente di autenticazione.
7. Il nuovo certificato, emesso al passaggio 5, verrà quindi archiviato sul server nell'archivio certificati Windows, in particolare nel percorso [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER).
    - Poiché la procedura di rinnovo dell'attendibilità si verifica in modo non interattivo, ovvero senza la presenza di un amministratore globale, l'agente di autenticazione non avrà più accesso per aggiornare il certificato esistente nel percorso CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Questa procedura non rimuove il certificato stesso dal percorso CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Il nuovo certificato viene usato per l'autenticazione da questo punto in poi. Ogni successivo rinnovo del certificato sostituisce il certificato che si trova nel percorso CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Aggiornamento automatico degli agenti di autenticazione

L'applicazione di aggiornamento aggiorna automaticamente l'agente di autenticazione quando viene rilasciata una nuova versione. L'applicazione non gestisce le richieste di convalida della password per il tenant. 

Azure AD ospita la nuova versione del software come un **pacchetto di Windows Installer** firmato. Il pacchetto di Windows Installer viene firmato usando [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) con SHA256 come algoritmo di digest. 

![Aggiornamento automatico](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Per eseguire l'aggiornamento automatico degli agenti di autenticazione:

1. L'applicazione di aggiornamento esegue ogni ora il ping di Azure AD per verificare l'eventuale presenza di una nuova versione dell'agente di autenticazione. 
    - Questa verifica viene eseguita in un canale HTTPS autenticato reciprocamente usando lo stesso certificato emesso durante la registrazione. L'agente di autenticazione e lo strumento di aggiornamento condividono il certificato archiviato nel server.
2. Se è disponibile una nuova versione, Azure AD restituisce il pacchetto di Windows Installer firmato allo strumento di aggiornamento.
3. Lo strumento di aggiornamento verifica che il pacchetto di Windows Installer sia firmato da Microsoft.
4. Lo strumento di aggiornamento esegue il pacchetto di Windows Installer. Questa azione include i passaggi seguenti:

 > [!NOTE]
 > L'applicazione di aggiornamento viene eseguita con privilegi [sistema locale](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).

    - Arresta il servizio agente di autenticazione
    - Installa la nuova versione dell'agente di autenticazione nel server
    - Riavvia il servizio agente di autenticazione

>[!NOTE]
>Se più agenti di autenticazione sono registrati nel tenant, Azure AD non rinnova i certificati né li aggiorna contemporaneamente. Azure AD esegue invece questa operazione in modo graduale per assicurare la disponibilità elevata delle richieste di accesso.
>


## <a name="next-steps"></a>Passaggi successivi
- [Limitazioni correnti](active-directory-aadconnect-pass-through-authentication-current-limitations.md): apprendere quali sono gli scenari supportati.
- [Guida introduttiva](active-directory-aadconnect-pass-through-authentication-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Blocco intelligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configurare la funzionalità Blocco intelligente nel tenant per proteggere gli account utente.
- [Come funziona](active-directory-aadconnect-pass-through-authentication-how-it-works.md): nozioni di base sul funzionamento dell'autenticazione pass-through di Azure AD.
- [Domande frequenti](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Seamless Single Sign-On di Azure AD](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
