<properties 
	pageTitle="Uso di New Relic con Azure - Guide alle funzionalità di Azure" 
	description="Informazioni sull'uso del servizio New Relic per gestire e monitorare l'applicazione Azure." 
	services="" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>



#Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure

In questa guida viene illustrata l'aggiunta di New Relic per un monitoraggio di qualità superiore delle prestazioni delle applicazioni ospitate in Azure. Verrà illustrato un processo semplice e rapido per aggiungere New Relic all'applicazione e ne verranno introdotte alcune delle funzionalità. Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Utilizzo di New Relic](#using-new-relic).

Informazioni su New Relic --

New Relic è uno strumento destinato agli sviluppatori per il monitoraggio delle applicazioni di produzione, in grado di fornire informazioni molto approfondite su prestazioni e affidabilità. Lo strumento è progettato per consentire di risparmiare tempo quando si identificano o si diagnosticano problemi di prestazioni ed è in grado di mettere alla portata dell'utente tutte le informazioni necessarie per la risoluzione di tali problemi.

New Relic tiene traccia del tempo di caricamento e della velocità effettiva della transazione Web, sia dal server che dai browser degli utenti. Visualizza il tempo trascorso nel database, analizza le query e le richieste Web più lente, fornisce il monitoraggio del tempo di attività e l'invio di avvisi, tiene traccia delle eccezioni delle applicazioni e molto altro ancora.

New Relic a un prezzo speciale tramite Azure Store --

New Relic Standard è gratuito per gli utenti di Azure. New Relic Pro viene offerto in base alle dimensioni delle istanze per i servizi cloud di Azure

Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Store](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE]Vengono indicati i prezzi solo fino a 10 istanze di elaborazione. Per un numero di istanze superiore a 10, contattare New Relic (sales@newrelic.com) per informazioni sui prezzi per volume.

I clienti di Azure che distribuiscono l'agente New Relic hanno diritto a una sottoscrizione di valutazione di New Relic Pro della durata di 2 settimane.

Iscrizione a New Relic tramite Azure Store --

New Relic si integra facilmente con i ruoli Web e di lavoro di Azure.

Per iscriversi a New Relic direttamente da Azure Store, attenersi alla procedura seguente, che consiste in tre semplici passaggi.

### Passaggio 1. Effettuare l'iscrizione tramite Azure Store

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Nel riquadro inferiore del portale di gestione fare clic su **New**.
3. Fare clic su **Store**.
4. Nella finestra di dialogo **Choose an Add-on** selezionare **New Relic** e fare clic su **Next**.
5. Nella finestra di dialogo **Personalizza componente aggiuntivo** selezionare il piano New Relic a cui si desidera iscriversi.
6. Se disponibile, immettere un codice di promozione.
7. Immettere il nome con il quale si desidera che il servizio New Relic venga visualizzato nelle impostazioni di Azure oppure utilizzare il valore predefinito **NewRelic**. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.
8. Scegliere un valore per l'area, ad esempio **West US**.
9. Fare clic su **Next**.
10. Nella finestra di dialogo **Review Purchase** rivedere le informazioni sul piano e sui prezzi e consultare le note legali. Se si accettano le condizioni, fare clic su **Purchase**.
11. Dopo avere fatto clic su **Purchase**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.
12. Per recuperare il codice di licenza di New Relic, fare clic su **Output Values**. 
13. Copiare il codice di licenza visualizzato. Sarà necessario immetterlo durante l'installazione del pacchetto New Relic Nuget.

### Passaggio 2. Installare il pacchetto NuGet

1. Aprire la soluzione Visual Studio o crearne una nuova selezionando **File > Nuovo > Progetto**.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Se nella soluzione non esiste ancora un progetto di servizio cloud di Azure, aggiungerne uno facendo clic con il pulsante destro del mouse sull'applicazione in Esplora soluzioni, quindi selezionando **Aggiungi progetto di servizio cloud di Azure**.

	![Creazione servizio cloud](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Aprire la console di gestione pacchetti selezionando **Strumenti > Gestione pacchetti libreria > Console di Gestione pacchetti**. Impostare il progetto come progetto predefinito nella parte superiore della finestra della console di Gestione pacchetti.

	![Console di Gestione pacchetti](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. Al prompt dei comandi di Gestione pacchetti, digitare `Install-Package
   NewRelicWindowsAzure` e premere **INVIO**.

	![Installazione in Gestione pacchetti](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. Al prompt del codice di licenza, immettere il codice di licenza ottenuto da Azure Store.

	![Immettere il codice di licenza](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Facoltativo: al prompt del nome dell'applicazione, immettere il nome dell'app come verrà visualizzato nel dashboard di New Relic. In alternativa, è possibile utilizzare il nome della soluzione come nome predefinito.

	![Immettere il nome dell'applicazione](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio cloud di Azure, quindi selezionare **Pubblica**.

	![Pubblicare il progetto cloud](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Nota:** se si sta distribuendo l'app in Azure per la prima volta, verrà richiesta l'immissione delle credenziali di Azure. Per altre informazioni, vedere l'articolo relativo alla <a href="/develop/net/tutorials/get-started/">distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure</a>.

![Impostazioni di pubblicazione](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Passaggio 3. Verificare le prestazioni dell'applicazione in New Relic.

Per visualizzare il dashboard di New Relic:

1. Nel portale di Azure fare clic sul pulsante **Manage**.
2. Accedere con l'account di New Relic sotto forma di indirizzo di posta elettronica e password.
3. Nella barra dei menu di New Relic selezionare **Applications > (nome dell'applicazione)**.

	Verrà automaticamente visualizzato il dashboard **Monitoring > Overview**.

	![Dashboard di monitoraggio di New Relics](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Dopo avere selezionato un'applicazione dall'elenco nel menu **Applications**, nel dashboard **Overview** verranno visualizzate le informazioni relative al server applicazioni e al browser.

### <a id="using-new-relic"></a>Usare New Relic

Dopo avere selezionato l'applicazione dall'elenco nel menu Applications, nel dashboard Overview verranno visualizzate le informazioni relative al server applicazioni e al browser. Per passare da una visualizzazione all'altra, fare clic sul pulsante **App server** o **Browser**.

Oltre alle funzioni fornite dall'<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interfaccia utente standard di New Relic</a> e dal <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">drill-down dei dashboard</a>, nel dashboard Applications Overview sono disponibili funzioni aggiuntive.

<table border="1">
  <thead>
    <tr>
      <th><b>Per...</b></th>
      <th><b>Eseguire l'operazione seguente...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Visualizzare informazioni del dashboard per il server applicazioni o browser selezionato</td>
       <td>Fare clic sul pulsante <b>App Server</b> o <b>Browser</b>.</td>
    </tr>
     <tr>
       <td>Visualizzare i livelli di soglia per il punteggio <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> dell'applicazione</td>
       <td>Selezionare l'icona del punteggio Apdex <b>?<b>.</b></b></td>
    </tr>
    <tr>
       <td>Visualizzare dettagli Apdex a livello globale</td>
       <td>Nella visualizzazione <b>Browser</b> della schermata Overview selezionare un punto qualsiasi nella mappa Global Apdex.<br /><b>Suggerimento:</b> per passare direttamente al dashboard <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">Geography</a> per l'app selezionata, fare clic sul titolo <b>Global Apdex</b> oppure fare clic in un punto qualsiasi della mappa Global Apdex.</td>
    </tr>
    <tr>
       <td>Visualizzare il dashboard <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a></td>
       <td>Fare clic sulla tabella Web Transactions nel dashboard Applications Overview. Oppure per visualizzare dettagli relativi a una transazione Web specifica (comprese quelle del gruppo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) fare clic sul nome della transazione.</td>
    </tr>
    <tr>
       <td>Visualizzare il dashboard <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td>Fare clic sul titolo del grafico Error rate nel dashboard Applications Overview.<br /><b>Suggerimento:</b> è anche possibile visualizzare il dashboard Errors selezionando <b>Applications</b> > (applicazione) > Events > Errors.</td>
    </tr>
    <tr>
       <td>Visualizzare i dettagli relativi al server applicazioni</td>
       <td><p>Eseguire una o più delle operazioni seguenti:<p>
        <ul>
          <li>Passare da una visualizzazione in tabella degli host a una dei dettagli sulle metriche di suddivisione per ogni host.</li>
          <li>Fare clic sul nome di un server specifico.</li>
          <li>Selezionare il punteggio Apdex per un server specifico.</li>
          <li>Fare clic sull'utilizzo della CPU o della memoria di un server specifico.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Di seguito è riportato un esempio del dashboard Applications Overview quando è selezionata la visualizzazione Browser.

![Console di Gestione pacchetti](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Passaggi successivi

Per ulteriori informazioni, è possibile consultare le seguenti risorse aggiuntive:

 * [Installazione dell'agente .NET in Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): procedure di installazione dell'agente .NET di New Relic 
 * [Interfaccia utente di New Relic](https://newrelic.com/docs/site/the-new-relic-ui): panoramica dell'interfaccia utente di New Relic, impostazione dei diritti e dei profili utente, uso delle funzioni standard e dettagli sui drill-down dei dashboard
 * [Applications Overview](https://newrelic.com/docs/site/applications-overview): funzionalità disponibili nel dashboard Applications Overview di New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): panoramica delle misurazioni Apdex della soddisfazione degli utenti finali riguardo all'applicazione
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): presentazione del modo in cui la funzionalità RUM registra i dettagli relativi all'ubicazione degli utenti, al tempo impiegato dai browser per caricare le pagine Web e ai tipi di browser usati
 * [Supporto](https://newrelic.com/docs/site/finding-help): risorse disponibili tramite l'Help Center di New Relic

<!---HONumber=July15_HO1-->