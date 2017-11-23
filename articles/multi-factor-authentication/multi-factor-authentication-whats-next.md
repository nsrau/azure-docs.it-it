---
title: Configurare Azure MFA | Microsoft Docs
description: "Questa è la pagina di Azure Multi-Factor Authentication che descrive le successive azioni da eseguire con Multi-Factor Authentication.  Sono inclusi i report, gli avvisi di illecito, il bypass monouso, i messaggi vocali personalizzati, la memorizzazione nella cache, gli indirizzi IP attendibili e le password dell'app."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4900707baa875ae4527d82e8189d5bc4d319ae0c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-multi-factor-authentication-settings---public-preview"></a>Configurare le impostazioni di Azure Multi-Factor Authentication: anteprima pubblica

Le informazioni in questo articolo sono utili per gestire Azure Multi-Factor Authentication ora che si è operativi.  L'articolo illustra diversi argomenti che permettono di ottenere il massimo da Azure Multi-Factor Authentication.  Non tutte queste funzionalità sono disponibili in ogni [versione di Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

>[!NOTE]
>Queste impostazioni sono in anteprima pubblica nel portale di Azure. Per informazioni su come gestire le impostazioni di Azure Multi-Factor Authentication nel portale pfweb, vedere [Configure Azure Multi-Factor Authentication settings](multi-factor-authentication-whats-next-pfweb.md) (Configurare le impostazioni di Azure Multi-Factor Authentication).

| Funzionalità | Descrizione | 
|:--- |:--- |
| [Bloccare e sbloccare utenti](#block-and-unblock) |La funzionalità Blocca/Sblocca utenti può impedire agli utenti di ricevere le richieste di autenticazione. |
| [Avviso di illecito](#fraud-alert) |È possibile configurare e impostare un avviso di illecito in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse. |
| [Bypass monouso](#one-time-bypass) |Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication. |
| [Messaggi vocali personalizzati](#custom-voice-messages) |I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. |
| [Memorizzazione nella cache](#caching-in-azure-multi-factor-authentication) |La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. |
| [Indirizzi IP attendibili](#trusted-ips) |Gli amministratori di un tenant federato o gestito possono usare gli IP attendibili per ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet locale dell'azienda. |
| [Password dell'app](#app-passwords) |Una password dell'app consente a un'applicazione che non è in grado di riconoscere MFA di ignorare questa funzionalità e continuare a funzionare. |
| [Memorizzare Multi-Factor Authentication per dispositivi e browser memorizzati](#remember-multi-factor-authentication-for-devices-that-users-trust) |Consente di memorizzare dispositivi per un determinato numero di giorni dopo che un utente ha effettuato correttamente l'accesso tramite MFA. |
| [Metodi di verifica selezionabili](#selectable-verification-methods) |Consente di scegliere i metodi di autenticazione disponibili per gli utenti. |

## <a name="block-and-unblock"></a>Bloccare e sbloccare
La funzionalità Blocca/Sblocca utenti può essere usata per impedire agli utenti di ricevere le richieste di autenticazione. Eventuali tentativi di autenticazione per gli utenti bloccati verranno negati automaticamente. Gli utenti bloccati resteranno tali per 90 giorni dal momento in cui vengono bloccati.

### <a name="block-a-user"></a>Bloccare un utente
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Blocca/Sblocca utenti**.
3. Fare clic su **Aggiungi** per bloccare un utente.
4. Selezionare il **Gruppo di replica**, immettere il nome utente bloccato come **username@domain.com** e un commento nel campo **Motivo**.
5. Fare clic su **Aggiungi** per completare il blocco dell'utente.

### <a name="unblock-a-user"></a>Sbloccare un utente
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Blocca/Sblocca utenti**.
3. Fare clic su **Sblocca** nella colonna **Azione** accanto all'utente che si desidera sbloccare.
4. Immettere un commento nel campo **Motivo dello sblocco**.
5. Fare clic su **Sblocca** per completare lo sblocco dell'utente.

## <a name="fraud-alert"></a>Avviso di illecito
È possibile configurare e impostare un avviso di illecito in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse.  Gli utenti possono segnalare gli illeciti con l'app per dispositivi mobili o tramite il telefono.

### <a name="turn-on-fraud-alert"></a>Attivare un avviso di illecito
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Avviso di illecito**.

   ![Avviso di illecito](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Impostare **Consenti agli utenti di inviare avvisi di illeciti** su **Sì**.
4. Selezionare **Salva**.

### <a name="configuration-options"></a>Opzioni di configurazione

- **Blocca utente se viene segnalato un illecito**: se un utente segnala un illecito, il relativo account viene bloccato per 90 giorni o finché l'amministratore non sblocca l'account. Un amministratore può verificare gli accessi usando il report sugli accessi ed eseguire le azioni necessarie per prevenire illeciti nel futuro. Un amministratore può quindi [sbloccare](#unblock-a-user) l'account dell'utente.
- **Codice per la segnalazione di illeciti durante il messaggio introduttivo iniziale**: quando gli utenti ricevono una chiamata telefonica per eseguire la verifica in due passaggi, in genere premono # per confermare l'accesso. Se vogliono segnalare un illecito, devono immettere un codice prima di premere #. Il codice predefinito è **0**, ma è possibile personalizzarlo.

> [!NOTE]
> I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere 0# per inviare un avviso di illecito. Se si vuole usare un codice diverso da 0, è necessario registrare e caricare messaggi vocali personalizzati con le istruzioni appropriate.

### <a name="view-fraud-reports"></a>Visualizzare le segnalazioni di illeciti
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Nella parte inferiore della pagina Impostazioni servizio selezionare **Vai al portale**.
7. Nel portale di gestione di Azure Multi-Factor Authentication fare clic su **Avviso di illecito** in Visualizza un report.
8. Specificare l'intervallo di date che si desidera visualizzare nel report. È anche possibile specificare nomi utente, numeri di telefono e lo stato dell'utente.
9. Fare clic su **Run**. Verrà visualizzato un report degli avvisi di illecito. Per esportare il report, fare clic su **Esporta in CSV**.

## <a name="one-time-bypass"></a>Bypass monouso
Un bypass monouso consente all'utente di eseguire l'autenticazione una sola volta senza eseguire la verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato. In situazioni in cui l'app per dispositivi mobili o il telefono non riceve una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa.

### <a name="create-a-one-time-bypass"></a>Creare un bypass monouso

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Bypass monouso**.

   ![Bypass monouso](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. Selezionare **Aggiungi**.
4. Se necessario, selezionare il gruppo di replica per questo bypass.
5. Immettere il nome utente come username@domain.com, il numero di secondi per il bypass e il motivo del bypass. 
6. Selezionare **Aggiungi**. Il limite di tempo diventa effettivo immediatamente, quindi l'utente deve eseguire l'accesso prima della scadenza del bypass monouso. 

### <a name="view-the-one-time-bypass-report"></a>Visualizzare il report del bypass monouso
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Nella parte inferiore della pagina Impostazioni servizio selezionare **Vai al portale**.
7. Nel portale di gestione di Azure Multi-Factor Authentication fare clic su **Bypass monouso** in Visualizza un report.
8. Specificare l'intervallo di date che si desidera visualizzare nel report. È anche possibile specificare nomi utente, numeri di telefono e lo stato dell'utente.
9. Fare clic su **Run**. Verrà visualizzato un report dei bypass. Per esportare il report, fare clic su **Esporta in CSV**.

## <a name="custom-voice-messages"></a>Messaggi vocali personalizzati
I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati per la verifica in due passaggi. Possono essere usati in aggiunta alle registrazioni di Microsoft o per sostituirle.

Prima di iniziare, tenere presente quanto segue:

* I formati di file supportati sono WAV e MP3.
* La dimensione massima dei file è 5 MB.
* I messaggi di autenticazione devono avere una durata inferiore a 20 secondi. Un messaggio più lungo può causare l'esito negativo della verifica, perché l'utente potrebbe non riuscire a rispondere prima della fine del messaggio, con conseguente timeout della verifica.

### <a name="set-up-a-custom-message"></a>Configurare un messaggio personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Impostazioni telefonata**.

   ![Impostazioni telefonata](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selezionare **Aggiungi messaggio di saluto**.
4. Scegliere il tipo di messaggio di saluto e la lingua.
5. Selezionare un file audio MP3 o WAV da caricare.
6. Selezionare **Aggiungi**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Memorizzazione nella cache in Azure Multi-Factor Authentication
La memorizzazione nella cache consente di impostare un periodo di tempo specifico in modo che i tentativi di autenticazione successivi, eseguiti in tale intervallo di tempo, abbiano automaticamente esito positivo. Questa opzione viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. In questo modo, dopo che l'utente ha completato la prima verifica in corso, le richieste successive hanno automaticamente esito positivo. 

La memorizzazione nella cache non può essere usata per gli accessi ad Azure AD.

### <a name="set-up-caching"></a>Configurare la memorizzazione nella cache 
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **MFA Server (Server MFA)** > **Regole di memorizzazione nella cache**.

   ![Regole di memorizzazione nella cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. Selezionare **Aggiungi**.
5. Selezionare il tipo di cache dall'elenco a discesa e specificare il numero di secondi per la durata massima della cache. 
6. Se necessario, selezionare un tipo di autenticazione e specificare un'applicazione. 
7. Selezionare **Aggiungi**.


## <a name="trusted-ips"></a>IP attendibili
Indirizzi IP attendibili è una funzionalità di Azure MFA che consente agli amministratori di un tenant gestito o federato di ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet locale dell'azienda. Questa funzionalità è disponibile con la versione completa di Azure Multi-Factor Authentication, ma non nella versione gratuita per amministratori. Per informazioni dettagliate su come ottenere la versione completa di Azure Multi-Factor Authentication, vedere [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Tipo di tenant di Azure AD | Opzioni disponibili per gli indirizzi IP attendibili |
|:--- |:--- |
| Gestito |<li>Intervalli di indirizzi IP specifici: gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda.</li> |
| Federato |<li>Tutti gli utenti federati: tutti gli utenti federati che eseguono l'accesso dall'interno dell'organizzazione possono ignorare la verifica in due passaggi usando un'attestazione rilasciata da AD FS.</li><br><li>Intervalli di indirizzi IP specifici: gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet dell'azienda. |

Questo bypass può essere usato solo all'interno della rete Intranet di un'azienda. Ad esempio, se sono stati selezionati solo tutti gli utenti federati e un utente accede dall'esterno della rete Intranet aziendale, tale utente deve eseguire l'autenticazione con la verifica in due passaggi anche se ha un'attestazione AD FS. 

**Esperienza dell'utente finale all'interno della rete aziendale:**

Quando la funzionalità Indirizzi IP attendibili è disabilitata, è necessario usare la verifica in due passaggi per i flussi del browser e le password dell'app per le applicazioni rich client meno recenti. 

Quando la funzionalità Indirizzi IP attendibili è abilitata, *non* è necessario usare la verifica in due passaggi per i flussi del browser e *non* è necessario usare le password dell'app per le applicazioni rich client meno recenti, purché l'utente non abbia già creato una password dell'app. Se si inizia a usare una password dell'app, questa rimane obbligatoria. 

**Esperienza dell'utente finale all'esterno della rete aziendale:**

Sia che la funzionalità Indirizzi IP attendibili sia abilitata o meno, è necessario usare la verifica in due passaggi per i flussi del browser e le password dell'app per le applicazioni rich client meno recenti. 

### <a name="to-enable-trusted-ips"></a>Per abilitare gli indirizzi IP attendibili
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Nella pagina Impostazioni servizio in Indirizzi IP attendibili sono disponibili due opzioni:
   
   * Casella di controllo **For requests from federated users originating from my intranet** (Per le richieste provenienti da utenti federati nella Intranet). Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale possono ignorare la verifica in due passaggi usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. È necessario creare la seguente regola in AD FS, se non esiste: "c:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);"



   * **For requests from a specific range of public IPs** (Per le richieste da un intervallo specifico di IP pubblici): immettere gli indirizzi IP nella casella disponibile usando la notazione CIDR. Ad esempio: xxx.xxx.xxx.0/24 per gli indirizzi IP nell'intervallo xxx.xxx.xxx.1 – xxx.xxx.xxx.254 oppure xxx.xxx.xxx.xxx/32 per un singolo indirizzo IP. È possibile immettere fino a 50 intervalli di indirizzi IP. Gli utenti che accedono da tali indirizzi IP possono ignorare la verifica in due passaggi.
7. Fare clic su **Save**.
8. Dopo aver applicato gli aggiornamenti, fare clic su **Chiudi**.

![Indirizzi IP attendibili](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Password dell'app
Alcune applicazioni, come Office 2010 o le versioni meno recenti di Apple Mail, non supportano la verifica in due passaggi e non sono configurate per accettare una seconda verifica. Per usare queste app, è necessario usare le password dell'app al posto della password tradizionale. La password dell'app consente all'applicazione di ignorare la verifica in due passaggi e continuare a funzionare.

> [!NOTE]
> Autenticazione moderna per i client di Office 2013
> 
> I client di Office 2013, tra cui Outlook, e le versioni più recenti supportano i protocolli di autenticazione moderni e possono essere abilitati per la verifica in due passaggi. Dopo aver abilitato la verifica in due passaggi, non è più necessario usare le password dell'app per questi client.  Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Informazioni importanti sulle password dell'app
Di seguito è riportato un elenco delle informazioni importanti da conoscere sulle password dell'app.

* Le password dell'app devono essere immesse una sola volta per ogni app. Non è necessario tenerne traccia e immetterle ogni volta.
* La password effettiva viene generata automaticamente e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
* Il limite è di 40 password per utente. 
* Le app che memorizzano le password nella cache e le usano in scenari locali potrebbero smettere di funzionare, perché la password dell'app non è nota all'esterno dell'ID organizzazione. Un esempio sono i messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. Non funziona la stessa password.
* Dopo aver abilitato Multi-factor Authentication nell’account di un utente, le password di app possono essere utilizzate con la maggior parte dei client non basati su browser come Outlook e Lync, ma non è possibile eseguire azioni amministrative usando password di app tramite applicazioni non basate su browser come Windows PowerShell, anche se tale utente dispone di un account di amministrazione.  Assicurarsi di creare un account di servizio con una password complessa per l'esecuzione degli script di PowerShell e di non abilitare tale account per la verifica in due passaggi.

> [!WARNING]
> Le password di app non funzionano in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud. Questo perché sono necessarie password di dominio per l'autenticazione locale e le password di app sono necessarie per l'autenticazione nel cloud.

### <a name="naming-guidance-for-app-passwords"></a>Indicazioni per la denominazione delle password dell'app
Per le password dell'app è consigliabile usare nomi che riflettano il dispositivo in cui vengono usate. Ad esempio, se si usa un portatile con app non basate su browser come Outlook, Word ed Excel, è sufficiente creare una sola password dell'app denominata Portatile e usarla in queste applicazioni. Creare quindi un'altra password dell'app denominata Desktop per le stesse applicazioni nel computer desktop. 

È consigliabile creare una password dell'app per ogni dispositivo, anziché una password dell'app per ogni applicazione.

### <a name="federated-sso-app-passwords"></a>Password dell'app federate (SSO)
Azure AD supporta la federazione (Single Sign-On) con Active Directory Domain Services (AD DS) di Windows Server in locale. Se l'organizzazione è federata con Azure AD e si intende usare Azure Multi-Factor Authentication, tenere presente le informazioni importanti sulle password dell'app riportate di seguito. Questa sezione si applica solo agli utenti federati (SSO).

* Le password dell'app vengono verificate da Azure AD e di conseguenza ignorano la federazione. La federazione viene usata attivamente solo durante l'impostazione delle password dell'app.
* Per gli utenti federati (SSO) non viene mai usato il provider di identità (IdP), a differenza del flusso passivo. Le password vengono archiviate nell'ID organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync in tempo reale. La disabilitazione o l'eliminazione dell'account può richiedere fino a 3 ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione della password dell'app in Azure AD.
* Le impostazioni locali di Controllo dell'accesso Client non vengono rispettate dalla password dell'app.
* Per la password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale.
* Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa la verifica in due passaggi con i client, a seconda della posizione in cui viene eseguita l'autenticazione. Per i client che si autenticano con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione. Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.

  Si supponga ad esempio di avere un'architettura costituita dagli elementi seguenti:

  * Si sta eseguendo la federazione dell'istanza locale di Active Directory con Azure AD
  * Si usa Exchange Online
  * Si usa Lync, specificamente in locale
  * Si usa Azure Multi-Factor Authentication

  ![Verifica](./media/multi-factor-authentication-whats-next/federated.png)

  In questi casi, è necessario eseguire le operazioni seguenti:

  * Quando si esegue l'accesso a Lync, usare il nome utente e la password dell'organizzazione.
  * Quando si tenta di accedere alla Rubrica tramite un client Outlook che si connette a Exchange Online, usare una password dell'app.

### <a name="allow-app-password-creation"></a>Consentire la creazione di password dell'app
Per impostazione predefinita, gli utenti non possono creare password dell'app. Questa funzionalità deve essere abilitata. Per consentire agli utenti di creare password dell'app, seguire questa procedura:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Selezionare il pulsante di opzione **Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser**.

![Creazione di password dell'app](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Creare password dell'app
Gli utenti possono creare password dell'app durante la registrazione iniziale. Al termine del processo di registrazione, viene offerta loro un'opzione che consente di crearle.

Gli utenti possono creare password dell'app anche dopo la registrazione, modificando le impostazioni nel portale di Azure o nel portale di Office 365. Per altre informazioni e procedure dettagliate per gli utenti, vedere [Che cosa sono le password per le app in Azure Multi-Factor Authentication](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Memorizza Multi-Factor Authentication per i dispositivi considerati attendibili dagli utenti
La memorizzazione di Multi-Factor Authentication per i dispositivi e i browser che gli utenti considerano attendibili è una funzionalità disponibile gratuitamente per tutti gli utenti di MFA. Con questa funzionalità gli utenti possono scegliere di ignorare MFA per un numero determinato di giorni dopo l'esecuzione di un accesso tramite MFA. Questo permette di migliorare l'usabilità, riducendo al minimo il numero di volte in cui un utente può eseguire la verifica in due passaggi nello stesso dispositivo.

Tuttavia, se un dispositivo o un account viene compromesso, la memorizzazione di MFA per i dispositivi attendibili può influire sulla sicurezza. Se viene compromesso un account aziendale o un dispositivo attendibile viene smarrito o rubato, è necessario [ripristinare Multi-Factor Authentication su tutti i dispositivi](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Questa azione revoca lo stato di attendibilità di tutti i dispositivi e l'utente deve eseguire nuovamente la verifica in due passaggi. È possibile anche istruire gli utenti a ripristinare Azure Multi-Factor Authentication sui propri dispositivi con le istruzioni disponibili in [Gestire le impostazioni per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Funzionamento

Si tenga presente che Multi-Factor Authentication imposta un cookie permanente nel browser quando l'utente seleziona la casella "Non visualizzare più il messaggio per **X** giorni" al momento dell'accesso. Quindi, fino alla scadenza del cookie, il browser non proporrà più all'utente la funzione Multi-Factor Authentication. Se l'utente apre un altro browser sullo stesso dispositivo o cancella il cookie, la verifica verrà richiesta di nuovo. 

La casella di controllo "Non visualizzare più il messaggio per **X** giorni" non viene visualizzata sulle applicazioni diverse dai browser, indipendentemente dal fatto che supportino l'autenticazione moderna. Queste app usano token di aggiornamento che forniscono nuovi token di accesso ogni ora. Quando un token di aggiornamento viene convalidato, Azure AD controlla che l'ultima verifica in due passaggi sia stata eseguita entro il numero di giorni configurato. 

Pertanto si tenga presente che MFA sui dispositivi attendibili riduce il numero di autenticazioni di App Web (la richiesta avviene in genere ogni volta) ma aumenta il numero di autenticazioni per i client con autenticazione moderna (la richiesta avviene normalmente ogni 90 giorni).

> [!NOTE]
>Questa funzionalità non è compatibile con la funzionalità "Mantieni l'accesso" di ADFS quando gli utenti eseguono la verifica in due passaggi per ADFS tramite il Azure MFA Server o una soluzione MFA di terze parti. Se gli utenti selezionano "Mantieni l'accesso" su AD FS e inoltre contrassegnano il dispositivo come attendibile per l'autenticazione a più fattori, non saranno in grado di eseguire la verifica dopo che è passato il numero di giorni "Memorizzazione di MFA". Azure AD richiede una nuova verifica in due passaggi, ma AD FS restituisce un token con l'attestazione MFA e la data originali invece di eseguire di nuovo la verifica in due passaggi. Ciò attiva un ciclo di verifica tra Azure AD e AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Abilitare la funzionalità Memorizza Multi-Factor Authentication
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Nella pagina Impostazioni servizio nelle impostazioni di gestione dei dispositivi utente selezionare la casella **Consenti agli utenti di memorizzare l'autenticazione a più fattori nei dispositivi attendibili**.
   ![Memorizzare dispositivi](./media/multi-factor-authentication-whats-next/remember.png)
7. Impostare il numero di giorni per cui consentire ai dispositivi attendibili di ignorare la verifica in due passaggi. Il valore predefinito è 14 giorni.
8. Fare clic su **Save**.
9. Fare clic su **Close**.

### <a name="mark-a-device-as-trusted"></a>Contrassegnare un dispositivo come attendibile

Dopo aver abilitato questa funzionalità, gli utenti possono contrassegnare un dispositivo come attendibile al momento dell'accesso, selezionando l'opzione **Non chiedere più**.

![Screenshot di Non chiedere più](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Metodi di verifica selezionabili
È possibile scegliere quali metodi di verifica mettere a disposizione degli utenti. La tabella seguente offre una breve panoramica di ogni metodo.

Quando gli utenti registrano i propri account per l'autenticazione MFA, scelgono il metodo di verifica preferito tra le opzioni abilitate. Le linee guida per il processo di registrazione sono disponibili in [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

| Metodo | Descrizione |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non viene sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo contenente un codice di verifica. L'utente deve rispondere al messaggio con il codice di verifica o immettere il codice di verifica nell'interfaccia di accesso. |
| Notifica tramite app per dispositivi mobili |Invia una notifica push al telefono o al dispositivo registrato. L'utente visualizza la notifica e seleziona **Verifica** per completare la verifica. <br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili |L'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso.<br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Come abilitare o disabilitare i metodi di autenticazione
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra, selezionare **Active Directory**.
3. Selezionare la directory da gestire. 
4. Selezionare **Configura**
5. In Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**.
6. Nella pagina Impostazioni servizio, nelle opzioni di verifica, selezionare o deselezionare le opzioni da usare.
   ![Opzioni di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Fare clic su **Save**.
8. Fare clic su **Close**.

