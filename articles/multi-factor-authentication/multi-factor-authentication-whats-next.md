---
title: Configurare Azure Multi-Factor Authentication | Microsoft Docs
description: Questo articolo descrive come configurare le impostazioni di Azure Multi-Factor Authentication per report, avvisi di illecito, bypass monouso, messaggi vocali personalizzati, memorizzazione nella cache, indirizzi IP attendibili e password dell'app.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurare le impostazioni di Azure Multi-Factor Authentication

Questo articolo consente di gestire Azure Multi-Factor Authentication quando l'utente è operativo. L'articolo illustra diversi argomenti che permettono di ottenere il massimo da Azure Multi-Factor Authentication. Non tutte le funzionalità sono disponibili in ogni [versione di Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Funzionalità | DESCRIZIONE | 
|:--- |:--- |
| [Bloccare e sbloccare utenti](#block-and-unblock-users) |Usare la funzionalità per bloccare/sbloccare utenti per evitare che gli utenti ricevano le richieste di autenticazione. |
| [Avviso di illecito](#fraud-alert) |Configurare la funzionalità di avviso di illecito in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse. |
| [Bypass monouso](#one-time-bypass) |Usare la funzionalità di bypass monouso per consentire a un utente di eseguire l'autenticazione una sola volta _ignorando_ Multi-Factor Authentication. |
| [Messaggi vocali personalizzati](#custom-voice-messages) |Usare la funzionalità messaggi vocali personalizzati per usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. |
| [Memorizzazione nella cache](#caching-in-azure-multi-factor-authentication) |Usare la funzionalità di memorizzazione nella cache per impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. |
| [Indirizzi IP attendibili](#trusted-ips) |Gli amministratori di un tenant federato o gestito possono usare la funzionalità di IP attendibili per ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda. |
| [Password dell'app](#app-passwords) |Usare la funzionalità password dell'app per consentire a un'applicazione di ignorare Multi-Factor Authentication e continuare a lavorare. |
| [Memorizzare Multi-Factor Authentication per dispositivi e browser attendibili](#remember-multi-factor-authentication-for-trusted-devices) |Usare questa funzionalità per memorizzare dispositivi e browser attendibili per un determinato numero di giorni dopo che un utente ha eseguito correttamente l'accesso tramite Multi-Factor Authentication. |
| [Metodi di verifica selezionabili](#selectable-verification-methods) |Usare questa funzionalità per selezionare l'elenco di metodi di autenticazione che gli utenti possono usare. |

## <a name="block-and-unblock-users"></a>Bloccare e sbloccare utenti

Usare la funzionalità per _bloccare/sbloccare utenti_ per evitare che gli utenti ricevano le richieste di autenticazione. Eventuali tentativi di autenticazione per gli utenti bloccati vengono negati automaticamente. Gli utenti restano bloccati per 90 giorni dal momento in cui vengono bloccati.

### <a name="block-a-user"></a>Bloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Aggiungi** per bloccare un utente.
4. Selezionare il **Gruppo di replica**. Immettere il nome utente per l'utente bloccato come **nome utente<span></span>@domain.com**. Immettere un commento nel campo**Motivo**.
5. Selezionare **Aggiungi** per completare il blocco dell'utente.

### <a name="unblock-a-user"></a>Sbloccare un utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Blocca/Sblocca utenti**.
3. Selezionare **Sblocca** nella colonna **Azione** accanto all'utente che si desidera sbloccare.
4. Immettere un commento nel campo **Motivo dello sblocco**.
5. Selezionare **Sblocca** per completare lo sblocco dell'utente.

## <a name="fraud-alert"></a>Avviso di illecito

Configurare la funzionalità di _avviso di illecito_ in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle proprie risorse. Gli utenti possono segnalare i tentativi illeciti usando l'app per dispositivi mobili o il telefono.

### <a name="turn-on-fraud-alerts"></a>Attivare gli avvisi di illecito

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Avviso di illecito**.

   ![Attivare gli avvisi di illecito](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Impostare l'impostazione **Consenti agli utenti di inviare avvisi di illeciti** su **Attivo**.
4. Selezionare **Salva**.

### <a name="configuration-options"></a>Opzioni di configurazione

- **Blocca utente se viene segnalato un illecito**: se un utente segnala un illecito, il suo account viene bloccato per 90 giorni o finché l'amministratore non lo sblocca. Un amministratore può verificare gli accessi usando il report sugli accessi ed eseguire le azioni necessarie per prevenire illeciti nel futuro. Un amministratore può quindi [sbloccare](#unblock-a-user) l'account dell'utente.
- **Codice per la segnalazione di illeciti durante il messaggio introduttivo iniziale**: quando gli utenti ricevono una chiamata telefonica per eseguire la verifica in due passaggi, in genere premono **#** per confermare l'accesso. Per segnalare un illecito, l'utente immette un codice prima di premere **#**. Il codice predefinito è **0**, ma è possibile personalizzarlo.

  >[!NOTE]
  >I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere **0#** per inviare un avviso di illecito. Se si vuole usare un codice diverso da **0**, registrare e caricare messaggi vocali personalizzati con istruzioni adeguate per l'utente.
  >

### <a name="view-fraud-reports"></a>Visualizzare le segnalazioni di illeciti

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **Accessi**. Il report di illecito fa ora parte del report standard di Azure AD relativo agli accessi.

## <a name="one-time-bypass"></a>Bypass monouso

La funzionalità _bypass monouso_ consente a un utente di eseguire l'autenticazione una sola volta senza eseguire la verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato. In situazioni in cui l'app per dispositivi mobili o il telefono non ricevono una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

### <a name="create-a-one-time-bypass"></a>Creare un bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Bypass monouso**.

   ![Creare un bypass monouso](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Selezionare **Aggiungi**.
4. Se necessario, selezionare il gruppo di replica per il bypass.
5. Immettere il nome utente come **nome utente<span></span>@domain.com**. Immettere il numero di secondi che indica la durata del bypass. Immettere il motivo del bypass. 
6. Selezionare **Aggiungi**. Il limite di tempo diventa immediatamente effettivo. L'utente deve accedere prima della scadenza del bypass monouso. 

### <a name="view-the-one-time-bypass-report"></a>Visualizzare il report del bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare ad **Active Directory** > **Server MFA** > **Bypass monouso**.

## <a name="custom-voice-messages"></a>Messaggi vocali personalizzati
È possibile usare registrazioni o messaggi introduttivi personalizzati per la verifica in due passaggi con la funzionalità _messaggi vocali personalizzati_. Tali messaggi possono essere usati in aggiunta alle registrazioni di Microsoft o per sostituirle.

Prima di iniziare, tenere presente le seguenti restrizioni:

* I formati di file supportati sono WAV e MP3.
* La dimensione massima dei file è 5 MB.
* I messaggi di autenticazione devono avere una durata inferiore a 20 secondi. Se i messaggi durano più di 20 secondi, è possibile che la verifica abbia esito negativo. L'utente potrebbe non rispondere prima della fine del messaggio e la verifica potrebbe scadere.

### <a name="set-up-a-custom-message"></a>Configurare un messaggio personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Impostazioni telefonata**.

   ![Registrare i messaggi telefonici personalizzati](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selezionare **Aggiungi messaggio di saluto**.
4. Scegliere il tipo di messaggio introduttivo. Scegliere la lingua.
5. Selezionare un file audio MP3 o WAV da caricare.
6. Selezionare **Aggiungi**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Memorizzazione nella cache in Azure Multi-Factor Authentication

È possibile impostare un periodo di tempo per attivare i tentativi di autenticazione in seguito all'autenticazione dell'utente tramite la funzionalità di _memorizzazione nella cache_. I tentativi di autenticazione successivi dell'utente entro un periodo di tempo specifico hanno automaticamente esito positivo. La memorizzazione nella cache viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. Dopo che l'utente ha completato la prima verifica in corso, grazie a questa funzionalità le richieste successive hanno automaticamente esito positivo. 

>[!NOTE]
>La funzionalità di memorizzazione nella cache non deve essere usata per accedere ad Azure Active Directory, ovvero Azure AD.

### <a name="set-up-caching"></a>Configurare la memorizzazione nella cache 

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Server MFA** > **Regole di memorizzazione nella cache**.

   ![Configurare le regole di memorizzazione nella cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Selezionare **Aggiungi**.
4. Selezionare il **tipo di cache** nell'elenco a discesa. Immettere il numero massimo di **secondi cache**. 
5. Se necessario, selezionare un tipo di autenticazione e specificare un'applicazione. 
6. Selezionare **Aggiungi**.


## <a name="trusted-ips"></a>Indirizzi IP attendibili

La funzionalità _Indirizzi IP attendibili_ di Azure Multi-Factor Authentication viene usata dagli amministratori di un tenant gestito o federato. La funzionalità consente di ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet aziendale. Questa funzionalità è disponibile nella versione completa di Azure Multi-Factor Authentication, ma non nella versione gratuita per amministratori. Per informazioni dettagliate su come ottenere la versione completa di Azure Multi-Factor Authentication, vedere [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Tipo di tenant di Azure AD | Opzioni della funzionalità Indirizzi IP attendibili |
|:--- |:--- |
| Gestito |**Specific range of IP addresses** (Intervallo di indirizzi IP specifico): gli amministratori possono specificare un intervallo di indirizzi IP che può ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda.|
| Federato |**All Federated Users** (Tutti gli utenti federati): tutti gli utenti federati che eseguono l'accesso dall'interno dell'organizzazione possono ignorare la verifica in due passaggi. Gli utenti ignorano la verifica usando un'attestazione emessa da Active Directory Federation Services (AD FS).<br/>**Specific range of IP addresses** (Intervallo di indirizzi IP specifico): gli amministratori possono specificare un intervallo di indirizzi IP che può ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda. |

Il bypass degli indirizzi IP attendibili funziona solo dall'interno della rete Intranet aziendale. Se si seleziona l'opzione **All Federated Users** (Tutti gli utenti federati) solo tutti gli utenti federati e un utente accede dall'esterno della rete Intranet aziendale, tale utente deve eseguire l'autenticazione con la verifica in due passaggi. Il processo è lo stesso anche se l'utente presenta un'attestazione AD FS. 

**Esperienza dell'utente finale all'interno della rete aziendale**

Se la funzionalità Indirizzi IP attendibili è disabilitata, per i flussi del browser è necessaria la verifica in due passaggi. Le password dell'app sono necessarie per applicazioni rich client meno recenti. 

Se la funzionalità Indirizzi IP attendibili è abilitata, per i flussi del browser *non* è necessaria la verifica in due passaggi. Le password delle app *non* sono necessarie per le applicazioni rich client meno recenti, a condizione che l'utente non abbia creato una password dell'app. Quando si usa una password dell'app, questa rimane obbligatoria. 

**Esperienza dell'utente finale all'esterno della rete aziendale**

Indipendentemente dal fatto che la funzionalità Indirizzi IP attendibili sia disabilitata, per i flussi del browser è necessaria la verifica in due passaggi. Le password dell'app sono necessarie per applicazioni rich client meno recenti. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Abilitare le località denominate usando l'accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Accesso condizionale** > **Località denominate**.
3. Selezionare **Nuova località**.
4. Immettere un nome per la località.
5. Selezionare **Contrassegna come posizione attendibile**.
6. Immettere l'intervallo di indirizzi IP nella notazione CIDR, ad esempio **192.168.1.1/24**.
7. Selezionare **Create**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Abilitare la funzionalità Indirizzi IP attendibili tramite l'accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Accesso condizionale** > **Località denominate**.
3. Selezionare **Configura indirizzi IP attendibili MFA**.
4. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una delle due opzioni seguenti:
   
   * **Per le richieste degli utenti federati originate dalla Intranet dell'utente**: per scegliere questa opzione selezionare la casella di controllo. Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignorano la verifica in due passaggi usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Per le richieste da un intervallo specifico di IP pubblici**: per scegliere questa opzione immettere gli indirizzi IP nella casella di testo usando la notazione CIDR.
   
     * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
     * Per un singolo indirizzo IP, usare una notazione, ad esempio **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

5. Selezionare **Salva**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Abilitare la funzionalità Indirizzi IP attendibili tramite le impostazioni del servizio

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una delle due opzioni seguenti:
   
   * **For requests from federated users on my intranet** (Per le richieste provenienti da utenti federati nella Intranet): per scegliere questa opzione selezionare la casella di controllo. Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignorano la verifica in due passaggi usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **For requests from a specified range of IP address subnets** (Per le richieste provenienti da un intervallo specifico di subnet di indirizzi IP): per scegliere questa opzione immettere gli indirizzi IP nella casella di testo usando la notazione CIDR. 
     
     * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
     * Per un singolo indirizzo IP, usare una notazione, ad esempio **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.

6. Selezionare **Salva**.

![Abilitare gli indirizzi IP attendibili con le impostazioni di servizio](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Password dell'app

Alcune applicazioni, come Office 2010 o le versioni meno recenti e Apple Mail, non supportano la verifica in due passaggi e non sono configurate per accettare una seconda verifica. Per usare queste applicazioni servirsi della funzionalità _password dell'app_. È possibile usare una password dell'app al posto della password tradizionali per consentire a un'app di ignorare la verifica in due passaggi e continuare a lavorare.

>[!NOTE]
>Autenticazione moderna per i client di Microsoft Office 2013 e versioni successive
> 
>I client di Office 2013 e delle versioni successive, tra cui Outlook, supportano i protocolli di autenticazione moderna e possono essere abilitati per la verifica in due passaggi. Dopo averlo abilitato, il client non richiede le password dell'app. Per altre informazioni, vedere l' [annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>

### <a name="considerations-about-app-passwords"></a>Considerazioni sulle password dell'app

Quando si usano le password dell'app, prendere in considerazione questi punti importanti:

* Le password dell'app vengono immesse solo una volta per ogni applicazione. Gli utenti non sono tenuti a tenerne traccia o a immetterle ogni volta.
* La password effettiva viene generata automaticamente e non viene fornita dall'utente. Per un utente malintenzionato indovinare una password generata automaticamente è più difficile e quindi la sicurezza è maggiore.
* Il limite è di 40 password per utente. 
* Le applicazioni che memorizzano le password nella cache e le usano in scenari locali possono potrebbero non funzionare perché la password dell'app non è nota all'esterno dell'account aziendale o dell'istituto di istruzione. Un esempio di questo scenario è rappresentato dai messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. In questo scenario non funziona la stessa password.
* Dopo l'abilitazione di Multi-Factor Authentication per un account utente, le password dell'app possono essere usate con la maggior parte dei client non basati su browser, ad esempio Outlook e Microsoft Skype for Business. Le azioni amministrative non possono essere eseguite usando le password dell'app tramite applicazioni non basate su browser, ad esempio Windows PowerShell. Tali azioni non possono essere eseguite anche se l'utente dispone di un account amministrativo. Per eseguire gli script di PowerShell, creare un account di servizio con una password complessa e non abilitare l'account per la verifica in due passaggi.

>[!WARNING]
>Le password di app non funzionano in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud. Le password del dominio sono necessarie per l'autenticazione locale. Le password dell'app sono necessarie per l'autenticazione con il cloud.
>

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

  ![Uso di password dell'app in un'organizzazione federata](./media/multi-factor-authentication-whats-next/federated.png)

  In questo scenario usare le credenziali seguenti:

  * Per accedere a Skype for Business, usare il nome utente e la password dell'account aziendale o dell'istituto di istruzione.
  * Per accedere alla rubrica da un client Outlook che si connette a Exchange Online, usare una password dell'app.

### <a name="allow-users-to-create-app-passwords"></a>Consentire agli utenti di creare password dell'app

Per impostazione predefinita, gli utenti non possono creare password dell'app. La funzionalità password dell'app deve essere abilitata. Per consentire agli utenti di creare password dell'app, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio** selezionare l'opzione **Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser**.

   ![Consentire agli utenti di creare password dell'app](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Creare password dell'app

Gli utenti possono creare password dell'app durante la registrazione iniziale. Alla fine del processo di registrazione l'utente può creare le password dell'app.

Gli utenti possono creare password delle app anche dopo la registrazione. Le password dell'app possono essere modificate tramite le impostazioni nel portale di Azure o nel portale di Office 365. Per altre informazioni e procedure dettagliate per gli utenti, vedere [Che cosa sono le password per le app in Azure Multi-Factor Authentication?](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Memorizzare Multi-Factor Authentication per dispositivi attendibili
La funzionalità di _memorizzazione di Multi-Factor Authentication_ per dispositivi e browser considerati attendibili dall'utente è una funzionalità gratuita per tutti gli utenti di Multi-Factor Authentication. Gli utenti possono ignorare le verifiche successive per un numero specificato di giorni, dopo aver effettuato correttamente l'accesso in un dispositivo tramite Multi-Factor Authentication. Questa funzionalità permette di migliorare l'usabilità, riducendo al minimo il numero di volte in cui un utente deve eseguire la verifica in due passaggi nello stesso dispositivo.

>[!IMPORTANT]
>Se un dispositivo o un account viene compromesso, la memorizzazione di Multi-Factor Authentication per i dispositivi attendibili può influire sulla sicurezza. Se viene compromesso un account aziendale o un dispositivo attendibile viene smarrito o rubato, è necessario [ripristinare Multi-Factor Authentication su tutti i dispositivi](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>L'azione di ripristino revoca lo stato di attendibilità di tutti i dispositivi e l'utente deve eseguire nuovamente la verifica in due passaggi. È possibile anche istruire gli utenti a ripristinare Multi-Factor Authentication sui propri dispositivi con le istruzioni disponibili in [Manage your settings for two-step verification](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted) (Gestire le impostazioni per la verifica in due passaggi).
>

### <a name="how-the-feature-works"></a>Funzionamento della funzionalità

La funzionalità di memorizzazione di Multi-Factor Authentication imposta un cookie permanente nel browser quando l'utente seleziona l'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) al momento dell'accesso. L'utente non visualizza più la richiesta di Multi-Factor Authentication da questo browser fino alla scadenza del cookie. Se l'utente apre un altro browser sullo stesso dispositivo o cancella i cookie, la verifica verrà richiesta di nuovo. 

L'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) non viene visualizzata nelle applicazioni non basate su browser, indipendentemente dal fatto che l'app supporti l'autenticazione moderna. Queste app usano _token di aggiornamento_ che creano nuovi token di accesso ogni ora. Quando un token di aggiornamento viene convalidato, Azure AD controlla che l'ultima verifica in due passaggi sia stata eseguita entro il numero di giorni specificato. 

La funzionalità riduce il numero di autenticazioni per le app Web, che in genere viene richiesta ogni volta. Questa comporta però un aumento del numero di autenticazioni per i client di autenticazione moderna che in genere la richiedono ogni 90 giorni.

>[!IMPORTANT]
>La funzionalità di **memorizzazione di Multi-Factor Authentication** non è compatibile con la funzionalità **mantieni l'accesso** di AD FS, quando gli utenti eseguono la verifica in due passaggi per AD FS tramite il server Azure Multi-Factor Authentication o una soluzione di autenticazione a più fattori di terze parti.
>
>Se si seleziona **Mantieni l'accesso** in AD FS e si contrassegna il dispositivo come attendibile per Multi-Factor Authentication, l'utente non esegue automaticamente la verifica quando terminano i giorni per la **memorizzazione di Multi-Factor Authentication**. Azure AD richiede una nuova verifica in due passaggi, ma AD FS restituisce un token con l'attestazione originale e la data di Multi-Factor Authentication invece di eseguire di nuovo la verifica in due passaggi. Questa reazione attiva un ciclo di verifica tra Azure AD e AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Abilitare la funzionalità Memorizza Multi-Factor Authentication

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **manage remember multi-factor authentication** (Gestisci memorizzazione di Multi-Factor Authentication) selezionare l'opzione **Consenti agli utenti di memorizzare l'autenticazione a più fattori nei dispositivi attendibili**.

   ![Memorizzare Multi-Factor Authentication per dispositivi attendibili](./media/multi-factor-authentication-whats-next/remember.png)

6. Impostare il numero di giorni per cui consentire ai dispositivi attendibili di ignorare la verifica in due passaggi. Il valore predefinito è 14 giorni.
7. Selezionare **Salva**.

### <a name="mark-a-device-as-trusted"></a>Contrassegnare un dispositivo come attendibile

Dopo aver abilitato la funzionalità Memorizza Multi-Factor Authentication, gli utenti possono contrassegnare un dispositivo come attendibile al momento dell'accesso selezionando **Non chiedere più**.

![Selezionare "Non chiedere più" per i dispositivi attendibili](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Metodi di verifica selezionabili

La funzionalità _Metodi di verifica selezionabili_ consente di scegliere i metodi di verifica disponibili per gli utenti. La tabella seguente offre una breve panoramica dei metodi.

Quando gli utenti registrano i propri account per Azure Multi-Factor Authentication, scelgono il metodo di verifica preferito tra le opzioni abilitate. Le linee guida per il processo di registrazione dell'utente sono disponibili in [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).

| Metodo | DESCRIZIONE |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non viene sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo contenente un codice di verifica. All'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso. Questo processo è denominato SMS unidirezionale. L'SMS bidirezionale significa che l'utente deve disporre il testo in un codice specifico. L'SMS bidirezionale è deprecato e non è supportato a partire dal 14 novembre 2018. In quella data gli utenti configurati per l'SMS bidirezionale passeranno automaticamente alla verifica _chiamata al telefono_.|
| Notifica tramite app per dispositivi mobili |Invia una notifica push al telefono o al dispositivo registrato. L'utente visualizza la notifica e seleziona **Verifica** per completare la verifica. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili |L'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Abilitare e disabilitare i metodi di verifica

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sulla sinistra selezionare **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
5. Nella pagina **Impostazioni servizio**, in **Opzioni di verifica** selezionare o deselezionare i metodi da mettere a disposizione per gli utenti.

   ![Selezionare i metodi di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Fare clic su **Save**.
