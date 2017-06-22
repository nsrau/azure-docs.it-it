---
title: 'Azure AD Connect: autenticazione pass-through | Documentazione Microsoft'
description: Questo articolo descrive l&quot;autenticazione pass-through di Azure Active Directory (Azure AD) e come consenta gli accessi ad Azure AD tramite la convalida delle password degli utenti in Active Directory locale.
services: active-directory
keywords: "cos&quot;è l&quot;autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurare l'accesso utente con l'autenticazione pass-through di Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Cos'è l'autenticazione pass-through di Azure Active Directory?

Consentire agli utenti di usare le stesse credenziali (password) per accedere sia alle risorse locali sia ai servizi basati su cloud è vantaggioso per loro stessi e per l'organizzazione. Gli utenti hanno una password in meno da ricordare e di conseguenza un'esperienza migliore. Si riducono anche le possibilità che dimentichino come eseguire l'accesso. I costi di help desk quindi si abbassano, poiché in genere i problemi correlati alle password assorbono la maggior parte delle risorse di supporto.

Molte organizzazioni usano la [sincronizzazione delle password di Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), una funzionalità di [Azure AD Connect](active-directory-aadconnect.md) che sincronizza gli hash delle password degli utenti da Active Directory locale ad Azure Active Directory (Azure AD), come modo per fornire agli utenti le stesse credenziali per le risorse locali e i servizi basati su cloud. Altre aziende richiedono invece che le password, anche in un formato hash, non lascino i confini dell'irganizzazione.

La funzionalità di autenticazione pass-through di Azure AD rappresenta una soluzione semplice per queste organizzazioni. Quando gli utenti accedono ad Azure AD, assicura che le loro password vengano convalidate direttamente in Active Directory locale. Questa funzionalità offre anche i vantaggi seguenti:

- È facile da usare
  - La convalida della password viene eseguita senza la necessità di distribuzioni locali o configurazione di rete complesse.
  - Il leggero connettore locale resta in ascolto e risponde alle richieste di convalida delle password.
  - Il connettore locale riceve automaticamente miglioramenti di funzionalità e correzioni di bug.
  - Può essere configurato insieme ad [Azure AD Connect](active-directory-aadconnect.md). Il connettore locale leggero viene installato nello stesso server di Azure AD Connect.
- Proteggere
  - Le password locali non vengono mai archiviate nel cloud in alcuna forma.
  - Il leggero connettore locale esegue solo le connessioni in uscita dalla rete. Pertanto, non è necessario installarlo in una rete perimetrale.
  - L'autenticazione pass-through funziona perfettamente con Azure Multi-Factor Authentication.
- È affidabile e scalabile
  - Altri connettori locali leggeri possono essere installati su più server per ottenere una disponibilità elevata delle richieste di accesso in locale.

![Autenticazione pass-through di Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

È possibile combinare l'autenticazione pass-through con la funzionalità [Seamless Single Sign-On](active-directory-aadconnect-sso.md) (SSO). In questo modo, quando gli utenti operano dai loro computer aziendali all'interno della rete aziendale, non dovranno neppure digitare la password per accedere ad Azure AD, ottenendo così un'esperienza davvero integrata.

## <a name="whats-available-during-preview"></a>Cos'è disponibile durante l'anteprima?

>[!NOTE]
>L'autenticazione pass-through di Azure AD è attualmente in fase di anteprima. È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla.

Gli scenari seguenti sono completamente supportati in fase di anteprima:

- Tutte le applicazioni basate su browser Web
- Applicazioni client di Office 365 che supportano l'[autenticazione moderna](https://aka.ms/modernauthga)

Gli scenari seguenti _non_ sono supportati in fase di anteprima:

- Applicazioni client legacy di Office ed Exchange ActiveSync (ovvero applicazioni di posta elettronica native sui dispositivi mobili). <br>Le organizzazioni sono incoraggiate a passare all'autenticazione moderna, se possibile. Ciò consente di supportare l'autenticazione pass-through e permette anche di proteggere le identità utilizzando le funzionalità di [accesso condizionale](../active-directory-conditional-access.md), ad esempio Multi-Factor Authentication.
- Aggiunta ad Azure AD per dispositivi Windows 10.

>[!IMPORTANT]
>Come soluzione alternativa per gli scenari che la funzionalità di autenticazione pass-through attualmente non supporta (applicazioni client legacy di Office, Exchange ActiveSync e aggiunta ad Azure AD per dispositivi Windows 10), la sincronizzazione delle password è abilitata per impostazione predefinita quando si abilita l'autenticazione pass-through. La sincronizzazione delle password funziona come fallback solo in questi scenari specifici. Se non è necessaria, è possibile disattivare la sincronizzazione delle password nella pagina [Funzionalità facoltative](active-directory-aadconnect-get-started-custom.md#optional-features) della procedura guidata di Azure AD Connect.

### <a name="prerequisites"></a>Prerequisiti

Prima di poter abilitare l'autenticazione pass-through di Azure AD, è necessario predisporre quanto segue:

- Un tenant di Azure AD di cui l'utente è un amministratore globale.

  >[!NOTE]
  >È consigliabile usare un account amministratore globale di tipo solo cloud. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md).

- Azure AD Connect versione 1.1.486.0 o successiva. È consigliabile usare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire Azure AD Connect.
  - Questo server deve appartenere alla stessa foresta Active Directory degli utenti le cui password devono essere convalidate.
  - Si noti che un connettore di autenticazione pass-through è installato nello stesso server di Azure AD Connect. Verificare che la versione del connettore sia 1.5.58.0 o successiva.

    >[!NOTE]
    >Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste AD e se il routing del suffisso del nome è configurato correttamente.

- Se si desidera disponibilità elevata, saranno necessari ulteriori server che eseguono Windows Server 2012 R2 o versione successiva per installare i connettori autonomi (versione 1.5.58.0 o successiva).
- Se è presente un firewall tra uno dei connettori e Azure AD:
    - Se il filtro degli URL è abilitato, verificare che i connettori possano comunicare con i seguenti URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Assicurarsi che i connettori eseguano anche connessioni IP dirette agli [intervalli IP del data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assicurarsi che il firewall non esegua l'ispezione SSL, poiché i connettori usano certificati client per comunicare con Azure AD.
    - Verificare che i connettori possano inviare richieste ad Azure AD sulle porte 80 e 443.
      - Se il firewall impone le regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.
      - I connettori eseguono le richieste HTTP sulla porta 80 per il download degli elenchi di revoche di certificati SSL. Questo serve anche per il funzionamento corretto dell'aggiornamento automatico.
      - I connettori eseguono le richieste HTTPS sulla porta 443 per tutte le altre operazioni quali l'abilitazione e disabilitazione della funzionalità, la registrazione di connettori, il download degli aggiornamenti del connettore e la gestione di tutte le richieste di accesso degli utenti.

     >[!NOTE]
     >Di recente sono stati apportati miglioramenti al fine di ridurre il numero di porte richieste dai connettori per comunicare con il servizio. Se si eseguono versioni precedenti dei connettori di Azure AD Connect e/o connettori autonomi, è necessario continuare a mantenere aperte queste porte aggiuntive (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120).

### <a name="enable-azure-ad-pass-through-authentication"></a>Abilitare l'autenticazione pass-through di Azure AD

L'autenticazione pass-through di Azure AD può essere abilitata tramite Azure AD Connect.

Se si esegue una nuova installazione di Azure AD Connect, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina **Accesso utente** selezionare **Autenticazione pass-through**. Se viene completata con successo, viene installato un connettore di autenticazione pass-through nello stesso server di Azure AD Connect. Inoltre viene abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso3.png)

Se Azure AD Connect è già stato installato, eseguire la configurazione usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md), scegliere la pagina **Cambia l'accesso utente** in Azure AD Connect e fare clic su **Avanti**. Quindi selezionare **Autenticazione pass-through** per installare un connettore di autenticazione pass-through nello stesso server di Azure AD Connect e abilitare la funzionalità per il tenant.

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through di Azure AD è una funzionalità a livello di tenant. Influisce sull'accesso utente in tutti i domini gestiti del tenant. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con Active Directory Federation Services (ADFS) o qualsiasi altro provider federativo configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. L'autenticazione pass-through non ha alcun effetto sugli utenti solo cloud.

Se si intende usare l'autenticazione pass-through in una distribuzione di produzione, è consigliabile installare un secondo connettore in un server separato (diverso da quello che esegue Azure AD Connect e il primo connettore). Ciò garantirà la disponibilità elevata delle richieste di accesso. È possibile installare tutti i connettori che occorrono. La quantità dipende dal numero di picco e dal numero medio di richieste di accesso gestite dal tenant.

Per distribuire un connettore autonomo, seguire queste istruzioni.

#### <a name="step-1-download-and-install-the-connector"></a>Passaggio 1: Scaricare e installare il connettore

In questo passaggio scaricare e installare il software del connettore sul server.

1.    [Scaricare](https://go.microsoft.com/fwlink/?linkid=837580) il connettore più recente. Verificare che la versione del connettore sia 1.5.58.0 o successiva.
2.    Aprire il prompt dei comandi come amministratore.
3.    Eseguire il comando indicato di seguito. L'opzione **/q** significa "installazione non interattiva", ovvero durante l'installazione non viene richiesto di accettare il contratto di licenza con l'utente finale: `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>È possibile installare solo un singolo connettore per ogni server.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Passaggio 2: Registrare il connettore con Azure AD per l'autenticazione pass-through

In questo passaggio si registra il connettore installato sul server con il servizio e lo si rende disponibile a ricevere le richieste di accesso.

1.    Aprire una finestra di PowerShell come amministratore.
2.    Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script, come segue: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando richiesto, immettere le credenziali dell'account amministratore globale per il tenant di Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Come funziona l'autenticazione pass-through di Azure AD?

Quando un utente tenta di accedere ad Azure AD (e se l'autenticazione pass-through è attivata per il tenant), si verifica quanto segue:

1. L'utente immette il nome utente e la password nella pagina di accesso di Azure AD. Il nostro servizio inserisce il nome utente e la password (crittografata con chiave pubblica) in una coda per la convalida.
2. Uno dei connettori disponibili in locale esegue una chiamata in uscita alla coda e recupera il nome utente e la password.
3. Il connettore convalida quindi il nome utente e la password in Active Directory usando le API Windows standard (un meccanismo simile a quello utilizzato da ADFS). Si noti che il nome utente può essere il nome utente predefinito locale (in genere `userPrincipalName`) o un altro attributo configurato in Azure AD Connect (noto come `Alternate ID`).
4. Il controller di dominio locale valuta quindi la richiesta e restituisce una risposta (esito positivo o negativo) al connettore.
5. Il connettore, a sua volta, restituisce la risposta ad Azure AD.
6. Azure AD valuta quindi la risposta e risponde all'utente nel modo appropriato. Ad esempio rilascia un token all'applicazione o richiede il servizio Multi-Factor Authentication.

Il diagramma seguente illustra i vari passaggi. Tutte le richieste e le risposte vengono inoltrate attraverso il canale HTTPS.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>Writeback delle password

Nel caso in cui sia stato configurato il [writeback delle password](../active-directory-passwords-update-your-own-password.md) nel tenant e per un utente specifico, se l'utente esegue l'accesso usando l'autenticazione pass-through potrà modificare o reimpostare le password come in precedenza. Le password vengono riscritte in Active Directory locale come previsto.

Se invece il writeback delle password non è configurato nel tenant o all'utente non è assegnata una licenza di Azure AD valida, l'utente non potrà aggiornare la propria password nel cloud. Questo vale anche se la password è scaduta. L'utente vedrà un messaggio simile a questo: "L'organizzazione non consente l'aggiornamento della password in questo sito. Aggiornare la password usando il metodo consigliato dall'organizzazione oppure contattare l'amministratore, se è necessaria assistenza".

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come abilitare la funzionalità [Seamless SSO di Azure AD](active-directory-aadconnect-sso.md) nel tenant.
- Per informazioni su come risolvere i problemi comuni dell'autenticazione pass-through di Azure AD, leggere la [guida alla risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md).

## <a name="feedback"></a>Commenti e suggerimenti
I commenti degli utenti sono importanti. Usare la sezione relativa ai commenti in caso di domande. Per richieste di nuove funzionalità, usare il [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect).


