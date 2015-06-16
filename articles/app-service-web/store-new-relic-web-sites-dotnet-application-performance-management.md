<properties 
	pageTitle="App Web .NET in Azure App Service con gestione delle prestazioni delle applicazioni New Relic" 
	description="Informazioni su come imparare a usare il monitoraggio delle prestazioni di New Relic per applicazioni ASP.NET in esecuzione su Azure App Service." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="stepsic"/>



# App Web .NET in Azure App Service con gestione delle prestazioni delle applicazioni New Relic

Questa guida spiega come aggiungere il monitoraggio delle prestazioni all'avanguardia di New Relic all'app Web in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Verrà illustrato un processo semplice e rapido per aggiungere New Relic all'applicazione e ne verranno introdotte alcune delle funzionalità. Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Utilizzo di New Relic](#using-new-relic).

## Informazioni su New Relic

New Relic è uno strumento destinato agli sviluppatori per il monitoraggio delle applicazioni di produzione, in grado di fornire informazioni molto approfondite su prestazioni e affidabilità. Lo strumento è progettato per consentire di risparmiare tempo quando si identificano o si diagnosticano problemi di prestazioni ed è in grado di mettere alla portata dell'utente tutte le informazioni necessarie per la risoluzione di tali problemi.

New Relic tiene traccia del tempo di caricamento e della velocità effettiva della transazione Web, sia dal server che dai browser degli utenti. Visualizza il tempo trascorso nel database, analizza le query e le richieste Web più lente, fornisce il monitoraggio del tempo di attività e l'invio di avvisi, tiene traccia delle eccezioni delle applicazioni e molto altro ancora.

## New Relic a un prezzo speciale tramite Azure Store

New Relic Standard è gratuito per gli utenti di Azure. New Relic Pro viene offerto in più pacchetti in base alla modalità del sito in uso e alle dimensioni delle istanze se si usa la modalità riservata.

Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Marketplace](/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE]Vengono indicati i prezzi solo fino a 10 istanze di elaborazione. Per un numero di istanze superiore a 10, contattare New Relic (sales@newrelic.com) per informazioni sui prezzi per volume.

I clienti di Azure che distribuiscono l'agente New Relic hanno diritto a una sottoscrizione di valutazione di New Relic Pro della durata di 2 settimane.

Iscriversi a New Relic usando Azure Marketplace

New Relic si integra facilmente con i ruoli Web, i ruoli di lavoro e Azure App Service di Azure.

Per iscriversi a New Relic direttamente da Azure Marketplace, seguire questa procedura in quattro semplici passaggi.

## Passaggio 1. Creare un account New Relic

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com) e fare clic su **Nuovo** nell'angolo in basso a sinistra.
3. Fare clic su **Servizi per gli sviluppatori** > **APM di New Relic**.
4. Configurare l'account New Relic specificando quanto segue, quindi fare clic su **Crea**.
	- **Nome**
	- **Livello di prezzo**
	- **Gruppo di risorse**
	- **Sottoscrizione**
	- **Località**
	- **Note legali**

11. Dopo aver fatto clic su **Crea**, verrà avviata la creazione dell'account New Relic. È possibile monitorare lo stato facendo clic sul pulsante **Notifiche**. Dopo che l'account New Relic è stato creato, verrà visualizzato il relativo pannello.

12. Per recuperare il codice di licenza di New Relic, vedere l'area **Essentials** nella parte superiore del pannello. L'istanza dell'app Web registrerà automaticamente la chiave di licenza nelle impostazioni dell'app quando si integra l'app Web con l'account New Relic.

## Passaggio 2: Configurare l'integrazione di New Relic per l'app Web

2. Aprire il pannello dell'app Web nel [portale di anteprima di Azure](https://portal.azure.com).
3. Fare clic su **Monitoraggio applicazioni ** > **New Relic**. Selezionare l'account creato nel passaggio precedente e fare clic su **OK**. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	Al completamento dell'operazione di salvataggio, fare clic su **Tutte le impostazioni** nel pannello dell'app Web e quindi fare clic su **Impostazioni applicazione**. L'impostazione **NEWRELIC_LICENSEKEY** verrà aggiunta alla sezione **Impostazioni applicazione** del pannello per supportare New Relic:

	>[AZURE.NOTE]L'applicazione della nuova impostazione per l'app può richiedere fino a 30 secondi. Per forzare l'applicazione immediata delle impostazioni, riavviare l'app Web.

## Passaggio 3: Pubblicare l'app Web ASP.NET

Pubblicare l'app Web con Visual Studio o WebMatrix. Se l'app Web è già stata pubblicata, pubblicarla nuovamente in modo che l'istanza dell'app Web aggiunga il pacchetto NuGet di New Relic necessario per abilitare il monitoraggio di New Relic.

## Passaggio 4. Verificare le prestazioni dell'applicazione in New Relic.

Per visualizzare il dashboard di New Relic:

2. Aprire il pannello dell'app Web nel [portale di anteprima di Azure](https://portal.azure.com).
3. Fare clic su **Monitoraggio applicazioni** > **nome applicazione** > **Visualizza in New Relic**.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. Se si usa l'account per la prima volta, configurare le informazioni sull'account.
3. Nella barra dei menu di New Relic selezionare **Applications > (nome dell'applicazione)**.

	Verrà automaticamente visualizzato il dashboard **Monitoring > Overview**.

	![Dashboard di monitoraggio di New Relics](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

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
       <td>Nella visualizzazione <b>Browser</b> della schermata Overview selezionare un punto qualsiasi nella mappa Global Apdex.<br /><b>Suggerimento:</b> per passare direttamente al dashboard <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> per l'app selezionata, fare clic sul titolo <b>Global Apdex</b> oppure fare clic in un punto qualsiasi della mappa Global Apdex.</td>
    </tr>
    <tr>
       <td>Visualizzare il dashboard <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
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

![Console di Gestione pacchetti](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## Passaggi successivi

Per ulteriori informazioni, è possibile consultare le seguenti risorse aggiuntive:

 * [Installazione dell'agente .NET in Azure](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual): procedure di installazione dell'agente .NET di New Relic 
 * [Interfaccia utente di New Relic](https://newrelic.com/docs/site/the-new-relic-ui): panoramica dell'interfaccia utente di New Relic, impostazione dei diritti e dei profili utente, uso delle funzioni standard e dettagli sui drill-down dei dashboard
 * [Applications Overview](https://newrelic.com/docs/site/applications-overview): funzionalità disponibili nel dashboard Applications Overview di New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): panoramica delle misurazioni Apdex della soddisfazione degli utenti finali riguardo all'applicazione
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): presentazione del modo in cui la funzionalità RUM registra i dettagli relativi all'ubicazione degli utenti, al tempo impiegato dai browser per caricare le pagine Web e ai tipi di browser usati
 * [Supporto](https://newrelic.com/docs/site/finding-help): risorse disponibili tramite l'Help Center di New Relic

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per informazioni su cosa è cambiato nel passaggio dal portale di Azure al portale di anteprima di Azure, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)


[webmatrixwebsite]: web-sites-dotnet-using-webmatrix.md
[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png

<!--HONumber=54--> 