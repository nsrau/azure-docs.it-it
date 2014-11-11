<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

Servizi BizTalk: Nome e chiave dell'autorità emittente
======================================================

Servizi BizTalk di Azure utilizza il nome e la chiave dell'autorità emittente del bus di servizio e il nome e la chiave dell'autorità emittente del Controllo di accesso. In particolare:

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Attivit&agrave;</strong></td>
<td><strong>Nome e chiave dell'autorit&agrave; emittente da utilizzare</strong></td>
</tr>
<tr>
<td>Distribuzione dell'applicazione da Visual Studio</td>
<td>Nome e chiave dell'autorit&agrave; emittente del Controllo di accesso</td>
</tr>
<tr>
<td>Configurazione del Portale Servizi BizTalk di Azure</td>
<td>Nome e chiave dell'autorit&agrave; emittente del Controllo di accesso</td>
</tr>
<tr>
<td>Creazione di inoltri LOB con i servizi dell'adapter di BizTalk in Visual Studio</td>
<td>Nome e chiave dell'autorit&agrave; emittente del bus di servizio</td>
</tr>
</table>

In questo argomento vengono elencati i passaggi necessari per recuperare il nome e la chiave dell'autorità emittente.

Nome e chiave dell'autorità emittente del Controllo di accesso
--------------------------------------------------------------

Il nome e la chiave dell'autorità emittente del Controllo di accesso vengono utilizzati dagli elementi seguenti:

-   Applicazione di Servizio BizTalk di Azure creata in Visual Studio: per distribuire correttamente l'applicazione di Servizio BizTalk in Visual Studio in Azure, immettere il nome e la chiave dell'autorità emittente del Controllo di accesso.
-   Portale Servizi BizTalk di Azure: quando si accede per la prima volta al portale Servizi BizTalk, si immette il proprio nome di Servizio BizTalk come provider di servizi e quindi si immette il nome e la chiave dell'autorità emittente del Controllo di accesso.

### Per recuperare il nome e la chiave dell'autorità emittente del Controllo di accesso

1.  Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  Nel pannello di navigazione sinistro selezionare **BizTalk Services**.
3.  Selezionare il servizio BizTalk desiderato.
4.  Selezionare **Connection Information** nella barra delle applicazioni. I valori per lo spazio dei nomi del servizio di Controllo di accesso, l'autorità emittente predefinita (Issuer Name) e la chiave predefinita (Issuer Key) sono elencati e possono essere copiati e incollati.  

     Per riepilogare:
     Issuer Name = Autorità emittente predefinita
     Issuer Key = Chiave predefinita

È inoltre possibile fare clic su **Open ACS Management Portal** per recuperare i valori del Controllo di accesso:

1.  Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  Nel pannello di navigazione sinistro selezionare **BizTalk Services**.
3.  Selezionare il servizio BizTalk desiderato.
4.  Selezionare il pulsante Connection Information e quindi **Open ACS Management Portal**.
5.  Nel portale fare clic su **Service Identities** fare clic su **Service Settings**. Verrà visualizzata l'identità del servizio, che corrisponde al valore del nome dell'autorità emittente del Controllo di accesso. Fare clic sul collegamento Service Identity per visualizzare la password, che corrisponde al valore della chiave dell'autorità emittente. Questi valori possono essere copiati.

     Ad esempio, in **Service Identities** viene visualizzato "owner". "Owner" corrisponde al nome dell'autorità emittente del Controllo di accesso. Quando si fa clic sul collegamento "owner", verrà visualizzato il valore **Password**. Quando si fa clic sul collegamento "Password", verrà visualizzato il valore corrispondente. Il valore della password corrisponde alla chiave dell'autorità emittente del Controllo di accesso.

     Per riepilogare:
     Issuer Name = Nome dell'identità del servizio
     Issuer Key = Valore della password

Nel pannello di navigazione sinistro è inoltre possibile selezionare **Active Directory** per recuperare i valori del Controllo di accesso.

**Importante**

Quando si crea uno spazio dei nomi del Controllo di accesso utilizzando **Active Directory**, **non** viene creata automaticamente un'identità del servizio. Quando si esegue il provisioning del Servizio BizTalk, vengono creati automaticamente lo spazio dei nomi del Controllo di accesso, un'identità del servizio con nome "owner" (Issuer Name), una password (Issuer Key e una chiave simmetrica.

[Procedura: Utilizzare il servizio di gestione ACS per configurare le identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942) sono disponibili ulteriori informazioni sulle identità del servizio del Controllo di accesso.

Nome e chiave dell'autorità emittente del bus di servizio
---------------------------------------------------------

Il nome e la chiave dell'autorità emittente del bus di servizio vengono utilizzati dai servizi dell'adapter di BizTalk. Nel progetto di Servizi BizTalk in Visual Studio è possibile utilizzare i servizi dell'adapter di BizTalk per la connessione a un sistema LOB (Line-of-Business) locale. Per effettuare la connessione, creare l'inoltro LOB e immettere i dettagli relativi al sistema LOB. Quando si esegue questa operazione, è necessario immettere anche il nome e la chiave dell'autorità emittente del bus di servizio.

### Per recuperare il nome e la chiave dell'autorità emittente del bus di servizio

1.  Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2.  Nel pannello di navigazione sinistro fare clic su **Service Bus**.
3.  Fare clic sul proprio spazio dei nomi. Nella barra delle applicazioni fare clic su **Connection Information**. Verranno visualizzati i valori relativi a **Default Issuer** (nome dell'autorità emittente) e **Default Key** (chiave dell'autorità emittente). Questi valori possono essere copiati.

     Per riepilogare:
     Issuer Name = Autorità emittente predefinita
     Issuer Key = Chiave predefinita

Passaggi successivi
-------------------

Ulteriori argomenti relativi a Servizi BizTalk di Azure:

-   [Installazione dell'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)
-   [Esercitazioni: Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

Vedere anche
------------

-   [Procedura: Utilizzare il servizio di gestione ACS per configurare le identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942)
-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: Grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servizi BizTalk: Schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servizi BizTalk: Limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)

