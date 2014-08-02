<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Servizi BizTalk: Schede Dashboard, Monitor e Scale
==================================================

Quando si apre il portale di gestione di Azure per la prima volta viene visualizzata automaticamente la scheda **ALL ITEMS**, al cui interno sono presenti delle colonne che è possibile ordinare****. Per visualizzare il Servizio BizTalk, selezionarlo nella scheda **ALL ITEMS** oppure fare clic sulla scheda **BIZTALK SERVICES**, quindi fare clic sul nome del proprio servizio BizTalk Service.

Verrà aperta una nuova finestra con le opzioni seguenti:

-   [Avvio rapido](#QuickStart)

-   [Dashboard](#Dashboard)

-   [Monitor](#Monitor)

-   [Scale](#Scale)

In questo argomento vengono descritte queste schede.

Quick Start
-----------

Nella scheda Quick Start è possibile eseguire le operazioni seguenti:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opzione</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
</tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Get the tools</td>

        <td data-morhtml="true">Scaricare l'SDK di Servizi BizTalk per installare i modelli di progetto di Visual Studio sul computer di sviluppo locale. I modelli consentono di creare i progetti Visuali Studio dei <strong data-morhtml="true">Servizi BizTalk</strong> (bridge) e degli <strong data-morhtml="true">elementi del servizio BizTalk</strong> (Transform) Visual Studio distribuiti nel proprio servizio BizTalk.

        <br data-morhtml="true" /><br data-morhtml="true" /> Negli articoli su <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302335">come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure</a> e <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=241589">installare l'SDK di Servizi BizTalk di Azure</a> sono elencate tutte le procedure introduttive.
        </td>
    </tr>

    <tr data-morhtml="true">
        <td data-morhtml="true">Create partner agreements</td>

        <td data-morhtml="true">Consente di aprire il portale Servizi BizTalk di Azure ospitato su Azure dove aggiungere partner e creare contratti EDI X12 e AS2.

        <br data-morhtml="true" /><br data-morhtml="true" />

        Nell'articolo relativo alla <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">configurazione di componenti per la messaggistica EDI sul portale Servizi BizTalk</a> sono elencate le procedure introduttive.
        </td>
    </tr>

<tr data-morhtml="true">
        <td data-morhtml="true">Ulteriori informazioni sui Servizi BizTalk</td>

        <td data-morhtml="true">Per ulteriori informazioni sui Servizi BizTalk di Azure visitare l'area risorse.</td>
</tr>

</table>

Nella barra delle applicazioni in basso è possibile **gestire** il servizio BizTalk, visualizzare le **informazioni di connessione** dello spazio dei nomi ACS, le **chiavi di sincronizzazione** dell'account di archiviazione oppure **eliminare** il servizio BizTalk:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opzione</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Manage</td>
<td data-morhtml="true">Quando si fa clic su Manage viene aperto il portale Servizi BizTalk di Azure. Il portale Servizi BizTalk di Azure &egrave; l'ingresso alla configurazione EDI; inclusa l'aggiunta di partner e la creazione di contratti AS2 e X12. <br data-morhtml="true" /><br data-morhtml="true" /> Questa opzione ha la stessa funzione dell'opzione <strong data-morhtml="true">Create partner agreements</strong> nella scheda <strong data-morhtml="true">Quick Start</strong>. <br data-morhtml="true" /><br data-morhtml="true" /> L'articolo relativo alla <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">configurazione di componenti per la messaggistica EDI sul portale Servizi BizTalk</a> contiene ulteriori informazioni sul portale Servizi BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Connection Information</td>
<td data-morhtml="true">Quando si fa clic su Connection Information vengono visualizzati i valori di Access Control Namespace, Default Issuer e Default Key. &Egrave; possibile copiare questi valori. <br data-morhtml="true" /><br data-morhtml="true" />&Egrave; anche possibile aprire il portale di gestione del servizio di controllo di accesso, che offre le stesse funzioni dell'opzione Active Directory nel pannello di navigazione a sinistra. <br data-morhtml="true" /><br data-morhtml="true" /> L'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestione dello spazio dei nomi ACS</a> offre ulteriori informazioni sul portale di gestione del servizio di controllo di accesso.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Sync Keys</td>
<td data-morhtml="true">Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi controllano l'accesso all'account di archiviazione. Il servizio BizTalk utilizza automaticamente la chiave primaria. L'opzione <strong data-morhtml="true">Sync Keys</strong> consente agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> Ad esempio, si potrebbe desiderare di utilizzare una nuova chiave primaria per l'account di archiviazione nel servizio BizTalk. A tale scopo, effettuare l'operazione seguente: <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">Fare clic sul servizio BizTalk e quindi fare clic su <strong data-morhtml="true">Sync Keys</strong>. Selezionare la chiave secondaria. Cos&igrave; facendo, il servizio BizTalk comincia a utilizzare la chiave secondaria.</li>
<li data-morhtml="true">Nel portale di gestione di Azure, fare clic sul proprio account di archiviazione e rigenerare la chiave primaria. Si tenga presente che il servizio BizTalk sta utilizzando la chiave secondaria.</li>
<li data-morhtml="true">Fare clic sul servizio BizTalk e quindi fare clic su <strong data-morhtml="true">Sync Keys</strong>. Selezionare adesso la chiave primaria, ossia la nuova chiave primaria rigenerata dall'utente.</li>
<li data-morhtml="true">Nel portale di gestione di Azure, fare clic sul proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br data-morhtml="true" /> Questo processo &egrave; denominato &quot;chiavi di rollover&quot;. Lo scopo &egrave; quello di consentire agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Delete</td>
<td data-morhtml="true">Quando si fa clic su Delete il servizio BizTalk e tutti gli elementi distribuiti verranno rimossi.</td>
</tr>
</table>

Dashboard
---------

Quando si fa clic sul nome del servizio BizTalk viene visualizzata la scheda Dashboard. Nella scheda Dashboard sono visualizzate le informazioni seguenti:

#### Metric Graph

Un grafico contenente un elenco fisso di metriche delle prestazioni, che forniscono valori in tempo reale riguardo lo stato corrente del servizio BizTalk. Tra le metriche sono incluse:

-   CPU Usage
-   Failures at Source
-   Failures in Process
-   Messages Processed
-   Messages Received
-   Processing Latency

Per una descrizione delle metriche delle prestazioni, vedere la sezione [Metriche disponibili](#Metrics) in questo argomento.

##### Relative o Absolute

Nel grafico vengono mostrate le tendenze e per ogni metrica viene visualizzato solo il valore corrente, ossia l'opzione **Relative**. Per visualizzare un asse Y in modo da vedere i valori assoluti, selezionare **Absolute**.

##### Interval

Consente di modificare l'intervallo di visualizzazione della metrica nel grafico. Le opzioni includono:

-   1 Hour
-   1 Day
-   7 Days

#### Quick Glance

Consente di elencare le proprietà del servizio BizTalk, incluse le seguenti:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opzione</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Update Tracking Database credentials</td>
<td data-morhtml="true">Consente di modificare il nome utente e la password utilizzati per accedere al database di rilevamento.<br data-morhtml="true" /><br data-morhtml="true" /> Quando si esegue il provisioning del servizio BizTalk si immettono un nome utente e una password per accedere al database di rilevamento. Tramite questa opzione, &egrave; possibile modificare il servizio BizTalk in modo da utilizzare un nome utente e una password diversi per accedere al database di rilevamento.<br data-morhtml="true" /><br data-morhtml="true" /> Nell'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servizi BizTalk: Provisioning tramite il portale di gestione di Azure</a> sono elencati i passaggi per eseguire il provisioning di un servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Update SSL Certificate</td>
<td data-morhtml="true">Consente di immettere un diverso certificato SSL.<br data-morhtml="true" /><br data-morhtml="true" /> Quando si esegue il provisioning del servizio BizTalk, viene creato automaticamente un certificato SSL autofirmato. Tramite questa opzione, &egrave; possibile modificare il servizio BizTalk in modo da utilizzare un diverso certificato SSL.<br data-morhtml="true" /><br data-morhtml="true" /> Nell'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servizi BizTalk: Provisioning tramite il portale di gestione di Azure</a> sono elencati i passaggi per eseguire il provisioning di un servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Download Certificate</td>
<td data-morhtml="true">Questa opzione consente di scaricare il certificato SSL utilizzato dal servizio BizTalk.<br data-morhtml="true" /><br data-morhtml="true" /> Nell'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">Servizi BizTalk: Provisioning tramite il portale di gestione di Azure</a> sono elencati i passaggi per eseguire il provisioning di un servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Status</td>
<td data-morhtml="true">Consente di visualizzare lo stato corrente del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Service URL</td>
<td data-morhtml="true">Questo URL reindirizza al servizio BizTalk. &Egrave; uguale all'URL del dominio immesso in <strong data-morhtml="true">Domain URL</strong> al momento del provisioning del servizio BizTalk. </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Public Virtual IP (VIP) Address</td>
<td data-morhtml="true">Questo indirizzo IP viene assegnato al servizio BizTalk, utilizzato per tutti gli endpoint di input ed &egrave; l'indirizzo di origine per il traffico in uscita. Questo indirizzo IP appartiene al proprio servizio BizTalk finch&eacute; &egrave; sottoposto a provisioning. Se si elimina il servizio BizTalk l'indirizzo IP viene assegnato alla distribuzione di un altro servizio.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ACS Namespace</td>
<td data-morhtml="true">Consente di eseguire l'autenticazione con il servizio BizTalk. &Egrave; uguale allo spazio dei nomi ACS immesso in <strong data-morhtml="true">ACS Namespace</strong> al momento del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Edition</td>
<td data-morhtml="true">Consente di indicare l'edizione. Tra le opzioni incluse vi sono le edizioni Developer, Basic, Standard e Premium. &Egrave; la stessa edizione immessa al momento del provisioning del servizio BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> Nell'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302281">Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium</a> vengono elencate le differenze tra le edizioni, costi inclusi.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Location</td>
<td data-morhtml="true">Indica l'area geografica in cui &egrave; ospitato il servizio BizTalk. &Egrave; uguale al valore immesso in <strong data-morhtml="true">Region</strong> al momento del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Created</td>
<td data-morhtml="true">Indica la data e l'ora del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Tracking Database</td>
<td data-morhtml="true">Database SQL di Azure in cui sono archiviate le tabelle di rilevamento utilizzate dal servizio BizTalk. &Egrave; uguale al valore immesso in <strong data-morhtml="true">Tracking Database</strong> al momento del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Monitoring/Archiving Storage</td>
<td data-morhtml="true">Nome dell'account di archiviazione di Azure in cui &egrave; archiviato l'output del monitoraggio del servizio BizTalk. &Egrave; uguale al valore immesso in <strong data-morhtml="true">Monitoring/Archiving Storage Account</strong> al momento del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Subscription Name</td>
<td data-morhtml="true">La sottoscrizione consente di gestire l'accesso al portale di gestione di Azure. &Egrave; lo stesso nome di <strong data-morhtml="true">sottoscrizione</strong> selezionato al momento del provisioning del servizio BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Subscription ID</td>
<td data-morhtml="true">La sottoscrizione consente di gestire l'accesso al portale di gestione di Azure. Quando si crea una sottoscrizione viene generato automaticamente un ID sottoscrizione. Quando si utilizzano le API REST pu&ograve; essere necessario immettere l'ID sottoscrizione.</td>
</tr>
</table>

Nell'articolo [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) sono elencati i passaggi per eseguire il provisioning di un servizio BizTalk.

#### Manage, Connection Information, Sync Keys e Delete

Nella barra delle applicazioni in basso è possibile **gestire** il servizio BizTalk, visualizzare le **informazioni di connessione** dello spazio dei nomi ACS, le **chiavi di sincronizzazione** dell'account di archiviazione oppure **eliminare** il servizio BizTalk:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">Opzione</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Manage</td>
<td data-morhtml="true">Quando si fa clic su Manage viene aperto il portale Servizi BizTalk di Azure. Il portale Servizi BizTalk di Azure &egrave; l'ingresso alla configurazione EDI; inclusa l'aggiunta di partner e la creazione di contratti AS2 e X12. <br data-morhtml="true" /><br data-morhtml="true" /> Questa opzione ha la stessa funzione dell'opzione <strong data-morhtml="true">Create partner agreements</strong> nella scheda <strong data-morhtml="true">Quick Start</strong>. <br data-morhtml="true" /><br data-morhtml="true" /> L'articolo relativo alla <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">configurazione di componenti per la messaggistica EDI sul portale Servizi BizTalk</a> contiene ulteriori informazioni sul portale Servizi BizTalk.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Connection Information</td>
<td data-morhtml="true">Quando si fa clic su Connection Information vengono visualizzati i valori di Access Control Namespace, Default Issuer e Default Key. &Egrave; possibile copiare questi valori. <br data-morhtml="true" /><br data-morhtml="true" />&Egrave; anche possibile aprire il portale di gestione del servizio di controllo di accesso, che offre le stesse funzioni dell'opzione Active Directory nel pannello di navigazione a sinistra. <br data-morhtml="true" /><br data-morhtml="true" /> L'articolo <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestione dello spazio dei nomi ACS</a> offre ulteriori informazioni sul portale di gestione del servizio di controllo di accesso.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Sync Keys</td>
<td data-morhtml="true">Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi controllano l'accesso all'account di archiviazione. Il servizio BizTalk utilizza automaticamente la chiave primaria. L'opzione <strong data-morhtml="true">Sync Keys</strong> consente agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk. <br data-morhtml="true" /><br data-morhtml="true" /> Ad esempio, si potrebbe desiderare di utilizzare una nuova chiave primaria per l'account di archiviazione nel servizio BizTalk. A tale scopo, effettuare l'operazione seguente: <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">Fare clic sul servizio BizTalk e quindi fare clic su <strong data-morhtml="true">Sync Keys</strong>. Selezionare la chiave secondaria. Cos&igrave; facendo, il servizio BizTalk comincia a utilizzare la chiave secondaria.</li>
<li data-morhtml="true">Nel portale di gestione di Azure, fare clic sul proprio account di archiviazione e rigenerare la chiave primaria. Si tenga presente che il servizio BizTalk sta utilizzando la chiave secondaria.</li>
<li data-morhtml="true">Fare clic sul servizio BizTalk e quindi fare clic su <strong data-morhtml="true">Sync Keys</strong>. Selezionare adesso la chiave primaria, ossia la nuova chiave primaria rigenerata dall'utente.</li>
<li data-morhtml="true">Nel portale di gestione di Azure, fare clic sul proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br data-morhtml="true" /> Questo processo &egrave; denominato &quot;chiavi di rollover&quot;. Lo scopo &egrave; quello di consentire agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.</td>
</tr>

<tr data-morhtml="true">
<td data-morhtml="true">Delete</td>
<td data-morhtml="true">Quando si fa clic su Delete il servizio BizTalk e tutti gli elementi distribuiti verranno rimossi.</td>
</tr>
</table>


Monitor
-------

Nella scheda Monitor sono visualizzate le informazioni seguenti:

#### Metric Graph

Un grafico contenente le metriche delle prestazioni selezionate, che forniscono valori in tempo reale riguardo lo stato corrente del servizio BizTalk. È l'utente a scegliere quali metriche visualizzare. È possibile visualizzare fino a un massimo di sei metriche delle prestazioni simultaneamente.

##### Relative o Absolute

Nel grafico vengono mostrate le tendenze e per ogni metrica viene visualizzato solo il valore corrente, ossia l'opzione **Relative**. Per visualizzare un asse Y in modo da vedere i valori assoluti, selezionare **Absolute**.

##### Interval

Consente di modificare l'intervallo di visualizzazione della metrica nel grafico. Le opzioni includono:

-   1 Hour
-   1 Day
-   7 Days

#### Per rimuovere o visualizzare le metriche nel grafico

> 1.  Fare clic sulla scheda **Monitor**.
> 2.  Fare clic su **Add Metrics** nella barra delle applicazioni:  
>      ![Fare clic su Add Metrics](./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png)
> 3.  Controllare le metriche delle prestazioni che si desidera visualizzare nella scheda **Monitor**.
> 4.  Fare clic sul segno di spunta per tornare alla scheda **Monitor**.
> 5.  Fare clic sul cerchio accanto alla metrica desiderata per visualizzarne il valore nel grafico.

>      Ad esempio, la metrica **CPU Usage** è inattiva e il suo output non è visualizzato nel grafico:  
>      ![Metrica CPU Usage inattiva](./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png)
>      Fare clic sul cerchio inattivo per abilitare la metrica **CPU Usage** e visualizzarne il valore nel grafico:  
>      ![Metrica CPU Usage abilitata](./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png)

> 1.  Per rimuovere una metrica dal grafico e dall'elenco, fare clic su **Delete Metric** nella barra delle applicazioni. Fare clic su **Delete Metric** per rimuovere la metrica dalla scheda Monitor. Per aggiungere di nuovo la metrica all'elenco, fare clic su **Add Metrics** nella barra delle applicazioni, controllare la metrica e fare clic sul segno di spunta per tornare alla scheda **Monitor**. Fare clic sul cerchio inattivo per abilitare la metrica nel grafico.

Metriche disponibili
--------------------

Sono disponibili i contatori/le metriche seguenti:

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
<td data-morhtml="true"><strong data-morhtml="true">Metrica</strong></td>
<td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">RountdTrip Latency</td>
<td data-morhtml="true">Questa metrica delle prestazioni indica l'intervallo medio, espresso in millisecondi (ms), impiegato per l'elaborazione completa di un messaggio da parte del servizio BizTalk dal momento della ricezione attraverso tutti i bridge. Vengono conteggiati solo i messaggi correttamente elaborati.<br data-morhtml="true" /><br data-morhtml="true" /> Quando si verificano gli eventi seguenti viene creato un timestamp:
<ul data-morhtml="true">
<li data-morhtml="true">Il messaggio accede al gateway</li>
<li data-morhtml="true">Il messaggio viene indirizzato alla destinazione</li>
<li data-morhtml="true">Si riceve una risposta dalla destinazione</li>
<li data-morhtml="true">La risposta di conferma della destinazione viene inviata al gateway</li>
</ul>
<br data-morhtml="true" /> Questa metrica indica il risultato del calcolo seguente: <br data-morhtml="true" /><br data-morhtml="true" /> [La risposta di conferma della destinazione viene inviata al gateway] - [Il messaggio accede al gateway]</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Failures At Source</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi che il servizio BizTalk non ha estratto correttamente dagli endpoint di origine.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">CPU Usage</td>
<td data-morhtml="true">Consente di elencare il valore % tempo processore di tutte le istanze del ruolo.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Processing Latency</td>
<td data-morhtml="true">Questa metrica delle prestazioni indica l'intervallo medio, espresso in millisecondi (ms), impiegato per l'elaborazione di un messaggio da parte del servizio BizTalk attraverso tutti i bridge, esclusi il tempo impiegato nelle destinazioni. Vengono conteggiati solo i messaggi correttamente elaborati.<br data-morhtml="true" /><br data-morhtml="true" /> Quando si verifica uno degli eventi seguenti viene creato un timestamp:

<ul data-morhtml="true">
<li data-morhtml="true">Il messaggio accede al gateway</li>
<li data-morhtml="true">Il messaggio viene indirizzato alla destinazione</li>
<li data-morhtml="true">Si riceve una risposta dalla destinazione</li>
<li data-morhtml="true">La risposta di conferma della destinazione viene inviata al gateway</li>
</ul>
<br data-morhtml="true" />Questa metrica indica il risultato del calcolo seguente:<br data-morhtml="true" /><br data-morhtml="true" /> [La risposta di conferma della destinazione viene inviata al gateway] - [Il messaggio accede al gateway] - [Si riceve una risposta dalla destinazione] + [Il messaggio viene indirizzato alla destinazione]</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Failures In Process</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi che il servizio BizTalk non ha elaborato correttamente attraverso tutti i bridge entro un determinato intervallo di tempo.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Messages Sent</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi inviati dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando un messaggio inviato da una pipeline raggiunge la destinazione della route. Questa metrica non indica la corretta elaborazione di un messaggio.<br data-morhtml="true" /><br data-morhtml="true" /> In uno scenario di tipo richiesta-risposta, la metrica viene incrementata quando la destinazione della route restituisce una conferma di ricezione alla pipeline.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Messages Received</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi ricevuti dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando si riceve un nuovo messaggio inviato dalla pipeline.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Messages In Process</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi attualmente in corso di elaborazione da parte del servizio BizTalk entro un determinato intervallo di tempo.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">Messages Processed</td>
<td data-morhtml="true">Metrica delle prestazioni che consente di visualizzare il numero totale di messaggi correttamente elaborati dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando si riceve correttamente un messaggio inviato dalla pipeline, che viene quindi correttamente indirizzato alla destinazione.</td>
</tr>
</table>

Scale
-----

Nella scheda Scale è possibile aggiungere o sottrarre il numero di unità utilizzate dal servizio BizTalk. Per impostazione predefinita è configurata una sola unità. È possibile aggiungere ulteriori unità per la scalabilità del servizio BizTalk. Quando si aumenta la scalabilità si aumenta anche la velocità effettiva. Aumenta inoltre la quantità di risorse, inclusi i bridge distribuiti, i contratti, le connessioni LOB e la potenza di elaborazione. Se ad esempio si aumenta la scalabilità da 1 unità a 2 unità, è possibile distribuire un numero doppio di bridge, di contratti e di connessioni LOB, oltre a una quantità doppia di potenza di elaborazione.

Alcune edizioni di BizTalk non offrono un'opzione di scalabilità: in questo caso è consentita una sola unità. Per determinare il numero di unità in base al quale è possibile scalare l'edizione in uso, consultare l'articolo [BizTalk Services: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279).

L'aumento del numero di unità può incidere sui prezzi. Se si aumentano le unità, quando si fa clic su **Save** verrà visualizzato un messaggio che informa dell'impatto sulla fatturazione. Sta all'utente sceglie se continuare. Quando si aumenta il numero di unità, lo stato del servizio BizTalk passa da Active ad Updating. Nello stato Updating l'esecuzione del servizio BizTalk non verrà interrotta.

Passaggi successivi
-------------------

Ora che è stata acquisita familiarità con le diverse schede è possibile ottenere ulteriori informazioni sulle funzionalità dei Servizi BizTalk di Azure:

-   [Servizi BizTalk: Limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servizi BizTalk: Nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)

Vedere anche
------------

-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: Tabella degli stati del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

