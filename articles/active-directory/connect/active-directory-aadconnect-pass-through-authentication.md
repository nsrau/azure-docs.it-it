---
title: 'Azure AD Connect: autenticazione pass-through | Documentazione Microsoft'
description: Questo articolo descrive l&quot;autenticazione pass-through di Azure Active Directory (Azure AD) e come consente gli accessi ad Azure AD tramite la convalida delle password degli utenti in Active Directory locale.
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
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0f54fb7d2d8cf010baf79409bc6a528d34982500
ms.lasthandoff: 04/22/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurare l'accesso utente con l'autenticazione pass-through di Azure Active Directory

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>Cos'è l'autenticazione pass-through di Azure Active Directory (Azure AD)?

Consentire agli utenti di usare le stesse credenziali (password) per accedere sia alle risorse locali sia ai servizi basati su cloud è vantaggioso per gli utenti e per l'organizzazione. Gli utenti avranno una password in meno da ricordare. Questo garantisce un'esperienza utente migliore e riduce le probabilità che gli utenti dimentichino come eseguire l'accesso. Ciò, a sua volta, riduce i costi di help desk poiché in genere la maggior parte delle risorse di supporto vengono assorbite da problemi relativi alla password.

Molte organizzazioni usano la [sincronizzazione delle password di Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), una funzionalità di [Azure AD Connect](active-directory-aadconnect.md) che sincronizza le password degli utenti da Active Directory locale ad Azure AD, come modo per fornire agli utenti le stesse credenziali per le risorse locali e i servizi basati su cloud. Altre aziende richiedono invece che le password, anche in un formato hash, non lascino i confini dell'irganizzazione.

L'autenticazione pass-through di Azure AD rappresenta una soluzione semplice per queste organizzazioni. Quando gli utenti accedono ad Azure AD, assicura che le loro password vengano convalidate direttamente nella Active Directory locale. Questa funzionalità offre anche i vantaggi seguenti:

- È facile da usare
  - La convalida della password viene eseguita senza la necessità di distribuzioni locali o configurazione di rete complesse.
  - Usa solo un connettore leggero in locale che resta in ascolto e risponde alle richieste di convalida delle password.
  - Può essere configurato insieme ad [Azure AD Connect](active-directory-aadconnect.md). Il connettore locale leggero viene installato nello stesso server di Azure AD Connect.
- Proteggere
  - Le password locali non vengono mai archiviate nel cloud in alcuna forma.
  - Il connettore locale leggero esegue solo le connessioni in uscita dalla rete. Pertanto non è necessario installare il connettore in una rete perimetrale.
  - L'autenticazione pass-through funziona perfettamente con Azure Multi-Factor Authentication.
- È affidabile e scalabile
  - Altri connettori locali leggeri possono essere installati su più server per ottenere una disponibilità elevata delle richieste di accesso in locale.

![Autenticazione pass-through di Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Se è combinata con la funzionalità [Seamless Single Sign-on](active-directory-aadconnect-sso.md), gli utenti non dovranno neppure digitare la password per accedere ad Azure AD dai loro computer aziendali all'interno della rete aziendale ottenendo così un'esperienza davvero integrata.

## <a name="whats-available-during-preview"></a>Cos'è disponibile durante l'anteprima?

>[!NOTE]
>L'autenticazione pass-through di Azure AD è attualmente in fase di anteprima. È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla. 

Gli scenari seguenti sono completamente supportati in fase di anteprima:

- Tutte le applicazioni basate su browser Web.
- Applicazioni client di Office 365 che supportano l'[autenticazione moderna](https://aka.ms/modernauthga).

Gli scenari seguenti NON sono supportati in fase di anteprima:

- Le applicazioni client legacy di Office ed Exchange ActiveSync (ad esempio le applicazioni di posta elettronica native sui dispositivi mobili).
  - Le organizzazioni sono incoraggiate a passare all'autenticazione moderna, se possibile. Questo consente di supportare l'autenticazione pass-through e permette anche di proteggere le identità utilizzando le funzionalità di [accesso condizionale](../active-directory-conditional-access.md) come l'autenticazione a più fattori.
- Aggiunta ad Azure AD per dispositivi Windows 10.

>[!IMPORTANT]
>Come soluzione alternativa per gli scenari che l'autenticazione pass-through attualmente non supporta (applicazioni client legacy di Office, Exchange ActiveSync e aggiunta ad Azure AD per dispositivi Window 10), la sincronizzazione delle password è abilitata per impostazione predefinita quando si abilita l'autenticazione pass-through. La sincronizzazione delle password funziona come fallback solo in questi scenari specifici. Se non è necessaria, è possibile disattivare la sincronizzazione delle password nella pagina [Funzionalità facoltative](active-directory-aadconnect-get-started-custom.md#optional-features) di Azure AD Connect.

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>Come abilitare l'autenticazione pass-through di Azure AD?

### <a name="pre-requisites"></a>Prerequisiti

Prima di poter abilitare l'autenticazione pass-through di Azure AD, è necessario disporre dei seguenti prerequisiti:

- Un tenant di Azure AD di cui l'utente è un amministratore globale.

>[!NOTE]
>È consigliabile usare un account amministratore di tipo solo cloud, in modo da poter gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. È possibile aggiungere un account di amministratore globale solo cloud, come illustrato [qui](../active-directory-users-create-azure-portal.md).

- Azure AD Connect versione 1.1.484.0 o versione successiva. È consigliabile usare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire Azure AD Connect.
  - Questo server deve appartenere alla stessa foresta Active Directory degli utenti le cui password devono essere convalidate.
  - Si noti che un connettore è installato nello stesso server di Azure AD Connect.

>[!NOTE]
>Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste AD e se il routing dei suffisso nome è configurato correttamente.

- Se si desidera disponibilità elevata, saranno necessari ulteriori server che eseguono Windows Server 2012 R2 o versione successiva per installare i connettori autonomi.
- Se è presente un firewall tra uno dei connettori e Azure AD, assicurarsi che:
    - Se il filtro degli URL è abilitato, verificare che i connettori possano comunicare con i seguenti URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - I connettori eseguono anche connessioni IP dirette agli [intervalli IP del data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assicurarsi che il firewall non esegua l'ispezione SSL poiché i connettori usano certificati client per comunicare con Azure AD.
    - Verificare che i connettori possano inviare richieste HTTPS (TCP) ad Azure AD sulle porte 80 e 443.
      - Se il firewall impone le regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.

>[!NOTE]
>Di recente sono stati apportati miglioramenti al fine di ridurre il numero di porte richieste dai connettori per comunicare con il servizio. Se si eseguono versioni precedenti dei connettori di Azure AD Connect e/o connettori autonomi, è necessario continuare a mantenere aperte queste porte aggiuntive (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120).

### <a name="enabling-azure-ad-pass-through-authentication"></a>Abilitare l'autenticazione pass-through di Azure AD

L'autenticazione pass-through di Azure AD può essere abilitata tramite Azure AD Connect.

Se si esegue una nuova installazione di Azure AD Connect, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina "Accesso dell'utente" selezionare "Autenticazione pass-through". Se viene completata con successo, viene installato un connettore di autenticazione pass-through nello stesso server di Azure AD Connect. Inoltre viene abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso3.png)

Se si ha già un'installazione di Azure AD Connect, eseguire la configurazione usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md), scegliere la pagina "Cambia l'accesso utente" in Azure AD Connect e fare clic su "Avanti". Quindi selezionare "Autenticazione pass-through" per installare un connettore di autenticazione pass-through nello stesso server di Azure AD Connect e abilitare la funzionalità per il tenant.

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through di Azure AD è una funzionalità a livello di tenant. Influisce sull'accesso utente in tutti i domini gestiti del tenant. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con Active Directory Federation Services (ADFS) o qualsiasi altro provider di federazione configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. L'autenticazione pass-through non ha alcun effetto sugli utenti solo cloud.

### <a name="ensuring-high-availability"></a>Assicurare la disponibilità elevata

Se si intende usare l'autenticazione pass-through in una distribuzione di produzione, è consigliabile installare un secondo connettore in un server separato (diverso da quello che esegue Azure AD Connect e il primo connettore) per assicurarsi di avere disponibilità elevata di richieste di accesso. È possibile installare tutti i connettori necessari: questo dipende dal numero di picco e dal numero medio di richieste di accesso che il tenant gestisce.

Per distribuire un connettore autonomo, seguire le istruzioni seguenti:

#### <a name="step-1-download-and-install-the-connector"></a>Passaggio 1: Scaricare e installare il connettore

In questo passaggio scaricare e installare il software del connettore sul server.

1.    [Scaricare](https://go.microsoft.com/fwlink/?linkid=837580) il connettore più recente.
2.    Aprire un prompt dei comandi come amministratore.
3.    Eseguire il comando riportato di seguito (il parametro /q significa installazione non interattiva, ovvero durante l'installazione non viene richiesto di accettare il contratto di licenza con l'utente finale):

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>È possibile installare solo un singolo connettore per ogni server.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Passaggio 2: Registrare il connettore con Azure AD per l'autenticazione pass-through

In questo passaggio si registra il connettore installato sul server con il servizio e lo si rende disponibile a ricevere le richieste di accesso.

1.    Aprire una finestra di PowerShell come amministratore.
2.    Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script, come segue: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando richiesto, immettere le credenziali dell'account amministratore globale nel tenant di Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Come funziona l'autenticazione pass-through di Azure AD?

Quando un utente tenta di accedere ad Azure AD (e se l'autenticazione pass-through è attivata nel tenant), si verifica quanto segue:

1. L'utente immette il nome utente e la password nella pagina di accesso di Azure AD. Il nostro servizio inserisce il nome utente e la password (crittografata con chiave pubblica) in una coda per la convalida.
2. Uno dei connettori disponibili in locale esegue una chiamata in uscita alla coda e recupera il nome utente e la password.
3. Il connettore convalida quindi il nome utente e la password in Active Directory usando le API di Windows standard (un meccanismo simile a quello utilizzato da ADFS). Si noti che il nome utente può essere il nome utente locale predefinito (in genere "userPrincipalName") o un altro attributo (noto come "ID alternativo") configurato in Azure AD Connect.
4. Il controller di dominio locale valuta quindi la richiesta e restituisce una risposta (esito positivo o negativo) al connettore.
5. Il connettore, a sua volta, restituisce la risposta ad Azure AD.
6. Azure AD valuta quindi la risposta e risponde all'utente nel modo appropriato. Ad esempio rilascia un token all'applicazione o richiede l'autenticazione a più fattori.

Il diagramma seguente illustra i diversi passaggi. Si noti che tutte le richieste e risposte vengono inoltrate attraverso il canale HTTPS.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>Nota sul writeback delle password

Se è stato configurato il [writeback delle password](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) nel tenant e per un utente specifico, se l'utente esegue l'accesso usando l'autenticazione pass-through potrà modificare o reimpostare le password come prima. Le password verranno riscritte nella Active Directory locale come previsto.

Se invece una delle condizioni seguenti non è verificata (il writeback della password non è configurato per il tenant o all'utente non è assegnata una licenza di Azure AD valida), l'utente non potrà aggiornare le proprie password nel cloud, neppure al momento della loro scadenza. L'utente vedrà un messaggio simile a questo: "L'organizzazione non consente di aggiornare la password in questo sito. Si prega di aggiornarla con il metodo consigliato dall'organizzazione o di rivolgersi all'amministratore per ottenere assistenza. ".

## <a name="troubleshooting-pass-through-authentication"></a>Risoluzione dei problemi relativi all'autenticazione pass-through

Questa sezione consentirà di trovare informazioni di risoluzione per alcuni problemi comuni che si riscontrano durante l'installazione, la registrazione o la disinstallazione dei connettori di autenticazione pass-through (tramite Azure AD Connect o autonomi), nonché durante l'abilitazione e l'uso della funzionalità nel tenant.

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problemi durante l'installazione dei connettori (tramite Azure AD Connect o autonomi)

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Un connettore del proxy di applicazione di Azure AD esiste già

Un connettore di autenticazione pass-through non può essere installato nello stesso server di un connettore [proxy dell'applicazione AD Azure](../../active-directory/active-directory-application-proxy-get-started.md). È necessario installare il connettore di autenticazione pass-through in un server diverso.

#### <a name="an-unexpected-error-occured"></a>Si è verificato un errore imprevisto

[Raccogliere i log connettore](#how-to-collect-pass-through-authentication-connector-logs?) dal server e contattare il supporto Microsoft per risolvere il problema.

### <a name="issues-during-registration-of-connectors"></a>Problemi durante la registrazione dei connettori

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>La registrazione del connettore non è riuscita a causa di porte bloccate

Verificare che il server in cui è installato il connettore possa comunicare con gli URL del nostro servizio e le porte elencate [qui](#pre-requisites).

#### <a name="an-unexpected-error-occurred"></a>Si è verificato un errore imprevisto

[Raccogliere i log connettore](#how-to-collect-pass-through-authentication-connector-logs?) dal server e contattare il supporto Microsoft per risolvere il problema.

### <a name="issues-during-un-installation-of-connectors"></a>Problemi durante la disinstallazione dei connettori

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Messaggio di avviso quando si disinstalla Azure AD Connect

Se l'autenticazione pass-through è abilitata nel tenant e si tenta di disinstallare Azure AD Connect, sarà visualizzato il seguente messaggio di avviso: "Gli utenti potranno accedere ad Azure AD solo se sono installati altri agenti di autenticazione pass-through su altri server.".

È necessario che sia stata configurata la [disponibilità elevata](#ensuring-high-availability) prima di disinstallare Azure AD Connect per evitare l'interruzione degli accessi utente.

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problemi con l'abilitazione della funzionalità di autenticazione pass-through

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>L'abilitazione della funzionalità non è riuscita perché non c'erano connettori disponibili

È necessario disporre di almeno un server del connettore attivo per poter abilitare l'autenticazione pass-through nel tenant. È possibile installare un connettore installando Azure AD Connect o installando un connettore autonomo.

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>L'abilitazione della funzionalità non è riuscita a causa di porte bloccate

Assicurasi che il server in cui è installato Azure AD Connect possa comunicare con gli URL del nostro servizio e le porte elencate [qui](#pre-requisites).

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problemi con l'utilizzo della funzionalità di autenticazione pass-through

#### <a name="user-facing-sign-in-errors"></a>Errori esposti all'utente in fase di accesso

La funzionalità segnala gli errori seguenti esposti all'utente nella schermata di accesso di Azure AD. Sono descritti in dettaglio di seguito con i passaggi appropriati per la risoluzione.

|Errore|Description|Risoluzione
| --- | --- | ---
|AADSTS80001|Impossibile connettersi ad Active Directory|Assicurarsi che i server del connettore siano membri della stessa foresta AD degli utenti le cui password devono essere convalidate e siano in grado di connettersi ad Active Directory.  
|AADSTS8002|Si è verificato un timeout di connessione ad Active Directory|Verificare che Active Directory sia disponibile e risponda alle richieste dei connettori.
|AADSTS80004|Il nome utente trasmesso al connettore non era valido|Assicurarsi che l'utente stia tentando di accedere con il nome utente corretto.
|AADSTS80005|La convalida ha rilevato un errore WebException imprevedibile|Si tratta probabilmente di un errore temporaneo. ripetere la richiesta. Se il problema persiste, contattare il supporto Microsoft.
|AADSTS80007|Errore durante la comunicazione con Active Directory|Controllare i log del connettore per ulteriori informazioni e verificare che Active Directory funzioni come previsto.

### <a name="how-to-collect-pass-through-authentication-connector-logs"></a>Come si raccolgono i log connettore dell'autenticazione pass-through?

A seconda del tipo di problema, i log connettore dell'autenticazione pass-through vanno cercati in posizioni diverse.

#### <a name="connector-event-logs"></a>Registri eventi del connettore

Per gli errori correlati al connettore aprire l'applicazione Visualizzatore eventi sul server e controllare in **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Per log dettagliati di analisi e debug è possibile abilitare il log "Sessione". Non eseguire il connettore con questo log abilitato durante le normali operazioni; utilizzarlo solo per la risoluzione dei problemi. Si noti che il contenuto del log è visibile solo dopo che il log è stato nuovamente disabilitato.

#### <a name="detailed-trace-logs"></a>Log di traccia dettagliati

Per risolvere gli errori di accesso utente, esaminare i log di traccia in **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Questi log includono i motivi per cui l'accesso di un utente specifico non è riuscito mediante la funzionalità di autenticazione pass-through. Di seguito è riportato un esempio di voce del log:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

È possibile ottenere una descrizione dettagliata dell'errore ("1328" nell'esempio precedente) aprendo il prompt dei comandi ed eseguendo il comando seguente. Nota: è necessario sostituire "1328" con il numero di errore effettivo riportato nei log.

`Net helpmsg 1328`

Il risultato dovrebbe essere simile a questo:

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>Log del controller di dominio

Se la registrazione di controllo è abilitata, sono disponibili informazioni aggiuntive nei log di sicurezza dei controller di dominio. Un modo semplice per eseguire query sulle richieste di accesso inviate dai connettori di autenticazione pass-through è il seguente:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>Commenti e suggerimenti

I commenti degli utenti sono importanti. È possibile inviare e-mail a Microsoft all'indirizzo [ aadopauthfeedback@microsoft.com ](mailto:aadopauthfeedback@microsoft.com). Se si hanno richieste per nuove funzionalità, usare il [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) di Microsoft, che viene monitorato regolarmente.

