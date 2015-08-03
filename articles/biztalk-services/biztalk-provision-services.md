<properties
	pageTitle="Creare Servizi BizTalk nel portale di gestione | Azure"
	description="Informazioni su come effettuare il provisioning o creare un Servizio BizTalk nel portale di gestione di Azure; MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/03/2015"
	ms.author="mandia"/>



# Creare un servizio BizTalk mediante il portale di gestione di Azure

Creare un Servizio BizTalk di Azure nel portale di gestione di Azure.

> [AZURE.TIP]Per accedere al portale di gestione di Azure, è necessario un account Azure e una sottoscrizione di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Vedere [Versione di valutazione gratuita di Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## Creare un servizio BizTalk
A seconda dell'edizione scelta, non tutte le impostazioni del servizio BizTalk Service saranno disponibili.

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nella parte inferiore del pannello di navigazione selezionare **NUOVO**: <br/> ![Selezionare il pulsante New][NEWButton]

3. Selezionare **SERVIZI APP** > **SERVIZIO BIZTALK** > **CREAZIONE PERSONALIZZATA**: <br/> ![Selezionare BizTalk Service e quindi Custom Create][NewBizTalkService]

4. Immettere le impostazioni del servizio BizTalk:

	<table border="1">
<tr>
<td><strong>Nome del Servizio BizTalk</strong></td>
<td>È possibile inserire qualsiasi nome, ma deve essere specifico. Di seguito sono riportati alcuni esempi:<br/><br/>
<em>società</em>.biztalk.windows.net<br/>
<em>societàapplicazione</em>.biztalk.windows.net<br/>
<em>applicazione </em>.biztalk.windows.net<br/><br/>".biztalk.windows.net" viene aggiunto automaticamente al nome digitato. Viene creato un URL usato per accedere al servizio BizTalk, ad esempio <strong>https://<em>applicazione</em>.biztalk.windows.net</strong>.
</td>
</tr>
<tr>
<td><strong>Edizione</strong></td>
<td>Se si è nella fase di testing/sviluppo, scegliere l'edizione <strong>Developer</strong>. Se si è nella fase di produzione, usare il <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Grafico edizioni di Servizi BizTalk</a> per determinare se la scelta corretta per il proprio scenario aziendale sia l'edizione <strong>Premium</strong>, <strong>Standard</strong> o <strong>Basic</strong>.
</td>
</tr>
<tr>
<td><strong>Area</strong></td>
<td>Selezionare l'area geografica per ospitare il servizio BizTalk.</td>
</tr>
<tr>
<td><strong>URL del dominio</strong></td>
<td><strong>Facoltativo</strong>. Per impostazione predefinita, l'URL del dominio è <em>NomeServizioBizTalk</em>.biztalk.windows.net. È anche possibile specificare un dominio personalizzato. Se ad esempio il dominio è <em>contoso</em>, è possibile immettere: <br/><br/>
<em>Società</em>.contoso.com<br/>
<em>SocietàApplicazione</em>.contoso.com<br/>
<em>Applicazione</em>.contoso.com<br/>
<em>NomeServizioBizTalk</em>.contoso.com<br/>
</td>
</tr>
</table>
Fare clic sulla freccia AVANTI.

5. Immettere le impostazioni per archiviazione e database:

	<table border="1">
<tr>
<td><strong>Account di monitoraggio/archiviazione</strong></td>
<td>Selezionare un account di archiviazione esistente o crearne uno nuovo. <br/><br/>Se si crea un nuovo account di archiviazione, specificarne il nome in <strong>Nome account di archiviazione</strong>.</td>
</tr>
<tr>
<td><strong>Database di rilevamento</strong></td>
<td>Se si usa un database SQL di Azure esistente, non potrà essere usato da un altro servizio BizTalk. È necessario disporre dell'account di accesso e della password immessi durante la creazione del server di database SQL di Azure.<br/><br/><strong>SUGGERIMENTO</strong> Creare il database di rilevamento e l'account di monitoraggio/archiviazione nella stessa area geografica del Servizio BizTalk.</td>
</tr>
</table>
Fare clic sulla freccia AVANTI.

6. Immettere le impostazioni del database:

	<table border="1">
<tr>
<td><strong>Nome</strong></td>
<td>Disponibile quando nella schermata precedente è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>.
<br/><br/>
Immettere il nome del database SQL da usare con il servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Server</strong></td>
<td>Disponibile quando nella schermata precedente è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>.
<br/><br/>
Selezionare un server di database SQL o crearne uno nuovo.</td>
</tr>
<tr>
<td><strong>Nome di accesso al server</strong></td>
<td>Immettere il proprio nome utente di accesso.</td>
</tr>
<tr>
<td><strong>Password di accesso al server</strong></td>
<td>Immettere la password di accesso.</td>
</tr>
<tr>
<td><strong>Area</strong></td>
<td>Disponibile quando è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>. Selezionare l'area geografica per ospitare il database SQL.</td>
</tr>
</table>

Per completare la procedura guidata, fare clic sul segno di spunta. Verrà visualizzata l'icona di avanzamento: <br/> ![Icona di avanzamento visualizzata al termine][ProgressComplete]

Al termine, viene creato il servizio BizTalk di Azure che sarà disponibile per le applicazioni. Le impostazioni predefinite sono sufficienti. Se si desidera modificare le impostazioni predefinite, selezionare **SERVIZI BIZTALK** nel pannello di navigazione sinistro e selezionare il proprio servizio BizTalk. Altre impostazioni sono disponibili nelle [schede Dashboard, Monitoraggio e Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281) in alto.

A seconda dello stato del servizio BizTalk, alcune operazioni potrebbero non essere completate. Per un elenco di queste operazioni, vedere [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870).


## Passaggi di post-provisioning

-  [Installare il certificato in un computer locale](#InstallCert)
-  [Aggiungere un certificato per l'ambiente di produzione](#AddCert)
-  [Ottenere lo spazio dei nomi ACS](#ACS)

#### <a name="InstallCert"></a>Installare il certificato in un computer locale
Come parte del provisioning del servizio BizTalk, un certificato autofirmato viene creato e associato alla sottoscrizione del servizio BizTalk. È necessario scaricare questo certificato e installarlo nei computer da cui si distribuiscono applicazioni del servizio BizTalk o si inviano messaggi all'endpoint del servizio BizTalk.

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Fare clic su **SERVIZI BIZTALK** nel pannello di navigazione sinistro e quindi selezionare la sottoscrizione del Servizio BizTalk.
3. Selezionare la scheda **Dashboard**.
4. Selezionare **Scarica certificato SSL**. <br/> ![Modificare un certificato SSL][QuickGlance]
5. Fare doppio clic sul certificato ed eseguire la procedura guidata per installarlo. Assicurarsi di installare il certificato nell'archivio **Autorità di certificazione radice attendibili**.

#### <a name="AddCert"></a>Aggiungere un certificato per l'ambiente di produzione
Il certificato autofirmato creato automaticamente durante la creazione dei Servizi BizTalk è destinato all'uso solo negli ambienti di sviluppo. Per gli scenari di produzione, è necessario sostituirlo con un certificato di produzione.

1. Nella scheda **Dashboard** fare clic su **Aggiorna certificato SSL**.
2. Cercare il certificato SSL privato (*NomeCertificato*.pfx) che include il nome del Servizio BizTalk, immettere la password e selezionare il segno di spunta.

#### <a name="ACS"></a>Ottenere lo spazio dei nomi ACS

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selezionare **BIZTALK SERVICES** nel pannello di navigazione sinistro e quindi scegliere il servizio BizTalk.
3. Nella barra delle applicazioni selezionare **Informazioni di connessione**: <br/> ![Selezionare Connection Information][ACSConnectInfo]

4. Copiare i valori di ACS.

Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi. Lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente per il servizio BizTalk.

I valori di ACS possono essere usati con qualsiasi applicazione. Dopo la creazione di Servizi BizTalk di Azure, lo spazio del nomi ACS controlla l'autenticazione con la distribuzione del servizio BizTalk. Se si desidera modificare la sottoscrizione o gestire lo spazio dei nomi, selezionare **ACTIVE DIRECTORY** nel pannello di navigazione sinistro e quindi selezionare lo spazio dei nomi. Le opzioni sono elencate nella bassa delle applicazioni.

Facendo clic su **Manage** è possibile aprire il portale di gestione del servizio di controllo di accesso. Nel portale il Servizio BizTalk usa le **identità del servizio**: <br/> ![Identità del servizio di controllo di accesso nel portale di gestione del servizio][ACSServiceIdentities]

Le identità del servizio di controllo di accesso sono set di credenziali che consentono ad applicazioni o client di eseguire l'autenticazione direttamente con il servizio di controllo di accesso e di ricevere un token.

> [AZURE.IMPORTANT]Il Servizio BizTalk usa **Owner** come identità predefinita del servizio e il valore **Password**. Se si usa il valore della chiave simmetrica anziché il valore Password, potrebbe verificarsi l'errore seguente:<br/><br/>*Impossibile connettersi all'account di gestione del servizio di controllo di accesso con le credenziali specificate*

In [Gestione dello spazio dei nomi del servizio di controllo di accesso](http://go.microsoft.com/fwlink/p/?LinkID=285670) sono fornite alcune linee guida e consigli utili.

## Descrizione dei requisiti

Questi requisiti non si applicano all'edizione Free. <table border="1"> <tr bgcolor="FAF9F9"> <td><strong>Elementi necessari</strong></td> <td><strong>Perché sono necessari</strong></td> </tr> <tr> <td>Sottoscrizione Azure</td> <td>La sottoscrizione determina chi può accedere al portale di gestione di Azure. Il titolare dell'account crea la sottoscrizione nella pagina <a HREF="https://account.windowsazure.com/Subscriptions"> Sottoscrizioni di Azure</a>. <br/><br/> L'account Azure può disporre di più sottoscrizioni e può essere gestito da qualsiasi utente autorizzato. Il titolare di un account Azure crea ad esempio una sottoscrizione denominata <em>SottoscrizioneServizioBizTalk</em> e concede agli amministratori di BizTalk dell'azienda (ad esempio ContosoBTSAdmins@live.com) l'accesso alla sottoscrizione. In questo scenario gli amministratori di BizTalk accedono al portale di gestione di Azure e dispongono dei diritti di amministratore completi per tutti i servizi ospitati nella sottoscrizione, inclusi i Servizi BizTalk di Azure. Gli amministratori di BizTalk non sono titolari dell'account Azure e pertanto non possono accedere alle informazioni di fatturazione. Nell'articolo <br/><br/> <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gestire account, sottoscrizioni e ruoli amministrativi</a> sono disponibili altre informazioni. </td> </tr> <tr> <td>Database SQL di Azure</td> <td>Archivia le tabelle, le viste e le stored procedure usate dal Servizio BizTalk di Azure, inclusi i dati di rilevamento. <br/><br/> Quando si crea un servizio BizTalk, è possibile usare un server SQL di Azure esistente, un database SQL di Azure oppure creare automaticamente un nuovo server o database. <br/><br/> La scalabilità del database SQL viene configurata automaticamente. In genere, la scalabilità predefinita è sufficiente per un servizio BizTalk. La modifica della scalabilità incide sui prezzi. Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Account e fatturazione nel database SQL di Azure</a> <br/><br/> <strong>Note</strong> <br/> <ul> <li> Quando si sceglie di creare un nuovo server SQL di Azure e un nuovo database, Servizi di Azure viene abilitato automaticamente. Il Servizio BizTalk richiede che Servizi di Azure sia abilitato.</li> <li>Se si crea un nuovo database SQL di Azure su un server SQL di Azure esistente, le regole del firewall del server non vengono modificate. Di conseguenza, è possibile che altri servizi di Azure non possano accedere ai database del server.</li> </ul> </td> </tr> <tr> <td>Spazio dei nomi del servizio di controllo di accesso di Azure</td> <td>Consente di eseguire l'autenticazione con Servizi BizTalk di Azure. Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi. Quando si crea un Servizio BizTalk, lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente.</td> </tr>

<tr>
<td>Account di archiviazione di Azure</td>
<td>Consente l'accesso alle tabelle, ai BLOB e alle code usati dal servizio BizTalk per salvare quando indicato di seguito:

<ul>
<li>File di log che monitorano il servizio BizTalk. L'output del monitoraggio viene anche visualizzato nella scheda Monitoring del portale di gestione di Azure.</li>
<li>Quando si crea un contratto X12 o AS2 tra partner, è possibile abilitare la funzionalità di archiviazione per archiviare le proprietà dei messaggi. I dati vengono salvati nell'account di archiviazione.</li>
</ul>
<br/>
Quando si crea un servizio BizTalk, è possibile usare un account di archiviazione esistente o crearne automaticamente uno nuovo.
<br/><br/>
Le impostazioni di archiviazione predefinite sono sufficienti per un servizio BizTalk.
<br/><br/>
Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi controllano l'accesso all'account di archiviazione. Il servizio BizTalk utilizza automaticamente la chiave primaria.
<br/><br/>
Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671"> Archiviazione</a> per altre informazioni.
</td>
</tr>

<tr>
<td>Certificato SSL privato</td>
<td>
Quando si crea un servizio BizTalk di Azure, viene creato anche un URL HTTPS che include il nome del servizio BizTalk. Questo URL viene configurato automaticamente per l'uso del certificato autofirmato solo per l'ambiente di sviluppo. Per l'ambiente di produzione, è necessario un certificato SSL privato.
<br/><br/>
<strong>Informazioni importanti sul certificato SSL</strong>

<ul>
<li>La data di scadenza del certificato deve essere inferiore ai 5 anni.</li>
<li>Tutti i certificati privati richiedono una password. Non dimenticare la password e, come procedura consigliata, condividerla con gli amministratori.</li>
<li>I certificati autofirmati possono essere utilizzati in ambienti di sviluppo/test. Quando si usano certificati autofirmati, è necessario importare il certificato nell'archivio dei certificati personale e nell'archivio dei certificati delle autorità di certificazione radice disponibili nell'elenco locale.</li>
</ul>
<br/>Quando si invia la richiesta del certificato di produzione all'autorità di certificazione, specificare le proprietà del certificato seguenti:
<br/>

<ul>
<li><strong>Utilizzo chiavi avanzato</strong>: Servizi BizTalk di Azure richiede almeno l'autenticazione server.</li>
<li><strong>Nome comune</strong>: immettere il nome di dominio completo dell'URL del Servizio BizTalk di Azure. Vedere <a HREF="#BizTalk">Creare un servizio BizTalk</a> in questo argomento.</li>
</ul>
<br/>
È possibile aggiungere un nuovo certificato o un certificato diverso al termine della creazione del servizio BizTalk.
</td>
</tr>
</table>



## connessioni ibride

Quando si crea un servizio BizTalk di Azure, è disponibile la scheda relativa alle **connessioni ibride**:

![scheda per le connessioni ibride][HybridConnectionTab]

Le connessioni ibride consentono la connessione di un sito Web o di un servizio mobile di Azure, a una risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP, Servizi mobili e la maggior parte dei servizi Web personalizzati. Le connessioni ibride e BizTalk Adapter Service presentano differenze. BizTalk Adapter Service viene usato per la connessione di Servizi BizTalk di a un sistema line-of-business locale.

 Per altre informazioni, inclusa la creazione e la gestione di connessioni ibride, vedere [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274).


## Avanti

Dopo la creazione del Servizio BizTalk, è possibile acquisire familiarità con le diverse [schede Dashboard, Monitoraggio e Scalabilità di Servizi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302281). Il Servizio BizTalk è pronto per le applicazioni. Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Vedere anche
- [Servizi BizTalk: Grafico edizioni](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)
- [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
 

<!---HONumber=July15_HO4-->