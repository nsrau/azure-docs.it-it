<properties 
	pageTitle="Nome e chiave dell'autorità emittente in Servizi BizTalk | Azure" 
	description="Informazioni su come recuperare il nome dell'autorità emittente e la chiave dell'autorità emittente per il bus di servizio o il Servizio di controllo di accesso (ACS) in Servizi BizTalk. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>




# Servizi BizTalk: nome e chiave dell'autorità emittente

Servizi BizTalk di Azure usa il nome e la chiave dell'autorità emittente del bus di servizio e il nome e la chiave dell'autorità emittente del Controllo di accesso. In particolare:

Attività | Nome e chiave dell'autorità emittente da utilizzare
--- | ---
Distribuzione dell'applicazione da Visual Studio | Nome e chiave dell'autorità emittente del Controllo di accesso
Configurazione del Portale Servizi BizTalk di Azure | Nome e chiave dell'autorità emittente del Controllo di accesso
Creazione di inoltri LOB con i servizi dell'adapter di BizTalk in Visual Studio | Nome e chiave dell'autorità emittente del bus di servizio

In questo argomento vengono elencati i passaggi necessari per recuperare il nome e la chiave dell'autorità emittente.

## Nome e chiave dell'autorità emittente del Controllo di accesso
Il nome e la chiave dell'autorità emittente del Controllo di accesso vengono usati dagli elementi seguenti:

- Applicazione di Servizio BizTalk di Azure creata in Visual Studio: per distribuire correttamente l'applicazione di Servizio BizTalk in Visual Studio in Azure, immettere il nome e la chiave dell'autorità emittente del Controllo di accesso. 
- Portale dei servizi BizTalk di Azure: quando si crea un Servizio BizTalk e si apre il portale dei servizi BizTalk, il nome e la chiave dell'autorità emittente del Controllo di accesso vengono registrati automaticamente per le distribuzioni con gli stessi valori del Controllo di accesso.

### Per copiare e incollare il nome e la chiave dell'autorità emittente del Controllo di accesso

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **BizTalk Services**.
3. Selezionare il servizio BizTalk desiderato. 
4. Selezionare **Connection Information** nella barra delle applicazioni. I valori per lo spazio dei nomi del servizio di Controllo di accesso, l'autorità emittente predefinita (Issuer Name) e la chiave predefinita (Issuer Key) sono elencati e possono essere copiati e incollati.<br/><br/> Per riepilogare:<br/> Nome autorità emittente = Autorità emittente predefinita<br/> Chiave autorità emittente = Chiave predefinita


È inoltre possibile fare clic su **Apri portale di gestione ACS** per recuperare i valori del Controllo di accesso:

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **BizTalk Services**.
3. Selezionare il servizio BizTalk desiderato.
4. Selezionare il pulsante Connection Information e quindi **Open ACS Management Portal**.
5. Nel portale, in **Impostazioni servizio**, selezionare **Identità del servizio**. Verrà visualizzata l'identità del servizio, che corrisponde al valore del nome dell'autorità emittente del Controllo di accesso. Selezionare il collegamento dell'identità del servizio per visualizzare la password, che corrisponde al valore della chiave dell'autorità emittente. Questi valori possono essere copiati.<br/><br/> Ad esempio, in **Service Identities** viene visualizzato "owner". "Owner" corrisponde al nome dell'autorità emittente del Controllo di accesso. Quando si fa clic sul collegamento "owner", verrà visualizzato il valore **Password**. Quando si fa clic sul collegamento "Password", verrà visualizzato il valore corrispondente. Il valore della password corrisponde alla chiave dell'autorità emittente del Controllo di accesso. <br/><br/> Per riepilogare:<br/> Nome autorità emittente = Nome identità del servizio<br/> Chiave autorità emittente = Valore della password

Nel pannello di navigazione sinistro è inoltre possibile selezionare **Active Directory** per recuperare i valori del Controllo di accesso.

> [AZURE.IMPORTANT]Quando si crea uno spazio dei nomi del Controllo di accesso usando <strong>Active Directory</strong>, **non** viene creata automaticamente un'identità del servizio. Quando si esegue il provisioning di un Servizio BizTalk, vengono creati automaticamente lo spazio dei nomi del Controllo di accesso, un'identità del servizio con nome "owner" (Nome dell'autorità emittente), una password (Chiave dell'autorità emittente) e una chiave simmetrica.<br /> L'articolo [Procedura: Usare il servizio di gestione ACS per la configurazione delle identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942) fornisce altre informazioni sulle identità del Servizio di controllo di accesso.


## Nome e chiave dell'autorità emittente del bus di servizio
Il nome e la chiave dell'autorità emittente del bus di servizio vengono usati da Servizi Adapter BizTalk. Nel progetto di Servizi BizTalk in Visual Studio è possibile usare Servizi Adapter BizTalk per la connessione a un sistema LOB (Line-of-Business) locale. Per effettuare la connessione, creare l'inoltro LOB e immettere i dettagli relativi al sistema LOB. Quando si esegue questa operazione, è necessario immettere anche il nome e la chiave dell'autorità emittente del bus di servizio.

### Per recuperare il nome e la chiave dell'autorità emittente del bus di servizio

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Bus di servizio**.
3. Selezionare lo spazio dei nomi. Nella barra delle applicazioni selezionare **Informazioni di connessione**. Verranno visualizzati i valori relativi a **Default Issuer** (nome dell'autorità emittente) e **Default Key** (chiave dell'autorità emittente). Questi valori possono essere copiati.<br/><br/> Per riepilogare:<br/> Nome autorità emittente = Autorità emittente predefinita<br/> Chiave autorità emittente = Chiave predefinita

## Avanti
Ulteriori argomenti relativi a Servizi BizTalk di Azure:

-  [Installare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Servizi BizTalk: Esercitazioni ed esempi](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Vedere anche
-  [Procedura: Usare il servizio di gestione ACS per la configurazione delle identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Servizi BizTalk: Grafico edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Creare un servizio BizTalk mediante il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=62-->