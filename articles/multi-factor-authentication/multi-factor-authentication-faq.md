---
title: Domande frequenti su Azure Multi-Factor Authentication | Microsoft Docs
description: "Domande frequenti e risposte su Azure Multi-Factor Authentication. Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b1d48d3ff0d264042f2d282c6b8006e16a251f5
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Domande frequenti su Azure Multi-Factor Authentication
Queste domande frequenti offrono risposte su Azure Multi-Factor Authentication e sull'uso del servizio Multi-Factor Authentication. Le domande sono suddivise fra servizio in generale, modelli di fatturazione, esperienze utente e risoluzione dei problemi.

## <a name="general"></a>Generale
**D: In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?**

Con il server Multi-Factor Authentication, i dati utente vengono archiviati solo nei server locali. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue l'autenticazione a due fattori, il server Multi-Factor Authentication invia dati al servizio cloud Azure Multi-Factor Authentication per l'autenticazione. La comunicazione tra il server Multi-Factor Authentication e il servizio cloud Multi-Factor Authentication avviene tramite Secure Sockets Layer (SSL) o Transport Layer Security (TLS) sulla porta 443 in uscita.

Quando le richieste di autenticazione vengono inviate al servizio cloud, vengono raccolti dati per i report di autenticazione e uso. Di seguito sono riportati i campi di dati inclusi nei log di verifica in due passaggi:

* **ID univoco:** nome utente o ID del server Multi-Factor Authentication locale
* **Nome e cognome:** facoltativo
* **Indirizzo di posta elettronica:** facoltativo
* **Numero di telefono:** quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* **Token del dispositivo:** quando si esegue l'autenticazione con l'app per dispositivi mobili
* **Modalità di autenticazione**
* **Risultato dell'autenticazione**
* **Nome del server Multi-Factor Authentication**
* **IP del server Multi-Factor Authentication**
* **IP client:** se disponibile

I campi facoltativi possono essere configurati nel server Multi-Factor Authentication.

Il risultato della verifica (esito positivo o rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme con i dati di autenticazione. Questi dati sono disponibili nei report di autenticazione e uso.

## <a name="billing"></a>Fatturazione
È possibile rispondere alla maggior parte delle domande sulla fatturazione facendo riferimento alla pagina [Multi-Factor Authentication Prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) o alla documentazione relativa a [Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**D: Alla mia organizzazione vengono addebitati i costi delle telefonate e degli SMS utilizzati per l'autenticazione?**

No, alle aziende non vengono addebitati i costi delle singole telefonate o SMS inviati agli utenti tramite Azure Multi-Factor Authentication. Se si usa un provider di autenticazione MFA con un accordo basato sul numero di autenticazioni, verrà fatturata ciascuna autenticazione ma non il metodo utilizzato.

Potrebbero essere addebitate agli utenti le chiamate telefoniche o gli SMS che ricevono, in base al loro servizio telefonico personale.

**D: Con il modello di fatturazione per utente mi vengono addebitati tutti gli utenti o solo quelli che hanno eseguito la verifica in due passaggi?**

La fatturazione si basa sul numero di utenti configurati per usare Multi-Factor Authentication, indipendentemente dal fatto che abbiano eseguito la verifica in due passaggi in quel mese.

**D: Come funziona la fatturazione per Multi-Factor Authentication?**

Quando si crea un provider di MFA per utente o per autenticazione, la sottoscrizione di Azure dell'organizzazione viene fatturata mensilmente in base all'utilizzo. Questo modello di fatturazione è simile a quello applicato da Azure per l'uso delle macchine virtuali e dei siti Web.

Quando si acquista una sottoscrizione per Azure Multi-Factor Authentication, l'organizzazione paga solo il diritto di licenza annuale per ogni utente. La fatturazione avviene in questo modo per le licenze MFA e per i bundle Office 365, Azure AD Premium o Enterprise Mobility + Security. 

Per altre informazioni sulle opzioni, vedere [Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication?**

In alcuni casi, sì.

Multi-Factor Authentication per amministratori di Azure offre un sottoinsieme delle funzionalità di Azure MFA senza costi aggiuntivi per l'accesso ai servizi online Microsoft, inclusi i portali di amministratore di Azure e Office 365. Questa offerta è valida solo per gli amministratori globali nelle istanze di Azure Active Directory che non dispongono della versione completa di Azure MFA tramite una licenza MFA, un bundle o un provider autonomo in base al consumo. Se gli amministratori usano la versione gratuita e quindi si acquista una versione completa di Azure MFA, tutti gli amministratori globali vengono passati automaticamente alla versione a pagamento.

Multi-Factor Authentication per utenti di Office 365 offre un sottoinsieme delle funzionalità di Azure MFA senza costi aggiuntivi per l'accesso ai servizi Office 365, inclusi Exchange Online e SharePoint Online. Questa offerta è valida per gli utenti con una licenza Office 365 assegnata quando l'istanza corrispondente di Azure Active Directory non dispone della versione completa di Azure MFA tramite una licenza MFA, un bundle o un provider autonomo in base al consumo.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo per utente a quelli per autenticazione in qualsiasi momento?**

Se l'organizzazione acquista MFA come servizio autonomo con fatturazione in base al consumo, il modello di fatturazione viene scelto quando si crea un provider di MFA. Dopo la creazione del provider di MFA non è più possibile cambiare il modello di fatturazione. È tuttavia possibile eliminare il provider di MFA e quindi crearne uno con un modello di fatturazione diverso.

Quando si crea un provider di MFA è possibile collegarlo ad Azure Active Directory (noto anche come "tenant di Azure AD"). Se il provider di MFA corrente è collegato a un tenant di Azure AD, è possibile eliminare tranquillamente il provider di MFA e crearne uno collegato allo stesso tenant di Azure AD. In alternativa, se è stato acquistato un numero sufficiente di licenze MFA, Azure AD Premium o Enterprise Mobility + Security (EMS) per tutti gli utenti che sono abilitati per l'autenticazione a più fattori, è possibile eliminare del tutto il provider di MFA.

Se invece il provider di MFA *non* è collegato a un tenant di Azure AD o si collega il nuovo provider di MFA a un diverso tenant di Azure AD, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il nuovo provider di MFA. La riattivazione dei server MFA per collegarli al nuovo provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile smetteranno di funzionare per tutti gli utenti fino a quando riattiveranno l'app mobile.

Per altre informazioni sui provider di MFA, vedere [Introduzione all'uso di un provider di Azure Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md).

**D: L'organizzazione può passare dal modello di fatturazione in base al consumo e la sottoscrizione (modello basato su licenza) in qualsiasi momento?**

In alcuni casi, sì.

Se la directory include un provider di Azure Multi-Factor Authentication *per utente*, è possibile aggiungere licenze MFA. Gli utenti con licenza non vengono conteggiati nella fatturazione in base al consumo per utente. Gli utenti senza licenza possono comunque essere abilitati per MFA tramite il provider di MFA. Se si acquistano e assegnano licenze per tutti gli utenti configurati per l'uso di Multi-Factor Authentication, è possibile eliminare il provider di Azure Multi-Factor Authentication. Se in futuro si avranno più utenti che licenze, sarà sempre possibile creare un altro provider di MFA per utente.

Se la directory include un provider di Multi-Factor Authentication *per autenticazione*, viene sempre fatturata ogni autenticazione, fintanto che il provider di MFA è collegato alla sottoscrizione. È possibile assegnare le licenze MFA agli utenti, ma verrà comunque fatturata ogni richiesta di verifica in due passaggi, che provenga o meno da un utente con assegnata una licenza di autenticazione a più fattori.

**D: È necessario che l'organizzazione usi e sincronizzi le identità per usare Azure Multi-Factor Authentication?**

Se l'organizzazione usa un modello di fatturazione in base al consumo, è possibile usare Azure Active Directory ma non è obbligatorio. Se il provider di MFA non è collegato a un tenant di Azure AD, è possibile distribuire il server di Azure Multi-Factor Authentication o l'SDK di Azure Multi-Factor Authentication in locale.

Quando si usa il modello di licenza, è necessario usare Azure Active Directory in quanto le licenze vengono aggiunte al tenant di Azure AD quando vengono acquistate e assegnate agli utenti nella directory.

## <a name="manage-and-support-user-accounts"></a>Gestire e supportare gli account utente

**D: Cosa dire agli utenti di fare se non ricevono una risposta al telefono o non hanno il telefono con loro?**

È auspicabile che tutti gli utenti abbiano configurato più metodi di verifica. Dire loro di tentare nuovamente l'accesso, ma selezionando un altro metodo di verifica nella pagina di accesso.

È possibile invitare gli utenti a leggere l'articolo [Problemi con la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**D: Cosa fare se uno degli utenti non riesce ad accedere al proprio account?**

È possibile reimpostare l'account utente chiedendo all'utente stesso di ripetere il processo di registrazione. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: Cosa fare se uno degli utenti perde un telefono che usa le password di app?**

L'amministratore può eliminare tutte le password di app dell'utente per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente potrà ricrearle. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: Cosa accade se un utente non riesce ad accedere alle applicazioni non basate su browser?**

Se l'organizzazione usa ancora client legacy ed è stato [consentito l'uso delle password di app](multi-factor-authentication-whats-next.md#app-passwords), gli utenti non possono eseguire l'accesso a questi client legacy con il loro nome utente e password. Devono invece [impostare le password di app](./end-user/multi-factor-authentication-end-user-app-passwords.md). Gli utenti devono cancellare (eliminare) le informazioni di accesso, riavviare l'app e quindi accedere con il proprio nome utente e *password di app* anziché le password normali.

Se l'organizzazione non ha client legacy, non è consigliabile consentire agli utenti di creare password di app.

> [!NOTE]
> Autenticazione moderna per i client di Office 2013
>
> Le password di app sono necessarie solo per le app che non supportano l'autenticazione moderna. I client Office 2013 supportano i protocolli dell'autenticazione moderna, ma devono essere configurati. I client Office più recenti supportano automaticamente i protocolli dell'autenticazione moderna. Per altre informazioni, vedere l' [annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**D: Gli utenti sostengono che talvolta non ricevono l'SMS o che rispondono agli SMS bidirezionale ma la verifica scade.**

Il recapito degli SMS e la ricezione di risposte SMS bidirezionale non sono garantiti in quanto sussistono fattori non controllabili che possono influire sull'affidabilità del servizio. Questi fattori includono il paese di destinazione, il gestore di telefonia mobile e la qualità del segnale.

Se agli utenti capita spesso di non ricevere in modo affidabile gli SMS, suggerire loro di usare il metodo dell'app per dispositivi mobili o della chiamata telefonica. L'app per dispositivi mobili può ricevere notifiche sia su rete cellulare che Wi-Fi. L'app per dispositivi mobili può generare codici di verifica anche in caso di totale assenza di segnale. L'app Microsoft Authenticator è disponibile per [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Se è necessario usare gli SMS, è consigliabile usare SMS unidirezionali anziché bidirezionali, poiché sono più affidabili ed evitano all'utente costi globali per l'SMS, causati dalla risposta a un messaggio di testo inviato da un altro paese.

**D: È possibile cambiare il tempo che gli utenti hanno a disposizione per immettere il codice di verifica dall'SMS prima del timeout?**

In alcuni casi sì. 

Per SMS unidirezionali con Azure MFA Server 7.0 o versione successiva, è possibile configurare il timeout impostando una chiave del Registro di sistema. Dopo l'invio dell'SMS dal servizio cloud MFA, a MFA Server viene restituito il codice di verifica (o passcode monouso). MFA Server archivia il codice in memoria per 300 secondi per impostazione predefinita. Se l'utente non immette il codice dopo 300 secondi, l'autenticazione viene negata. Usare questa procedura per modificare l'impostazione di timeout predefinita:

1. Passare ad HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Creare una chiave DWORD del Registro di sistema denominata **pfsvc_pendingSmsTimeoutSeconds** e impostare il tempo, in secondi, per cui il server Azure MFA dovrà archiviare i passcode monouso.

>[!TIP] 
>Se sono disponibili più MFA Server, il codice di verifica inviato all'utente e noto solo per il server che ha elaborato la richiesta di autenticazione originale. Quando l'utente immette il codice, la richiesta di autenticazione da convalidare deve essere inviata allo stesso server. Se la convalida del codice viene inviata a un server diverso, l'autenticazione viene negata. 

Per gli SMS bidirezionali con Azure MFA Server è possibile configurare l'impostazione di timeout nel portale di gestione di MFA. Se gli utenti non rispondono all'SMS entro il periodo di timeout definito, l'autenticazione viene negata. 

Non è possibile configurare l'impostazione di timeout per SMS unidirezionali con Azure MFA nel cloud, inclusi l'adattatore AD FS o l'estensione Server dei criteri di rete. Azure AD archivia il codice di verifica per 180 secondi. 

**D: È possibile usare i token hardware con il server Azure multi-Factor Authentication?**

Se si usa il server Azure Multi-Factor Authentication, è possibile importare token OATH TOPT di terze parti e quindi usarli per la verifica in due passaggi.

È possibile usare token ActiveIdentity che siano token OATH TOTP se si inserisce la chiave privata in un file CSV e lo si importa nel server Azure Multi-Factor Authentication. I token OATH possono essere usati con Active Directory Federation Services (AD FS), con l'autenticazione basata su moduli di Internet Information Server (IIS) e con il servizio RADIUS (Remote Authentication Dial-In User Service), a condizione che il sistema client possa accettare l'input dell'utente.

È possibile importare token OATH TOTP di terze parti con i formati seguenti:  

- Portable Symmetric Key Container (PSKC)  
- CSV se il file contiene un numero di serie, una chiave privata in formato Base 32 e un intervallo di tempo  

**D: È possibile usare il server di Azure Multi-Factor Authentication per proteggere Servizi Terminal?**

Sì, ma se si usa Windows Server 2012 R2 o versione successiva è possibile proteggere i Servizi Terminal solo usando Gateway Desktop remoto.

Le modifiche della sicurezza in Windows Server 2012 R2 hanno variato le modalità di connessione del server di Azure Multi-Factor Authentication al pacchetto di sicurezza dell'autorità di protezione locale in Windows Server 2012 e versioni precedenti. Per le versioni di Servizi terminal in Windows Server 2012 o versioni precedenti è possibile [proteggere un'applicazione con l'autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si usa Windows Server 2012 R2, è necessario un Gateway Desktop remoto.

**D: È stato configurato l'ID chiamante in MFA Server ma gli utenti continuano a ricevere le chiamate di Multi-Factor Authentication da un chiamante anonimo.**

Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. Per questo motivo, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori lo invia sempre.

**D: Perché agli utenti viene chiesto di registrare le informazioni di sicurezza?**
Esistono diversi motivi per cui agli utenti potrebbe essere chiesto di registrare le informazioni di sicurezza:

- L'utente è stato abilitato per MFA dall'amministratore in Azure AD, ma non ha informazioni di sicurezza registrate per il proprio account.
- L'utente è stato abilitato per la reimpostazione password self-service in Azure AD. Le informazioni di sicurezza lo aiuteranno a reimpostare la password in futuro, se dovesse dimenticarla.
- L'utente ha eseguito l'accesso a un'applicazione che dispone di un criterio di accesso condizionale per richiedere MFA e non è stato registrato in precedenza per MFA.
- L'utente sta registrando un dispositivo con Azure AD (inclusa l'aggiunta ad Azure AD) e l'organizzazione richiede MFA per la registrazione del dispositivo, ma l'utente non è stato registrato in precedenza per MFA.
- L'utente sta generando Windows Hello for Business in Windows 10 (che richiede MFA) e non è stato registrato in precedenza per MFA.
- L'organizzazione ha creato e attivato un criterio di registrazione MFA che è stato applicato all'utente.
- L'utente è stato registrato in precedenza per MFA, ma ha scelto un metodo di verifica che un amministratore ha poi disabilitato. Pertanto l'utente deve eseguire di nuovo la registrazione MFA per scegliere un nuovo metodo di verifica predefinito.


## <a name="errors"></a>Errori
**D: Cosa fare quando viene visualizzato l'errore "Authentication request is not for an activated account" (La richiesta di autenticazione non si riferisce a un account attivato) quando si usano le notifiche delle app per dispositivi mobili?**

Seguire questa procedura per rimuovere il proprio account dall'app per dispositivi mobili e quindi aggiungerlo di nuovo:

1. Passare al [profilo nel portale di Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account dell'organizzazione.
2. Selezionare **Verifica aggiuntiva di sicurezza**.
3. Rimuovere l'account esistente dall'app per dispositivi mobili.
4. Fare clic su **Configura**e seguire le istruzioni per riconfigurare l'app per dispositivi mobili.

**D: Cosa è necessario fare se viene visualizzato un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non basata su browser?**

L'errore 0x800434D4L si verifica quando si tenta di accedere a un'applicazione diversa da un browser, installata in un computer locale, che non funziona con account che richiedono la verifica in due passaggi.

Una soluzione alternativa per questo errore consiste nel disporre di account utente separati per le operazioni correlate all'amministrazione e per quelle non amministrative. In un secondo momento è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere a Outlook usando l'account non amministratore. Per altri dettagli su questa soluzione, vedere come [consentire a un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passaggi successivi
Se la domanda non ha ricevuto risposta qui, riproporla nei commenti in fondo alla pagina. In alternativa, di seguito vengono elencate alcune opzioni aggiuntive per ottenere assistenza:

* Cercare nella [Knowledge Base del supporto tecnico Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) le soluzioni ai problemi tecnici comuni.
* È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* I clienti legacy di PhoneFactor che hanno domande o necessitano di assistenza per reimpostare una password possono usare il collegamento per la [reimpostazione della password](mailto:phonefactorsupport@microsoft.com) per aprire una richiesta di assistenza.
* Contattare un tecnico del supporto tramite [Azure Multi-Factor Authentication - PhoneFactor](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Microsoft, è utile includere il maggior numero possibile di informazioni relative al problema. Tali informazioni includono la pagina in cui viene visualizzato l'errore, il codice dell'errore specifico, l'ID della sessione specifico e l'ID dell'utente che visualizza l'errore.
