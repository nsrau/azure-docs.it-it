---
title: Azure Multi-Factor Authentication - Passaggi successivi
description: "Questa è la pagina di Azure Multi-Factor Authentication che descrive le successive azioni da eseguire con Multi-Factor Authentication.  Sono inclusi i report, gli avvisi di illecito, il bypass monouso, i messaggi vocali personalizzati, la memorizzazione nella cache, gli indirizzi IP attendibili e le password dell&quot;app."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e4ef793b2c1bc0026581fa4b414300721678d7d3
ms.openlocfilehash: ecf2afc7a6e35b99a110e2e259e005e2164ebb27


---
# <a name="configuring-azure-multi-factor-authentication"></a>Configurazione di Azure Multi-Factor Authentication
Le informazioni in questo articolo sono utili per gestire Azure Multi-Factor Authentication ora che si è operativi.  Viene illustrata un'ampia gamma di argomenti per ottenere il massimo da Azure Multi-Factor Authentication.  Non tutte queste funzionalità sono disponibili in ogni versione di Azure Multi-Factor Authentication.

La configurazione di alcune delle funzionalità riportate di seguito è disponibile nel portale di gestione di Azure Multi-Factor Authentication. È possibile accedere al portale di gestione di MFA in due modi, in entrambi i casi tramite il portale di Azure. Il primo è tramite la gestione di un provider di Multi-Factor Authentication se si usa MFA in base all'utilizzo. Il secondo è tramite le impostazioni del servizio MFA. La seconda opzione richiede un provider di Multi-Factor Authentication o una licenza di Azure MFA, Azure AD Premium o Enterprise Mobility Suite.

Per accedere al portale di gestione di MFA tramite un provider di Azure Multi-Factor Authentication, accedere al portale di Azure come amministratore e selezionare l'opzione Active Directory. Fare clic sulla scheda **Provider di Autenticazione a più fattori** e quindi selezionare la directory e fare clic sul pulsante **Gestisci** nella parte inferiore.

Per accedere al portale di gestione di MFA tramite la pagina delle impostazioni del servizio MFA, accedere al portale di Azure come amministratore e selezionare l'opzione Active Directory. Selezionare la directory e fare clic sulla scheda **Configura** . Nella sezione Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**. Nella parte inferiore della pagina delle impostazioni del servizio MFA fare clic sul collegamento **Vai al portale** .

| Funzionalità | Descrizione | Argomenti trattati |
|:--- |:--- |:--- |
| [Avviso di illecito](#fraud-alert) |È possibile configurare e impostare un avviso di illecito in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse. |Come impostare, configurare e segnalare gli illeciti |
| [Bypass monouso](#one-time-bypass) |Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication. |Come impostare e configurare un bypass monouso |
| [Messaggi vocali personalizzati](#custom-voice-messages) |I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. |Come impostare e configurare messaggi introduttivi e messaggi personalizzati |
| [Memorizzazione nella cache](#caching-in-azure-multi-factor-authentication) |La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. |Come impostare e configurare la memorizzazione nella cache dell'autenticazione |
| [Indirizzi IP attendibili](#trusted-ips) |Gli indirizzi IP attendibili sono una funzionalità di Multi-Factor Authentication che offre agli amministratori di un tenant gestito o federato la possibilità di ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet locale dell'azienda. |Configurare e impostare gli indirizzi IP che non sono interessati da Multi-Factor Authentication |
| [Password dell'app](#app-passwords) |Una password dell'app consente a un'applicazione che non è in grado di riconoscere MFA di ignorare questa funzionalità e continuare a funzionare. |Informazioni sulle password dell'app. |
| [Memorizzare Multi-Factor Authentication per dispositivi e browser memorizzati](#remember-multi-factor-authentication-for-devices-users-trust) |Consente di memorizzare dispositivi per un determinato numero di giorni dopo che un utente ha effettuato correttamente l'accesso tramite MFA. |Informazioni sull'abilitazione di questa funzionalità e sull'impostazione del numero di giorni. |
| [Metodi di verifica selezionabili](#selectable-verification-methods) |Consente di scegliere i metodi di autenticazione disponibili per gli utenti. |Informazioni sull'abilitazione o disabilitazione di specifici metodi di autenticazione, ad esempio chiamate o SMS. |

## <a name="fraud-alert"></a>Avviso di illecito
È possibile configurare e impostare un avviso di illecito in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse.  Gli utenti possono segnalare gli illeciti con l'app per dispositivi mobili o tramite il telefono.

### <a name="to-set-up-and-configure-fraud-alert"></a>Per impostare e configurare gli avvisi di illecito
1. Accedere a http://azure.microsoft.com
2. Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3. Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Impostazioni .
4. Nella sezione Avviso di illecito della pagina Impostazioni selezionare la casella di controllo Consenti agli utenti di inviare avvisi di illeciti.
5. Se si desidera che gli utenti siano bloccati quando viene segnalato un illecito, selezionare Blocca utente se viene segnalato un illecito.
6. Nella casella di testo **Codice per segnalare illeciti durante il messaggio introduttivo iniziale** immettere un codice numerico da usare durante la verifica della chiamata. Se un utente immette questo codice insieme al simbolo # anziché solo il simbolo #, verrà segnalato un avviso di illecito.
7. Nella parte inferiore fare clic su Salva.

> [!NOTE]
> I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere 0# per inviare un avviso di illecito. Se si vuole usare un codice diverso da 0, è necessario registrare e caricare messaggi vocali personalizzati con le istruzioni appropriate.
> 
> 

![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Per segnalare un avviso di illecito
Gli avvisi di illecito possono essere segnalati in due modi:  mediante l'app per dispositivi mobili o tramite il telefono.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Per segnalare un avviso di illecito con l'app per dispositivi mobili
1. Quando viene inviata una verifica al telefono, selezionarla per avviare l'app Microsoft Authenticator.
2. Per segnalare l'illecito, fare clic su Annulla e segnala illecito. Viene visualizzata una finestra che indica che il personale di supporto IT dell'organizzazione riceverà una notifica.
3. Fare clic su Segnala illecito.
4. Nell'app fare clic su Chiudi.

![Cloud](./media/multi-factor-authentication-whats-next/report1.png)

![Cloud](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Per segnalare un avviso di illecito con il telefono
1. Quando si riceve una chiamata di verifica sul telefono, rispondere.  
2. Per segnalare l'illecito, immettere il codice che è stato configurato per la segnalazione di illeciti tramite il telefono e quindi il simbolo #. Verrà segnalato che è stato inviato un avviso di illecito.
3. Terminare la chiamata.

### <a name="to-view-the-fraud-report"></a>Per visualizzare il report sull'illecito
1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Viene visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se è disponibile un solo provider, fare clic su Gestisci. Viene aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Avviso di illecito in Visualizza un report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È anche possibile specificare nomi utente, numeri di telefono e lo stato dell'utente.
7. Fare clic su Esegui. Viene visualizzato un report simile a quello riportato di seguito. Se si desidera esportare il report, è inoltre possibile fare clic su Esporta in CSV.

## <a name="one-time-bypass"></a>Bypass monouso
Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication. Il bypass è temporaneo e scade dopo il numero di secondi specificato.  Pertanto, nelle situazioni in cui l'app per dispositivi mobili o il telefono non riceve una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

### <a name="to-create-a-one-time-bypass"></a>Per creare un bypass monouso
1. Accedere a http://azure.microsoft.com
2. Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3. Nel portale di gestione di Azure Multi-Factor Authentication se viene visualizzato il nome del tenant o del provider di Azure MFA a sinistra con accanto un segno +, fare clic sul segno + per vedere i diversi gruppi di replica del server MFA e il gruppo predefinito di Azure. Fare clic sul gruppo appropriato.
4. In Amministrazione utenti fare clic su **Bypass monouso**.
   ![Cloud](./media/multi-factor-authentication-whats-next/create1.png)
5. Nella pagina Bypass monouso fare clic su **Nuovo bypass monouso**.
6. Immettere il nome utente, il numero di secondi per il bypass, il motivo del bypass e fare clic su **Bypass**.
   ![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7. A questo punto, l'utente deve accedere prima della scadenza del bypass monouso.

### <a name="to-view-the-one-time-bypass-report"></a>Per visualizzare il report del bypass monouso
1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Viene visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se è disponibile un solo provider, fare clic su Gestisci. Viene aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Bypass monouso in Visualizza un report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È anche possibile specificare nomi utente, numeri di telefono e lo stato dell'utente.
7. Fare clic su Esegui. Viene visualizzato un report simile a quello riportato di seguito. Se si desidera esportare il report, è inoltre possibile fare clic su Esporta in CSV.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Messaggi vocali personalizzati
I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication.  Possono essere usati in aggiunta alle registrazioni di Microsoft o per sostituirle.

Prima di iniziare, tenere presente quanto segue:

* I formati di file attualmente supportati sono WAV e MP3.
* La dimensione massima dei file è 5 MB.
* È consigliabile che la durata dei messaggi di autenticazione non sia superiore a 20 secondi. Qualsiasi valore superiore potrebbe impedire la verifica, in quanto l'utente potrebbe non riuscire a rispondere prima della fine del messaggio e del timeout della verifica.

### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Per configurare i messaggi vocali personalizzati in Azure Multi-Factor Authentication
1. Creare un messaggio vocale personalizzato usando uno dei formati di file supportati.
2. Accedere a http://azure.microsoft.com
3. Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
4. Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Messaggi vocali.
5. Nella sezione Messaggi vocali fare clic su **Nuovo messaggio vocale**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6. Nella pagina Configura: Nuovi messaggi vocali fare clic su **Gestisci file audio**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7. Nella pagina Configura: File audio fare clic su **Carica file audio**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8. In Configura: Carica file audio fare clic su **Sfoglia**, individuare il messaggio vocale e fare clic su **Apri**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9. Aggiungere una descrizione e fare clic su Carica.
10. Al termine, viene visualizzato un messaggio per confermare che il file è stato caricato.
11. A sinistra fare clic su Messaggi vocali.
12. Nella sezione Messaggi vocali fare clic su Nuovo messaggio vocale.
13. Dall'elenco a discesa Lingua selezionare una lingua.
14. Se questo messaggio è per un'applicazione specifica, specificarlo nella casella Applicazione.
15. In Tipo di messaggio selezionare il tipo di messaggio da sostituire con il nuovo messaggio personalizzato.
16. Nell'elenco a discesa File audio selezionare il file audio.
17. Fare clic su **Crea**. Viene visualizzato un messaggio per confermare che è stato creato un messaggio vocale.
    ![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Memorizzazione nella cache in Azure Multi-Factor Authentication
La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. Questa opzione viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. In questo modo, dopo che l'utente ha completato la verifica in corso, le richieste successive hanno automaticamente esito positivo. Si noti che la memorizzazione nella cache non può essere usata per gli accessi ad Azure AD.

### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Per configurare la memorizzazione nella cache in Azure Multi-Factor Authentication
1. Accedere a http://azure.microsoft.com
2. Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3. Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Memorizzazione nella cache.
4. Nella pagina Configura memorizzazione nella cache fare clic su Nuova cache
5. Selezionare il tipo di cache e i secondi per la cache. Fare clic su Crea.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Indirizzi IP attendibili
Gli indirizzi IP attendibili sono una funzionalità di Multi-Factor Authentication che offre agli amministratori di un tenant gestito o federato la possibilità di ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet locale dell'azienda. Questa funzionalità è disponibile con la versione completa di Azure Multi-Factor Authentication. Per informazioni dettagliate su come ottenere la versione completa di Azure Multi-Factor Authentication, vedere [Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication.md#how-to-get-azure-multi-factor-authentication).

| Tipo di tenant di Azure AD | Opzioni disponibili per gli indirizzi IP attendibili |
|:--- |:--- |
| Gestito |Intervalli di indirizzi IP specifici - Gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet dell'azienda. |
| Federato |<li>Tutti gli utenti federati - Tutti gli utenti federati che eseguono l'accesso dall'interno dell'organizzazione ignoreranno Multi-Factor Authentication usando un'attestazione rilasciata da AD FS.</li><li>Intervalli di indirizzi IP specifici - Gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet dell'azienda. |

Questo bypass può essere usato solo all'interno della rete Intranet di un'azienda. Ad esempio, se sono stati selezionati solo tutti gli utenti federati e un utente accede dall'esterno della rete Intranet aziendale, tale l'utente deve eseguire l'autenticazione usando Multi-Factor Authentication, anche se l'utente presenta un'attestazione AD FS. Nella tabella seguente viene descritto quando sono necessari Multi-Factor Authentication e le password dell'app all'interno e all'esterno della rete aziendale quando sono abilitati gli indirizzi IP attendibili.

| Indirizzi IP attendibili abilitati | Indirizzi IP attendibili disabilitati |
|:--- |:--- |:--- |
| All'interno della rete aziendale |Per i flussi del browser Multi-Factor Authentication NON è necessaria. |
| Per le app rich client, le password normali funzionano se l'utente non ha creato password dell'app. Dopo aver creato una password dell'app, le password dell'app sono necessarie. |Per le app rich client, le password dell'app sono necessarie |
| All'esterno della rete aziendale |Per i flussi del browser Multi-Factor Authentication è necessaria. |
| Per le app rich client, le password dell'app sono necessarie. |Per le app rich client, le password dell'app sono necessarie. |

### <a name="to-enable-trusted-ips"></a>Per abilitare gli indirizzi IP attendibili
1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory per cui si desidera impostare gli indirizzi IP attendibili.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, in Indirizzi IP attendibili, selezionare:
   
   * Per le richieste degli utenti federati originate dalla Intranet dell'utente: tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignoreranno Multi-Factor Authentication usando un'attestazione rilasciata da AD FS.
   * Per le richieste da un intervallo specifico di IP pubblici: immettere gli indirizzi IP nelle caselle disponibili usando la notazione CIDR. Ad esempio: xxx.xxx.xxx.0/24 per gli indirizzi IP nell'intervallo xxx.xxx.xxx.1 – xxx.xxx.xxx.254 oppure xxx.xxx.xxx.xxx/32 per un singolo indirizzo IP. È possibile immettere fino a 50 intervalli di indirizzi IP.
7. Fare clic su Salva.
8. Una volta che gli aggiornamenti sono stati applicati, fare clic su Chiudi.

![Indirizzi IP attendibili](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Password dell'app
In alcune app, come Office 2010 o versioni precedenti e Apple Mail, non è possibile usare Multi-Factor Authenticaton.  Per usare queste app, è necessario usare "password dell'app" al posto della password tradizionale.  Le password dell'app consentono all'applicazione di ignorare Multi-Factor Authentication e continuare a lavorare.

> [!NOTE]
> Autenticazione moderna per i client di Office 2013
> 
> I Client di Office 2013 (tra cui Outlook) ora supportano i nuovi Protocolli di autenticazione e possono essere abilitati per supportare la Multi-Factor Authentication.  Ciò significa che una volta attivati, le password della app non vengono richieste per l'utilizzo con i Client di Office 2013.  Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
> 
> 

### <a name="important-things-to-know-about-app-passwords"></a>Informazioni importanti sulle password dell'app
Di seguito è riportato un elenco delle informazioni importanti da conoscere sulle password dell'app.

* Gli utenti possono avere più password dell'app, cosa che aumenta la superficie di attacco per i furti. Poiché le password dell'app sono difficili da ricordare, gli utenti potrebbero essere indotti ad annotarle. Questa operazione non è consigliabile e dovrebbe essere evitata, perché è necessario un solo fattore per l'accesso con la password dell'app.
* Le app che memorizzano le password nella cache e le usano in scenari locali potrebbero smettere di funzionare, perché la password dell'app non è nota all'esterno dell'ID organizzazione. Un esempio sono i messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. Non funziona la stessa password.
* La password effettiva viene generata automaticamente e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
* Al momento esiste un limite di 40 password per utente. Verrà richiesto di eliminare una delle password dell'app esistenti prima di crearne una nuova.
* Dopo aver abilitato Multi-factor Authentication nell’account di un utente, le password di app possono essere utilizzate con la maggior parte dei client non basati su browser come Outlook e Lync, ma non è possibile eseguire azioni amministrative usando password di app tramite applicazioni non basate su browser come Windows PowerShell, anche se tale utente dispone di un account di amministrazione.  Assicurarsi di creare un account di servizio con una password complessa per l'esecuzione degli script di PowerShell e di non abilitare tale account per Multi-factor Authentication.

> [!WARNING]
> Le password di app non funzionano in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud. Questo perché sono necessarie password di dominio per l'autenticazione locale e le password di app sono necessarie per l'autenticazione nel cloud.
> 
> 

### <a name="naming-guidance-for-app-passwords"></a>Indicazioni per la denominazione delle password dell'app
Per le password dell'app è consigliabile usare nomi che riflettano il dispositivo in cui vengono usate. Ad esempio, se si dispone di un portatile con app non basate su browser come Outlook, Word ed Excel, è sufficiente creare una sola password dell'app denominata Portatile e usarla in tutte queste applicazioni. Sebbene sia possibile creare password distinte per tutte queste applicazioni, non è consigliabile. È preferibile usare una sola password dell'app per ogni dispositivo.

<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Password dell'app federate (SSO)
Azure AD supporta la federazione con Servizi di dominio Active Directory (AD DS) di Windows Server in locale. Se l'organizzazione è federata (SSO) con Azure AD e si intende usare Azure Multi-Factor Authentication, di seguito sono riportate importanti informazioni che occorre tenere presente per l'uso di password dell'app. Questo vale solo per i clienti federati (SSO).

* La password dell'app è verificata da Azure AD e di conseguenza ignora la federazione. La federazione viene usata attivamente solo durante l'impostazione della password dell'app.
* Per gli utenti federati (SSO) non viene mai usato il provider di identità (IdP), diversamente dal flusso passivo. Le password vengono archiviate nell'ID organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync in tempo reale. La disabilitazione o l'eliminazione dell'account può richiedere fino a&3; ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione della password dell'app in Azure AD.
* Le impostazioni locali di controllo dell'accesso client non vengono rispettate dalla password dell'app
* Per la password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale
* Per il client Microsoft Lync 2013 è necessaria ulteriore formazione per gli utenti finali. Per i passaggi necessari, vedere Come modificare la password nella posta elettronica per la password dell'app.
* Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa Multi-Factor Authentication con i client, a seconda della posizione in cui viene eseguita l'autenticazione. Per i client che eseguono l'autenticazione con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione. Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.

Si supponga ad esempio di avere un'architettura costituita dagli elementi seguenti:

* Si sta eseguendo la federazione dell'istanza locale di Active Directory con Azure AD
* Si usa Exchange Online
* Si usa Lync, specificamente in locale
* Si usa Azure Multi-Factor Authentication

![Verifica](./media/multi-factor-authentication-whats-next/federated.png)

 In questi casi, è necessario eseguire le operazioni seguenti:

* Quando si esegue l'accesso a Lync, usare il nome utente e la password dell'organizzazione.
* Quando si tenta di accedere alla Rubrica tramite un client Outlook che si connette a Exchange Online, usare una password dell'app.

### <a name="allowing-app-password-creation"></a>Consentire la creazione della password dell'app
Per impostazione predefinita, gli utenti non possono creare password dell'app.  Questa funzionalità deve essere abilitata.  Per consentire agli utenti di creare password dell'app, attenersi alla procedura seguente.

#### <a name="to-enable-users-to-create-app-passwords"></a>Per consentire agli utenti di creare password dell'app
1. Accedere al portale di Microsoft Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente che si desidera attivare.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth.  
6. Nella parte superiore della pagina Multi-Factor Authentication fare clic su Impostazioni servizio.
7. Assicurarsi che il pulsante di opzione Consentire agli utenti di creare password dell'app per accedere alle applicazioni non basate su browser sia selezionato.

![Creazione di password dell'app](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Creazione di password dell'app
Gli utenti possono creare password dell'app durante la registrazione iniziale.  Al termine del processo di registrazione, viene offerta loro un'opzione che consente di crearle.

Gli utenti possono inoltre creare password dell'app in un secondo momento modificando le impostazioni nel portale di Azure o nel portale di Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Per creare password dell'app nel portale di Office 365
- - -
1. Accedere al portale di Office 365.
2. Nell'angolo superiore destro selezionare il widget delle impostazioni.
3. A sinistra selezionare Verifica aggiuntiva di sicurezza.
4. A destra selezionare **Aggiorna i numeri telefonici usati per la sicurezza dell'account**
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Per creare password dell'app nel portale di Azure
- - -
1. Accedere al portale di Microsoft Azure classico.
2. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
3. Nella parte superiore della pagina di verifica selezionare le password dell'app.
4. Fare clic su **Crea**
5. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
6. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

![Password dell'app](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Per creare password per le app se non si ha una sottoscrizione di Office 365 o Azure
- - -
1. Effettuare l'accesso ad [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore selezionare il profilo.
3. Fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
4. Nella parte superiore della pagina di verifica selezionare le password dell'app.
5. Fare clic su **Crea**
6. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**
7. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

![Password dell'app](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Memorizzare Multi-Factor Authentication per i dispositivi che gli utenti considerano attendibili
La memorizzazione di Multi-Factor Authentication per i dispositivi e i browser che gli utenti considerano attendibili è una funzionalità disponibile gratuitamente per tutti gli utenti di MFA.  Con questa funzionalità gli utenti possono scegliere di ignorare MFA per un numero determinato di giorni dopo l'esecuzione di un accesso tramite MFA. In questo modo l'usabilità per gli utenti è maggiore.

Tuttavia, poiché agli utenti viene consentito di memorizzare MFA per i dispositivi attendibili, questa funzionalità può ridurre la sicurezza degli account. Per garantire la sicurezza degli account, è necessario ripristinare Multi-Factor Authentication per i dispositivi in entrambi gli scenari seguenti:

* Se l'account aziendale dell'utente è stato compromesso
* Se un dispositivo memorizzato viene smarrito o rubato

> [!NOTE]
> Questa funzionalità è implementata come una cache di cookie del browser. Non funziona se i cookie del browser non sono abilitati.
> 
> 

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Come abilitare o disabilitare la memorizzazione dell'autenticazione a più fattori
1. Accedere al portale di Microsoft Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Active Directory fare clic sulla directory per cui si desidera impostare la memorizzazione di Multi-Factor Authentication per i dispositivi.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, nelle impostazioni di gestione dei dispositivi utente, selezionare o deselezionare **Consenti agli utenti di memorizzare l'autenticazione a più fattori nei dispositivi attendibili**.
   ![Memorizzare dispositivi](./media/multi-factor-authentication-whats-next/remember.png)
7. Impostare il numero di giorni per cui si vuole consentire la sospensione. Il valore predefinito è 14 giorni.
8. Fare clic su Salva.
9. Fare clic su Chiudi.

## <a name="selectable-verification-methods"></a>Metodi di verifica selezionabili
Nelle versioni cloud e locale è possibile scegliere i metodi di verifica disponibili per gli utenti. La tabella seguente offre una breve panoramica di ogni metodo.

Quando gli utenti registrano i propri account per l'autenticazione MFA, scelgono il metodo di verifica preferito tra le opzioni abilitate. Le linee guida per il processo di registrazione sono disponibili in [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

| Metodo | Descrizione |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata sul telefono predisposto all'autenticazione. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non viene sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo contenente un codice di verifica per l'utente. L'utente deve rispondere al messaggio con il codice di verifica o immettere il codice di verifica nell'interfaccia di accesso. |
| Notifica tramite app per dispositivi mobili |In questa modalità l'app Microsoft Authenticator impedisce l'accesso agli account non autorizzati e arresta le transazioni illecite. L’avvertenza è una notifica push inviata sul telefono o sul dispositivo registrato. Visualizzare la notifica e, se del caso, selezionare Verifica. In caso contrario, è possibile scegliere Nega oppure Nega e segnala la notifica illecita. Per informazioni sulla segnalazione di notifiche illecite, vedere l'argomento relativo a come usare le funzionalità Nega e Segnala illecito per Multi-Factor Authentication.</br></br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili |In questa modalità l'app Microsoft Authenticator può essere usata come token software per generare un codice di verifica OATH. Questo codice di verifica quindi può pertanto essere immesso con il nome utente e con la password come seconda modalità di autenticazione.</li><br><p> L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Come abilitare o disabilitare i metodi di autenticazione
1. Accedere al portale di Microsoft Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Active Directory fare clic sulla directory per cui si vogliono abilitare o disabilitare i metodi di autenticazione.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, nelle opzioni di verifica, selezionare o deselezionare le opzioni da usare.</br></br>
   ![Opzioni di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Fare clic su Salva.
8. Fare clic su Chiudi.




<!--HONumber=Dec16_HO2-->


