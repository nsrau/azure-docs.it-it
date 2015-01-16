<properties urlDisplayName="Issuer name and issuer key" pageTitle="Nome e chiave dell'autorità emittente in Servizi BizTalk | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Informazioni su come recuperare il nome dell'autorità emittente e la chiave dell'autorità emittente per Bus di servizio o Servizio di controllo di accesso (ACS) nei Servizi BizTalk." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />




# Servizi BizTalk: nome e chiave dell'autorità emittente

Servizi BizTalk di Azure usa il nome e la chiave dell'autorità emittente del bus di servizio e il nome e la chiave dell'autorità emittente del Controllo di accesso. In particolare:


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Attività</strong></td>
<td><strong>Nome e chiave dell'autorità emittente da usare</strong></td>
</tr>
<tr>
<td>Distribuzione dell'applicazione da Visual Studio</td>
<td>Nome e chiave dell'autorità emittente del Controllo di accesso</td>
</tr>
<tr>
<td>Configurazione del Portale Servizi BizTalk di Azure</td>
<td>Nome e chiave dell'autorità emittente del Controllo di accesso</td>
</tr>
<tr>
<td>Creazione di inoltri LOB con i servizi dell'adapter di BizTalk in Visual Studio</td>
<td>Nome e chiave dell'autorità emittente del bus di servizio</td>
</tr>
</table>

In questo argomento vengono elencati i passaggi necessari per recuperare il nome e la chiave dell'autorità emittente. 

## Nome e chiave dell'autorità emittente del Controllo di accesso
Il nome e la chiave dell'autorità emittente del Controllo di accesso vengono usati dagli elementi seguenti:

- Applicazione di Servizio BizTalk di Azure creata in Visual Studio: per distribuire correttamente l'applicazione di Servizio BizTalk in Visual Studio in Azure, immettere il nome e la chiave dell'autorità emittente del Controllo di accesso. 
- Portale Servizi BizTalk di Azure: quando si accede per la prima volta al portale Servizi BizTalk, si immette il proprio nome di Servizio BizTalk come provider di servizi e quindi si immette il nome e la chiave dell'autorità emittente del Controllo di accesso.

### Per recuperare il nome e la chiave dell'autorità emittente del Controllo di accesso

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**.
3. Selezionare il servizio BizTalk desiderato. 
4. Selezionare **Informazioni di connessione** nella barra delle applicazioni. I valori Spazio dei nomi di Access Control, Autorità di certificazione predefinita (Nome dell'autorità di certificazione) e Chiave predefinita (Chiave autorità di certificazione) sono elencati e possono essere copiati e incollati.<br/><br/>
Per riepilogare:<br/>
Nome dell'autorità di certificazione = Autorità di certificazione predefinita<br/>
Chiave autorità di certificazione = Chiave predefinita


È inoltre possibile fare clic su **Apri portale di gestione ACS** per recuperare i valori del Controllo di accesso:

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**.
3. Selezionare il servizio BizTalk desiderato.
4. Selezionare il pulsante Informazioni di connessione e quindi **Apri portale di gestione ACS**..
5. Nel portale, in **Impostazioni servizio**, fare clic su **Identità del servizio**. Verrà visualizzata l'identità del servizio, che corrisponde al valore del nome dell'autorità emittente del Controllo di accesso. Fare clic sul collegamento Service Identity per visualizzare la password, che corrisponde al valore della chiave dell'autorità emittente. Questi valori possono essere copiati.<br/><br/>
Ad esempio, in **Identità del servizio**, viene visualizzato "proprietario". "Owner" corrisponde al nome dell'autorità emittente del Controllo di accesso. Quando si fa clic sul collegamento "owner", verrà visualizzato il valore **Password**. Quando si fa clic sul collegamento "Password", verrà visualizzato il valore corrispondente. Il valore della password corrisponde alla chiave dell'autorità emittente del Controllo di accesso. <br/><br/>
Per riepilogare:<br/>
Nome dell'autorità di certificazione = Nome dell'identità del servizio<br/>
Chiave autorità di certificazione = Valore della password

Nel pannello di navigazione sinistro è inoltre possibile selezionare **Active Directory** per recuperare i valori del Controllo di accesso. 

<div class="dev-callout"> 
<b>Importante</b> 
<p>Quando si crea uno spazio dei nomi del Controllo di accesso usando <strong>Active Directory</strong>, <strong>non</strong> viene creata automaticamente un'identità del servizio. Quando si esegue il provisioning del Servizio BizTalk, vengono creati automaticamente lo spazio dei nomi del Controllo di accesso, un'identità del servizio con nome "owner" (Nome dell'autorità di certificazione), una password (Chiave autorità di certificazione) e una chiave simmetrica.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">Procedura: Usare il servizio di gestione ACS per configurare le identità del servizio</a> fornisce altre informazioni sulle identità di Servizio di controllo di accesso.</p>
</div>


## Nome e chiave dell'autorità emittente del bus di servizio
Il nome e la chiave dell'autorità emittente del bus di servizio vengono usati da Servizi Adapter BizTalk. Nel progetto di Servizi BizTalk in Visual Studio è possibile usare i servizi dell'adapter di BizTalk per la connessione a un sistema LOB (Line-of-Business) locale. Per effettuare la connessione, creare l'inoltro LOB e immettere i dettagli relativi al sistema LOB. Quando si esegue questa operazione, è necessario immettere anche il nome e la chiave dell'autorità emittente del bus di servizio.

### Per recuperare il nome e la chiave dell'autorità emittente del bus di servizio

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro fare clic su **Bus di servizio**.
3. Fare clic sullo spazio dei nomi. Nella barra delle applicazioni fare clic su **Connection Information**. Verranno visualizzati i valori relativi a **Default Issuer** (nome dell'autorità emittente) e **Default Key** (chiave dell'autorità emittente). Questi valori possono essere copiati.<br/><br/>
Per riepilogare:<br/>
Nome dell'autorità di certificazione = Autorità di certificazione predefinita<br/>
Chiave autorità di certificazione = Chiave predefinita

## Passaggi successivi
Ulteriori argomenti relativi a Servizi BizTalk di Azure:

-  [Installazione dell'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Esercitazioni: Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Vedere anche
-  [Procedura: Usare il servizio di gestione ACS per configurare le identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Servizi BizTalk: tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servizi BizTalk: grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servizi BizTalk: schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
