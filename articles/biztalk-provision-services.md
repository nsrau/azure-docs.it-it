<properties  linkid="provisioning-biztalk-service" urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Provision BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/it-it/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

# Servizi BizTalk: Provisioning tramite il portale di gestione di Azure

<div  class="dev-callout" markdown="1">
**Suggerimento**
Per accedere al portale di gestione di Azure, è necessario un account Azure e una sottoscrizione di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][1].


</div>

Un servizio BizTalk di Azure include i seguenti componenti:

<table  border="1">
<tr  bgcolor="FAF9F9">
        <td><strong>Requisito</strong>
</td>

        <td><strong>Descrizione</strong>
</td>

</tr>

<tr>
<td>Sottoscrizione di Azure</td>

<td><p>La sottoscrizione regola l'accesso al portale di gestione di Azure e viene creata dal titolare dell'account Azure nel sito di <a href="https://account.windowsazure.com/Subscriptions">sottoscrizione di Azure</a>
.</p>

<p>Un account Azure può disporre di più sottoscrizioni e può essere gestito dal titolare dell'account o da altri utenti o gruppi. Il titolare di un account Azure crea ad esempio una sottoscrizione denominata <em>SottoscrizioneServizioBizTalk</em>
 e concede agli amministratori di BizTalk dell'azienda (ad esempio ContosoAdminSBT@live.com) l'accesso alla sottoscrizione. In questo scenario, gli amministratori di BizTalk accedono al portale di gestione di Azure e dispongono dei diritti di amministratore completi per tutti i servizi ospitati nella sottoscrizione, inclusi Servizi BizTalk di Azure. Gli amministratori di BizTalk non sono titolari dell'account Azure e pertanto non possono accedere alle informazioni di fatturazione.</p>

In <a href="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gestire le sottoscrizioni e gli account di archiviazione nel portale di gestione di Azure</a>
 sono disponibili ulteriori informazioni sugli account Azure e sulle sottoscrizioni.
</td>

</tr>

<tr>
<td>Database SQL di Azure</td>

<td><p>In un database SQL vengono archiviate le tabelle, le viste e le stored procedure utilizzate da Servizi BizTalk di Azure.</p>

<p>Quando si esegue il provisioning di un servizio BizTalk, è possibile utilizzare un server SQL di Azure esistente, un database SQL di Azure oppure creare automaticamente un nuovo server o database. Quando si sceglie di creare un nuovo server SQL di Azure e un nuovo database, Servizi di Azure viene abilitato automaticamente.</p>

<p>Se si crea un nuovo database SQL di Azure su un server SQL di Azure esistente, le regole del firewall del server non vengono modificate. Di conseguenza, è possibile che altri servizi di Azure non possano accedere ai database del server.</p>

Non esistono requisiti minimi di scala per le impostazioni del database SQL.</td>

</tr>

<tr>
<td>Spazio dei nomi del servizio di controllo di accesso di Azure</td>

<td>Lo spazio dei nomi del servizio di controllo di accesso esegue l'autenticazione con Servizi BizTalk di Azure. Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi. Quando si esegue il provisioning di un servizio BizTalk, lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente.</td>

</tr>


<tr>
<td>Account di archiviazione di Azure</td>

<td><p>L'account di archiviazione di Azure consente di accedere a tabelle, BLOB e code. Quando si esegue il provisioning di un servizio BizTalk, è possibile utilizzare un account di archiviazione esistente o crearne automaticamente uno nuovo. Le tabelle, i BLOB e le code vengono utilizzati dal servizio BizTalk per eseguire le operazioni seguenti:</p>

<ul>
<li>Archiviazione dei file di log che monitorano il servizio BizTalk. L'output del monitoraggio viene anche visualizzato nella scheda Monitoring del portale di gestione di Azure.</li>

<li>Quando si crea un contratto X12 o AS2 tra partner, è possibile abilitare la funzionalità di archiviazione per archiviare le proprietà dei messaggi. Questi dati vengono salvati nell'account di archiviazione.</li>

</ul>

</td>

</tr>


<tr>
<td>Certificato SSL privato</td>

<td><p>Quando si esegue il provisioning di Servizi BizTalk di Azure, si crea un URL che include il nome del servizio BizTalk. Questo certificato SSL privato (con estensione pfx) viene utilizzato come certificato di autenticazione del server HTTPS quando vengono inviate richieste all'URL del servizio BizTalk. Quando si esegue il provisioning di un servizio BizTalk, viene creato automaticamente un certificato autofirmato. </p>

<strong>Informazioni importanti del certificato SSL</strong>


<ul>
<li>La data di scadenza del certificato deve essere inferiore ai 5 anni.</li>

<li>Tutti i certificati privati richiedono una password. Non dimenticare la password e, come procedura consigliata, condividerla con gli amministratori.</li>

<li>I certificati autofirmati possono essere utilizzati in un ambiente di test o di sviluppo. Quando si utilizzano certificati autofirmati, è necessario importare il certificato nell'archivio dei certificati personale e nell'archivio dei certificati delle autorità di certificazione radice disponibili nell'elenco locale.</li>

</ul>

<br  />
Quando si invia la richiesta del certificato di produzione all'autorità di certificazione, specificare le proprietà del certificato seguenti: <br  />


<ul>
<li><p><strong>Utilizzo chiavi avanzato</strong>
: è possibile abilitare ulteriori utilizzi della chiave per l'autenticazione del server sul certificato. Servizi BizTalk di Azure richiede almeno l'autenticazione server.</p>
</li>

<li><p><strong>Nome comune</strong>
: immettere il nome di dominio completo (FQDN) dell'URL di Servizi BizTalk di Azure, che verrà creato quando si eseguirà il provisioning del servizio BizTalk nella sezione <a href="#BizTalk">Eseguire il provisioning di un servizio BizTalk</a>
 nel presente argomento.</p>

<p>Occorre pertanto sapere quale sarà l'URL quando si invierà la richiesta di certificato all'autorità di certificazione. È possibile aggiungere un nuovo certificato o un certificato diverso al termine del provisioning del servizio BizTalk.</p>
</li>

</ul>

<br  />

</td>

</tr>

</table>

In questo argomento vengono elencati i passaggi per eseguire il provisioning di Servizi BizTalk di Azure.

* [Passaggio 1: Eseguire il provisioning di un servizio
  BizTalk](#BizTalk)
* [Passaggio 2: Passaggi di post-provisioning](#PostProv)
* [Facoltativo: Creare il server di database SQL](#SQLDB)
* [Facoltativo: Creare un account di archiviazione](#Storage)

## <a name="BizTalk"></a>Passaggio 1: Eseguire il provisioning di un servizio BizTalk

Il servizio BizTalk ospita le applicazioni di Servizi BizTalk di Azure. Quando si esegue il provisioning di un servizio BizTalk, lo spazio dei nomi del servizio di controllo di accesso e il certificato SSL autofirmato vengono creati automaticamente. È possibile scegliere di creare un nuovo database SQL di Azure e un nuovo account di archiviazione. Al termine del provisioning del servizio BizTalk, è possibile aggiornare alcuni di questi requisiti.

La procedura seguente consente di eseguire il provisioning di un nuovo servizio BizTalk di Azure:

1.  Accedere al [portale di gestione di Azure][2].

2.  Nella parte inferiore del pannello di navigazione selezionare
    **+NEW**:
    
    ![Selezionare il pulsante
    New](./media/biztalk-provision-services/WABS_New.png)

3.  Selezionare **APP SERVICES**, **BIZTALK SERVICE** e quindi **CUSTOM
    CREATE**:
    
    ![Selezionare BizTalk Service e quindi Custom
    Create](./media/biztalk-provision-services/WABS_NewBizTalkService.png)

4.  Specificare le impostazioni del servizio BizTalk seguenti:
    
    <table  border="1">
     <tr>
     <td><strong>Nome del servizio BizTalk</strong>
    </td>
    
     <td>Immettere un nome per il servizio BizTalk. ".biztalk.windows.net" viene aggiunto automaticamente al nome digitato. Viene creato un URL utilizzato per accedere al servizio BizTalk. È possibile inserire qualsiasi nome, ma è consigliabile essere specifici. Di seguito sono riportati alcuni esempi:<br  />
    <br  />
     <em>società</em>
    .biztalk.windows.net<br  />
     <em>applicazionesocietà</em>
    .biztalk.windows.net<br  />
     <em>applicazione</em>
    .biztalk.windows.net
     </td>
    
     </tr>
    
     <tr>
     <td><strong>URL del dominio</strong>
    </td>
    
     <td><strong>Facoltativo</strong>
    . Per impostazione predefinita, l'URL del dominio è <em>NomeServizioBizTalk</em>
    .biztalk.windows.net. È tuttavia possibile specificare un dominio personalizzato. Se ad esempio il dominio è <em>contoso</em>
    , è possibile immettere: <br  />
    <br  />
     <em>Azienda</em>
    .contoso.com<br  />
     <em>AziendaApplicazione</em>
    .contoso.com<br  />
     <em>Applicazione</em>
    .contoso.com<br  />
     <em>NomeServizioBizTalk</em>
    .contoso.com<br  />
    
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Edizione</strong>
    </td>
    
     <td>Le opzioni includono:
     <ul>
     <li>Developer</li>
    
     <li>Standard</li>
    
     <li>Basic</li>
    
     <li>Premium</li>
    
     </ul>
    
     In <a href="http://go.microsoft.com/fwlink/p/?LinkID=302279">Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium</a>
     vengono elencate le differenze tra le edizioni. Se si è nella fase di testing/sviluppo, scegliere l'edizione <strong>Developer</strong>
    . Se si è nella fase di produzione, utilizzare la tabella per determinare se la scelta corretta per il proprio scenario aziendale sia l'edizione Premium, Standard o Basic.
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Area geografica</strong>
    </td>
    
     <td>Selezionare l'area geografica per ospitare il servizio BizTalk.</td>
    
     </tr>
    
     <tr>
     <td><strong>Database di rilevamento</strong>
    </td>
    
     <td><p>Selezionare il database SQL per archiviare le tabelle utilizzate dal servizio BizTalk. Scegliere una delle opzioni seguenti:</p>
    
     <ul>
     <li><strong>Use an existing SQL Database instance</strong>
    : Selezionare questa opzione per utilizzare un database SQL di Azure esistente. È possibile utilizzare un database SQL di Azure esistente se non è utilizzato da un altro servizio BizTalk. È necessario disporre dell'account di accesso e della password specificati durante la creazione del server di database SQL di Azure.</li>
    
     <li><p><strong>Create a new SQL Database instance</strong>
    : Selezionare questa opzione per creare un nuovo database SQL.</p>
    </li>
    
     <p><strong>Nota</strong>
    </p>
    
     <p>Quando si sceglie di creare un nuovo server SQL di Azure e un nuovo database, Servizi di Azure viene abilitato automaticamente nel database SQL. Il servizio BizTalk richiede che Servizi di Azure sia abilitato nel database SQL di Azure.</p>
    
     <p><strong>Suggerimento</strong>
    </p>
    
     Creare il database di rilevamento e l'account di monitoraggio/archiviazione nella stessa area geografica del servizio BizTalk.
     </ul>
    
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Sottoscrizione</strong>
    </td>
    
     <td><strong>Facoltativo</strong>
    . Disponibile solo se sono presenti più sottoscrizioni. Selezionare la propria sottoscrizione per ospitare il servizio BizTalk.</td>
    
     </tr>
    
     </table>
    
    Fare clic sulla freccia NEXT.

5.  Immettere le impostazioni del database:
    
    <table  border="1">
     <tr>
     <td><strong>Sottoscrizione</strong>
    </td>
    
     <td><strong>Facoltativo</strong>
    . Disponibile solo se sono presenti più sottoscrizioni. Selezionare la propria sottoscrizione per ospitare il database SQL di Azure.</td>
    
     </tr>
    
     <tr>
     <td><strong>Database</strong>
    </td>
    
     <td><p>Disponibile quando nella schermata precedente è stata selezionata l'opzione <strong>Use an existing SQL Database instance</strong>
    .</p>
    
     Selezionare il database SQL per archiviare le tabelle utilizzate dal servizio BizTalk.
     </td>
    
     </tr>
    
     <tr>
     <td><strong>Nome</strong>
    </td>
    
     <td><p>Disponibile quando nella schermata precedente è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>
    .</p>
    
     Immettere il nome del database SQL da utilizzare con il servizio BizTalk. Per impostazione predefinita, viene inserito <em>NomeServizioBizTalk</em>
    _db.</td>
    
     </tr>
    
     <tr>
     <td><strong>Server</strong>
    </td>
    
     <td><p>Disponibile quando nella schermata precedente è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>
    .</p>
    
     Selezionare un server di database SQL esistente. In alternativa, selezionare <strong>New SQL database server</strong>
     per creare un nuovo server di database SQL.</td>
    
     </tr>
    
     <tr>
     <td><strong>Nome di accesso al server</strong>
    </td>
    
     <td>Immettere il proprio nome utente di accesso.</td>
    
     </tr>
    
     <tr>
     <td><strong>Password di accesso al server</strong>
    </td>
    
     <td>Immettere la password di accesso.</td>
    
     </tr>
    
     <tr>
     <td><strong>Area geografica</strong>
    </td>
    
     <td>Disponibile quando è stata selezionata l'opzione <strong>Create a new SQL Database instance</strong>
    . Selezionare l'area geografica per ospitare il database SQL.</td>
    
     </tr>
    
     </table>
    
    Fare clic sulla freccia NEXT.

6.  Immettere le impostazioni di monitoraggio di Azure:
    
    <table  border="1">
     <tr>
     <td><strong>Account di monitoraggio/archiviazione</strong>
    </td>
    
     <td>Selezionare un account di archiviazione esistente o scegliere <strong>Crea un nuovo account di archiviazione</strong>
    .</td>
    
     </tr>
    <tr>
     <td><strong>Nome account di archiviazione</strong>
    </td>
    
     <td>Disponibile quando è stata selezionata l'opzione <strong>Crea un nuovo account di archiviazione</strong>
    . Immettere un nome per l'account di archiviazione che verrà utilizzato dal servizio BizTalk.</td>
    
     </tr>
    
     </table>

Per completare la procedura guidata, fare clic sul segno di spunta. Al termine, verrà visualizzata l'icona di avanzamento:  


![Icona di avanzamento visualizzata al
termine](./media/biztalk-provision-services/WABS_ProgressComplete.png)

A questo punto, viene eseguito il provisioning di un servizio BizTalk di Azure che sarà disponibile per essere utilizzato con le applicazioni desiderate.

Le impostazioni predefinite sono sufficienti. Se si desidera modificare le impostazioni predefinite, selezionare **BIZTALK SERVICES** nel pannello di navigazione sinistro e selezionare il proprio servizio BizTalk. Nelle schede Dashboard, Monitor e Scale sono disponibili altre impostazioni.

A seconda dello stato del servizio BizTalk, alcune operazioni potrebbero non essere completate. Per un elenco di queste operazioni, vedere
[Servizi BizTalk: Tabella degli stati del servizio][3].

## <a name="PostProv"></a>Passaggio 2: Passaggi di post-provisioning

In questa sezione vengono elencati i passaggi seguenti:

* [Aggiungere un certificato privato](#AddCert)
* [Recuperare lo spazio dei nomi del servizio di controllo di
  accesso](#ACS)

#### <a name="AddCert"></a>Aggiungere un certificato privato

Quando si esegue il provisioning di un servizio BizTalk di Azure, viene creato un URL che include il nome del servizio BizTalk. Un certificato SSL privato (con estensione pfx) viene utilizzato come certificato di autenticazione del server HTTPS quando vengono inviate richieste all'URL del servizio BizTalk.

Viene creato automaticamente un certificato autofirmato per il servizio BizTalk. Questo certificato può essere scaricato o sostituito nel dashboard del servizio BizTalk. I certificati autofirmati possono essere utilizzati in ambienti di sviluppo.

Per aggiungere un certificato per l'ambiente di produzione:

1.  Accedere al [portale di gestione di Azure][2]. 2.  Selezionare **BIZTALK SERVICES** nel pannello di navigazione
    sinistro e quindi scegliere il servizio BizTalk.
3.  Selezionare la scheda **Dashboard**. 4.  Selezionare **Update SSL Certificate**:  
      
     ![Modificare un certificato
    SSL](./media/biztalk-provision-services/WABS_QuickGlance.png)

5.  Cercare il certificato SSL privato (*NomeCertificato*.pfx) che
    include il nome del servizio BizTalk, immettere la password e
    selezionare il segno di spunta.

#### <a name="ACS"></a>Recuperare lo spazio dei nomi del servizio di controllo di accesso

Lo spazio dei nomi del servizio di controllo di accesso esegue l'autenticazione con Servizi BizTalk di Azure. Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi.

Lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente per il servizio BizTalk. Per recuperare lo spazio dei nomi del servizio di controllo di accesso, l'emittente predefinito e la chiave emittente, selezionare **Connection Information** nel dashboard del servizio BizTalk.

Per recuperare lo spazio dei nomi del servizio di controllo di accesso:

1.  Accedere al [portale di gestione di Azure][2]. 2.  Selezionare **BIZTALK SERVICES** nel pannello di navigazione
    sinistro e quindi scegliere il servizio BizTalk.
3.  Nella barra delle applicazioni selezionare **Connection
    Information**:  
      
     ![Selezionare Connection
    Information](./media/biztalk-provision-services/WABS_ACSConnectInformation.png)

È possibile copiare e incollare i valori del servizio di controllo di
accesso.

Quando viene creato lo spazio dei nomi del servizio di controllo di accesso, è possibile utilizzare i valori del controllo di accesso con qualsiasi applicazione. Quando viene eseguito il provisioning di Servizi BizTalk di Azure, lo spazio del nomi del servizio di controllo di accesso controlla l'autenticazione con la distribuzione del servizio BizTalk. Se si desidera modificare la sottoscrizione o gestire lo spazio dei nomi, selezionare **ACTIVE DIRECTORY** nel pannello di navigazione sinistro e quindi selezionare lo spazio dei nomi. Nella parte inferiore del pannello di navigazione sono elencate le opzioni.

Facendo clic su **Manage** è possibile aprire il portale di gestione del servizio di controllo di accesso. Nel portale il servizio BizTalk utilizza le **identità del servizio**:  
  
 ![Identità del servizio di controllo di
accesso nel portale di gestione del servizio](./media/biztalk-provision-services/WABS_ACSServiceIdentities.png)

Le identità del servizio di controllo di accesso sono set di credenziali che consentono ad applicazioni o client di eseguire l'autenticazione direttamente con il servizio di controllo di accesso e di ricevere un token.

**Importante**  
 Il servizio BizTalk utilizza **Owner** come
identità predefinita del servizio e il valore **Password**. Se si utilizza il valore della chiave simmetrica anziché il valore della password, potrebbe verificarsi l'errore seguente:

*Could not connect to the Access Control Management Service account with
the specified credentials*

In [Gestione dello spazio dei nomi del servizio di controllo di accesso][4] sono fornite alcune linee guida e consigli utili.

## <a name="SQLDB"></a>Facoltativo: Creare il server di database SQL

Quando si esegue il provisioning di un servizio BizTalk di Azure, viene creato automaticamente un server di database SQL. Se si preferisce creare un server di database SQL indipendente dal servizio BizTalk, vedere [Come utilizzare il database SQL di Azure in applicazioni
.NET][5].

Al termine, sarà disponibile un nuovo database SQL di Azure a cui connettersi per creare tabelle, viste e stored procedure.

Per impostazione predefinita, il database SQL è configurato come segue:

* Edizione: Web
* Dimensioni database: 1 GB

La configurazione predefinita è sufficiente per un servizio BizTalk. Se si desidera modificare le impostazioni di configurazione di scala, selezionare **SQL DATABASES** nel pannello di navigazione sinistro, fare doppio clic sul database SQL e selezionare la scheda **Configure**. La modifica della scala può influire sui prezzi. In [Account e fatturazione nel database SQL di Azure][6] sono disponibili informazioni sulle edizioni e sulla fatturazione.

## <a name="Storage"></a>Facoltativo: Creazione di un account di archiviazione

Quando si esegue il provisioning di un servizio BizTalk di Azure, viene creato automaticamente un account di archiviazione di Azure. Se si preferisce creare un account di archiviazione di Azure indipendente dal servizio BizTalk, vedere [Come creare un account di archiviazione][7].

Al termine, sarà disponibile un nuovo account di archiviazione di Azure per accedere alle tabelle, ai BLOB e alle code.

Le impostazioni predefinite sono sufficienti per un servizio BizTalk. Se si desidera modificare le impostazioni predefinite, selezionare
**STORAGE** nel pannello di navigazione sinistro e selezionare il
proprio account di archiviazione. Le impostazioni si trovano nelle schede Dashboard, Monitor, Configure e Containers.

Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi controllano l'accesso all'account di archiviazione. Il servizio BizTalk utilizza automaticamente la chiave primaria.

In [Archiviazione][8] sono disponibili informazioni sull'account di archiviazione.

## Passaggi successivi

Dopo il provisioning del servizio BizTalk, è possibile acquisire familiarità con le diverse schede leggendo [Servizi BizTalk: Schede Dashboard, Monitor e Scale][9]. Il servizio BizTalk è pronto per le applicazioni. Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure][10].

## Vedere anche

* [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e
  Premium][11]  
  
* [Servizi BizTalk: Tabella degli stati del servizio BizTalk][3]  
  
* [Servizi BizTalk: Backup e ripristino][12]  
  
* [Servizi BizTalk: Limitazione][13]  
  
* [Servizi BizTalk: Nome e chiave dell'autorità emittente][14]  
  
* [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure][15]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=239738
[2]: http://go.microsoft.com/fwlink/p/?LinkID=213885
[3]: http://go.microsoft.com/fwlink/p/?LinkID=329870
[4]: http://go.microsoft.com/fwlink/p/?LinkID=285670
[5]: http://go.microsoft.com/fwlink/p/?LinkID=251285
[6]: http://go.microsoft.com/fwlink/p/?LinkID=234930
[7]: http://go.microsoft.com/fwlink/p/?LinkID=279823
[8]: http://go.microsoft.com/fwlink/p/?LinkID=285671
[9]: http://go.microsoft.com/fwlink/p/?LinkID=302281
[10]: http://go.microsoft.com/fwlink/p/?LinkID=235197
[11]: http://go.microsoft.com/fwlink/p/?LinkID=302279
[12]: http://go.microsoft.com/fwlink/p/?LinkID=329873
[13]: http://go.microsoft.com/fwlink/p/?LinkID=302282
[14]: http://go.microsoft.com/fwlink/p/?LinkID=303941
[15]: http://go.microsoft.com/fwlink/p/?LinkID=302335