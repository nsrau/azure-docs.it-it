---
title: Configurare Azure multi-Factor Authentication | Documenti Microsoft
description: In questo articolo viene descritto come configurare le impostazioni di Azure multi-Factor Authentication per i report, gli avvisi di illecito, bypass monouso, i messaggi vocali personalizzati, la memorizzazione nella cache, indirizzi IP attendibili e le password di app.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4421b995e69e115fbb6c7379af79aaef537aed0d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurare le impostazioni di Azure Multi-Factor Authentication

In questo articolo consente di gestire Azure multi-Factor Authentication ora che si è in esecuzione. L'articolo illustra diversi argomenti che permettono di ottenere il massimo da Azure Multi-Factor Authentication. Non tutte le funzionalità disponibili in ogni [versione di Azure multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Funzionalità | DESCRIZIONE | 
|:--- |:--- |
| [Bloccare e sbloccare utenti](#block-and-unblock-users) |La funzione Blocca/Sblocca utenti per impedire agli utenti di ricevere le richieste di autenticazione. |
| [Avviso di illecito](#fraud-alert) |Configurare la funzionalità di avviso di illecito, in modo che gli utenti possono segnalare fraudolenti tenta di accedere alle risorse. |
| [Bypass monouso](#one-time-bypass) |Utilizzare la funzione di bypass monouso per consentire agli utenti di eseguire l'autenticazione una sola volta da _ignorando_ multi-Factor Authentication. |
| [Messaggi vocali personalizzati](#custom-voice-messages) |Usare la funzionalità di messaggi vocali personalizzati per le registrazioni o i messaggi di saluto personalizzati multi-Factor Authentication. |
| [Memorizzazione nella cache](#caching-in-azure-multi-factor-authentication) |Utilizzare la funzionalità di memorizzazione per impostare un periodo di tempo specifico, in modo che i tentativi di autenticazione successivi esito positivo automaticamente. |
| [Indirizzi IP attendibili](#trusted-ips) |Gli amministratori di un tenant gestito o federato possono utilizzare la funzionalità indirizzi IP attendibili per ignorare la verifica in due passaggi per gli utenti che accedono dalla rete intranet aziendale. |
| [Password di App](#app-passwords) |Utilizzare la funzionalità di password di app per abilitare un'applicazione ignorare multi-Factor Authentication e continuare a lavorare. |
| [Ricordare di multi-Factor Authentication per i browser e dispositivi attendibili](#remember-multi-factor-authentication-for-trusted-devices) |Utilizzare questa funzionalità per ricordare di browser e dispositivi attendibili per un numero prefissato di giorni dopo che un utente ha firmato il nelle correttamente tramite multi-Factor Authentication. |
| [Metodi di verifica selezionabili](#selectable-verification-methods) |Utilizzare questa funzionalità per selezionare l'elenco di metodi di autenticazione che gli utenti sono in grado di utilizzare. |

## <a name="block-and-unblock-users"></a>Bloccare e sbloccare gli utenti

Utilizzare il _bloccare e sbloccare gli utenti_ funzionalità per impedire agli utenti di ricevere le richieste di autenticazione. Eventuali tentativi di autenticazione per gli utenti bloccati vengono negati automaticamente. Gli utenti rimangono bloccati per 90 giorni dal momento in cui sono bloccati.

### <a name="block-a-user"></a>Bloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Aggiungi** per bloccare un utente.
4. Selezionare il **gruppo di replica**. Immettere il nome utente per l'utente bloccato come **username<span></span>@domain.com**. Immettere un commento nel **motivo** campo.
5. Selezionare **Aggiungi** per completare il blocco dell'utente.

### <a name="unblock-a-user"></a>Sbloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Unblock** nel **azione** colonna accanto all'utente di sblocco.
4. Immettere un commento nel campo **Motivo dello sblocco**.
5. Selezionare **Unblock** per completare lo sblocco dell'utente.

## <a name="fraud-alert"></a>Avviso di illecito

Configurare il _avviso illecito_ funzionalità in modo che gli utenti possono segnalare fraudolenti tenta di accedere alle risorse. Gli utenti possono segnalare l'illecito tentativi tramite l'app mobile o tramite telefono.

### <a name="turn-on-fraud-alerts"></a>Attivare gli avvisi di illecito

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **avviso illecito**.

   ![Attivare gli avvisi di illecito](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Impostare il **consentire agli utenti di inviare avvisi di illeciti** impostando su **su**.
4. Selezionare **Salva**.

### <a name="configuration-options"></a>Opzioni di configurazione

- **Blocca utente se viene segnalato un illecito**: se un utente segnala illecito, l'account è bloccato per 90 giorni o fino a quando un amministratore Sblocca il proprio account. Un amministratore può esaminare accessi utilizzando il report di accesso e intraprendere l'azione appropriata per prevenire le frodi future. Un amministratore può quindi [sbloccare](#unblock-a-user) l'account dell'utente.
- **Codice per segnalare illeciti durante il messaggio introduttivo iniziale**: quando gli utenti ricevono una chiamata telefonica per eseguire la verifica in due passaggi, sono in genere premere  **#**  per confermare le Accedi. Per segnalare gli illeciti, l'utente immette un codice prima di premere  **#** . Il codice predefinito è **0**, ma è possibile personalizzarlo.

  >[!NOTE]
  >I messaggi di saluto vocali predefinito da Microsoft indicare agli utenti di premere **# 0** per inviare un avviso di illecito. Se si desidera utilizzare un codice diverso da **0**, registrare e caricare i propri messaggi vocali personalizzati con le istruzioni appropriate per gli utenti.
  >

### <a name="view-fraud-reports"></a>Visualizzare le segnalazioni di illeciti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **accessi**. Il report illecito fa ora parte del report di Azure AD accessi standard.

## <a name="one-time-bypass"></a>Bypass monouso

Il _bypass monouso_ funzionalità consente agli utenti di eseguire l'autenticazione una sola volta senza eseguire una verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato. In situazioni in cui un telefono o app per dispositivi mobili non riceve una notifica o telefonata, è possibile consentire un bypass monouso, pertanto l'utente può accedere alla risorsa desiderata.

### <a name="create-a-one-time-bypass"></a>Creare un bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **bypass monouso**.

   ![Creare un bypass monouso](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Selezionare **Aggiungi**.
4. Se necessario, selezionare il gruppo di replica per il bypass.
5. Immettere il nome utente come **username<span></span>@domain.com**. Immettere il numero di secondi che deve durare il bypass. Immettere il motivo del bypass. 
6. Selezionare **Aggiungi**. Il limite di tempo diventa immediatamente effettiva. L'utente deve accedere prima che scada il bypass monouso. 

### <a name="view-the-one-time-bypass-report"></a>Visualizzare il report del bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Active Directory** > **Server MFA** > **bypass monouso**.

## <a name="custom-voice-messages"></a>Messaggi vocali personalizzati
È possibile utilizzare le registrazioni o i messaggi di saluto personalizzati per la verifica in due passaggi con il _messaggi vocali personalizzati_ funzionalità. Questi messaggi possono essere utilizzati inoltre per o sostituire le registrazioni di Microsoft.

Prima di iniziare, tenere presente le restrizioni seguenti:

* I formati di file supportati sono WAV e MP3.
* La dimensione massima dei file è 5 MB.
* I messaggi di autenticazione devono avere una durata inferiore a 20 secondi. I messaggi che sono più di 20 secondi possono causare la verifica di avere esito negativo. L'utente potrebbe non rispondere prima della fine del messaggio e la verifica del timeout.

### <a name="set-up-a-custom-message"></a>Configurare un messaggio personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **impostazioni telefonata**.

   ![Registrare i messaggi di telefono personalizzato](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selezionare **Aggiungi messaggio di saluto**.
4. Scegliere il tipo di messaggio introduttivo. Scegliere la lingua.
5. Selezionare un file audio MP3 o WAV da caricare.
6. Selezionare **Aggiungi**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Memorizzazione nella cache in Azure Multi-Factor Authentication

È possibile impostare un periodo di tempo per consentire i tentativi di autenticazione, dopo aver autenticato un utente tramite il _la memorizzazione nella cache_ funzionalità. Tentativi di autenticazione successivi per l'utente entro il periodo di tempo completata automaticamente. La memorizzazione nella cache viene utilizzato principalmente per i sistemi locali, ad esempio VPN, inviano più richieste di verifica durante la prima richiesta è ancora in corso. Questa funzionalità consente le richieste successive completare automaticamente, dopo che l'utente ha esito positivo prima verifica in corso. 

>[!NOTE]
>La funzionalità di memorizzazione nella cache non deve essere utilizzato per accessi ad Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Configurare la memorizzazione nella cache 

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory** > **Server MFA** > **la memorizzazione nella cache regole**.

   ![Impostare le regole di memorizzazione nella cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Selezionare **Aggiungi**.
4. Selezionare il **tipo di cache** dall'elenco a discesa. Immettere il numero massimo di **secondi cache**. 
5. Se necessario, selezionare un tipo di autenticazione e specificare un'applicazione. 
6. Selezionare **Aggiungi**.


## <a name="trusted-ips"></a>Indirizzi IP attendibili

Il _gli indirizzi IP attendibili_ funzionalità di Azure multi-Factor Authentication viene usato dagli amministratori di un tenant gestito o federato. La funzionalità consente di ignorare la verifica in due passaggi per gli utenti che accedono dalla rete intranet aziendale. La funzionalità è disponibile con la versione completa di Azure multi-Factor Authentication e non la versione gratuita per gli amministratori. Per informazioni dettagliate su come ottenere la versione completa di Azure Multi-Factor Authentication, vedere [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Tipo di tenant Azure Active Directory | Opzioni di funzionalità di indirizzi IP attendibili |
|:--- |:--- |
| Gestito |**Specifico intervallo di indirizzi IP**: gli amministratori di specificare un intervallo di indirizzi IP che è possano ignorare la verifica in due passaggi per gli utenti che accedono dalla rete intranet aziendale.|
| Federato |**Tutti gli utenti federati**: tutti gli utenti federati che accedono dall'interno dell'organizzazione possono ignorare la verifica in due passaggi. Gli utenti di ignorare la verifica del usando un'attestazione che viene emesso da Active Directory Federation Services (ADFS).<br/>**Specifico intervallo di indirizzi IP**: gli amministratori di specificare un intervallo di indirizzi IP che è possano ignorare la verifica in due passaggi per gli utenti che accedono dalla rete intranet aziendale. |

Gli indirizzi IP attendibili ignorare funziona solo da all'interno della rete intranet aziendale. Se si seleziona il **tutti gli utenti federati** opzione e un utente accede all'esterno della rete intranet aziendale, l'utente deve eseguire l'autenticazione usando verifica in due passaggi. Il processo è lo stesso anche se presenta un'attestazione ADFS. 

**Esperienza dell'utente finale all'interno della rete aziendale**

Quando la funzionalità indirizzi IP attendibili è disabilitata, è necessaria per i flussi dei browser la verifica in due passaggi. Le password dell'App sono necessari ad applicazioni rich client meno recenti. 

Quando è abilitata la funzionalità indirizzi IP attendibili, verifica in due passaggi è *non* necessari per i flussi dei browser. Le password di app *non* necessari ad applicazioni rich client meno recenti, condizione che l'utente non è stata creata una password di app. Dopo che una password di app è in uso, la password rimane obbligatorio. 

**Esperienza dell'utente finale di fuori della rete aziendale**

Indipendentemente dal fatto se è abilitata la funzionalità di indirizzi IP attendibili, è necessaria per i flussi dei browser la verifica in due passaggi. Le password dell'App sono necessari ad applicazioni rich client meno recenti. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Abilitare percorsi denominate con accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **accesso condizionale** > **posizioni denominate**.
3. Selezionare **nuova posizione**.
4. Immettere un nome per il percorso.
5. Selezionare **contrassegna come percorso attendibile**.
6. Immettere l'intervallo di IP in notazione CIDR, ad esempio **192.168.1.1/24**.
7. Selezionare **Create**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Abilitare la funzionalità indirizzi IP attendibili utilizzando l'accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **accesso condizionale** > **posizioni denominate**.
3. Selezionare **MFA configurare indirizzi IP attendibili**.
4. Nel **le impostazioni del servizio** pagina **gli indirizzi IP attendibili**, scegliere tra le due opzioni seguenti:
   
   * **Per le richieste degli utenti federati originate dalla intranet dell'utente**: per scegliere questa opzione, selezionare la casella di controllo. Gli utenti accesso dalla rete aziendale ignora la verifica in due passaggi usando un'attestazione che viene rilasciata da ADFS tutti federati. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la seguente regola in ADFS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Per le richieste da un determinato intervallo di indirizzi IP pubblici**: per scegliere questa opzione, immettere gli indirizzi IP nella casella di testo utilizzando la notazione CIDR.
   
     * Per gli indirizzi IP in xxx.xxx.xxx.1 l'intervallo tramite xxx.xxx.xxx.254, utilizzare la notazione come **xxx.xxx.xxx.0/24**.
     * Per un singolo indirizzo IP, utilizzare la notazione come **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Immettere un massimo di 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

5. Selezionare **Salva**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Abilitare la funzionalità indirizzi IP attendibili utilizzando le impostazioni di servizio

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nel **le impostazioni del servizio** pagina **gli indirizzi IP attendibili**, scegliere tra le due opzioni seguenti:
   
   * **Per le richieste dagli utenti federati nella intranet**: per scegliere questa opzione, selezionare la casella di controllo. Gli utenti accesso dalla rete aziendale ignora la verifica in due passaggi usando un'attestazione che viene rilasciata da ADFS tutti federati. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la seguente regola in ADFS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Per le richieste provenienti da una subnet di indirizzi specificato intervallo di indirizzi IP**: per scegliere questa opzione, immettere gli indirizzi IP nella casella di testo utilizzando la notazione CIDR. 
     
     * Per gli indirizzi IP in xxx.xxx.xxx.1 l'intervallo tramite xxx.xxx.xxx.254, utilizzare la notazione come **xxx.xxx.xxx.0/24**.
     * Per un singolo indirizzo IP, utilizzare la notazione come **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Immettere un massimo di 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

6. Selezionare **Salva**.

![Abilitare gli indirizzi IP attendibili con le impostazioni del servizio](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Password dell'app

Alcune applicazioni, ad esempio Office 2010 o versioni precedenti e Apple Mail, non supportano la verifica in due passaggi. Le app non sono configurate per accettare una verifica di secondo. Per l'utilizzo di queste applicazioni possono sfruttare il _le password di app_ funzionalità. È possibile utilizzare una password dell'app al posto della password tradizionali per consentire un'app ignorare la verifica in due passaggi e continuare a lavorare.

>[!NOTE]
>Autenticazione moderna per i client di Microsoft Office 2013 e versioni successive
> 
>I client di Office 2013 e versioni successive (incluso Outlook), supportano protocolli di autenticazione moderna ed è possibile abilitare per lavorare con la verifica. Dopo che il client è abilitato, le password di app non sono necessari per il client. Per altre informazioni, vedere l' [annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>

### <a name="considerations-about-app-passwords"></a>Considerazioni sulle password di app

Quando si utilizzano le password di app, prendere in considerazione i seguenti punti importanti:

* Le password di App vengono immesse solo una volta per ogni applicazione. Gli utenti non sono necessario tenere traccia delle password o immetterle ogni volta.
* La password effettiva viene generata automaticamente e non viene fornita dall'utente. Per un utente malintenzionato indovinare una password generata automaticamente è più difficile e quindi la sicurezza è maggiore.
* Il limite è di 40 password per utente. 
* Le applicazioni che memorizzano nella cache le password e usati in scenari locali possono avviare l'esito negativo perché la password dell'app non è noto di fuori l'account aziendale o dell'istituto di istruzione. Un esempio di questo scenario è posta elettronica di Exchange in locale, ma la posta archiviata è nel cloud. In questo scenario, la stessa password non funziona.
* Dopo aver abilitato multi-Factor Authentication su un account utente, password di app sono utilizzabile con i client più non basate su browser come Outlook e Microsoft Skype for Business. Impossibile eseguire azioni amministrative usando password di app tramite applicazioni non basate su browser, ad esempio Windows PowerShell. Impossibile eseguire le azioni anche quando l'utente dispone di un account amministrativo. Per eseguire gli script di PowerShell, creare un account di servizio con una password complessa e non abilitare l'account per la verifica in due passaggi.

>[!WARNING]
>Le password di app non funzionano in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud. Le password del dominio sono necessari per l'autenticazione locale. Sono necessarie password di App per l'autenticazione con il cloud.
>

### <a name="guidance-for-app-password-names"></a>Linee guida per i nomi delle password di app

I nomi delle password di App in base al dispositivo in cui vengono utilizzati. Se si dispone di un computer portatile con applicazioni non basate su browser come Outlook, Word ed Excel, creare una password dell'app denominata **portatile** per queste app. Creare un'altra password dell'app denominata **Desktop** per le stesse applicazioni eseguite in computer desktop. 

>[!NOTE]
>È consigliabile creare una password per ogni dispositivo, anziché la password di un'app per ogni applicazione.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federati o single sign-on le password dell'app

Azure AD supporta la federazione o single sign-on (SSO), con locale Windows Server Active Directory Domain Services (AD DS). Se l'organizzazione è federata con Azure AD e si usa Azure multi-Factor Authentication, considerare gli aspetti seguenti sulle password di app.

>[!NOTE]
>I punti seguenti si applicano solo ai clienti di federati (SSO).

* Le password dell'App vengono verificate da Azure AD e ignorare di conseguenza, la federazione. Federazione viene utilizzata attivamente solo quando si imposta password di app.
* Il Provider di identità (IdP) non è stato contattato utenti federati (SSO), a differenza del flusso passivo. La password dell'app vengono archiviate nell'account aziendale o dell'istituto di istruzione. Se un utente lascia la società, le informazioni dell'utente passano all'account aziendale o dell'istituto di istruzione tramite **DirSync** in tempo reale. La disabilitazione o eliminazione dell'account può richiedere fino a tre ore per la sincronizzazione, che possono ritardare la disabilitazione o eliminazione della password di app in Azure AD.
* Le impostazioni locali di controllo di accesso client non sono accettate dalla funzionalità di password di app.
* Nessuna autenticazione locale funzionalità di registrazione o controllo è disponibile per l'utilizzo con la funzionalità di password di app.
* Alcune architetture avanzate richiedono una combinazione di credenziali per la verifica in due passaggi con i client. Queste credenziali possono includere un lavoro o scuola account username e password password dell'app. I requisiti dipendono dal modo in cui viene eseguita l'autenticazione. Per i client per autenticare un'infrastruttura locale, un account aziendale o dell'istituto di istruzione nome utente e la password necessaria. Per i client che eseguono l'autenticazione con Azure AD, è necessario una password di app.

  Ad esempio, si supponga di che disporre l'architettura seguente:

  * L'istanza locale di Active Directory è federato con Azure AD.
  * Si usa Exchange online.
  * Si sta utilizzando Skype per Business locale.
  * Si usa Azure multi-Factor Authentication.

  ![Utilizzo di password di app in un'organizzazione con federazione](./media/multi-factor-authentication-whats-next/federated.png)

  In questo scenario, utilizzare le seguenti credenziali:

  * Per accedere a Skype for Business, utilizzare il nome di account utente dell'istituto di istruzione e la password aziendali.
  * Per accedere alla Rubrica da un client di Outlook che si connette a Exchange online, utilizzare una password di app.

### <a name="allow-users-to-create-app-passwords"></a>Consentire agli utenti di creare password dell'app

Per impostazione predefinita, gli utenti non è possibile creare password di app. La funzionalità di password di app deve essere abilitata. Per concedere agli utenti la possibilità di creare password dell'app, utilizzare la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nel **le impostazioni del servizio** pagina, selezionare il **consentire agli utenti di creare password dell'app per accedere ad App non basate su browser** opzione.

   ![Consentire agli utenti di creare password dell'app](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Creare password dell'app

Gli utenti possono creare password dell'app durante la registrazione iniziale. L'utente ha la possibilità di creare password dell'app alla fine del processo di registrazione.

Gli utenti possono creare password delle app anche dopo la registrazione. Le password di app possono essere modificate mediante le impostazioni nel portale di Azure o il portale di Office 365. Per ulteriori informazioni e procedure dettagliate per gli utenti, vedere [quali sono le password di app in Azure multi-Factor Authentication?](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Ricordare di multi-Factor Authentication per dispositivi attendibili
Il _ricordare multi-Factor Authentication_ funzionalità per i dispositivi e browser che sono considerati attendibili dall'utente è una funzionalità disponibile gratuitamente per tutti gli utenti di multi-Factor Authentication. Gli utenti possono ignorare il controllo a posteriori per un numero specificato di giorni, dopo che hai correttamente firmato in un dispositivo tramite multi-Factor Authentication. La funzionalità migliora l'usabilità, riducendo il numero di volte in cui che un utente deve eseguire la verifica sullo stesso dispositivo.

>[!IMPORTANT]
>Se un dispositivo o l'account viene compromesso, ricordare di multi-Factor Authentication per dispositivi attendibili possono influire sulla sicurezza. Se viene compromesso un account aziendale o un dispositivo attendibile viene smarrito o rubato, è necessario [ripristinare Multi-Factor Authentication su tutti i dispositivi](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>L'azione di ripristino revoca lo stato attendibile da tutti i dispositivi e all'utente viene richiesto di eseguire di nuovo la verifica in due passaggi. È anche possibile indicare agli utenti di ripristinare multi-Factor Authentication sui propri dispositivi mobili con le istruzioni in [gestire le impostazioni per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Il funzionamento

La funzionalità di multi-Factor Authentication tenere presente che imposta un cookie permanente nel browser quando un utente seleziona il **non chiedere più per X giorni** opzione al momento dell'accesso. L'utente non è richiesto nuovamente multi-Factor Authentication da tale browser stesso fino alla scadenza del cookie. Se l'utente apre un browser diverso sullo stesso dispositivo o Cancella i cookie, è richiesto nuovamente di verificare. 

Il **non chiedere più per X giorni** opzione non è visualizzata nelle applicazioni non basate su browser, indipendentemente dal fatto che l'app supporta l'autenticazione moderna. Usano queste app _token di aggiornamento_ che forniscono nuovi token di accesso di ogni ora. Quando un token di aggiornamento viene convalidato, Azure AD verifica l'ultima verifica in due passaggi che si è verificato entro il numero di giorni specificato. 

La funzionalità riduce il numero di autenticazioni per le app web, che in genere richiesta ogni volta. La funzionalità aumenta il numero di autenticazioni per i client di autenticazione moderna che richiedono in genere ogni 90 giorni.

>[!IMPORTANT]
>Il **ricordare multi-Factor Authentication** funzionalità non è compatibile con il **Mantieni l'accesso** funzionalità di AD FS, quando gli utenti eseguono verifica in due passaggi per ADFS tramite Azure multi-Factor Autenticazione Server o una soluzione di autenticazione a più fattori di terze parti.
>
>Se gli utenti selezionano **Mantieni l'accesso** in AD FS e contrassegna il dispositivo come attendibili per l'autenticazione a più fattori, l'utente non viene verificato automaticamente dopo il **ricordare multi-factor authentication**numero di giorni di scadenza. Azure AD richiede una verifica di nuovo in due passaggi, ma AD FS restituisce un token con l'attestazione di multi-Factor Authentication originale e data, piuttosto che verifica in due passaggi verrà eseguito nuovamente. Questa risposta viene attivato un ciclo di verifica tra Azure Active Directory e AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Ricordare di abilitare multi-Factor Authentication

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nel **le impostazioni del servizio** nella pagina **gestire ricordare multi-factor authentication**, selezionare il **consentire agli utenti di ricordare multi-factor authentication sui dispositivi devono considerare attendibili**opzione.

   ![Ricordare di multi-Factor Authentication per dispositivi attendibili](./media/multi-factor-authentication-whats-next/remember.png)

6. Impostare il numero di giorni per consentire ai dispositivi attendibili ignorare la verifica in due passaggi. Il valore predefinito è 14 giorni.
7. Selezionare **Salva**.

### <a name="mark-a-device-as-trusted"></a>Contrassegnare un dispositivo come attendibile

Dopo aver abilitato la funzionalità di multi-Factor Authentication tenere presente che, gli utenti possono contrassegnare un dispositivo come attendibile quando accedi selezionando **non chiedere più**.

![Selezionare "Non chiedere più" per i dispositivi attendibili](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Metodi di verifica selezionabili

È possibile scegliere i metodi di verifica che siano disponibili per gli utenti utilizzando il _metodi di verifica selezionabile_ funzionalità. Nella tabella seguente fornisce una breve panoramica dei metodi.

Quando gli utenti registrano i propri account Azure multi-Factor Authentication, sceglieranno il metodo di verifica preferito tra le opzioni che è stata abilitata. Vengono fornite istruzioni per il processo di registrazione utente [Imposta account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

| Metodo | DESCRIZIONE |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non è sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo che contiene un codice di verifica. All'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso. Questo processo è denominato SMS unidirezionale. L'SMS bidirezionale significa che l'utente deve disporre il testo in un codice specifico. SMS bidirezionale è deprecato e non è supportata dopo 14 novembre 2018. Gli utenti che sono configurati per il SMS bidirezionale viene automaticamente attivato _chiamata al telefono_ verifica in quel momento.|
| Notifica tramite app per dispositivi mobili |Invia una notifica push al telefono o al dispositivo registrato. L'utente visualizza la notifica e seleziona **Verifica** per completare la verifica. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili |L'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Abilitare e disabilitare i metodi di verifica

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nel **le impostazioni del servizio** pagina **le opzioni di verifica**, selezionare/deselezionare i metodi per fornire agli utenti.

   ![Selezionare i metodi di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Fare clic su **Save**.
