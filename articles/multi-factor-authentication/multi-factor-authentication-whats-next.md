<properties 
	pageTitle="Azure Multi-Factor Authentication - Passaggi successivi" 
	description="Questa è la pagina di Azure Multi-Factor Authentication che descrive le successive azioni da eseguire con Multi-Factor Authentication. Sono inclusi i report, gli avvisi di illecito, il bypass monouso, i messaggi vocali personalizzati, la memorizzazione nella cache, gli indirizzi IP attendibili e le password dell'app." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Configurazione di Azure Multi-Factor Authentication

L'articolo seguente consentirà di gestire Azure Multi-Factor Authentication ora che si è operativi. Viene illustrata un'ampia gamma di argomenti che consentiranno di ottenere il massimo da Azure Multi-Factor Authentication. Tenere presente che non tutte queste funzionalità sono disponibili in ogni versione di Azure Multi-Factor Authentication.

Funzionalità| Descrizione| Argomenti trattati
:------------- | :------------- | :------------- | 
[Avviso di illecito](#fraud-alert)|Avviso di illecito può essere configurato e impostato in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse.|Come impostare, configurare e segnalare gli illeciti
[Bypass monouso](#one-time-bypass) |Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication.|Come impostare e configurare un bypass monouso
[Messaggi vocali personalizzati](#custom-voice-messages) |I messaggi vocali personalizzati consentono di usare registrazioni o messaggi introduttivi personalizzati con Multi-Factor Authentication. |Come impostare e configurare messaggi introduttivi e messaggi personalizzati
[Memorizzazione nella cache](#caching)|La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. |Come impostare e configurare la memorizzazione nella cache dell'autenticazione.
[Indirizzi IP attendibili](#trusted-ips)|Gli indirizzi IP attendibili sono una funzionalità di Multi-Factor Authentication che offre agli amministratori di un tenant gestito o federato la possibilità di ignorare Multi-Factor Authentication per gli utenti che accedono dalla rete Intranet locale dell'azienda.|Configurare e impostare gli indirizzi IP che non sono interessati da Multi-Factor Authentication	
[Password dell'app](#app-passwords)|Le password dell'app consentono a un'applicazione che non è in grado di riconoscere Multi-Factor Authentication di ignorare questa funzionalità e continuare a lavorare.|Informazioni sulle password dell'app.
[Sospensione di Multi-Factor Authentication per dispositivi e browser memorizzati (anteprima pubblica)](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|Consente di sospendere Multi-Factor Authentication per un determinato numero di giorni dopo che un utente ha effettuato correttamente l'accesso tramite Multi-Factor Authentication.|Informazioni sull'abilitazione di questa funzionalità e sull'impostazione del numero di giorni.




## Avviso di illecito
Avviso di illecito può essere configurato e impostato in modo che gli utenti possano segnalare i tentativi illeciti di accedere alle loro risorse. Gli utenti possono segnalare gli illeciti con l'app per dispositivi mobili o tramite il telefono.

### Per impostare e configurare gli avvisi di illecito


1. Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello per cui si desidera abilitare gli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Impostazioni.
6. Nella sezione Avviso di illecito selezionare Consenti agli utenti di inviare avvisi di illeciti.
7. Se si desidera che gli utenti siano bloccati quando viene segnalato un illecito, selezionare Blocca utente se viene segnalato un illecito.
8. Nella casella sotto Codice per segnalare illeciti durante il messaggio introduttivo iniziale immettere un codice numerico che può essere usato durante la verifica della chiamata. Se un utente immette questo codice anziché il simbolo #, verrà segnalato un avviso di illecito.
9. Nella parte inferiore fare clic su Salva.

<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### Per segnalare un avviso di illecito
Gli avvisi di illecito possono essere segnalati in due modi: mediante l'app per dispositivi mobili o tramite il telefono.

### Per segnalare un avviso di illecito con l'app per dispositivi mobili
<ol>
<li>Quando viene inviata al telefono una verifica, fare clic su di essa e sarà avviata l'app Multi-Factor Authentication.</li>
<li>Per segnalare l'illecito, fare clic su Annulla e segnala illecito. Verrà visualizzata una finestra che indica che il personale del supporto tecnico</li> dell'organizzazione verrà informato. Fare clic su Segnala illecito.
<li>Nell'app fare clic su Chiudi.</li></ol>

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### Per segnalare un avviso di illecito con il telefono
<ol>
<li>Quando si riceve una chiamata di verifica sul telefono, rispondere.</li>
<li>Per segnalare l'illecito, immettere il codice che è stato configurato per la segnalazione di illeciti tramite il telefono e quindi il simbolo #. Verrà segnalato che è stato inviato un avviso di illecito.</li>
<li>Terminare la chiamata.</li></ol>

### Per visualizzare il report sull'illecito

1. Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Avviso di illecito in Visualizza un report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È inoltre possibile specificare determinati nomi utente, numeri di telefono e lo stato degli utenti.
7. Fare clic su Esegui. Verrà visualizzato un report simile a quello riportato di seguito. Se si desidera esportare il report, è inoltre possibile fare clic su Esporta in CSV.

## Bypass monouso

Un bypass monouso consente a un utente di eseguire l'autenticazione una sola volta "ignorando" Multi-Factor Authentication. Il bypass è temporaneo e scade dopo il numero di secondi specificato. Pertanto, nelle situazioni in cui l'app per dispositivi mobili o il telefono non riceve una notifica o una chiamata telefonica, è possibile abilitare un bypass monouso in modo che l'utente possa accedere alla risorsa desiderata.

### Per creare un bypass monouso

<ol>
<li>Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)</li>
<li>A sinistra selezionare Active Directory.</li>
<li>Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.</li>
<li>Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello associato alla directory per l'utente per cui si desidera creare un bypass monouso e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.</li>
<li>Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Impostazioni in Amministrazione utenti.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create1.png)</center>

<li>Nella pagina Bypass monouso fare clic su Nuovo bypass monouso.</li>
<li>Immettere il nome utente, il numero di secondi per il bypass, il motivo del bypass e fare clic su Bypass.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/create2.png)</center>

<li>A questo punto, l'utente deve accedere prima della scadenza del bypass monouso.</li>

### Per visualizzare il report del bypass monouso

1. Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)
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
<ol>
<li>Creare un messaggio vocale personalizzato usando uno dei formati di file supportati. Vedere le raccomandazioni per i messaggi vocali personalizzati riportate di seguito.</li>
<li>Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)</li>
<li>A sinistra selezionare Active Directory.</li>
<li>Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.</li>
<li>Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello per cui si desidera configurare il messaggio vocale personalizzato e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.</li>
<li>Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Messaggi vocali.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)</center>

<li>Nella sezione Messaggi vocali fare clic su Nuovo messaggio vocale.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)</center>

<li>Nella pagina Configura: Nuovo messaggio vocale fare clic su Gestisci file audio.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)</center>

<li>Nella pagina Configura: File audio fare clic su Carica file audio.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)</center>

<li>Nella pagina Configura: Carica file audio fare clic su Sfoglia, individuare il messaggio vocale e fare clic su Apri.</li>
<li>Aggiungere una descrizione e fare clic su Carica.</li>
<li>Al termine, verrà visualizzato un messaggio che indica che il file è stato caricato.</li>
<li>A sinistra fare clic su Messaggi vocali.</li>
<li>Nella sezione Messaggi vocali fare clic su Nuovo messaggio vocale.</li>
<li>Dall'elenco a discesa Lingua selezionare una lingua.</li>
<li>Se questo messaggio è per un'applicazione specifica, specificarlo nella casella Applicazione.</li>
<li>In Tipo di messaggio selezionare il tipo di messaggio che verrà sostituito dal nuovo messaggio personalizzato.</li>
<li>Nell'elenco a discesa File audio selezionare il file audio.</li>
<li>Fare clic su Crea. Verrà visualizzato un messaggio che indica che è stato creato un messaggio vocale.</li>

<center>![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>


## Memorizzazione nella cache in Azure Multi-Factor Authentication

La memorizzazione nella cache consente di impostare uno specifico periodo di tempo in modo che i tentativi di autenticazione successivi abbiano automaticamente esito positivo. Questo consente agli utenti di evitare di dover attendere le chiamate telefoniche o i messaggi di testo se eseguono l'autenticazione entro questo periodo di tempo.



### Per impostare la memorizzazione nella cache in Azure Multi-Factor Authentication
<ol>

1. Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello per cui si desidera abilitare gli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra fare clic su Memorizzazione nella cache.
6. Nella pagina Configura memorizzazione nella cache fare clic su Nuova cache
7. Selezionare il tipo di cache e i secondi per la cache. Fare clic su Crea.


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

 
<ol>
<li>Accedere al portale di gestione di Azure.</li>
<li>A sinistra fare clic su Active Directory.</li>
<li>In Directory fare clic sulla directory per cui si desidera impostare gli indirizzi IP attendibili.</li>
<li>Per la directory selezionata fare clic su Configura.</li>
<li>Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.</li>
<li>Nella pagina Impostazioni servizio, in Indirizzi IP attendibili, selezionare: <ul> <li>Per le richieste degli utenti federati originate dalla Intranet dell'utente - Tutti gli utenti federati che eseguono l'accesso dalla rete aziendale ignoreranno Multi-Factor Authentication usando un'attestazione rilasciata da ADFS.

<li>Per le richieste da un intervallo specifico di IP pubblici - Immettere gli indirizzi IP nelle caselle disponibili usando la notazione CIDR. Ad esempio: xxx.xxx.xxx.0/24 per gli indirizzi IP nell'intervallo xxx.xxx.xxx.1 – xxx.xxx.xxx.254 oppure xxx.xxx.xxx.xxx/32 per un singolo indirizzo IP. È possibile immettere fino a 12 intervalli di indirizzi IP.</li></ul>


<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>


<li>Fare clic su Salva.</li>
<li>Una volta che gli aggiornamenti sono stati applicati, fare clic su Chiudi.</li>
## Password dell'app

In alcune app, come Office 2010 o versioni precedenti e Apple Mail, non è possibile usare Multi-Factor Authenticaton. Per usare queste app, è necessario usare "password dell'app" al posto della password tradizionale. Le password dell'app consentono all'applicazione di ignorare Multi-Factor Authentication e continuare a lavorare.

>[AZURE.NOTE]Autenticazione moderna per i client di Office 2013
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


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 In questi casi, è necessario eseguire le operazioni seguenti:

- Quando si esegue l'accesso a Lync, usare il nome utente e la password dell'organizzazione.
- Quando si tenta di accedere alla Rubrica tramite un client Outlook che si connette a Exchange Online, usare una password dell'app.

### Consentire la creazione della password dell'app
Per impostazione predefinita, gli utenti non possono creare password dell'app. Questa funzionalità deve essere abilitata. Per consentire agli utenti di creare password dell'app, attenersi alla procedura seguente.

#### Per consentire agli utenti di creare password dell'app



1. Accedere al portale di gestione di Azure.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory relativa all'utente che si desidera attivare.
4. Nella parte superiore fare clic su Utenti.
5. Nella parte inferiore della pagina fare clic su Gestisci Multi-Factor Auth. Verrà visualizzata la pagina Multi-Factor Authentication.
6. Nella parte superiore della pagina Multi-Factor Authentication fare clic su Impostazioni servizio.
7. Assicurarsi che il pulsante di opzione Consentire agli utenti di creare password dell'app per accedere alle applicazioni non basate su browser sia selezionato.

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

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
6. Fare clic su **Crea**.
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Per creare password dell'app nel portale di Azure
--------------------------------------------------------------------------------
1. Accedere al portale di gestione di Azure
3. Nella parte superiore fare clic con il pulsante destro del mouse sul nome utente, quindi scegliere Verifica aggiuntiva di sicurezza.
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**.
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### Per creare password dell'app se non si ha una sottoscrizione di Office 365 o Azure
--------------------------------------------------------------------------------
1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Nella parte superiore selezionare il profilo.
3. Fare clic sul nome utente e selezionare Verifica aggiuntiva di sicurezza.
5. Nella parte superiore della pagina di verifica selezionare le password dell'app.
6. Fare clic su **Crea**.
7. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.
8. Copiare la password dell'app negli Appunti, quindi incollarla nell'app.

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## Sospensione di Multi-Factor Authentication per dispositivi e browser memorizzati (anteprima pubblica)

La sospensione di Multi-Factor Authentication per dispositivi e browser memorizzati è una funzionalità che consente di offrire agli utenti la possibilità di sospendere Multi-Factor Authentication per un determinato numero di giorni dopo l'esecuzione di un accesso tramite tale funzionalità. Si tratta di una funzionalità gratuita per tutti gli utenti di Multi-Factor Authentication e consente di migliorare l'usabilità per gli utenti. Tuttavia, poiché agli utenti viene consentito di sospendere Multi-Factor Authentication, questa funzionalità può ridurre la sicurezza degli account.

Per garantire la sicurezza degli account utente, è necessario ripristinare Multi-Factor Authentication per i dispositivi in entrambi gli scenari seguenti:

- Se l'account aziendale dell'utente è stato compromesso
- Se un dispositivo memorizzato viene smarrito o rubato

> [AZURE.NOTE]Questa funzionalità è implementata come una cache di cookie del browser. Non funzionerà se i cookie del browser non sono abilitati.

### Come abilitare o disabilitare la sospensione di Multi-Factor Authentication per i dispositivi memorizzati

<ol>
<li>Accedere al portale di gestione di Azure.</li>
<li>A sinistra fare clic su Active Directory.</li>
<li>In Active Directory fare clic sulla directory per cui si desidera impostare la sospensione di Multi-Factor Authentication per i dispositivi memorizzati.</li>
<li>Per la directory selezionata fare clic su Configura.</li>
<li>Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.</li>
<li>Nella pagina Impostazioni servizio, in Gestisci impostazioni del dispositivo utente, selezionare o deselezionare **Consenti agli utenti di sospendere Multi-Factor Authentication memorizzando i relativi dispositivi**.</li>
![Sospensione dei dispositivi](./media/multi-factor-authentication-manage-users-and-devices/suspend.png) <li>Impostare il numero di giorni per cui si desidera consentire la sospensione. Il valore predefinito è 14 giorni.</li> <li>Fare clic su Salva.</li> <li>Fare clic su Chiudi.</li>

<!---HONumber=August15_HO6-->