<properties 
	pageTitle="Azure Multi-Factor Authentication - Passaggi successivi" 
	description="Questa è la pagina di Azure Multi-Factor Authentication che descrive le successive azioni da eseguire con Multi-Factor Authentication. Sono inclusi i report, gli avvisi di illecito, il bypass monouso, i messaggi vocali personalizzati, la memorizzazione nella cache, gli indirizzi IP attendibili e le password dell'app." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="billmath"/>

# Configurazione di Azure Multi-Factor Authentication

L'articolo seguente consentirà di gestire Azure Multi-Factor Authentication ora che si è operativi. Viene illustrata un'ampia gamma di argomenti che consentiranno di ottenere il massimo da Azure Multi-Factor Authentication. Tenere presente che non tutte queste funzionalità sono disponibili in ogni versione di Azure Multi-Factor Authentication.

La configurazione di alcune delle funzionalità riportate di seguito è disponibile nel portale di gestione di Azure Multi-Factor Authentication. È possibile accedere al portale in due modi, in entrambi i casi tramite il portale di Azure. Il primo è tramite la gestione di un provider di Multi-Factor Authentication se si usa MFA in base all'utilizzo. Il secondo è tramite le impostazioni del servizio MFA. La seconda opzione richiede un provider di Multi-Factor Authentication o una licenza di Azure MFA, Azure AD Premium o Enterprise Mobility Suite.

Per accedere al portale di gestione di MFA tramite un provider di Azure Multi-Factor Authentication, accedere al portale di Azure come amministratore e selezionare l'opzione Active Directory. Fare clic sulla scheda **provider di Multi-Factor Authentication**, quindi selezionare la directory e fare clic sul pulsante **Gestisci** nella parte inferiore.

Per accedere al portale di gestione di MFA tramite la pagina delle impostazioni del servizio MFA, accedere al portale di Azure come amministratore e selezionare l'opzione Active Directory. Selezionare la directory e fare clic sulla scheda **Configura**. Nella sezione Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**. Nella parte inferiore della pagina delle impostazioni del servizio MFA fare clic sul collegamento **Vai al portale**.


Funzionalità| Descrizione| Argomenti trattati
:------------- | :------------- | :------------- | 
[Avviso di illecito](#fraud-alert)|Avviso di illecito può essere configurato e impostato in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse.|Come impostare, configurare e segnalare gli illeciti
[Bypass monouso](#one-time-bypass) |Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication.|Come impostare e configurare un bypass monouso
[Messaggi vocali personalizzati](#custom-voice-messages) |I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. |Come impostare e configurare messaggi introduttivi e messaggi personalizzati
[Memorizzazione nella cache](#caching)|La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. |Come impostare e configurare la memorizzazione nella cache dell'autenticazione.
[Indirizzi IP attendibili](#trusted-ips)|Gli indirizzi IP attendibili sono una funzionalità di Multi-Factor Authentication che offre agli amministratori di un tenant gestito o federato la possibilità di ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet locale dell'azienda.|Configurare e impostare gli indirizzi IP che non sono interessati da Multi-Factor Authentication	
[Password dell'app](#app-passwords)|Le password dell'app consentono a un'applicazione che non è in grado di riconoscere Multi-Factor Authentication di ignorare questa funzionalità e continuare a lavorare.|Informazioni sulle password dell'app.
[Memorizzazione di Multi-Factor Authentication per dispositivi e browser memorizzati](#remember-multi-factor-authentication-for-devices-users-trust)|Consente di memorizzare dispositivi per un determinato numero di giorni dopo che un utente ha effettuato correttamente l'accesso tramite MFA.|Informazioni sull'abilitazione di questa funzionalità e sull'impostazione del numero di giorni.
[Metodi di verifica selezionabili](#selectable-verification-methods)|Consente di scegliere i metodi di autenticazione disponibili per gli utenti.|Informazioni sull'abilitazione o disabilitazione di specifici metodi di autenticazione, ad esempio chiamate o SMS.



## Avviso di illecito
Avviso di illecito può essere configurato e impostato in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse. Gli utenti possono segnalare gli illeciti con l'app per dispositivi mobili o tramite il telefono.

### Per impostare e configurare gli avvisi di illecito

1.	Accedere a http://azure.microsoft.com
2.	Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3.	Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Impostazioni .
4.	Nella sezione Avviso di illecito della pagina Impostazioni selezionare la casella di controllo Consenti agli utenti di inviare avvisi di illeciti.
5.	Se si desidera che gli utenti siano bloccati quando viene segnalato un illecito, selezionare Blocca utente se viene segnalato un illecito.
6.	Nella casella di testo **Codice per segnalare illeciti durante il messaggio introduttivo iniziale** immettere un codice numerico da usare durante la verifica della chiamata. Se un utente immette questo codice insieme al simbolo # anziché solo il simbolo #, verrà segnalato un avviso di illecito. 
7.	Nella parte inferiore fare clic su Salva.

>[AZURE.NOTE]
I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere 0# per inviare un avviso di illecito. Se si usa un codice diverso da 0, sarà necessario registrare e caricare messaggi vocali personalizzati con le istruzioni appropriate.


<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### Per segnalare un avviso di illecito
Gli avvisi di illecito possono essere segnalati in due modi: mediante l'app per dispositivi mobili o tramite il telefono.

### Per segnalare un avviso di illecito con l'app per dispositivi mobili



1. Quando viene inviata al telefono una verifica, fare clic su di essa per avviare l'app Azure Authenticator.
2. Per segnalare l'illecito, fare clic su Annulla e segnala illecito. Verrà visualizzata una finestra che indica che il personale del supporto tecnico dell'organizzazione riceverà una notifica. 
3. Fare clic su Segnala illecito.
4. Nell'app fare clic su Chiudi.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### Per segnalare un avviso di illecito con il telefono

1. Quando si riceve una chiamata di verifica sul telefono, rispondere.</li>
2. Per segnalare l'illecito, immettere il codice che è stato configurato per la segnalazione di illeciti tramite il telefono e quindi il simbolo #. Verrà segnalato che è stato inviato un avviso di illecito.
3. Terminare la chiamata.

### Per visualizzare il report sull'illecito

1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Avviso di illecito in Visualizza un report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È inoltre possibile specificare determinati nomi utente, numeri di telefono e lo stato degli utenti.
7. Fare clic su Esegui. Verrà visualizzato un report simile a quello riportato di seguito. Se si desidera esportare il report, è inoltre possibile fare clic su Esporta in CSV.

## Bypass monouso

Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication. Il bypass è temporaneo e scade dopo il numero di secondi specificato. Pertanto, nelle situazioni in cui l'app per dispositivi mobili o il telefono non riceve una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

### Per creare un bypass monouso

1.	Accedere a http://azure.microsoft.com
2.	Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3.	Nel portale di gestione di Azure Multi-Factor Authentication se viene visualizzato il nome del tenant o del provider di Azure MFA a sinistra con accanto un segno +, fare clic sul segno + per vedere i diversi gruppi di replica del server MFA e il gruppo predefinito di Azure. Fare clic sul gruppo appropriato.
4.	In Amministrazione utenti fare clic su **Bypass monouso**. ![Cloud](./media/multi-factor-authentication-whats-next/create1.png)
5.	Nella pagina Bypass monouso fare clic su **Nuovo bypass monouso**.
6.	Immettere il nome utente, il numero di secondi per il bypass, il motivo del bypass e fare clic su **Bypass**. ![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7.	A questo punto, l'utente deve accedere prima della scadenza del bypass monouso.



### Per visualizzare il report del bypass monouso

1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Bypass monouso in Visualizza un report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È inoltre possibile specificare determinati nomi utente, numeri di telefono e lo stato degli utenti.
7. Fare clic su Esegui. Verrà visualizzato un report simile a quello riportato di seguito. Se si desidera esportare il report, è inoltre possibile fare clic su Esporta in CSV.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## Messaggi vocali personalizzati

I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. Possono essere usati in aggiunta alle registrazioni di Microsoft o per sostituirle.

Prima di iniziare, tenere presente quanto segue:

- I formati di file attualmente supportati sono WAV e MP3.
- La dimensione massima dei file è 5 MB.
- È consigliabile che la durata dei messaggi di autenticazione non sia superiore a 20 secondi. Qualsiasi valore superiore potrebbe impedire la verifica, in quanto l'utente potrebbe non riuscire a rispondere prima della fine del messaggio e del timeout della verifica.



### Per configurare i messaggi vocali personalizzati in Azure Multi-Factor Authentication
1.	Creare un messaggio vocale personalizzato usando uno dei formati di file supportati.
2.	Accedere a http://azure.microsoft.com
3.	Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
4.	Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Messaggi vocali.
5.	Nella sezione Messaggi vocali fare clic su **Nuovo messaggio vocale**. ![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6.	Nella pagina Configura: Nuovi messaggi vocali fare clic su **Gestisci file audio**. ![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7.	Nella pagina Configura: File audio fare clic su **Carica file audio**. ![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8.	In Configura: Carica file audio fare clic su **Sfoglia**, individuare il messaggio vocale e fare clic su **Apri**. ![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9.	Aggiungere una descrizione e fare clic su Carica.
10.	Al termine, verrà visualizzato un messaggio che indica che il file è stato caricato.
11.	A sinistra fare clic su Messaggi vocali.
12.	Nella sezione Messaggi vocali fare clic su Nuovo messaggio vocale.
13.	Dall'elenco a discesa Lingua selezionare una lingua.
14.	Se questo messaggio è per un'applicazione specifica, specificarlo nella casella Applicazione.
15.	In Tipo di messaggio selezionare il tipo di messaggio che verrà sostituito dal nuovo messaggio personalizzato.
16.	Nell'elenco a discesa File audio selezionare il file audio.
17.	Fare clic su **Crea**. Verrà visualizzato un messaggio che indica che è stato creato un messaggio vocale. ![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## Memorizzazione nella cache in Azure Multi-Factor Authentication

La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. Questa opzione viene usata principalmente quando i sistemi locali, ad esempio VPN, inviano più richieste di verifica mentre la prima richiesta è ancora in corso. In questo modo, dopo che l'utente ha completato la verifica in corso, le richieste successive hanno automaticamente esito positivo. Si noti che la memorizzazione nella cache non può essere usata per gli accessi ad Azure AD.


### Per impostare la memorizzazione nella cache in Azure Multi-Factor Authentication

1.	Accedere a http://azure.microsoft.com
2.	Passare al portale di gestione di MFA seguendo le istruzioni riportate nella parte superiore di questa pagina.
3.	Nel portale di gestione di Azure Multi-Factor Authentication, nella sezione Configura, fare clic su Memorizzazione nella cache.
4.	Nella pagina Configura memorizzazione nella cache fare clic su Nuova cache
5.	Selezionare il tipo di cache e i secondi per la cache. Fare clic su Crea.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## Indirizzi IP attendibili

Gli indirizzi IP attendibili sono una funzionalità di Multi-Factor Authentication che offre agli amministratori di un tenant gestito o federato la possibilità di ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet locale dell'azienda. Le funzionalità sono disponibili per i tenant di Azure AD che dispongono di licenze Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.

 
Tipo di tenant di Azure AD| Opzioni disponibili per gli indirizzi IP attendibili
:------------- | :------------- | 
Gestito|Intervalli di indirizzi IP specifici - Gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet dell'azienda.
Federato|<li>Tutti gli utenti federati - Tutti gli utenti federati che eseguono l'accesso all'interno dell'organizzazione possono ignorare Multi-Factor Authentication usando un'attestazione rilasciata da ADFS.</li><li>Intervalli di indirizzi IP specifici - Gli amministratori possono specificare un intervallo di indirizzi IP che possono ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet dell'azienda.

Questo bypass può essere usato solo all'interno della rete Intranet di un'azienda. Se ad esempio sono stati selezionati solo tutti gli utenti federati e un utente accede dall'esterno della rete Intranet aziendale, tale l'utente dovrà eseguire l'autenticazione usando Multi-Factor Authentication, anche se l'utente presenta un'attestazione ADFS. Nella tabella seguente viene descritto quando sono necessari Multi-Factor Authentication e le password dell'app all'interno e all'esterno della rete aziendale quando sono abilitati gli indirizzi IP attendibili.


|Indirizzi IP attendibili abilitati| Indirizzi IP attendibili disabilitati
:------------- | :------------- | :------------- | 
All'interno della rete aziendale|Per i flussi del browser Multi-Factor Authentication NON è necessaria.|Per i flussi del browser Multi-Factor Authentication è necessaria
|Per le app rich client, le password normali funzionano se l'utente non ha creato password dell'app. Dopo aver creato una password dell'app, le password dell'app sono necessarie.|Per le app rich client, le password dell'app sono necessarie
All'esterno della rete aziendale|Per i flussi del browser Multi-Factor Authentication è necessaria.|Per i flussi del browser Multi-Factor Authentication è necessaria.
|Per le app rich client, le password dell'app sono necessarie.|Per le app rich client, le password dell'app sono necessarie.

### Per abilitare gli indirizzi IP attendibili

1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory per cui si desidera impostare gli indirizzi IP attendibili.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, in Indirizzi IP attendibili, selezionare:

	- Per le richieste degli utenti federati originate dalla Intranet dell'utente: tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignoreranno Multi-Factor Authentication usando un'attestazione rilasciata da AD FS. 
	- Per le richieste da un intervallo specifico di IP pubblici: immettere gli indirizzi IP nelle caselle disponibili usando la notazione CIDR. Ad esempio: xxx.xxx.xxx.0/24 per gli indirizzi IP nell'intervallo xxx.xxx.xxx.1 – xxx.xxx.xxx.254 oppure xxx.xxx.xxx.xxx/32 per un singolo indirizzo IP. È possibile immettere fino a 50 intervalli di indirizzi IP.

7. Fare clic su Salva.
8. Una volta che gli aggiornamenti sono stati applicati, fare clic su Chiudi.



![IP attendibili](./media/multi-factor-authentication-whats-next/trustedips3.png)



 
## Password dell'app

In alcune app, come Office 2010 o versioni precedenti e Apple Mail, non è possibile usare Multi-Factor Authenticaton. Per usare queste app, è necessario usare "password dell'app" al posto della password tradizionale. Le password dell'app consentono all'applicazione di ignorare Multi-Factor Authentication e continuare a lavorare.

>[AZURE.NOTE] Autenticazione moderna per i client di Office 2013
>
> I Client di Office 2013 (tra cui Outlook) ora supportano i nuovi Protocolli di autenticazione e possono essere abilitati per supportare la Multi-Factor Authentication. Ciò significa che una volta attivati, le password della app non vengono richieste per l'utilizzo con i Client di Office 2013. Per altre informazioni, vedere l'[annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### Informazioni importanti sulle password dell'app

Di seguito è riportato un elenco delle informazioni importanti da conoscere sulle password dell'app.

Esperienza di autenticazione|Per le app basate su browser|Per le app non basate su browser
:------------- | :------------- | :------------- 
|<ul><li>Il primo fattore di autenticazione viene eseguito in locale</li><li>Il secondo fattore è un metodo basato sul telefono eseguito dall'identità cloud.</li>|<ul><li>Amministratori e utenti possono usare le password dell'app per effettuare l'accesso.

- Gli utenti possono avere più password dell'app, cosa che aumenta la superficie di attacco per i furti. Poiché le password dell'app sono difficili da ricordare, gli utenti potrebbero essere indotti ad annotarle. Questa operazione non è consigliabile e dovrebbe essere evitata, perché è necessario un solo fattore per l'accesso con la password dell'app.
- Le app che memorizzano le password nella cache e le usano in scenari locali potrebbero smettere di funzionare, perché la password dell'app non sarà nota all'esterno dell'ID organizzazione. Un esempio sono i messaggi di posta elettronica di Exchange in locale, mentre la posta archiviata si trova nel cloud. La stessa password non funzionerà.
- La password effettiva viene generata automaticamente e non viene fornita dall'utente. Questo avviene perché è più difficile per un utente malintenzionato indovinare una password generata automaticamente, aumentando quindi la sicurezza.
- Al momento esiste un limite di 40 password per utente. Verrà richiesto di eliminare una delle password dell'app esistenti prima di crearne una nuova.
- Dopo aver abilitato Multi-factor Authentication nell’account di un utente, le password di app possono essere utilizzate con la maggior parte dei client non basati su browser come Outlook e Lync, ma non è possibile eseguire azioni amministrative usando password di app tramite applicazioni non basate su browser come Windows PowerShell, anche se tale utente dispone di un account di amministrazione. Assicurarsi di creare un account di servizio con una password complessa per l'esecuzione degli script di PowerShell e di non abilitare tale account per Multi-factor Authentication.

>[AZURE.WARNING]  Le password di app non funzioneranno in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud.
>
>Tenere presente che le password di app non funzionano in ambienti ibridi dove i client comunicano con endpoint di individuazione automatica sia locali che nel cloud, poiché per l'autenticazione locale sono necessarie le password di dominio e per l’autenticazione nel cloud sono necessarie le password di app.


### Indicazioni per la denominazione delle password dell'app
Per le password dell'app è consigliabile usare nomi che riflettano il dispositivo in cui verranno usate. Ad esempio, se si dispone di un portatile con app non basate su browser come Outlook, Word ed Excel, è sufficiente creare una sola password dell'app denominata Portatile e usarla in tutte queste applicazioni. Sebbene sia possibile creare password distinte per tutte queste applicazioni, non è consigliabile. È preferibile usare una sola password dell'app per ogni dispositivo.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### Password dell'app federate (SSO)
Azure AD supporta la federazione con Servizi di dominio Active Directory (AD DS) di Windows Server in locale. Se l'organizzazione è federata (SSO) con Azure AD e si intende usare Azure Multi-Factor Authentication, di seguito sono riportate importanti informazioni che occorre tenere presente per l'uso di password dell'app. Questo vale solo per i clienti federati (SSO).

- La password dell'app è verificata da Azure AD e di conseguenza ignora la federazione. La federazione viene usata attivamente solo durante l'impostazione della password dell'app.
- Per gli utenti federati (SSO) non verrà mai usato il provider di identità (IdP), a differenza del flusso passivo. Le password verranno archiviate nell'ID organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync in tempo reale. La disabilitazione o l'eliminazione dell'account può richiedere fino a 3 ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione della password dell'app in Azure AD.
- Le impostazioni locali di controllo dell'accesso client non vengono rispettate dalla password dell'app
- Per la password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale
- Per il client Microsoft Lync 2013 è necessaria ulteriore formazione per gli utenti finali. Per i passaggi necessari, vedere Come modificare la password nella posta elettronica per la password dell'app.
- Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa Multi-Factor Authentication con i client, a seconda della posizione in cui viene eseguita l'autenticazione. Per i client che eseguono l'autenticazione con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione. Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.

Si supponga ad esempio di avere un'architettura costituita dagli elementi seguenti:

- Si sta eseguendo la federazione dell'istanza locale di Active Directory con Azure AD
- Si usa Exchange Online
- Si usa Lync, specificamente in locale
- Si usa Azure Multi-Factor Authentication


![Verifica](./media/multi-factor-authentication-whats-next/federated.png)

 In questi casi, è necessario eseguire le operazioni seguenti:

- Quando si esegue l'accesso a Lync, usare il nome utente e la password dell'organizzazione.
- Quando si tenta di accedere alla Rubrica tramite un client Outlook che si connette a Exchange Online, usare una password dell'app.

### Consentire la creazione della password dell'app
Per impostazione predefinita, gli utenti non possono creare password dell'app. Questa funzionalità deve essere abilitata. Per consentire agli utenti di creare password dell'app, attenersi alla procedura seguente.

#### Per consentire agli utenti di creare password dell'app



1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente che si desidera attivare.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Nella parte superiore della pagina Multi-Factor Authentication fare clic su Impostazioni servizio.
7. Assicurarsi che il pulsante di opzione Consentire agli utenti di creare password dell'app per accedere alle applicazioni non basate su browser sia selezionato.


![Creazione di password dell'app](./media/multi-factor-authentication-whats-next/trustedips3.png)

### Creazione di password dell'app
Gli utenti possono creare password dell'app durante la registrazione iniziale. Al termine del processo di registrazione, viene offerta loro un'opzione che consente di crearle.

Gli utenti possono inoltre creare password dell'app in un secondo momento modificando le impostazioni nel portale di Azure o nel portale di Office 365.

### Per creare password dell'app nel portale di Office 365
--------------------------------------------------------------------------------


1. Accedere al portale di Office 365.
2. Nell'angolo superiore destro selezionare il widget delle impostazioni.
3. A sinistra selezionare Verifica aggiuntiva di sicurezza.
4. A destra selezionare **Aggiorna i numeri telefonici usati per la sicurezza dell'account**
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Per creare password dell'app nel portale di Azure
--------------------------------------------------------------------------------
1. Accedere al portale di Azure classico.
3. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.


![Password dell'app](./media/multi-factor-authentication-whats-next/app2.png)

### Per creare password dell'app se non si ha una sottoscrizione di Office 365 o Azure
--------------------------------------------------------------------------------
1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Nella parte superiore selezionare il profilo.
3. Fare clic sul nome utente e selezionare Verifica aggiuntiva di sicurezza.
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

![Password dell'app](./media/multi-factor-authentication-whats-next/myapp.png)

## Memorizzare Multi-Factor Authentication per i dispositivi che gli utenti considerano attendibili

La memorizzazione di Multi-Factor Authentication per i dispositivi e i browser che gli utenti considerano attendibili è una funzionalità disponibile gratuitamente per tutti gli utenti di MFA. Con questa funzionalità gli utenti possono scegliere di ignorare MFA per un numero determinato di giorni dopo l'esecuzione di un accesso tramite MFA. In questo modo l'usabilità per gli utenti è maggiore.

Tuttavia, poiché agli utenti viene consentito di memorizzare MFA per i dispositivi attendibili, questa funzionalità può ridurre la sicurezza degli account. Per garantire la sicurezza degli account, è necessario ripristinare Multi-Factor Authentication per i dispositivi in entrambi gli scenari seguenti:

- Se l'account aziendale dell'utente è stato compromesso
- Se un dispositivo memorizzato viene smarrito o rubato

> [AZURE.NOTE] Questa funzionalità è implementata come una cache di cookie del browser. Non funzionerà se i cookie del browser non sono abilitati.

### Come abilitare o disabilitare la memorizzazione dell'autenticazione a più fattori

1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Active Directory fare clic sulla directory per cui si desidera impostare la memorizzazione di Multi-Factor Authentication per i dispositivi.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, nelle impostazioni di gestione dei dispositivi utente, selezionare o deselezionare **Consenti agli utenti di memorizzare l'autenticazione a più fattori nei dispositivi attendibili**. ![Memorizzare dispositivi](./media/multi-factor-authentication-whats-next/remember.png)
8. Impostare il numero di giorni per cui si vuole consentire la sospensione. Il valore predefinito è 14 giorni.
9. Fare clic su Salva.
10. Fare clic su Chiudi.


## Metodi di verifica selezionabili
È ora possibile scegliere i metodi di autenticazione disponibili per gli utenti quando si usa Azure Multi-Factor Authentication. Questa funzionalità è stata precedentemente disponibile solo nella versione del server locale. La tabella seguente fornisce una breve panoramica dei vari metodi di autenticazione che possono essere abilitati o disabilitati per gli utenti.

Metodo|Descrizione
:------------- | :------------- | 
[Chiamata al telefono](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Invia una chiamata vocale automatizzata sul telefono predisposto all'autenticazione. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non verrà sincronizzato con Active Directory locale.
[SMS al telefono](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Invia un messaggio di testo contenente un codice di verifica per l'utente. L'utente deve rispondere al messaggio con il codice di verifica o immettere il codice di verifica nell'interfaccia di accesso.
[Notifica tramite app per dispositivi mobili](multi-factor-authentication-end-user-first-time-mobile-app.md)|In questa modalità l'app Azure Authenticator impedisce l'accesso agli account non autorizzati e arresta le transazioni illecite. L’avvertenza è una notifica push inviata sul telefono o sul dispositivo registrato. Visualizzare la notifica e, se del caso, selezionare Verifica. In caso contrario, è possibile scegliere Nega oppure Nega e segnala la notifica illecita. Per informazioni sulla segnalazione di notifiche illecite, vedere l'argomento relativo a come usare le funzionalità Nega e Segnala illecito per Multi-Factor Authentication.</br></br>L'app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/it-IT/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).|
[Codice di verifica dall'app per dispositivi mobili](multi-factor-authentication-end-user-first-time-mobile-app.md)|In questa modalità, l'app Azure Authenticator può essere usata come token software per generare un codice di verifica OATH. Questo codice di verifica quindi può pertanto essere immesso con il nome utente e con la password come seconda modalità di autenticazione.</li><br><p> L'app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/it-IT/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

### Come abilitare o disabilitare i metodi di autenticazione

1. Accedere al portale di Azure classico.
2. A sinistra fare clic su Active Directory.
3. In Active Directory fare clic sulla directory per cui si vogliono abilitare o disabilitare i metodi di autenticazione.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio, nelle opzioni di verifica, selezionare o deselezionare le opzioni da usare.</br></br> ![Opzioni di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.

<!---HONumber=AcomDC_0413_2016-->