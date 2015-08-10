<properties 
	pageTitle="Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery" 
	description="Questo argomento illustra le considerazioni sulla sicurezza e sulla privacy correlate a Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery

Microsoft si impegna a proteggere la privacy e i dati degli utenti, mettendo a disposizione software e servizi che aiutino a gestire la sicurezza dell'organizzazione. <br> Microsoft è a conoscenza della necessità di investimenti e competenze per la progettazione di rigorose misure di sicurezza quando si affidano i dati a terzi. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalle procedure consigliate per il ciclo di vita dello sviluppo di software sicuro alla gestione di un servizio. <br> La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft.

Questo argomento descrive le modalità di raccolta, elaborazione e protezione dei dati in Azure Active Directory Cloud App Discovery.




##Panoramica

Cloud App Discovery è una funzionalità di Azure AD ed è ospitata in Microsoft Azure. <br> Gli agenti di Cloud App Discovery Endpoint Agent vengono usati per raccogliere dati di individuazione applicazioni dai computer gestiti dal reparto IT. <br> I dati raccolti vengono inviati in modo sicuro su un canale crittografato al servizio Azure AD Cloud App Discovery. <br> I dati di Cloud App Discovery per un'organizzazione saranno quindi visibili nel portale di Azure.


<center>![Funzionamento di Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


Le sezioni seguenti seguono il flusso di informazioni e illustrano il modo in cui viene protetto durante il trasferimento dall'organizzazione al servizio Cloud App Discovery e infine al portale di Cloud App Discovery.



## Raccolta di dati dall'organizzazione

Per usare la funzionalità Cloud App Discovery di Azure Active Directory per ottenere informazioni approfondite sulle applicazioni usate dai dipendenti dell'organizzazione, sarà prima di tutto necessario distribuire gli agenti di Azure AD Cloud App Discovery Endpoint Agent nei computer dell'organizzazione.

Gli amministratori del tenant di Azure Active Directory o i rispettivi delegati possono scaricare il pacchetto di installazione dell'agente dal portale di Azure. Gli agenti possono essere installati manualmente oppure possono essere installati in più computer dell'organizzazione tramite SCCM o Criteri di gruppo.

Per altre istruzioni sulle opzioni di distribuzione, vedere la pagina relativa alla [Guida alla distribuzione di Criteri di gruppo in Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Dati raccolti dall'agente

Le informazioni incluse nell'elenco seguente vengono raccolte dall'agente quando vengono stabilite connessioni alle applicazioni Web. Le informazioni vengono raccolte solo per le applicazioni configurate dall'amministratore per l'individuazione applicazioni. <br> L'elenco di applicazioni per cui si sceglie di raccogliere i metadati può essere configurato nella scheda Impostazioni del portale.



> [AZURE.NOTE]Per altri dettagli, vedere la pagina [introduttiva per Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 
**Categoria delle informazioni**: Informazioni sull'utente<br> **Descrizione**: <br> Nome utente di Windows del processo che ha effettuato una richiesta all'applicazione Web di destinazione (ad esempio DOMINIO\\nomeutente), oltre al SID (Security Identifier) Windows dell'utente.


**Categoria delle informazioni**: Informazioni sul processo <br> **Descrizione**: <br> Nome del processo che ha effettuato la richiesta all'applicazione Web di destinazione (ad esempio "iexplore.exe").

**Categoria delle informazioni**: Informazioni sul computer <br> **Descrizione**: <br> Nome del computer NetBIOS in cui è installato l'agente.

**Categoria delle informazioni**: Informazioni sul traffico dell'app <br> **Descrizione**: <br>

Informazioni sulla connessione

- Indirizzi IP di origine (computer locale) e destinazione e numeri di porta 

- Indirizzo IP pubblico dell'organizzazione attraverso cui viene trasmessa la richiesta.

- Ora della richiesta.

- Volume del traffico inviato e ricevuto.

- Versione IP (4 o 6)

- Solo per connessioni TLS: nome host di destinazione dall'estensione Indicazione nome server o dal certificato del server.

Informazioni HTTP seguenti:

- Metodo (GET, POST e così via) 

- Protocollo (HTTP/1.1 e così via)

- Stringa agente utente

- Nome host

- URI di destinazione (esclusa la stringa di query)

- Informazioni sul tipo di contenuto

- Informazioni sull'URL di referrer (esclusa la stringa di query)



> [AZURE.NOTE]Le informazioni HTTP precedenti vengono raccolte per tutte le connessioni non crittografate. Per le connessioni TLS queste informazioni vengono acquisite solo se l'impostazione Ispezione approfondita è attivata nel portale. Questa impostazione è attivata per impostazione predefinita. Per altri dettagli, vedere la pagina [introduttiva per Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 

 
### Funzionamento dell'agente

L'installazione dell'agente include due componenti:

- Un componente per la modalità utente

- Un componente per il driver della modalità kernel (driver della Piattaforma filtro Windows)



Quando l'agente viene installato per la prima volta, archivia nel computer un certificato attendibile specifico del computer e lo usa quindi per stabilire una connessione sicura al servizio Cloud App Discovery. <br> L'agente recupera periodicamente la configurazione dei criteri dal servizio Cloud App Discovery tramite questa connessione sicura. <br> I criteri includono ad esempio informazioni sulle applicazioni cloud da monitorare e sulla necessità di abilitare o meno l'aggiornamento automatico.

Quando il traffico Web viene inviato e ricevuto nel computer, Cloud App Discovery analizza il traffico ed estrae i metadati rilevanti (vedere la tabella precedente). <br> L'agente carica ogni minuto i metadati raccolti nel servizio Cloud App Discovery su un canale crittografato.

Per le connessioni crittografate viene eseguito un passaggio aggiuntivo per migliorare la precisione dei dati raccolti.<br> Questo passaggio viene definito ispezione approfondita. <br> Il componente driver intercetta il traffico crittografato e si inserisce nel flusso crittografato. Esegue questa operazione creando un certificato radice autofirmato nel computer, in modo che l'applicazione client ritenga attendibile l'agente di Cloud App Discovery. Il certificato radice autofirmato è contrassegnato come non esportabile e viene sottoposto ad ACL per gli amministratori. Non deve mai lasciare il computer in cui è stato creato.


### Rispetto della privacy degli utenti

Il nostro obiettivo consiste nell'offrire agli amministratori gli strumenti necessari per trovare un equilibrio tra informazioni dettagliate sull'utilizzo delle applicazioni e la privacy degli utenti, in base alle esigenze dell'organizzazione. Per questo scopo, nella pagina Impostazioni del portale sono disponibili le manopole seguenti:

- **Raccolta dati**: gli amministratori possono scegliere di specificare le applicazioni o le categorie di applicazioni per cui ottenere dati di individuazione.


- **Ispezione approfondita**: gli amministratori possono scegliere di specificare se l'agente raccoglie il traffico HTTP per le connessioni SSL/TLS.



Cloud App Discovery Endpoint Agent raccoglie solo le informazioni indicate nella tabella precedente.



> [AZURE.NOTE]Per altri dettagli, vedere la pagina [introduttiva per Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 



## Invio di dati a Cloud App Discovery

Dopo la raccolta da parte dell'agente, i metadati vengono memorizzati nella cache del computer per al massimo un minuto o fino a quando i dati memorizzati nella cache non raggiungono una dimensione pari a 5 MB. Vengono quindi compressi e inviati tramite una connessione sicura al servizio Cloud App Discovery.

Se l'agente non riesce a comunicare con il servizio Cloud App Discovery per qualsiasi motivo, i metadati raccolti vengono archiviati in una cache dei file locale, a cui possono accedere solo gli utenti con privilegi elevati nel computer, ad esempio il gruppo Administrators. <br> L'agente tenta automaticamente di inviare di nuovo i metadati memorizzati nella cache fino alla ricezione corretta da parte del servizio Cloud App Discovery.



## Ricezione dei dati sul lato servizio

Gli agenti eseguono l'autenticazione per il servizio Cloud App Discovery usando il certificato di autenticazione client specifico del computer indicato in precedenza e inoltrano i dati su un canale crittografato. <br> La pipeline di analisi del servizio Cloud App Discovery elabora i metadati separatamente per ogni cliente, partizionandoli logicamente in tutte le fasi della pipeline di analisi. I metadati analizzati sono alla base dei diversi report disponibili nel portale.

I metadati non elaborati e i metadati analizzati vengono archiviati per un massimo di 180 giorni. I clienti possono anche scegliere di acquisire i metadati analizzati nell'account di archiviazione BLOB di Azure desiderato. Ciò risulta utile per l'analisi offline dei metadati, oltre che per la conservazione più duratura dei dati.

## Accesso ai dati mediante il portale di Azure

Nel tentativo di proteggere i metadati raccolti, per impostazione predefinita solo gli amministratori globali del tenant possono accedere alla funzionalità Cloud App Discovery del portale di Azure. <br> Gli amministratori possono tuttavia scegliere di delegare l'accesso ad altri utenti o gruppi.



> [AZURE.NOTE]Per altri dettagli, vedere la pagina [introduttiva per Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 


Qualsiasi utente che accede ai dati nel portale deve avere una licenza per Azure AD Premium.



**Risorse aggiuntive**


* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->