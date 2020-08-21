---
title: Domande frequenti su Azure Multi-Factor Authentication-Azure Active Directory
description: Domande frequenti e risposte su Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4e09637a7d990e1555b5443b9931cf8ff8c271b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718867"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Domande frequenti su Azure Multi-Factor Authentication

Queste domande frequenti offrono risposte su Azure Multi-Factor Authentication e sull'uso del servizio Multi-Factor Authentication. Le domande sono suddivise fra servizio in generale, modelli di fatturazione, esperienze utente e risoluzione dei problemi.

> [!IMPORTANT]
> A partire dal 1° luglio 2019, Microsoft non offrirà più il server MFA per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori per gli utenti dovranno usare il servizio Azure Multi-Factor Authentication basato sul cloud. Gli attuali clienti che anno attivato il server MFA prima del 1° luglio potranno scaricare la versione più recente e gli aggiornamenti futuri, oltre a generare le credenziali di attivazione come di consueto.
>
> Le informazioni riportate di seguito relative all'server Multi-Factor Authentication di Azure sono valide solo per gli utenti che hanno già eseguito il server di autenticazione a più fattori.
>
> Le licenze basate sul consumo non sono più disponibile per i nuovi clienti a partire dal 1° settembre 2018.
> A partire dal 1 ° settembre 2018 non è più possibile creare nuovi provider di autenticazione. I provider di autenticazione esistenti potranno continuare a essere usati e aggiornato. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

## <a name="general"></a>Generale

* [In che modo Azure server Multi-Factor Authentication gestisce i dati degli utenti?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Quali codici brevi SMS vengono usati per l'invio di messaggi SMS agli utenti?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?

Con server Multi-Factor Authentication, i dati utente vengono archiviati solo nei server locali. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue l'autenticazione a due fattori, il server Multi-Factor Authentication invia dati al servizio cloud Azure Multi-Factor Authentication per l'autenticazione. La comunicazione tra il server Multi-Factor Authentication e il servizio cloud Multi-Factor Authentication avviene tramite Secure Sockets Layer (SSL) o Transport Layer Security (TLS) sulla porta 443 in uscita.

Quando le richieste di autenticazione vengono inviate al servizio cloud, vengono raccolti dati per i report di autenticazione e uso. I campi dati seguenti sono inclusi nei log di verifica in due passaggi:

* **ID univoco:** nome utente o ID del server Multi-Factor Authentication locale
* **Nome e cognome** (facoltativo)
* **Indirizzo di posta elettronica** (facoltativo)
* **Numero di telefono:** quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* **Token del dispositivo:** quando si esegue l'autenticazione con l'app per dispositivi mobili
* **Modalità di autenticazione**
* **Risultato dell'autenticazione**
* **Nome del server Multi-Factor Authentication**
* **IP del server Multi-Factor Authentication**
* **IP client:** se disponibile

I campi facoltativi possono essere configurati nel server Multi-Factor Authentication.

Il risultato della verifica (esito positivo o rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme con i dati di autenticazione. Questi dati sono disponibili nei report di autenticazione e uso.

Per altre informazioni, vedere Data [Residency e Customer data per Azure multi-factor authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Quali codici brevi SMS vengono usati per inviare i messaggi SMS agli utenti?

Nella Stati Uniti vengono usati i seguenti codici brevi SMS:

* *97671*
* *69829*
* *51789*
* *99399*

In Canada vengono usati i codici brevi SMS seguenti:

* *759731*
* *673801*

Non vi sono garanzie di recapito di messaggi di richiesta di SMS o Multi-Factor Authentication basati su voce coerenti con lo stesso numero. Nell'interesse degli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento per eseguire modifiche di route e migliorare il recapito degli SMS.

Non sono supportati i codici brevi per i paesi o le aree oltre il Stati Uniti e il Canada.

## <a name="billing"></a>Fatturazione

È possibile rispondere alla maggior parte delle domande di fatturazione facendo riferimento alla [pagina dei prezzi multi-factor authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) o alla documentazione per le [versioni di Azure multi-factor authentication e i piani a consumo](concept-mfa-licensing.md).

* [L'organizzazione addebita l'invio di telefonate e SMS usati per l'autenticazione?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Il modello di fatturazione per utente addebita tutti gli utenti abilitati o solo quelli che hanno eseguito la verifica in due passaggi?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Come funziona la fatturazione Multi-Factor Authentication?](#how-does-multi-factor-authentication-billing-work)
* [È disponibile una versione gratuita di Azure Multi-Factor Authentication?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [L'organizzazione può passare dai modelli di fatturazione a consumo per utente e per autenticazione in qualsiasi momento?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [L'organizzazione può passare dalla fatturazione al consumo e dalle sottoscrizioni (un modello basato su licenza) in qualsiasi momento?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [L'organizzazione deve usare e sincronizzare le identità per usare Multi-Factor Authentication di Azure?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Alla mia organizzazione vengono addebitati i costi delle telefonate e degli SMS usati per l'autenticazione?

No, non vengono addebitate le singole chiamate effettuate o gli SMS inviati agli utenti tramite Azure Multi-Factor Authentication. Se si usa un provider di autenticazione a più fattori per autenticazione, viene addebitata la fatturazione per ogni autenticazione, ma non per il metodo usato.

Potrebbero essere addebitate agli utenti le chiamate telefoniche o gli SMS che ricevono, in base al loro servizio telefonico personale.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Con il modello di fatturazione per utente mi vengono addebitati tutti gli utenti o solo quelli che hanno eseguito la verifica in due passaggi?

La fatturazione si basa sul numero di utenti configurati per usare Multi-Factor Authentication, indipendentemente dal fatto che abbiano eseguito la verifica in due passaggi in quel mese.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Come funziona la modalità di fatturazione di autenticazione a più fattori?

Quando si crea un provider di MFA per utente o per autenticazione, la sottoscrizione di Azure dell'organizzazione viene fatturata mensilmente in base all'utilizzo. Questo modello di fatturazione è simile al modo in cui Azure fattura l'utilizzo di macchine virtuali e app Web.

Quando si acquista una sottoscrizione per Azure Multi-Factor Authentication, l'organizzazione paga solo il diritto di licenza annuale per ogni utente. La fatturazione avviene in questo modo per le licenze MFA e per i bundle Office 365, Azure AD Premium o Enterprise Mobility + Security.

Per altre informazioni, vedere [Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>È disponibile una versione gratuita di Azure Multi-Factor Authentication?

Le impostazioni predefinite di sicurezza possono essere abilitate nel livello Azure AD Free. Con le impostazioni predefinite di sicurezza, tutti gli utenti sono abilitati per l'autenticazione a più fattori usando l'app Microsoft Authenticator. Non è possibile usare l'SMS o la verifica telefonica con le impostazioni predefinite di sicurezza, ma solo l'app Microsoft Authenticator.

Per altre informazioni, vedere [che cosa sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo per utente a quelli per autenticazione in qualsiasi momento?

Se l'organizzazione acquista MFA come servizio autonomo con fatturazione in base al consumo, il modello di fatturazione viene scelto quando si crea un provider di MFA. Non è possibile modificare il modello di fatturazione dopo la creazione di un provider di autenticazione a più fattori. 

Se il provider di autenticazione a più fattori *non* è collegato a un tenant di Azure ad o si collega il nuovo provider di autenticazione a più fattori a un tenant diverso Azure ad, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il nuovo provider di MFA. La riattivazione dei server MFA per collegarli al nuovo provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile smetteranno di funzionare per tutti gli utenti fino a quando riattiveranno l'app mobile.

Per altre informazioni sui provider di MFA, vedere [Introduzione all'uso di un provider di Azure Multi-Factor Authentication](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>L'organizzazione può passare dal modello di fatturazione in base al consumo alla sottoscrizione (modello basato su licenza) in qualsiasi momento?

In alcuni casi, sì.

Se la directory include un provider di Azure Multi-Factor Authentication *per utente*, è possibile aggiungere licenze MFA. Gli utenti con licenza non vengono conteggiati nella fatturazione in base al consumo per utente. Gli utenti senza licenza possono comunque essere abilitati per MFA tramite il provider di MFA. Se si acquistano e assegnano licenze per tutti gli utenti configurati per l'uso di Multi-Factor Authentication, è possibile eliminare il provider di Azure Multi-Factor Authentication. Se in futuro si avranno più utenti che licenze, sarà sempre possibile creare un altro provider di MFA per utente.

Se la directory ha un provider di Azure Multi-Factor Authentication *per autenticazione* , viene sempre addebitata la fatturazione per ogni autenticazione, purché il provider di autenticazione a più fattori sia collegato alla sottoscrizione. È possibile assegnare le licenze MFA agli utenti, ma verrà comunque fatturata ogni richiesta di verifica in due passaggi, che provenga o meno da un utente con assegnata una licenza di autenticazione a più fattori.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>È necessario che l'organizzazione usi e sincronizzi le identità per usare Azure Multi-Factor Authentication?

Se l'organizzazione usa un modello di fatturazione in base al consumo, è possibile usare Azure Active Directory ma non è obbligatorio. Se il provider di autenticazione a più fattori non è collegato a un tenant di Azure AD, è possibile distribuire solo Azure server Multi-Factor Authentication in locale.

Quando si usa il modello di licenza, è necessario usare Azure Active Directory in quanto le licenze vengono aggiunte al tenant di Azure AD quando vengono acquistate e assegnate agli utenti nella directory.

## <a name="manage-and-support-user-accounts"></a>Gestire e supportare gli account utente

* [Cosa si deve fare se gli utenti non ricevono una risposta sul telefono?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Quali operazioni è necessario eseguire se uno degli utenti non riesce a raggiungere il proprio account?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Cosa è necessario fare se uno degli utenti perde un telefono che usa le password dell'app?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Cosa accade se un utente non riesce ad accedere alle app non basate su browser?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Gli utenti dicono che talvolta non ricevono il messaggio di testo o il timeout della verifica.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [È possibile modificare la quantità di tempo per cui gli utenti devono immettere il codice di verifica da un SMS prima del timeout del sistema?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [È possibile usare I token hardware con server Multi-Factor Authentication di Azure?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [È possibile usare server Multi-Factor Authentication di Azure per proteggere Servizi terminal?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Ho configurato l'ID chiamante nel server multi-factor authentication, ma gli utenti continuano a ricevere Multi-Factor Authentication chiamate da un chiamante anonimo.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Perché agli utenti viene richiesto di registrare le informazioni di sicurezza?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Cosa si deve fare se gli utenti non ricevono una risposta sul telefono?

Chiedere agli utenti di effettuare un tentativo fino a cinque volte in 5 minuti per ottenere una telefonata o un SMS per l'autenticazione. Microsoft usa più provider per la distribuzione di chiamate e messaggi SMS. Se questo approccio non funziona, aprire un caso di supporto per la risoluzione dei problemi.

Le app per la sicurezza di terze parti possono anche bloccare l'SMS o la telefonata del codice di verifica. Se si usa un'app di sicurezza di terze parti, provare a disabilitare la protezione, quindi richiedere l'invio di un altro codice di verifica dell'autenticazione a più fattori.

Se i passaggi precedenti non funzionano, controllare se gli utenti sono configurati per più di un metodo di verifica. Provare a eseguire nuovamente l'accesso, ma selezionare un metodo di verifica diverso nella pagina di accesso.

Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi dell'utente finale](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Cosa fare se uno degli utenti non riesce ad accedere al proprio account?

È possibile reimpostare l'account utente chiedendo all'utente stesso di ripetere il processo di registrazione. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Cosa fare se uno degli utenti perde un telefono che usa le password dell'app?

L'amministratore può eliminare tutte le password di app dell'utente per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente potrà ricrearle. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Cosa accade se un utente non riesce ad accedere alle applicazioni non basate su browser?

Se l'organizzazione usa ancora client legacy ed è stato [consentito l'uso delle password di app](howto-mfa-app-passwords.md), gli utenti non possono eseguire l'accesso a questi client legacy con il loro nome utente e password. Devono invece [impostare le password di app](../user-help/multi-factor-authentication-end-user-app-passwords.md). Gli utenti devono cancellare (eliminare) le informazioni di accesso, riavviare l'app e quindi accedere con il proprio nome utente e *password di app* anziché le password normali.

Se l'organizzazione non ha client legacy, non è necessario consentire agli utenti di creare password dell'app.

> [!NOTE]
> **Autenticazione moderna per i client di Office 2013**
>
> Le password di app sono necessarie solo per le app che non supportano l'autenticazione moderna. I client Office 2013 supportano i protocolli dell'autenticazione moderna, ma devono essere configurati. L'autenticazione moderna è disponibile per tutti i clienti che eseguono l'aggiornamento di marzo 2015 o versione successiva per Office 2013. Per altre informazioni, vedere il post di Blog [aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Gli utenti dicono che talvolta non ricevono il messaggio di testo o il timeout della verifica.

Il recapito di messaggi SMS non è garantito perché sono presenti fattori non controllabili che possono influire sull'affidabilità del servizio. Questi fattori includono il paese o l'area di destinazione, il gestore di telefonia mobile e il livello di attendibilità del segnale.

Le app per la sicurezza di terze parti possono anche bloccare l'SMS o la telefonata del codice di verifica. Se si usa un'app di sicurezza di terze parti, provare a disabilitare la protezione, quindi richiedere l'invio di un altro codice di verifica dell'autenticazione a più fattori.

Se gli utenti hanno spesso problemi con la ricezione affidabile di messaggi di testo, indicare loro di usare invece il metodo Microsoft Authenticator app o telefonata. Il Microsoft Authenticator può ricevere le notifiche sia sulle connessioni Wi-Fi. L'app per dispositivi mobili può generare codici di verifica anche in caso di totale assenza di segnale. L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>È possibile cambiare il tempo che gli utenti hanno a disposizione per immettere il codice di verifica dall'SMS prima del timeout?

In alcuni casi sì.

Per SMS unidirezionali con Azure MFA Server 7.0 o versione successiva, è possibile configurare il timeout impostando una chiave del Registro di sistema. Dopo l'invio dell'SMS dal servizio cloud MFA, a MFA Server viene restituito il codice di verifica (o passcode monouso). MFA Server archivia il codice in memoria per 300 secondi per impostazione predefinita. Se l'utente non immette il codice dopo 300 secondi, l'autenticazione viene negata. Usare questa procedura per modificare l'impostazione di timeout predefinita:

1. Passare a `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Creare una chiave del registro di sistema **DWORD** denominata *pfsvc_pendingSmsTimeoutSeconds* e impostare il tempo in secondi per cui si vuole che il server di autenticazione a più fattori di Azure memorizzi i codici di accesso una volta sola.

>[!TIP]
>
> Se sono disponibili più MFA Server, il codice di verifica inviato all'utente e noto solo per il server che ha elaborato la richiesta di autenticazione originale. Quando l'utente immette il codice, la richiesta di autenticazione da convalidare deve essere inviata allo stesso server. Se la convalida del codice viene inviata a un server diverso, l'autenticazione viene negata.

Se gli utenti non rispondono all'SMS entro il periodo di timeout definito, l'autenticazione viene negata.

Per gli SMS unidirezionali con l'autenticazione a più fattori di Azure nel cloud (incluso l'adapter AD FS o l'estensione del server dei criteri di rete), non è possibile configurare l'impostazione del timeout. Azure AD archivia il codice di verifica per 180 secondi.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>È possibile usare i token hardware con il server Azure multi-Factor Authentication?

Se si usa Azure server Multi-Factor Authentication, è possibile importare token monouso per la password monouso (TOTP) di terze parti e quindi usarli per la verifica in due passaggi.

È possibile usare token ActiveIdentity che siano token OATH TOTP se si inserisce la chiave privata in un file CSV e lo si importa nel server Azure Multi-Factor Authentication. I token OATH possono essere usati con Active Directory Federation Services (AD FS), con l'autenticazione basata su moduli di Internet Information Server (IIS) e con il servizio RADIUS (Remote Authentication Dial-In User Service), a condizione che il sistema client possa accettare l'input dell'utente.

È possibile importare token OATH TOTP di terze parti con i formati seguenti:  

- Portable Symmetric Key Container (PSKC)
- CSV se il file contiene un numero di serie, una chiave privata in formato Base 32 e un intervallo di tempo

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>È possibile usare il server di Azure Multi-Factor Authentication per proteggere Servizi Terminal?

Sì, ma se si usa Windows Server 2012 R2 o versione successiva, è possibile proteggere Servizi terminal solo usando Desktop remoto Gateway (Gateway Desktop remoto).

Le modifiche della sicurezza in Windows Server 2012 R2 hanno variato le modalità di connessione del server di Azure Multi-Factor Authentication al pacchetto di sicurezza dell'autorità di protezione locale in Windows Server 2012 e versioni precedenti. Per le versioni di Servizi terminal in Windows Server 2012 o versioni precedenti è possibile [proteggere un'applicazione con l'autenticazione di Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si usa Windows Server 2012 R2, è necessario Gateway Desktop remoto.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>È stato configurato l'ID chiamante in MFA Server ma gli utenti continuano a ricevere le chiamate di Multi-Factor Authentication da un chiamante anonimo.

Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. A causa di questo comportamento del vettore, l'ID chiamante non è garantito, anche se il sistema Multi-Factor Authentication lo invia sempre.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Perché agli utenti viene chiesto di registrare le informazioni di sicurezza?

Esistono diversi motivi per cui agli utenti potrebbe essere chiesto di registrare le informazioni di sicurezza:

- L'utente è stato abilitato per MFA dall'amministratore in Azure AD, ma non ha informazioni di sicurezza registrate per il proprio account.
- L'utente è stato abilitato per la reimpostazione password self-service in Azure AD. Le informazioni di sicurezza lo aiuteranno a reimpostare la password in futuro, se dovesse dimenticarla.
- L'utente ha eseguito l'accesso a un'applicazione che dispone di criteri di accesso condizionale per richiedere l'autenticazione a più fattori e non è stata precedentemente registrata per
- L'utente sta registrando un dispositivo con Azure AD (incluso Azure AD join) e l'organizzazione richiede l'autenticazione a più fattori per la registrazione del dispositivo, ma l'utente non è stato registrato in precedenza per l'autenticazione a più fattori.
- L'utente sta generando Windows Hello for business in Windows 10 (che richiede l'autenticazione a più fattori) e non è stato registrato in precedenza per l'autenticazione a
- L'organizzazione ha creato e attivato un criterio di registrazione MFA che è stato applicato all'utente.
- L'utente è stato registrato in precedenza per MFA, ma ha scelto un metodo di verifica che un amministratore ha poi disabilitato. Pertanto l'utente deve eseguire di nuovo la registrazione MFA per scegliere un nuovo metodo di verifica predefinito.

## <a name="errors"></a>Errors

* [Cosa dovrebbero fare gli utenti se viene visualizzato un messaggio di errore "richiesta di autenticazione non per un account attivato" quando si usano le notifiche di app per dispositivi mobili?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Cosa possono fare gli utenti se visualizzano un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non basata su browser?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Cosa dovrebbero fare gli utenti se viene visualizzato un messaggio di errore "richiesta di autenticazione non per un account attivato" quando si usano le notifiche di app per dispositivi mobili?

Richiedere all'utente di completare la procedura seguente per rimuovere l'account dal Microsoft Authenticator, quindi aggiungerlo di nuovo:

1. Passare al [profilo portale di Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con un account aziendale.
2. Selezionare **verifica aggiuntiva di sicurezza**.
3. Rimuovere l'account esistente dall'app Microsoft Authenticator.
4. Fare clic su **Configura**e quindi seguire le istruzioni per riconfigurare il Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Cosa è necessario fare se viene visualizzato un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non basata su browser?

L'errore *0x800434D4L* si verifica quando si tenta di accedere a un'applicazione non basata su browser, installata in un computer locale, che non funziona con account che richiedono la verifica in due passaggi.

Una soluzione alternativa per questo errore consiste nel disporre di account utente separati per le operazioni correlate all'amministrazione e per quelle non amministrative. In un secondo momento è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere a Outlook usando l'account non amministratore. Per altri dettagli su questa soluzione, vedere come [consentire a un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passaggi successivi

Se la domanda non risponde qui, sono disponibili le opzioni di supporto seguenti:

* Cercare nella [Knowledge Base del supporto tecnico Microsoft](https://support.microsoft.com) le soluzioni ai problemi tecnici comuni.
* Cercare e consultare le domande e le risposte tecniche della community o porre una domanda nel [Azure Active Directory domande e risposte&a](/answers/topics/azure-active-directory.html).
* Contattare Microsoft Professional tramite il [supporto server multi-factor authentication di Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Microsoft, è utile includere il maggior numero possibile di informazioni relative al problema. Tali informazioni includono la pagina in cui viene visualizzato l'errore, il codice dell'errore specifico, l'ID della sessione specifico e l'ID dell'utente che visualizza l'errore.
* Se si è un cliente legacy di PhoneFactor e si hanno domande o se è necessario assistenza per la reimpostazione di una password, usare l' [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) indirizzo di posta elettronica per aprire un caso di supporto.