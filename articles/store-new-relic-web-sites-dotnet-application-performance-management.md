<properties urlDisplayName="New Relic App Performance Management" pageTitle="Gestione delle prestazioni delle app con New Relic in Azure" metaKeywords="new relic Azure, performance azure" description="Informazioni sull'uso del monitoraggio delle prestazioni di New Relic in Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="carolz" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />



#Gestione delle prestazioni delle app con New Relic in Siti Web di Azure

Questa guida descrive come aggiungere New Relic al sito Web di Azure per un monitoraggio di qualità superiore. Verrà illustrato un processo semplice e rapido per aggiungere New Relic all'applicazione e ne verranno presentate alcune delle funzionalità. Per altre informazioni sull'uso di New Relic, vedere [Uso di New Relic](#using-new-relic).

Informazioni su New Relic
--

New Relic è uno strumento destinato agli sviluppatori per il monitoraggio delle applicazioni di produzione, in grado di fornire informazioni molto approfondite su prestazioni e affidabilità. Lo strumento è progettato per consentire di risparmiare tempo quando si identificano o si diagnosticano problemi di prestazioni ed è in grado di mettere alla portata dell'utente tutte le informazioni necessarie per la risoluzione di tali problemi.

New Relic tiene traccia del tempo di caricamento e della velocità effettiva della transazione Web, sia dal server che dai browser degli utenti. Visualizza il tempo trascorso nel database, analizza le query e le richieste Web più lente, fornisce il monitoraggio del tempo di attività e l'invio di avvisi, tiene traccia delle eccezioni delle applicazioni e molto altro ancora.

New Relic a un prezzo speciale tramite Azure Store
--

New Relic Standard è gratuito per gli utenti di Azure.
New Relic Pro viene offerto in più pacchetti in base alla modalità del sito in uso e alle dimensioni delle istanze se si usa la modalità riservata.

Per informazioni sui prezzi, vedere la pagina dedicata a [New Relic in Azure Store](http://www.windowsazure.com/it-it/gallery/store/new-relic/new-relic/).

<div class="dev-callout"> 
<strong>Nota:</strong>
<p>Vengono indicati i prezzi solo fino a 10 istanze di elaborazione. Per un numero di istanze superiore a 10, contattare New Relic (sales@newrelic.com) per informazioni sui prezzi per volume.</p>
</div>

I clienti di Azure che distribuiscono l'agente New Relic hanno diritto a una sottoscrizione di valutazione di New Relic Pro della durata di 2 settimane.

Iscrizione a New Relic usando Azure Store
--

New Relic si integra facilmente con i ruoli Web, i ruoli di lavoro e i siti Web di Azure.

Per iscriversi a New Relic direttamente da Azure Store, attenersi alla procedura seguente, che consiste in quattro semplici passaggi.

### Passaggio 1. Effettuare l'iscrizione tramite Azure Store

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Nel riquadro inferiore del portale di gestione fare clic su **Nuovo**.
3. Fare clic su **Store**.
4. Nella finestra di dialogo **Scegliere un componente aggiuntivo** selezionare **New Relic** e fare clic su **Avanti**.
5. Nella finestra di dialogo **Personalizza componente aggiuntivo** selezionare il piano New Relic a cui si desidera iscriversi.
7. Immettere il nome con il quale si desidera che il servizio New Relic venga visualizzato nelle impostazioni di Azure oppure usare il valore predefinito **NewRelic**. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.
8. Scegliere un valore per l'area, ad esempio **Stati Uniti occidentali**.
9. Fare clic su **Avanti**.
10. Nella finestra di dialogo **Rivedi acquisto** rivedere le informazioni sul piano e sui prezzi e consultare le note legali. Se si accettano le condizioni, fare clic su **Acquista**.
11. Dopo avere fatto clic su **Acquista**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.
12. Per recuperare il codice di licenza di New Relic fare clic sul componente aggiuntivo appena creato e quindi fare clic su **Informazioni di connessione**. 
13. Copiare il codice di licenza visualizzato. Sarà necessario immetterlo durante l'installazione del pacchetto New Relic Nuget.

### Passaggio 2. Installare il modulo New Relic.

L'agente per siti Web di New Relic viene distribuito come pacchetto NuGet, che può essere aggiunto al sito Web con Visual Studio o WebMatrix. Se non si ha familiarità con l'uso di Visual Studio o WebMatrix con un sito Web di Azure, vedere l'articolo relativo alla [distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure con Visual Studio][vswebsite] oppure l'articolo relativo allo [sviluppo e alla distribuzione di un sito Web con Microsoft WebMatrix][webmatrixwebsite].

Eseguire la procedura seguente per l'ambiente di sviluppo specifico in uso:

**Visual Studio**

1. Aprire il sito Web in Visual Studio.

2. Aprire la console di Gestione pacchetti selezionando **Strumenti > Gestione pacchetti libreria > Console di Gestione pacchetti**. Impostare il progetto come progetto predefinito nella parte superiore della finestra della console di Gestione pacchetti.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. Al prompt dei comandi di Gestione pacchetti usare il comando seguente per installare il pacchetto:

		Install-Package NewRelic.Azure.WebSites

4. Al prompt del codice di licenza, immettere il codice di licenza ottenuto da Azure Store.

	![enter license key][vslicensekey]

<!--5. Optional: At the application name prompt, enter your app's name as it will
   appear in New Relic's dashboard. Or, use your solution name as the default.

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Aprire il sito Web in WebMatrix.

2. Nella scheda **Home** della barra multifunzione selezionare **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. Nella raccolta NuGet impostare l'origine su **NuGet Official Package Source**, quindi eseguire la ricerca di NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Selezionare la voce **New Relic for Azure Websites**, quindi fare clic su **Install**.

5. Dopo aver installato il pacchetto il sito conterrà una cartella denominata **newrelic**. Espandere questa cartella e aprire il file **newrelic.config**. In questo file, sostituire il valore **REPLACE\_WITH\_LICENSE_KEY** con il codice di licenza ottenuto da Azure Store.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	Dopo aver immesso il codice di licenza salvare le modifiche nel file **newrelic.config**.

### Passaggio 3. Configurare il sito Web e pubblicare l'applicazione.

Il pacchetto New Relic aggiunto all'applicazione nel passaggio precedente viene configurato dalle impostazioni in **Impostazioni app** aggiunte al sito Web di Azure. Eseguire la procedura seguente per aggiungere queste impostazioni.

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) e passare al proprio sito Web.

2. Dal sito Web selezionare **Configura**. Nella sezione **Developer Analytics** selezionare **Componente aggiuntivo** o **Personalizzato**. Ciascuno dei due metodi produce lo stesso output, ma richiede un input leggermente diverso. **Il metodo Personalizzato** consente di elencare le attuali licenze New-Relic e di selezionarne una, mentre il metodo **Personalizzato** richiede che il codice di licenza sia specificato manualmente.

	Se si è selezionato **Componente aggiuntivo**, usare il campo **Scegli componente aggiuntivo** per selezionare la licenza New-Relic.

	![Image of the add-on fields][add-on]

	Se si è selezionato **Personalizzato**, selezionare New-Relic come **Provider** e quindi immettere il codice di licenza nel campo **Chiave provider**.

	![Image of the custom fields][custom]

3. Dopo aver specificato la licenza in **Developer Analytics**, fare clic su **Salva**. Al termine dell'operazione di salvataggio, alla sezione **Impostazioni app** della pagina saranno stati aggiunti i valori seguenti per supportare New-Relic:

	<table border="1">
	<thead>
	<tr>
	<td>Chiave</td>
	<td>Valore</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>Il codice di licenza dell'utente</td>
	</tr>
	</tbody>
	</table><br/>

	<div class="dev-callout"> 
	<strong>Nota</strong> 
	<p>L'applicazione delle nuove impostazioni in <strong>Impostazioni app</strong> può richiedere fino a 30 secondi. Per forzare l'applicazione immediata delle impostazioni, riavviare il sito Web.</p> 
	</div>


4. Pubblicare l'applicazione con Visual Studio o WebMatrix.

### Passaggio 4. Verificare le prestazioni dell'applicazione in New Relic.

Per visualizzare il dashboard di New Relic:

1. Nel portale di Azure fare clic sul pulsante **Gestione**.
2. Accedere con l'account di New Relic sotto forma di indirizzo di posta elettronica e password.
3. Nella barra dei menu di New Relic selezionare **Applications > (nome dell'applicazione)**.

	Verrà automaticamente visualizzato il dashboard **Monitoring > Overview**.

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	Dopo avere selezionato un'app dall'elenco nel menu **Applications**, nel dashboard **Overview** verranno visualizzate le informazioni relative al server applicazioni e al browser.

### <a id="using-new-relic"></a>Usare New Relic

Dopo avere selezionato l'applicazione dall'elenco nel menu Applications, nel dashboard Overview verranno visualizzate le informazioni relative al server applicazioni e al browser. Per passare da una visualizzazione all'altra, fare clic sul pulsante **App server** o **Browser**.

Oltre alle funzioni fornite dall'<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">interfaccia utente standard di New Relic</a> e dal <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">drill-down dei dashboard</a>, nel dashboard Applications Overview sono disponibili funzioni aggiuntive.

<table border="1">
  <thead>
    <tr>
      <th><b>Per...</b></th>
      <th><b>Effettuare l'operazione seguente:</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Visualizzare informazioni del dashboard per il server applicazioni o il browser selezionato</td>
       <td>Fare clic sul pulsante <b>App Server</b> o <b>Browser</b>.</td>
    </tr>
     <tr>
       <td>Visualizzare i livelli di soglia per il punteggio <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> dell'app.</td>
       <td>Selezionare l'icona <b>?<b> del punteggio Apdex.</b></b></td>
    </tr>
    <tr>
       <td>Visualizzare dettagli Apdex a livello globale</td>
       <td>Nella visualizzazione <b>Browser</b> della schermata Overview, selezionare un punto qualsiasi nella mappa Global Apdex.<br /><b>Suggerimento:</b> per passare direttamente al dashboard <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> per l'app selezionata, fare clic sul titolo <b>Global Apdex</b> oppure fare clic in un punto qualsiasi della mappa Global Apdex.</td>
    </tr>
    <tr>
       <td>Visualizzare il dashboard <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Fare clic sulla tabella Web Transactions nel dashboard Applications Overview. Oppure per visualizzare dettagli relativi a una transazione Web specifica (comprese quelle del gruppo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) fare clic sul nome della transazione.</td>
    </tr>
    <tr>
       <td>Visualizzare il dashboard <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td>Fare clic sul titolo del grafico Error rate nel dashboard Applications Overview.<br /><b>Suggerimento:</b> è possibile visualizzare il dashboard Errors anche selezionando <b>Applications</b> &gt; (app) &gt; Events &gt; Errors.</td>
    </tr>
    <tr>
       <td>Visualizzare i dettagli relativi al server applicazioni</td>
       <td><p>Eseguire una o più delle operazioni seguenti:<p>
        <ul>
          <li>Passare da una visualizzazione in tabella degli host a una dei dettagli sulle metriche di suddivisione per ogni host.</li>
          <li>Fare clic sul nome di un server specifico.</li>
          <li>Selezionare il punteggio Apdex per un server specifico.</li>
          <li>Fare clic sull'uso della CPU o della memoria di un server specifico.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Di seguito è riportato un esempio del dashboard Applications Overview quando è selezionata la visualizzazione Browser.

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Passaggi successivi

Per altre informazioni, è possibile consultare le seguenti risorse aggiuntive:

 * [Installazione dell'agente .NET per Siti Web di Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): procedure di installazione dell'agente .NET di New Relic 
 * [Interfaccia utente di New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
panoramica dell'interfaccia utente di New Relic, impostazione dei diritti e dei profili utente, uso delle funzioni standard e dettagli sui drill-down dei dashboard
 * [Applications Overview](https://newrelic.com/docs/site/applications-overview): funzionalità disponibili nel dashboard Applications Overview di New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): panoramica delle misurazioni Apdex della soddisfazione degli utenti finali riguardo all'applicazione
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): presentazione del modo in cui la funzionalità RUM registra i dettagli 
relativi all'ubicazione degli utenti, al tempo impiegato dai browser per caricare le pagine Web e ai tipi di browser usati
 * [Supporto](https://newrelic.com/docs/site/finding-help): risorse disponibili tramite l'Help Center di New Relic


[webmatrixwebsite]: http://www.windowsazure.com/it-it/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png

<!--HONumber=35.1-->
