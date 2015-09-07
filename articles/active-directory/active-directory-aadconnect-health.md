<properties 
	pageTitle="Monitorare l’infrastruttura di identità locale nel cloud."
	description="Si tratta della pagina di Azure AD Connect Health in cui viene descritto Azure AD Connect Health e il relativo utilizzo."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="billmath"/>

# Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud

Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione disponibili tramite Azure AD Connect. Lo strumento offre la possibilità di visualizzare gli avvisi, le prestazioni, i modelli di utilizzo, le impostazioni di configurazione, consente di mantenere una connessione affidabile a Office 365 e molto altro ancora. Tutto ciò viene realizzato mediante un agente installato nei server di destinazione.

![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Queste informazioni vengono visualizzate tutte nel portale di Azure AD Connect Health. Il portale di Azure AD Connect Health consente di visualizzare gli avvisi, il monitoraggio delle prestazioni e i dati analitici di utilizzo. Queste informazioni vengono visualizzate tutte in un'unica posizione facile da utilizzare, in modo da non sprecare tempo per la ricerca delle informazioni di cui si ha bisogno.

![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Gli aggiornamenti futuri ad Azure AD Connect Health includeranno ulteriore monitoraggio e informazioni dettagliate su altri componenti di identità e servizi, ad esempio i servizi Azure AD Connect Sync. In tal modo, viene fornito un dashboard unico dal punto di vista delle identità, consentendo di disporre di un ambiente più affidabile, integro e integrato che gli utenti possono sfruttare per aumentare la capacità di svolgere il proprio lavoro.


<center>![Informazioni su Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)</center>




## Perché utilizzare Azure AD Connect Health

L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Tuttavia, questa integrazione comporta la difficoltà di garantire che questo ambiente sia integro, per consentire agli utenti di accedere in modo affidabile alle risorse locali e cloud da qualsiasi dispositivo. Azure AD Connect Health fornisce un approccio semplice, basato sul cloud, per monitorare e ottenere informazioni dettagliate sull’infrastruttura di identità locale utilizzata per accedere a Office 365 o ad altre applicazioni Azure AD. È semplice come installare un agente in ogni server di identità locale in uso.

Azure AD Connect Health per ADFS supporta AD FS 2.0 in Windows Server 2008/2008 R2, ADFS in Windows Server 2012/2012R2. È inoltre incluso qualsiasi server Proxy ADFS o Proxy applicazione Web Application che fornisce il supporto per l’autenticazione per l’accesso alla rete Extranet. Azure AD Connect Health per ADFS fornisce il seguente insieme di funzionalità chiave:

- Visualizzazione degli avvisi e intervento per un accesso affidabile alle applicazioni di ADFS protette tra cui Azure AD
- Notifiche tramite posta elettronica per gli avvisi critici
- Visualizzazione dei dati sulle prestazioni per determinare la pianificazione della capacità
- Visualizzazioni dettagliate dei modelli di accesso di ADFS per determinare le anomalie o stabilire linee di base per la pianificazione della capacità

Nel video seguente viene fornita una panoramica di Azure AD Connect Health:

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]



## Iniziare a usare il portale di Azure
Per iniziare a usare Azure Active Directory Connect Health, attenersi alla procedura seguente.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com/).
2. Per accedere ad Azure Active Directory Connect Health, andare al Marketplace e cercarlo oppure selezionare Marketplace e selezionare Sicurezza e identità.
3. Nel pannello introduttivo (un pannello è una parte della visualizzazione complessiva, paragonabile a una finestra o a un riquadro a comparsa) fare clic su **Crea**. Verrà aperto un altro pannello con le informazioni sulla directory.
4. Nel pannello della directory fare clic su **Crea**. Se non si dispone di una licenza di Azure Active Directory Premium, sarà necessario usare Azure AD Connect Health. Per informazioni su Azure AD Premium, vedere Introduzione a Azure AD Premium.

>[AZURE.NOTE]Tenere presente che, prima di visualizzare i dati nell'istanza di Azure AD Connect Health, è necessario installare l'agente di Azure AD Connect Health nei server di destinazione. Per scaricare l'agente di Azure AD Connect Health, nel primo pannello selezionare Avvio rapido e Scarica strumenti. Per scaricare l'agente direttamente, fare clic sul [collegamento](#download-the-agent) seguente. Per usare Azure Active Directory Connect Health, procedere come segue:



### Portale e servizi di Azure AD Connect Health
Il portale di Azure AD Connect Health consente di visualizzare gli avvisi, il monitoraggio delle prestazioni e i dati analitici di utilizzo. Al primo accesso ad Azure AD Connect Health, verrà visualizzato il primo pannello. paragonabile a una finestra. Il primo pannello visualizzato mostra Avvio rapido, Servizi e Configura. Sotto la schermata è disponibile una breve descrizione di ciascun elemento. Questa sezione mostra i servizi attivi e le istanze dei servizi monitorate da Azure AD Connect Health.

![Portale di Azure AD Connect Health](./media/active-directory-aadconnect-health/portal2.png)

- **Avvio rapido**: selezionando questa opzione, viene visualizzato il pannello Avvio rapido. in cui è possibile scaricare l'agente di Azure AD Connect Health scegliendo Scarica strumenti, accedere alla documentazione e fornire commenti e suggerimenti.
- **Active Directory Federation Services**: indica tutti i servizi di AD FS attualmente monitorati da Azure AD Connect Health. Selezionando una delle istanze, verrà visualizzato un altro pannello che contiene informazioni su tale istanza del servizio. Queste informazioni includono una panoramica, le proprietà, gli avvisi, informazioni di monitoraggio e analisi di utilizzo. 
- Configura: consente di attivare e disattivare i comportamenti seguenti:
<ol>
1. Aggiornamento automatico dell'agente di Azure AD Connect Health alla versione più recente: l'agente di Azure AD Connect Health viene aggiornato automaticamente ogni volta che è disponibile una nuova versione. Questa opzione è abilitata per impostazione predefinita.
2. Accesso ai dati di integrità della directory di Azure AD da parte di Microsoft solo ai fini della risoluzione dei problemi: se questa opzione è abilitata, Microsoft potrà accedere agli stessi dati visualizzati dall'utente. Questa opzione può essere utile per la risoluzione dei problemi e per fornire l'assistenza necessaria. Questa opzione è disabilitata per impostazione predefinita.





## Requisiti
La tabella seguente rappresenta un elenco di requisiti che devono essere soddisfatti per poter usare il servizio Azure AD Connect Health.

| Requisito | Descrizione|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health è una funzionalità di Azure AD Premium e richiede una licenza di Azure AD Premium. </br></br>Per altre informazioni, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).</br></br>Per ottenere una versione di valutazione gratuita valida 30 giorni, vedere [Avvia una versione di valutazione gratuita.](https://azure.microsoft.com/trial/get-started-active-directory/)|.
|È necessario essere un amministratore globale della directory di Azure AD|Per impostazione predefinita, gli amministratori globali possono accedere alle informazioni fornite da Azure AD Connect Health. Se non si è un amministratore globale della directory di Azure AD, non sarà possibile creare un'istanza del servizio Azure AD Connect Health. Assicurarsi di essere un amministratore globale. Per altre informazioni, vedere [Amministrare la directory di Azure AD](active-directory-administer.md).</br></br>**Importante:** l'account che si usa per l'installazione degli agenti deve essere un account aziendale o dell'istituto di istruzione e non può essere un account Microsoft. Per altre informazioni, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md)|
|Per ADFS, è necessario che sia attivato il controllo di ADFS perché sia possibile usare la funzionalità di analisi dei dati di utilizzo| Se si prevede di usare le analisi di utilizzo con AD FS, è necessario abilitare AD FS. </br></br>Vedere [Abilitare il controllo per AD FS.](active-directory-aadconnect-health-operations.md#enable-auditing-for-ad-fs)
|Requisiti dell'agente di Azure AD Connect Health|Per informazioni sui requisiti specifici dell'agente, vedere la tabella seguente.

La tabella seguente mostra un elenco di requisiti dell'agente che devono essere soddisfatti per poter usare il servizio Azure AD Connect Health.

| Requisito | Descrizione|
| ----------- | ---------- |
|Agente di Azure AD Connect Health installato in ogni server di destinazione| Per Azure AD Connect Health è necessario che nei server di destinazione sia installato un agente, che fornirà i dati visualizzati nel portale. </br></br>Questo significa che, per poter ottenere i dati nell'infrastruttura locale di AD FS, l'agente deve essere installato nei server AD FS. inclusi i server proxy ADFS e i server proxy applicazione Web. </br></br>Per informazioni su come installare l'agente, vedere [Installazione dell'agente di Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md).</br></br>**Importante:** l'account che si usa per l'installazione degli agenti deve essere un account aziendale o dell'istituto di istruzione e non può essere un account Microsoft. Per altre informazioni, vedere [Iscrizione ad Azure come organizzazione](sign-up-organization.md)|
|Connettività in uscita agli endpoint di servizio di Azure|Durante l'installazione e la fase di esecuzione, l'agente richiede la connettività agli endpoint di servizio di Azure AD Connect Health elencati sotto. Se si blocca la connettività in uscita, assicurarsi che gli endpoint seguenti vengano aggiunti all'elenco di quelli consentiti: </br></br><li>&#42;.servicebus.windows.net - Porta: 5671</li><li>https://&#42;.adhybridhealth.azure.com/</li><li>https://&#42;.table.core.windows.net/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Porte del firewall nel server che esegue l'agente.| Per consentire la comunicazione dell'agente con gli endpoint del servizio Azure AD Connect Health è necessario che le porte del firewall seguenti siano aperte.</br></br><li>Porta TCP/UDP 80</li><li>Porta TCP/UDP 443</li>
|Consentire i siti Web seguenti se è abilitata la funzionalità Protezione avanzata di IE|I siti Web seguenti devono essere consentiti se la funzionalità Protezione avanzata di Internet Explorer è abilitata nel server in cui verrà installato l'agente.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Server federativo dell'organizzazione considerato attendibile da Azure Active Directory. Ad esempio: https://sts.contoso.com</li> 

## Scaricare l'agente

Per una guida introduttiva su Azure AD Connect Health, è possibile scaricare la versione più recente dell’agente da qui: [Download dell’agente di Azure AD Connect Health.](http://go.microsoft.com/fwlink/?LinkID=518973) Assicurarsi di aver aggiunto il servizio da Marketplace prima di installare gli agenti.


## Collegamenti correlati

* [Installazione dell'agente di Azure AD Connect Health per AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)


 

<!---HONumber=August15_HO9-->