<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor, Scale, Configure and Hybrid Connections tabs" pageTitle="Dashboard, Monitor, and Scale in BizTalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale, wabs, mabs" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Servizi BizTalk: Schede Dashboard, Monitoraggio, Scala, Configura e Connessione ibrida

Quando si apre il portale di gestione di Azure per la prima volta viene visualizzata automaticamente la scheda **TUTTI GLI ELEMENTI**, al cui interno sono presenti delle colonne che è possibile ordinare. Per visualizzare il Servizio BizTalk, selezionarlo nella scheda **TUTTI GLI ELEMENTI** oppure fare clic sulla scheda **SERVIZI BIZTALK**, quindi selezionare il nome del proprio servizio BizTalk Service.

Verrà aperta una nuova finestra con le schede seguenti: In questo argomento vengono descritte queste schede.

-   ![Quick Start][Quick Start] [Avvio rapido](#QuickStart)

-   [Dashboard](#Dashboard)

-   [Monitoraggio](#Monitor)

-   [Scala](#Scale)

-   [Configura](#Configure)

-   [Connessioni ibride](#HybridConnections)

## <a name="QuickStart"></a>Avvio rapido (![Quick Start][Quick Start])

A seconda dell'edizione di Servizi BizTalk, non tutte le opzioni elencate potrebbero essere disponibili.

<table border="1">

<tr>

<td>
<strong>Scarica gli strumenti</strong>

</td>
</p>
        <td>Download the BizTalk Services SDK to install the Visual Studio project templates on your on-premises development computer. These templates create the <strong>BizTalk Services</strong> (bridge) and the <strong>BizTalk Service Artifacts</strong> (Transform) Visual Studio projects that are deployed to your BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK </a> and <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK</a> lists the steps to get started.
        </td>
    </tr>

    <tr>
        <td><strong>Create partner agreements</strong></td>

        <td>Opens the Azure BizTalk Services Portal hosted on Azure where you add partners and create X12, AS2, and EDIFACT EDI agreements.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal</a> lists the steps to get started.
        </td>
    </tr>

<tr>
<td>
<strong>Altre informazioni sui servizi BizTalk</strong>

</td>
<td>
Per altre informazioni sui Servizi BizTalk di Azure visitare l'<a HREF="http://azure.microsoft.com/it-it/documentation/services/biztalk-services/">area risorse</a>.

</td>
</tr>
</table>
Sulla barra delle applicazioni in basso è possibile:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Gestire</strong> la distribuzione delle applicazioni</td>
<td align="left">Viene aperto il portale di Servizi BizTalk di Azure. Il portale di Servizi BizTalk è l'ingresso alla configurazione EDI, incluse l'aggiunta di partner e la creazione di contratti X12, AS2 ed EDIFACT.<br /><br /> Questa opzione ha la stessa funzione dell'opzione <strong>Crea contratti partner</strong> nella scheda <strong>Avvio rapido</strong>.<br /><br /> L'articolo relativo alla <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">configurazione di componenti per la messaggistica EDI sul portale di Servizi BizTalk</a> contiene altre informazioni sul portale di Servizi BizTalk.</td>
</tr>
<tr class="even">
<td align="left">Visualizzare <strong>Informazioni di connessione</strong> dello spazio dei nomi ACS</td>
<td align="left">Quando si seleziona Informazioni di connessione, vengono visualizzati i valori di Spazio dei nomi di Access Control, Autorità di certificazione predefinita e Chiave predefinita. È possibile copiare questi valori.<br /><br /> È anche possibile aprire il portale di gestione di Access Control, che offre le stesse funzioni dell'opzione <strong>Active Directory</strong> nel pannello di navigazione a sinistra.<br /><br /> L'articolo <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestione dello spazio dei nomi ACS</a> offre altre informazioni sul portale di gestione del servizio di controllo di accesso.</td>
</tr>
<tr class="odd">
<td align="left">Usare <strong>Chiavi di sincronizzazione</strong> nell'account di archiviazione</td>
<td align="left">Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi di crittografia controllano l'accesso all'account di archiviazione. Il servizio BizTalk usa automaticamente la chiave primaria. L'opzione <strong>Chiavi di sincronizzazione</strong> consente agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.<br /><br /> Ad esempio, si potrebbe voler usare una nuova chiave primaria per l'account di archiviazione nel servizio BizTalk. A tale scopo, effettuare l'operazione seguente:<br /><br />
<ol>
<li>Selezionare il servizio BizTalk e quindi <strong>Chiavi di sincronizzazione</strong>. Selezionare la chiave secondaria. Così facendo, il servizio BizTalk comincia a usare la chiave secondaria.</li>
<li>Nel portale di gestione di Azure, selezionare il proprio account di archiviazione e rigenerare la chiave primaria. Si tenga presente che il servizio BizTalk sta usando la chiave secondaria.</li>
<li>Selezionare il servizio BizTalk e quindi <strong>Chiavi di sincronizzazione</strong>. Selezionare adesso la chiave primaria, ossia la nuova chiave primaria rigenerata dall'utente.</li>
<li>Nel portale di gestione di Azure, selezionare il proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br /> Questo processo è denominato &quot;chiavi di rollover&quot;. Lo scopo è quello di consentire agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Eliminare</strong> l'applicazione</td>
<td align="left">Quando si fa clic su Elimina, il servizio BizTalk e tutti gli elementi distribuiti verranno rimossi.</td>
</tr>
</tbody>
</table>

## <a name="Dashboard"></a>Dashboard

A seconda dell'edizione di Servizi BizTalk, non tutte le opzioni elencate potrebbero essere disponibili.

Quando si seleziona il nome del servizio BizTalk, viene visualizzata la scheda Dashboard. Nella scheda Dashboard sono visualizzate le informazioni seguenti:

##### Panoramica sull'utilizzo mostra il numero delle connessioni ibride in uso

Visualizza anche l'uso dei dati espresso in GB.

##### Grafico metrica: mostra un elenco fisso della metrica delle prestazioni

Include i valori in tempo reale riguardo l'integrità del servizio BizTalk. È anche possibile specificare un valore **Relativo** o **Assoluto** e l'**Intervallo** della metrica visualizzata nel grafico.

Per una descrizione delle metriche delle prestazioni, vedere la sezione [Metriche disponibili](#Metrics) in questo argomento.

##### Riepilogo rapido: elenca le proprietà del servizio BizTalk

<table border="1">

<tr>
<td><strong>Aggiorna credenziali del database di rilevamento</strong></td>
<td>Consente di modificare il nome utente e la password usati per accedere al database di rilevamento.</td>
</tr>
<tr>
<td><strong>Aggiorna certificato SSL</strong></td>
<td>Consente di modificare il servizio BizTalk in modo da usare un certificato SSL diverso. Quando si <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">crea il servizio BizTalk</a>, viene creato automaticamente un certificato SSL autofirmato.</td>
</tr>
<tr>
<td><strong>Scarica certificato</strong></td>
<td>Consente di scaricare il certificato SSL usato dal servizio BizTalk su un computer locale.</td>
</tr>
<tr>
<td><strong>Stato</strong></td>
<td>Visualizza lo stato corrente del servizio BizTalk. Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Servizi BizTalk: tabella degli stati del servizio</a>. </td>
</tr>
<tr>
<td><strong>URL del servizio</strong></td>
<td>L'URL del servizio BizTalk. &Egrave; uguale al valore di <strong>URL del dominio</strong> immesso al momento della creazione del servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Public Virtual IP (VIP) Address</strong></td>
<td>Questo indirizzo IP viene assegnato al servizio BizTalk, usato per tutti gli endpoint di input ed &egrave; l'indirizzo di origine per il traffico in uscita. Questo indirizzo IP appartiene al proprio servizio BizTalk finch&eacute; &egrave; sottoposto a provisioning. Se si elimina il servizio BizTalk l'indirizzo IP viene assegnato a un altro servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Spazio dei nomi ACS</strong></td>
<td>Consente di eseguire l'autenticazione con il servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Edizione</strong></td>
<td>Elenca l'edizione immessa al momento della creazione del servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Percorso</strong></td>
<td>Visualizza l'area geografica in cui &egrave; ospitato il servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Data di creazione</strong></td>
<td>Visualizza la data e l'ora della creazione del servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Database di rilevamento</strong></td>
<td>Database SQL di Azure in cui sono archiviate le tabelle di rilevamento usate dal servizio BizTalk. 
<br/><br/>
Nella pagina relativa alla <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">descrizione dei requisiti</a> sono disponibili informazioni dettagliate sul database di rilevamento.</td>
</tr>
<tr>
<td><strong>Account di archiviazione di monitoraggio/archiviazione</strong></td>
<td>Nome dell'account di archiviazione di Azure in cui &egrave; archiviato l'output del monitoraggio del servizio BizTalk.
<br/><br/>
Nella pagina relativa alla <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">descrizione dei requisiti</a> sono disponibili informazioni dettagliate sull'account di archiviazione.</td>
</tr>
<tr>
<td><strong>Nome sottoscrizione</strong></td>
<td>Indica il nome della sottoscrizione che ospita il servizio BizTalk. La sottoscrizione consente di gestire l'accesso al portale di gestione di Azure.</td>
</tr>
<tr>
<td><strong>ID sottoscrizione</strong></td>
<td>Quando si crea una sottoscrizione viene generato automaticamente un ID sottoscrizione. Quando si usano le API REST pu&ograve; essere necessario immettere l'ID sottoscrizione.</td>
</tr>
</table>

[Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) elenca i passaggi per creare un servizio BizTalk.

##### Gestisci, Informazioni di connessione, Chiavi di sincronizzazione ed Elimina sulla barra delle applicazioni:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Gestire</strong> la distribuzione delle applicazioni</td>
<td align="left">Viene aperto il portale di Servizi BizTalk di Azure. Il portale di Servizi BizTalk è l'ingresso alla configurazione EDI, incluse l'aggiunta di partner e la creazione di contratti X12, AS2 ed EDIFACT.<br /><br /> Questa opzione ha la stessa funzione dell'opzione <strong>Crea contratti partner</strong> nella scheda <strong>Avvio rapido</strong>.<br /><br /> L'articolo relativo alla <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">configurazione di componenti per la messaggistica EDI sul portale di Servizi BizTalk</a> contiene altre informazioni sul portale di Servizi BizTalk.</td>
</tr>
<tr class="even">
<td align="left">Visualizzare <strong>Informazioni di connessione</strong> dello spazio dei nomi ACS</td>
<td align="left">Visualizza i valori di Spazio dei nomi di Access Control, Autorità di certificazione predefinita e Chiave predefinita, che possono essere copiati.<br /><br /> È anche possibile aprire il portale di gestione di Access Control, che offre le stesse funzioni dell'opzione Active Directory nel pannello di navigazione a sinistra.<br /><br /> L'articolo <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestione dello spazio dei nomi ACS</a> offre altre informazioni sul portale di gestione del servizio di controllo di accesso.</td>
</tr>
<tr class="odd">
<td align="left">Usare <strong>Chiavi di sincronizzazione</strong> nell'account di archiviazione</td>
<td align="left">Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi di crittografia controllano l'accesso all'account di archiviazione. Il servizio BizTalk usa automaticamente la chiave primaria. L'opzione <strong>Chiavi di sincronizzazione</strong> consente agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.<br /><br /> Ad esempio, si potrebbe voler usare una nuova chiave primaria per l'account di archiviazione nel servizio BizTalk. A tale scopo, effettuare l'operazione seguente:<br /><br />
<ol>
<li>Selezionare il servizio BizTalk e quindi <strong>Chiavi di sincronizzazione</strong>. Selezionare la chiave secondaria. Così facendo, il servizio BizTalk comincia a usare la chiave secondaria.</li>
<li>Nel portale di gestione di Azure, selezionare il proprio account di archiviazione e rigenerare la chiave primaria. Si tenga presente che il servizio BizTalk sta usando la chiave secondaria.</li>
<li>Selezionare il servizio BizTalk e quindi <strong>Chiavi di sincronizzazione</strong>. Selezionare adesso la chiave primaria, ossia la nuova chiave primaria rigenerata dall'utente.</li>
<li>Nel portale di gestione di Azure, selezionare il proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br /> Questo processo è denominato &quot;chiavi di rollover&quot;. Lo scopo è quello di consentire agli utenti di passare dalla chiave primaria alla secondaria e viceversa senza interrompere il servizio BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Eliminare</strong> l'applicazione</td>
<td align="left">Il servizio BizTalk e tutti gli elementi distribuiti verranno rimossi.</td>
</tr>
</tbody>
</table>

## <a name="Monitor"></a>Monitoraggio

Non si applica all'edizione gratuita.

Quando si seleziona il nome del servizio BizTalk, la scheda Monitoraggio è disponibile e visualizza quanto indicato di seguito:

##### Grafico metrica: visualizza la metrica delle prestazioni selezionata

Include i valori in tempo reale riguardo l'integrità del servizio BizTalk. È l'utente a scegliere quale metrica visualizzare. È possibile visualizzare fino a un massimo di sei metriche delle prestazioni simultaneamente.

È anche possibile specificare un valore **Relativo** o **Assoluto** e l'**Intervallo** della metrica visualizzata.

##### Per rimuovere o visualizzare la metrica nel grafico

1.  Selezionare la scheda **Monitoraggio**.
2.  Selezionare **Aggiungi metriche** sulla barra delle applicazioni:
    ![Select Add Metrics][AddMetrics]
3.  Controllare la metrica delle prestazioni che si desidera visualizzare.
4.  Fare clic sul segno di spunta per tornare alla scheda **Monitoraggio**.
5.  Selezionare il cerchio accanto alla metrica per visualizzarne i valori nel grafico.
    Ad esempio, la metrica **Utilizzo di CPU** è in grigio, quindi il relativo output non viene visualizzato nel grafico:
    ![CPU Usage metric is grayed out][GrayedMetric]
    Selezionare il cerchio in grigio per abilitare la metrica **Utilizzo di CPU** in modo da visualizzarne l'output nel grafico:
    ![CPU Usage metric is enabled][EnabledMetric]

6.  Per rimuovere una metrica dal grafico e dall'elenco, fare clic su **Elimina metrica** sulla barra delle applicazioni. Per aggiungere di nuovo la metrica all'elenco, fare clic su **Aggiungi metriche** sulla barra delle applicazioni, controllare la metrica e fare clic sul segno di spunta per tornare alla scheda **Monitoraggio**. Selezionare il cerchio in grigio per abilitare la metrica.

## <a name="Metrics"></a>Metriche disponibili

Sono disponibili i contatori/le metriche seguenti:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Latenza di andata e ritorno</strong></td>
<td align="left">Visualizza il tempo medio, espresso in millisecondi (ms), impiegato per l'elaborazione completa di un messaggio da parte del servizio BizTalk dal momento della ricezione attraverso tutti i bridge. Vengono conteggiati solo i messaggi correttamente elaborati.<br /><br /> Quando si verificano gli eventi seguenti viene creato un timestamp:
<ul>
<li>Il messaggio accede al gateway</li>
<li>Il messaggio viene indirizzato alla destinazione</li>
<li>Si riceve una risposta dalla destinazione</li>
<li>La risposta di conferma della destinazione viene inviata al gateway</li>
</ul>
<br /> Questa metrica indica il risultato del calcolo seguente:<br /><br /> [La risposta di conferma della destinazione viene inviata al gateway] - [Il messaggio accede al gateway]</td>
</tr>
<tr class="even">
<td align="left"><strong>Errore nell'origine</strong></td>
<td align="left">Visualizza il numero totale di messaggi di cui il servizio BizTalk non eseguito correttamente il pull dagli endpoint di origine.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Utilizzo di CPU</strong></td>
<td align="left">Consente di elencare il valore % tempo processore di tutte le istanze del ruolo.</td>
</tr>
<tr class="even">
<td align="left"><strong>Latenza di elaborazione</strong></td>
<td align="left">Visualizza il tempo medio, espresso in millisecondi (ms), impiegato per l'elaborazione di un messaggio da parte del servizio BizTalk attraverso tutti i bridge, esclusi il tempo impiegato nelle destinazioni. Vengono conteggiati solo i messaggi correttamente elaborati.<br /><br /> Quando si verifica uno degli eventi seguenti viene creato un timestamp:
<ul>
<li>Il messaggio accede al gateway</li>
<li>Il messaggio viene indirizzato alla destinazione</li>
<li>Si riceve una risposta dalla destinazione</li>
<li>La risposta di conferma della destinazione viene inviata al gateway</li>
</ul>
<br />Questa metrica indica il risultato del calcolo seguente:<br /><br /> [La risposta di conferma della destinazione viene inviata al gateway] - [Il messaggio accede al gateway] - [Si riceve una risposta dalla destinazione] + [Il messaggio viene indirizzato alla destinazione]\</td>
</tr>
<tr class="odd">
<td align="left"><strong>Errori nel processo</strong></td>
<td align="left">Visualizza il numero totale di messaggi che il servizio BizTalk non ha elaborato correttamente attraverso tutti i bridge entro un determinato intervallo di tempo.</td>
</tr>
<tr class="even">
<td align="left"><strong>Messaggi inviati</strong></td>
<td align="left">Visualizza il numero totale di messaggi inviati dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando un messaggio inviato da una pipeline raggiunge la destinazione della route. Questa metrica non indica la corretta elaborazione di un messaggio.<br /><br /> In uno scenario di tipo richiesta-risposta, la metrica viene incrementata quando la destinazione della route restituisce una conferma di ricezione alla pipeline.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Messaggi ricevuti</strong></td>
<td align="left">Visualizza il numero totale di messaggi ricevuti dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando si riceve un nuovo messaggio inviato dalla pipeline.</td>
</tr>
<tr class="even">
<td align="left"><strong>Messaggi nel processo</strong></td>
<td align="left">Visualizza il numero totale di messaggi attualmente in corso di elaborazione da parte del servizio BizTalk entro un determinato intervallo di tempo.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Messaggi elaborati</strong></td>
<td align="left">Visualizza il numero totale di messaggi correttamente elaborati dal servizio BizTalk attraverso tutti i bridge entro un determinato intervallo di tempo. Questa metrica viene incrementata quando si riceve correttamente un messaggio inviato dalla pipeline, che viene quindi correttamente indirizzato alla destinazione.</td>
</tr>
</tbody>
</table>

## <a name="Scale"></a>Scala

Nella scheda Scala è possibile aggiungere o sottrarre il numero di unità usate dal servizio BizTalk. Per impostazione predefinita è configurata una sola unità. È possibile aggiungere altre unità per la scalabilità del servizio BizTalk. Quando si aumenta la scalabilità si aumenta anche la velocità effettiva. Aumenta inoltre la quantità di risorse, inclusi i bridge distribuiti, i contratti, le connessioni LOB e la potenza di elaborazione. Se ad esempio si aumenta la scalabilità da 1 unità a 2 unità, è possibile distribuire un numero doppio di bridge, di contratti e di connessioni LOB, oltre a una quantità doppia di potenza di elaborazione.

Alcune edizioni di BizTalk non offrono un'opzione di scalabilità: in questo caso è consentita una sola unità. Per determinare il numero di unità in base al quale è possibile scalare l'edizione in uso, vedere l'articolo [BizTalk Services: Tabella delle edizioni](http://go.microsoft.com/fwlink/p/?LinkID=302279).

L'aumento del numero di unità può incidere sui prezzi. Se si aumentano le unità, quando si seleziona **Salva** verrà visualizzato un messaggio che informa dell'impatto sulla fatturazione. Sta all'utente sceglie se continuare. Quando si aumenta il numero di unità, lo stato del servizio BizTalk passa da Attivo ad Aggiornamento. Nello stato Aggiornamento l'esecuzione del servizio BizTalk non verrà interrotta.

In [Servizi BizTalk: Tabella delle edizioni](http://go.microsoft.com/fwlink/p/?LinkID=302279) viene definita una "Unità".

## <a name="Configure"></a>Configura

Non si applica alle connessioni ibride.

Imposta Stato backup su Nessuno o Automatico. Se è impostato su Nessuno, non vengono creati backup automaticamente. Se è impostato su Automatico, configurare la posizione di backup, la frequenza e la durata di mantenimento dei file di backup.

In [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873) sono disponibili informazioni dettagliate.

## <a name="HybridConnections"></a>Connessioni ibride

consentono la connessione di un'applicazione di Azure, come Siti Web o Servizi mobili, a una risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati. Le connessioni ibride vengono gestite in Servizi BizTalk nel portale di gestione di Azure.

Per creare connessioni ibride in Siti Web di Azure, vedere l'articolo relativo a [Connessioni ibride: connettere un sito Web di Azure a una risorsa locale](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Per usa re connessioni ibride in Servizi mobili di Azure, vedere l'articolo relativo a [Servizi mobili di Azure e connessioni ibride](http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).

Per creare o gestire connessioni ibride in Siti Web di Azure, vedere l'articolo relativo alle [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Avanti

Ora che è stata acquisita familiarità con le diverse schede è possibile ottenere altre informazioni sulle funzionalità dei Servizi BizTalk di Azure:

-   [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)

## Vedere anche

-   [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)
-   [Servizi BizTalk: tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: tabella degli stati del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

  [Quick Start]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
  [AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png


  [Metriche disponibili]: #Metrics
  [Select Add Metrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [CPU Usage metric is grayed out]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [CPU Usage metric is enabled]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
  [BizTalk Services: Tabella delle edizioni]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Servizi BizTalk: backup e ripristino]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Connessioni ibride: connettere un sito Web di Azure a una risorsa locale]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Servizi mobili di Azure e connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [Servizi BizTalk: limitazione]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Servizi BizTalk: nome e chiave dell'autorità emittente]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Come iniziare a usare l'SDK di Servizi BizTalk di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
