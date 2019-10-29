---
title: Configurare Multi-Factor Authentication di Azure-Azure Active Directory
description: Questo articolo illustra come configurare le impostazioni di Azure Multi-Factor Authentication nel portale di Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfca7ef1dabb8c6021c9df2e3174d3e208a839b5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73042102"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurare le impostazioni di Azure Multi-Factor Authentication

Questo articolo offre informazioni utili per gestire le impostazioni di Azure Multi-Factor Authentication nel portale di Azure. L'articolo illustra diversi argomenti che permettono di ottenere il massimo da Azure Multi-Factor Authentication. Non tutte le funzionalità sono disponibili in tutte le versioni di Azure Multi-Factor Authentication.

È possibile accedere alle impostazioni correlate ad Azure Multi-Factor Authentication dalla portale di Azure passando a **Azure Active Directory** ** > multi**-Factor Authentication.

![Portale di Azure - Impostazioni di Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Impostazioni

Alcune di queste impostazioni si applicano al server MFA, ad Azure MFA o a entrambi.

| Funzionalità | Description |
| ------- | ----------- |
| Blocco dell'account | Blocca temporaneamente gli account nel servizio di autenticazione a più fattori se si verificano troppi tentativi di autorizzazione negata di seguito. Questa funzionalità si applica solo agli utenti che effettuano l'autenticazione tramite PIN (server MFA). |
| [Blocca/Sblocca utenti](#block-and-unblock-users) | Utilizzato per impedire a utenti specifici di ricevere richieste di Multi-Factor Authentication. Eventuali tentativi di autenticazione per gli utenti bloccati vengono negati automaticamente. Gli utenti restano bloccati per 90 giorni dal momento in cui vengono bloccati. |
| [Avviso di illecito](#fraud-alert) | Configurare le impostazioni relative alla capacità degli utenti di segnalare richieste di verifica fraudolente |
| [Notifiche](#notifications) | Abilita le notifiche degli eventi dal server MFA. |
| [Token OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Funzionalità usata in ambienti Azure MFA basati sul cloud per gestire i token OATH per gli utenti. |
| [Impostazioni telefonata](#phone-call-settings) | Permette di configurare le impostazioni correlate a chiamate telefoniche e messaggi di saluto per gli ambienti cloud e locali. |
| Provider | Mostra tutti i provider di autenticazione esistenti che possono essere stati associati all'account. Non è stato possibile creare nuovi provider di autenticazione fino al 1° settembre 2018 |

## <a name="manage-mfa-server"></a>Gestisci il server MFA

Le impostazioni in questa sezione sono valide solo per il server MFA.

| Funzionalità | Description |
| ------- | ----------- |
| Impostazioni del server | Permette di scaricare il server MFA e di generare le credenziali di attivazione per inizializzare l'ambiente |
| [Bypass monouso](#one-time-bypass) | Permette a un utente di eseguire l'autenticazione senza completare la verifica in due passaggi per un periodo di tempo limitato. |
| [Regole di memorizzazione nella cache](#caching-rules) |  La memorizzazione nella cache viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. Dopo che l'utente ha completato la prima verifica in corso, grazie a questa funzionalità le richieste successive hanno automaticamente esito positivo. |
| Stato del server | Visualizza lo stato dei server MFA locali, insieme a informazioni su versione, stato, indirizzo IP e data e ora dell'ultima comunicazione. |

## <a name="activity-report"></a>Report attività

I report disponibili qui sono specifici per il server MFA (locale). Per i report di Azure MFA (cloud), vedere i report degli accessi in Azure AD.

## <a name="block-and-unblock-users"></a>Bloccare e sbloccare utenti

Usare la funzionalità per _bloccare/sbloccare utenti_ per evitare che gli utenti ricevano le richieste di autenticazione. Eventuali tentativi di autenticazione per gli utenti bloccati vengono negati automaticamente. Gli utenti restano bloccati per 90 giorni dal momento in cui vengono bloccati.

### <a name="block-a-user"></a>Bloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Aggiungi** per bloccare un utente.
4. Selezionare il **Gruppo di replica**. Immettere il nome utente per l'utente bloccato come **nome utente\@Domain.com**. Immettere un commento nel campo **motivo** .
5. Selezionare **Aggiungi** per completare il blocco dell'utente.

### <a name="unblock-a-user"></a>Sbloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Sblocca** nella colonna **Azione** accanto all'utente che si desidera sbloccare.
4. Immettere un commento nel campo **Motivo dello sblocco**.
5. Selezionare **Sblocca** per completare lo sblocco dell'utente.

## <a name="fraud-alert"></a>Avviso sulle frodi

Configurare la funzionalità di _avviso di illecito_ in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle proprie risorse. Gli utenti possono segnalare i tentativi illeciti usando l'app per dispositivi mobili o il telefono.

### <a name="turn-on-fraud-alerts"></a>Attivare gli avvisi di illecito

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **MFA** > **Avviso di illecito**.
3. Impostare l'impostazione **Consenti agli utenti di inviare avvisi di illeciti** su **Attivo**.
4. Selezionare **Salva**.

### <a name="configuration-options"></a>Opzioni di configurazione

* **Blocca utente se viene segnalato un illecito**: se un utente segnala un illecito, il suo account viene bloccato per 90 giorni o finché l'amministratore non lo sblocca. Un amministratore può verificare gli accessi usando il report sugli accessi ed eseguire le azioni necessarie per prevenire illeciti nel futuro. Un amministratore può quindi [sbloccare](#unblock-a-user) l'account dell'utente.
* **Codice per la segnalazione di illeciti durante il messaggio introduttivo iniziale**: quando gli utenti ricevono una chiamata telefonica per eseguire la verifica in due passaggi, in genere premono **#** per confermare l'accesso. Per segnalare un illecito, l'utente immette un codice prima di premere **#** . Il codice predefinito è **0**, ma è possibile personalizzarlo.

   >[!NOTE]
   >I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere **0#** per inviare un avviso di illecito. Se si vuole usare un codice diverso da **0**, registrare e caricare messaggi vocali personalizzati con istruzioni adeguate per l'utente.
   >

### <a name="view-fraud-reports"></a>Visualizzare le segnalazioni di illeciti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **accessi**. Il report illecito fa ora parte del report degli accessi Azure AD standard.

## <a name="notifications"></a>Notifiche

Configurare gli indirizzi di posta elettronica per gli utenti che riceveranno messaggi di posta elettronica di avviso illecito.

![Esempio di messaggio di posta elettronica di avviso di illecito](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Impostazioni telefonata

### <a name="caller-id"></a>ID chiamante

**Numero ID chiamante MFA**: numero che verrà visualizzato sul telefono degli utenti. Sono consentiti solo numeri degli Stati Uniti.

>[!NOTE]
>Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. Per questo motivo, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori lo invia sempre.

Nel Stati Uniti, se l'ID chiamante dell'autenticazione a più fattori non è stato configurato, le chiamate vocali da Microsoft provengono dai numeri seguenti: + 1 (866) 539 4191, + 1 (855) 330 8653 e + 1 (877) 668 6536. Se si usano i filtri di posta indesiderata, assicurarsi di escludere questi numeri.

### <a name="custom-voice-messages"></a>Messaggi vocali personalizzati

È possibile usare registrazioni o messaggi introduttivi personalizzati per la verifica in due passaggi con la funzionalità _messaggi vocali personalizzati_. Tali messaggi possono essere usati in aggiunta alle registrazioni di Microsoft o per sostituirle.

Prima di iniziare, tenere presente le seguenti restrizioni:

* I formati di file supportati sono WAV e MP3.
* La dimensione massima dei file è 5 MB.
* I messaggi di autenticazione devono avere una durata inferiore a 20 secondi. Se i messaggi durano più di 20 secondi, è possibile che la verifica abbia esito negativo. L'utente potrebbe non rispondere prima della fine del messaggio e la verifica potrebbe scadere.

### <a name="custom-message-language-behavior"></a>Comportamento per la lingua del messaggio personalizzato

Per la riproduzione di un messaggio vocale personalizzato per l'utente, la lingua del messaggio dipende da questi fattori:

* Lingua dell'utente corrente.
  * Lingua rilevata dal browser dell'utente.
  * Altri scenari di autenticazione possono presentare comportamenti diversi.
* Lingua degli eventuali messaggi personalizzati disponibili.
  * Questa lingua viene scelta dall'amministratore, quando viene aggiunto un messaggio personalizzato.

Ad esempio, se è presente un solo messaggio personalizzato, con la lingua tedesca:

* Un utente che esegue l'autenticazione in lingua tedesca ascolterà il messaggio personalizzato in tedesco.
* Il messaggio inglese standard verrà riprodotto per un utente che esegue l'autenticazione in lingua inglese.

### <a name="set-up-a-custom-message"></a>Configurare un messaggio personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Passare a **Azure Active Directory** > **MFA** > **Impostazioni telefonata**.
1. Selezionare **Aggiungi messaggio di saluto**.
1. Scegliere il tipo di messaggio introduttivo.
1. Scegliere la lingua.
1. Selezionare un file audio MP3 o WAV da caricare.
1. Selezionare **Aggiungi**.

### <a name="custom-voice-message-defaults"></a>Impostazioni predefinite del messaggio vocale personalizzato

Script di esempio per la creazione di messaggi personalizzati.

| Nome messaggio | Script |
| --- | --- |
| Autenticazione riuscita | La verifica dell'accesso è stata completata. Goodbye. |
| Richiesta di estensione | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per continuare. |
| Conferma illecito | È stato inviato un avviso di illecito. Per sbloccare l'account, contattare il help desk IT dell'azienda. |
| Messaggio introduttivo illecito (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. Se la verifica non è stata avviata, è possibile che qualcuno stia tentando di accedere all'account. Premere zero libbre per inviare un avviso di illecito. Questo invierà una notifica al team IT dell'azienda e bloccherà ulteriori tentativi di verifica. |
| È stato inviato un avviso di illecito. | Per sbloccare l'account, contattare il help desk IT dell'azienda. |
| Attivazione | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. |
| Tentativo di autenticazione negato | Verifica negata. |
| Nuovo tentativo (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. |
| Messaggio introduttivo (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. |
| Messaggio introduttivo (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Per completare la verifica, immettere il PIN seguito dal tasto cancelletto. |
| Messaggio introduttivo illecito (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft.  Per completare la verifica, immettere il PIN seguito dal tasto cancelletto. Se la verifica non è stata avviata, è possibile che qualcuno stia tentando di accedere all'account. Premere zero libbre per inviare un avviso di illecito. Questo invierà una notifica al team IT dell'azienda e bloccherà ulteriori tentativi di verifica. |
| Nuovo tentativo (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Per completare la verifica, immettere il PIN seguito dal tasto cancelletto. |
| Richiesta di estensione dopo le cifre | Se si è già in questa estensione, premere il tasto cancelletto per continuare. |
| Autenticazione negata | Non è possibile eseguire l'accesso in questo momento. Riprova più tardi. |
| Messaggio introduttivo di attivazione (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. |
| Tentativo di attivazione (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere il tasto cancelletto per completare la verifica. |
| Messaggio introduttivo di attivazione (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Per completare la verifica, immettere il PIN seguito dal tasto cancelletto. |
| Richiesta di estensione prima delle cifre | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Trasferire la chiamata all'estensione... |

## <a name="one-time-bypass"></a>Bypass monouso

La funzionalità _bypass monouso_ consente a un utente di eseguire l'autenticazione una sola volta senza eseguire la verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato. In situazioni in cui l'app per dispositivi mobili o il telefono non ricevono una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

### <a name="create-a-one-time-bypass"></a>Creare un bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **MFA** > **Bypass monouso**.
3. Selezionare **Aggiungi**.
4. Se necessario, selezionare il gruppo di replica per il bypass.
5. Immettere il nome utente **\@Domain.com**. Immettere il numero di secondi che indica la durata del bypass. Immettere il motivo del bypass.
6. Selezionare **Aggiungi**. Il limite di tempo diventa immediatamente effettivo. L'utente deve accedere prima della scadenza del bypass monouso.

### <a name="view-the-one-time-bypass-report"></a>Visualizzare il report del bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Azure Active Directory** > **MFA** > **Bypass monouso**.

## <a name="caching-rules"></a>Regole di memorizzazione nella cache

È possibile impostare un periodo di tempo per attivare i tentativi di autenticazione in seguito all'autenticazione dell'utente tramite la funzionalità di _memorizzazione nella cache_. I tentativi di autenticazione successivi dell'utente entro un periodo di tempo specifico hanno automaticamente esito positivo. La memorizzazione nella cache viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. Dopo che l'utente ha completato la prima verifica in corso, grazie a questa funzionalità le richieste successive hanno automaticamente esito positivo.

>[!NOTE]
>La funzionalità di memorizzazione nella cache non deve essere usata per accedere ad Azure Active Directory, ovvero Azure AD.

### <a name="set-up-caching"></a>Configurare la memorizzazione nella cache

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **MFA** > **Regole di memorizzazione nella cache**.
3. Selezionare **Aggiungi**.
4. Selezionare il **tipo di cache** nell'elenco a discesa. Immettere il numero massimo di **secondi cache**.
5. Se necessario, selezionare un tipo di autenticazione e specificare un'applicazione.
6. Selezionare **Aggiungi**.

## <a name="mfa-service-settings"></a>Impostazioni del servizio MFA

Le impostazioni per password delle app, IP attendibili, opzioni di verifica e memorizzazione dell'autenticazione a più fattori per Azure Multi-Factor Authentication sono disponibili nelle impostazioni del servizio. Per accedere alle impostazioni del servizio, è possibile usare il portale di Azure passando a **Azure Active Directory** > **MFA** > **Attività iniziali** > **Configura** > **Altre impostazioni di MFA basate sul cloud**.

![Impostazioni del servizio Azure Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Password app

Alcune applicazioni, come Office 2010 o versioni precedenti e Apple Mail precedenti a iOS 11 non supportano la verifica in due passaggi e non sono configurate per accettare una seconda verifica. Per usare queste applicazioni servirsi della funzionalità _password dell'app_. È possibile usare una password dell'app al posto della password tradizionali per consentire a un'app di ignorare la verifica in due passaggi e continuare a lavorare.

L'autenticazione moderna è supportata per i client di Microsoft Office 2013 e versioni successive. I client di Office 2013, tra cui Outlook, supportano i protocolli di autenticazione moderni e possono essere abilitati per la verifica in due passaggi. Dopo averlo abilitato, il client non richiede le password dell'app.

>[!NOTE]
>Le password dell'app non funzionano con i criteri di autenticazione a più fattori basati sull'accesso condizionale e l'autenticazione moderna.

### <a name="considerations-about-app-passwords"></a>Considerazioni sulle password dell'app

Quando si usano le password dell'app, prendere in considerazione questi punti importanti:

* Le password dell'app vengono immesse solo una volta per ogni applicazione. Gli utenti non sono tenuti a tenerne traccia o a immetterle ogni volta.
* La password effettiva viene generata automaticamente e non viene fornita dall'utente. Per un utente malintenzionato indovinare una password generata automaticamente è più difficile e quindi la sicurezza è maggiore.
* Il limite è di 40 password per utente.
* Le applicazioni che memorizzano le password nella cache e le usano in scenari locali possono potrebbero non funzionare perché la password dell'app non è nota all'esterno dell'account aziendale o dell'istituto di istruzione. Un esempio di questo scenario è rappresentato dai messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. In questo scenario non funziona la stessa password.
* Dopo l'abilitazione di Multi-Factor Authentication per un account utente, le password dell'app possono essere usate con la maggior parte dei client non basati su browser, ad esempio Outlook e Microsoft Skype for Business. Le azioni amministrative non possono essere eseguite usando le password dell'app tramite applicazioni non basate su browser, ad esempio Windows PowerShell. Tali azioni non possono essere eseguite anche se l'utente dispone di un account amministrativo. Per eseguire gli script di PowerShell, creare un account di servizio con una password complessa e non abilitare l'account per la verifica in due passaggi.

>[!WARNING]
>Le password delle app non funzionano in ambienti ibridi in cui i client comunicano con endpoint di individuazione automatica sia locali sia cloud. Le password del dominio sono necessarie per l'autenticazione locale. Le password dell'app sono necessarie per l'autenticazione con il cloud.

### <a name="guidance-for-app-password-names"></a>Indicazioni per i nomi delle password dell'app

Per le password dell'app è consigliabile usare nomi che riflettano il dispositivo in cui vengono usate. Se si usa un portatile con app non basate su browser come Outlook, Word ed Excel, creare una sola password dell'app denominata **Portatile** per queste app. Creare un'altra password dell'app denominata **Desktop** per le stesse applicazioni eseguite nel computer desktop.

>[!NOTE]
>È consigliabile creare una password dell'app per ogni dispositivo, anziché una password dell'app per ogni applicazione.

### <a name="federated-or-single-sign-on-app-passwords"></a>Password dell'app Single Sign-On o federate

Azure AD supporta la federazione o il Single Sign-On (SSO) con Active Directory Domain Services (AD DS) di Windows Server in locale. Se l'organizzazione è federata con Azure AD e si usa Azure Multi-Factor Authentication, tenere presente i punti seguenti sulle password dell'app.

>[!NOTE]
>I punti seguenti si applicano solo ai clienti federati (SSO).

* Le password dell'app vengono verificate da Azure AD e di conseguenza ignorano la federazione. La federazione viene usata attivamente solo durante l'impostazione delle password dell'app.
* Per gli utenti federati (SSO) non viene contattato il provider di identità (IdP), a differenza del flusso passivo. Le password dell'app vengono archiviate nell'account aziendale o dell'istituto di istruzione. Se un utente lascia l'azienda, le sue informazioni passano all'account aziendale o dell'istituto di istruzione tramite **DirSync** in tempo reale. La sincronizzazione in seguito alla disabilitazione o all'eliminazione dell'account può richiedere fino a tre ore e questo può ritardare la disabilitazione o l'eliminazione della password dell'app in Azure AD.
* Le impostazioni locali di Controllo di accesso client non vengono rispettate dalla funzionalità password dell'app.
* Per la funzionalità password dell'app non è possibile usare alcuna possibilità di registrazione o controllo dell'autenticazione in locale.
* Per alcune architetture avanzate è necessaria una combinazione di credenziali per la verifica in due passaggi con i client. Queste credenziali possono includere nome utente e password dell'account aziendale o dell'istituto di istruzione, oltre alle password dell'app. I requisiti dipendono dal modo in cui viene eseguita l'autenticazione. Per i client che si autenticano in un'infrastruttura locale, sono necessari un nome utente e una password dell'account aziendale o dell'istituto di formazione. Per i client che eseguono l'autenticazione in AD Azure, è necessaria la password dell'app.

  Ad esempio, si supponga di disporre dell'architettura seguente:

  * Si esegue la federazione dell'istanza locale di Active Directory con Azure AD.
  * Si usa Exchange Online.
  * Si usa Skype per Business locale.
  * Si usa Azure Multi-Factor Authentication.

  In questo scenario usare le credenziali seguenti:

  * Per accedere a Skype for Business, usare il nome utente e la password dell'account aziendale o dell'istituto di istruzione.
  * Per accedere alla rubrica da un client Outlook che si connette a Exchange Online, usare una password dell'app.

### <a name="allow-users-to-create-app-passwords"></a>Consentire agli utenti di creare password dell'app

Per impostazione predefinita, gli utenti non possono creare password dell'app. La funzionalità password dell'app deve essere abilitata. Per consentire agli utenti di creare password dell'app, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio** selezionare l'opzione **Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser**.

### <a name="create-app-passwords"></a>Creare password dell'app

Gli utenti possono creare password dell'app durante la registrazione iniziale. Alla fine del processo di registrazione l'utente può creare le password dell'app.

Gli utenti possono creare password delle app anche dopo la registrazione. Per altre informazioni e procedure dettagliate per gli utenti, vedere [Che cosa sono le password per le app in Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>IP attendibili

La funzionalità _Indirizzi IP attendibili_ di Azure Multi-Factor Authentication viene usata dagli amministratori di un tenant gestito o federato. La funzionalità consente di ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet aziendale. Questa funzionalità è disponibile nella versione completa di Azure Multi-Factor Authentication, ma non nella versione gratuita per amministratori. Per informazioni dettagliate su come ottenere la versione completa di Azure Multi-Factor Authentication, vedere [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Gli indirizzi IP attendibili a più fattori e i percorsi con accesso condizionale vengono usati solo con indirizzi IPV4.

Se l'organizzazione distribuisce l'estensione del server dei criteri di rete per garantire l'autenticazione a più fattori ad applicazioni locali, si prega di notare che gli indirizzi IP originali sembreranno sempre il server dei criteri di rete attraverso cui passa il tentativo di autenticazione.

| Tipo di tenant di Azure AD | Opzioni della funzionalità Indirizzi IP attendibili |
|:--- |:--- |
| Managed Disks |**Specific range of IP addresses** (Intervallo di indirizzi IP specifico): gli amministratori possono specificare un intervallo di indirizzi IP che può ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda.|
| Federato |**All Federated Users** (Tutti gli utenti federati): tutti gli utenti federati che eseguono l'accesso dall'interno dell'organizzazione possono ignorare la verifica in due passaggi. Gli utenti ignorano la verifica usando un'attestazione emessa da Active Directory Federation Services (AD FS).<br/>**Specific range of IP addresses** (Intervallo di indirizzi IP specifico): gli amministratori possono specificare un intervallo di indirizzi IP che può ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda. |

Il bypass degli indirizzi IP attendibili funziona solo dall'interno della rete Intranet aziendale. Se si seleziona l'opzione **All Federated Users** (Tutti gli utenti federati) solo tutti gli utenti federati e un utente accede dall'esterno della rete Intranet aziendale, tale utente deve eseguire l'autenticazione con la verifica in due passaggi. Il processo è lo stesso anche se l'utente presenta un'attestazione AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Esperienza dell'utente finale all'interno della rete aziendale

Se la funzionalità Indirizzi IP attendibili è disabilitata, per i flussi del browser è necessaria la verifica in due passaggi. Le password dell'app sono necessarie per applicazioni rich client meno recenti.

Se la funzionalità Indirizzi IP attendibili è abilitata, per i flussi del browser *non* è necessaria la verifica in due passaggi. Le password delle app *non* sono necessarie per le applicazioni rich client meno recenti, a condizione che l'utente non abbia creato una password dell'app. Quando si usa una password dell'app, questa rimane obbligatoria. 

### <a name="end-user-experience-outside-corpnet"></a>Esperienza dell'utente finale all'esterno della rete aziendale

Indipendentemente dal fatto che la funzionalità Indirizzi IP attendibili sia disabilitata, per i flussi del browser è necessaria la verifica in due passaggi. Le password dell'app sono necessarie per applicazioni rich client meno recenti.

### <a name="enable-named-locations-by-using-conditional-access"></a>Abilitare le località denominate tramite l'accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **sicurezza** > **l'accesso condizionale** > **località denominate**.
3. Selezionare **Nuova località**.
4. Immettere un nome per la località.
5. Selezionare **Contrassegna come posizione attendibile**.
6. Immettere l'intervallo di indirizzi IP nella notazione CIDR, ad esempio **192.168.1.1/24**.
7. Selezionare **Create** (Crea).

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Abilitare la funzionalità indirizzi IP attendibili tramite l'accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **sicurezza** >  **l'accesso condizionale** > **località denominate**.
3. Selezionare **Configura indirizzi IP attendibili MFA**.
4. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una delle due opzioni seguenti:

   * **Per le richieste degli utenti federati originate dalla Intranet dell'utente**: per scegliere questa opzione selezionare la casella di controllo. Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignorano la verifica in due passaggi usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Per le richieste da un intervallo specifico di IP pubblici**: per scegliere questa opzione immettere gli indirizzi IP nella casella di testo usando la notazione CIDR.
      * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
      * Per un singolo indirizzo IP, usare una notazione simile a **xxx.xxx.xxx.xxx/32**.
      * Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

5. Selezionare **Salva**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Abilitare la funzionalità Indirizzi IP attendibili tramite le impostazioni del servizio

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una o entrambe le opzioni seguenti:

   * **For requests from federated users on my intranet** (Per le richieste provenienti da utenti federati nella Intranet): per scegliere questa opzione selezionare la casella di controllo. Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignorano la verifica in due passaggi usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **For requests from a specified range of IP address subnets** (Per le richieste provenienti da un intervallo specifico di subnet di indirizzi IP): per scegliere questa opzione immettere gli indirizzi IP nella casella di testo usando la notazione CIDR.
      * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
      * Per un singolo indirizzo IP, usare una notazione simile a **xxx.xxx.xxx.xxx/32**.
      * Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

6. Selezionare **Salva**.

## <a name="verification-methods"></a>Metodi di verifica

È possibile scegliere i metodi di verifica disponibili per gli utenti. La tabella seguente offre una breve panoramica dei metodi.

Quando gli utenti registrano i propri account per Azure Multi-Factor Authentication, scelgono il metodo di verifica preferito tra le opzioni abilitate. Le linee guida per il processo di registrazione dell'utente sono disponibili in [Configurare l'account per la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metodo | Description |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non viene sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo contenente un codice di verifica. All'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso. Questo processo è denominato SMS unidirezionale. L'SMS bidirezionale significa che l'utente deve disporre il testo in un codice specifico. L'SMS bidirezionale è deprecato e non è supportato a partire dal 14 novembre 2018. In quella data gli utenti configurati per l'SMS bidirezionale passeranno automaticamente alla verifica _chiamata al telefono_.|
| Notifica tramite app per dispositivi mobili |Invia una notifica push al telefono o al dispositivo registrato. L'utente visualizza la notifica e seleziona **Verifica** per completare la verifica. L'app Microsoft Authenticator è disponibile per [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |L'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'app Microsoft Authenticator è disponibile per [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Abilitare e disabilitare i metodi di verifica

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **Opzioni di verifica** selezionare o deselezionare i metodi da mettere a disposizione per gli utenti.
6. Fare clic su **Salva**

Altre informazioni sull'uso dei metodi di autenticazione sono disponibili nell'articolo [Cosa si intende per metodi di autenticazione?](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Memorizza Multi-Factor Authentication

La funzionalità di _memorizzazione di Multi-Factor Authentication_ per dispositivi e browser considerati attendibili dall'utente è una funzionalità gratuita per tutti gli utenti di Multi-Factor Authentication. Gli utenti possono ignorare le verifiche successive per un numero specificato di giorni, dopo aver effettuato correttamente l'accesso in un dispositivo tramite Multi-Factor Authentication. Questa funzionalità permette di migliorare l'usabilità, riducendo al minimo il numero di volte in cui un utente deve eseguire la verifica in due passaggi nello stesso dispositivo.

>[!IMPORTANT]
>Se un dispositivo o un account viene compromesso, la memorizzazione di Multi-Factor Authentication per i dispositivi attendibili può influire sulla sicurezza. Se un account aziendale viene compromesso o un dispositivo attendibile viene smarrito o rubato, è necessario [revocare le sessioni](howto-mfa-userdevicesettings.md)di autenticazione a più fattori.
>
>L'azione di ripristino revoca lo stato di attendibilità di tutti i dispositivi e l'utente deve eseguire nuovamente la verifica in due passaggi. È possibile anche istruire gli utenti a ripristinare Multi-Factor Authentication sui propri dispositivi con le istruzioni disponibili in [Manage your settings for two-step verification](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) (Gestire le impostazioni per la verifica in due passaggi).

### <a name="how-the-feature-works"></a>Funzionamento della funzionalità

La funzionalità di memorizzazione di Multi-Factor Authentication imposta un cookie permanente nel browser quando l'utente seleziona l'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) al momento dell'accesso. L'utente non visualizza più la richiesta di Multi-Factor Authentication da questo browser fino alla scadenza del cookie. Se l'utente apre un altro browser sullo stesso dispositivo o cancella i cookie, la verifica verrà richiesta di nuovo.

L'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) non viene visualizzata nelle applicazioni non basate su browser, indipendentemente dal fatto che l'app supporti l'autenticazione moderna. Queste app usano _token di aggiornamento_ che creano nuovi token di accesso ogni ora. Quando un token di aggiornamento viene convalidato, Azure AD controlla che l'ultima verifica in due passaggi sia stata eseguita entro il numero di giorni specificato.

La funzionalità riduce il numero di autenticazioni per le app Web, che in genere viene richiesta ogni volta. Questa comporta però un aumento del numero di autenticazioni per i client di autenticazione moderna che in genere la richiedono ogni 90 giorni. Può anche aumentare il numero di autenticazioni in combinazione con i criteri di accesso condizionale.

>[!IMPORTANT]
>La funzionalità di **memorizzazione di Multi-Factor Authentication** non è compatibile con la funzionalità **mantieni l'accesso** di AD FS, quando gli utenti eseguono la verifica in due passaggi per AD FS tramite il server Azure Multi-Factor Authentication o una soluzione di autenticazione a più fattori di terze parti.
>
>Se si seleziona **Mantieni l'accesso** in AD FS e si contrassegna il dispositivo come attendibile per Multi-Factor Authentication, l'utente non esegue automaticamente la verifica quando terminano i giorni per la **memorizzazione di Multi-Factor Authentication**. Azure AD richiede una nuova verifica in due passaggi, ma AD FS restituisce un token con l'attestazione originale e la data di Multi-Factor Authentication invece di eseguire di nuovo la verifica in due passaggi. **Questa reazione attiva un ciclo di verifica tra Azure AD e AD FS.**
>
>La funzionalità **ricorda multi-factor authentication** non è compatibile con gli utenti B2B e non sarà visibile agli utenti B2B durante l'accesso ai tenant invitati.
>

### <a name="enable-remember-multi-factor-authentication"></a>Abilitare la funzionalità Memorizza Multi-Factor Authentication

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra selezionare **Azure Active Directory** > **Utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **manage remember multi-factor authentication** (Gestisci memorizzazione di Multi-Factor Authentication) selezionare l'opzione **Consenti agli utenti di memorizzare l'autenticazione a più fattori nei dispositivi attendibili**.
6. Impostare il numero di giorni per cui consentire ai dispositivi attendibili di ignorare la verifica in due passaggi. Il valore predefinito è 14 giorni.
7. Selezionare **Salva**.

### <a name="mark-a-device-as-trusted"></a>Contrassegnare un dispositivo come attendibile

Dopo aver abilitato la funzionalità Memorizza Multi-Factor Authentication, gli utenti possono contrassegnare un dispositivo come attendibile al momento dell'accesso selezionando **Non chiedere più**.

## <a name="next-steps"></a>Passaggi successivi

[Modify Azure AD sign-in page branding](../fundamentals/customize-branding.md) (Modificare la personalizzazione della pagina di accesso di Azure AD)
