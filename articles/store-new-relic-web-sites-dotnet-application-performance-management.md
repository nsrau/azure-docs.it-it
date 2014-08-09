<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Web Sites" authors="larryfr" solutions="" manager="" editor="" />

Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure
================================================================================

In questa guida viene illustrata l'aggiunta di New Relic per un monitoraggio di qualità superiore nel sito Web di Azure. Verrà illustrato un processo semplice e rapido per aggiungere New Relic all'applicazione e ne verranno introdotte alcune delle funzionalità. Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Utilizzo di New Relic](#using-new-relic).

Informazioni su New Relic
-------------------------

New Relic è uno strumento destinato agli sviluppatori per il monitoraggio delle applicazioni di produzione, in grado di fornire informazioni molto approfondite su prestazioni e affidabilità. Lo strumento è progettato per consentire di risparmiare tempo quando si identificano o si diagnosticano problemi di prestazioni ed è in grado di mettere alla portata dell'utente tutte le informazioni necessarie per la risoluzione di tali problemi.

New Relic tiene traccia del tempo di caricamento e della velocità effettiva della transazione Web, sia dal server che dai browser degli utenti. Visualizza il tempo trascorso nel database, analizza le query e le richieste Web più lente, fornisce il monitoraggio del tempo di attività e l'invio di avvisi, tiene traccia delle eccezioni delle applicazioni e molto altro ancora.

New Relic a un prezzo speciale tramite Azure Store
--------------------------------------------------

New Relic Standard è gratuito per gli utenti di Azure. New Relic Pro viene offerto in più pacchetti in base alla modalità del sito in uso e alle dimensioni delle istanze se si utilizza la modalità riservata.

Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Store](http://www.windowsazure.com/it-it/gallery/store/new-relic/new-relic/).

**Nota:**

Vengono indicati i prezzi solo fino a 10 istanze di elaborazione. Per un numero di istanze superiore a 10, contattare New Relic (sales@newrelic.com) per informazioni sui prezzi per volume.

I clienti di Azure che distribuiscono l'agente New Relic hanno diritto a una sottoscrizione di valutazione di New Relic Pro della durata di 2 settimane.

Iscrizione a New Relic utilizzando Azure Store
----------------------------------------------

New Relic si integra facilmente con i ruoli Web, i ruoli di lavoro e i siti Web di Azure.

Per iscriversi a New Relic direttamente da Azure Store, attenersi alla procedura seguente, che consiste in quattro semplici passaggi.

### Passaggio 1. Effettuare l'iscrizione tramite Azure Store

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2.  Nel riquadro inferiore del portale di gestione fare clic su **New**.
3.  Fare clic su **Store**.
4.  Nella finestra di dialogo **Choose an Add-on** selezionare **New Relic** e fare clic su **Next**.
5.  Nella finestra di dialogo **Personalize Add-on** selezionare il piano New Relic a cui si desidera iscriversi.
6.  Immettere il nome con il quale si desidera che il servizio New Relic venga visualizzato nelle impostazioni di Azure oppure utilizzare il valore predefinito **NewRelic**. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.
7.  Scegliere un valore per l'area, ad esempio **West US**.
8.  Fare clic su **Next**.
9.  Nella finestra di dialogo **Review Purchase** rivedere le informazioni sul piano e sui prezzi e consultare le note legali. Se si accettano le condizioni, fare clic su **Purchase**.
10. Dopo avere fatto clic su **Purchase**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.
11. Per recuperare il codice di licenza di New Relic fare clic sul componente aggiuntivo appena creato e quindi fare clic su **Connection Info**.
12. Copiare il codice di licenza visualizzato. Sarà necessario immetterlo durante l'installazione del pacchetto New Relic Nuget.

### Passaggio 2. Installazione del pacchetto New Relic

L'agente per siti Web di New Relic viene distribuito come pacchetto NuGet, che potrà essere aggiunto al sito Web con Visual Studio o WebMatrix. Se non si ha familiarità con l'utilizzo di Visual Studio o WebMatrix con un sito Web di Azure, vedere [Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure con Visual Studio](http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/) oppure [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](http://www.windowsazure.com/it-it/develop/net/tutorials/website-with-webmatrix/).

Eseguire la procedura seguente per l'ambiente di sviluppo specifico in uso:

**Visual Studio**

1.  Aprire il sito Web in Visual Studio.

2.  Aprire la console di gestione pacchetti selezionando **Strumenti \> Gestione pacchetti libreria \> Console di Gestione pacchetti**. Impostare il progetto come progetto predefinito nella parte superiore della finestra della console di Gestione pacchetti.

    ![Console di Gestione pacchetti](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3.  Al prompt dei comandi di Gestione pacchetti utilizzare il comando seguente per installare il pacchetto:

         Install-Package NewRelic.Azure.WebSites

4.  Al prompt del codice di licenza, immettere il codice di licenza ottenuto da Azure Store.

    ![Immettere il codice di licenza](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png)

**WebMatrix**

1.  Aprire il sito Web in WebMatrix.

2.  Nella scheda **Home** della barra multifunzione selezionare **NuGet**.

    ![pulsante nuget su scheda home](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png)

3.  Nella raccolta NuGet impostare l'origine su **NuGet Official Package Source** quindi eseguire la ricerca di NewRelic.Azure.WebSites.

    ![nuget gallery searching for NewRelic.Azure.WebSites](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png)

4.  Selezionare la voce **New Relic for Azure Web Sites** e quindi fare clic su **Install**.

5.  Dopo aver installato il pacchetto il sito conterrà una cartella denominata **newrelic**. Espandere questa cartella e aprire il file **newrelic.config**. In questo file, sostituire il valore **REPLACE\_WITH\_LICENSE\_KEY** con il codice di licenza ottenuto da Azure Store.

    ![cartella newrelic espansa con newrelic.conf selezionato](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png)

    Dopo aver immesso il codice di licenza salvare le modifiche nel file **newrelic.config**.

### Passaggio 3. Configurazione del sito Web e pubblicazione dell'applicazione.

Il pacchetto New Relic aggiunto all'applicazione nel passaggio precedente viene configurato dalle impostazioni in **App Settings** aggiunte al sito Web di Azure. Eseguire la procedura seguente per aggiungere queste impostazioni.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) e passare al proprio sito Web.

2.  Dal sito Web selezionare **Configure**. Nella sezione **Developer Analytics** selezionare **Add-on** o **Custom**. Ciascuno dei due metodi produce lo stesso output, ma richiede un input leggermente diverso. Il metodo **Add-on** consente di elencare le attuali licenze New-Relic e di selezionarne una, mentre il metodo **Custom** richiede che il codice di licenza sia specificato manualmente.

    Se si è selezionato **Add-on**, utilizzare il campo **choose add-on** per selezionare la licenza New-Relic.

    ![Immagine dei campi add-on](./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png)

    Se si è selezionato **Custom**, selezionare New-Relic come **Provider** e quindi immettere il codice di licenza nel campo **Provider Key**.

    ![Immagine dei campi custom](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png)

3.  Dopo aver specificato la licenza in **Developer Analytics** fare clic su **Save**. Al termine dell'operazione di salvataggio, alla sezione **App Settings** della pagina saranno stati aggiunti i valori seguenti per supportare New-Relic:

<table data-morhtml="true" border="1">
 <thead data-morhtml="true">
 <tr data-morhtml="true">
 <td data-morhtml="true">Chiave</td>
 <td data-morhtml="true">Valore</td>
 </tr>
 </thead>
 <tbody data-morhtml="true">
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_ENABLE\_PROFILING</td><td data-morhtml="true">1</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_PROFILER</td><td data-morhtml="true">{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">COR\_PROFILER\_PATH</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">NEWRELIC\_HOME</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic</td>
 </tr>
 <tr data-morhtml="true">
 <td data-morhtml="true">NEWRELIC\_LICENSEKEY</td><td data-morhtml="true">Il codice di licenza dell'utente</td>
 </tr>
 </tbody>
 </table>


   **Nota**

   L'applicazione delle nuove impostazioni in **App Settings** può richiedere fino a 30 secondi. Per forzare l'applicazione immediata delle impostazioni riavviare il sito Web.

4.  Pubblicare l'applicazione con Visual Studio o WebMatrix.

### Passaggio 4. Verificare le prestazioni dell'applicazione in New Relic.

Per visualizzare il dashboard di New Relic:

1.  Nel portale di Azure fare clic sul pulsante **Manage**.
2.  Accedere con l'account di New Relic sotto forma di indirizzo di posta elettronica e password.
3.  Nella barra dei menu di New Relic selezionare **Applications \> (nome dell'applicazione)**.

    Verrà automaticamente visualizzato il dashboard **Monitoring \> Overview**.

    ![Dashboard di monitoraggio di New Relics](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

    Dopo avere selezionato un'applicazione dall'elenco nel menu **Applications**, nel dashboard **Overview** verranno visualizzate le informazioni relative al server applicazioni e al browser.

### Utilizzare New Relic

Dopo avere selezionato l'applicazione dall'elenco nel menu Applications, nel dashboard Overview verranno visualizzate le informazioni relative al server applicazioni e al browser. Per passare da una visualizzazione all'altra, fare clic sul pulsante **App server** o **Browser**.

Oltre alle funzioni fornite dall'[interfaccia utente standard di New Relic](https://newrelic.com/docs/site/the-new-relic-ui#functions) e dal [drill-down dei dashboard](https://newrelic.com/docs/site/the-new-relic-ui#drilldown), nel dashboard Applications Overview sono disponibili funzioni aggiuntive.

<table data-morhtml="true" border="1">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
      <th data-morhtml="true"><b data-morhtml="true">Per...</b></th>
      <th data-morhtml="true"><b data-morhtml="true">Effettuare l'operazione seguente:</b></th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare informazioni del dashboard per il server applicazioni o browser selezionato</td>
       <td data-morhtml="true">Fare clic sul pulsante <b data-morhtml="true">App Server</b> o <b data-morhtml="true">Browser</b>.</td>
    </tr>
     <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare i livelli di soglia per il punteggio <a data-morhtml="true" href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> dell'applicazione</td>
       <td data-morhtml="true">Selezionare l'icona del punteggio Apdex <b data-morhtml="true">?<b data-morhtml="true">.</b></b></td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare dettagli Apdex a livello globale</td>
       <td data-morhtml="true">Nella visualizzazione <b data-morhtml="true">Browser</b> della schermata Overview, selezionare un punto qualsiasi nella mappa Global Apdex.<br data-morhtml="true" /><b data-morhtml="true">Suggerimento:</b> per passare direttamente al dashboard <a data-morhtml="true" href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> per l'applicazione selezionata, fare clic sul titolo <b data-morhtml="true">Global Apdex</b> oppure fare clic in un punto qualsiasi della mappa Global Apdex.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare il dashboard <a data-morhtml="true" href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a></td>
       <td data-morhtml="true">Fare clic sulla tabella Web Transactions nel dashboard Applications Overview. Oppure per visualizzare dettagli relativi a una transazione Web specifica (comprese quelle del gruppo <a data-morhtml="true" href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>) fare clic sul nome della transazione.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare il dashboard <a data-morhtml="true" href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td data-morhtml="true">Fare clic sul titolo del grafico Error rate nel dashboard Applications Overview.<br data-morhtml="true" /><b data-morhtml="true">Suggerimento:</b> &egrave; possibile visualizzare il dashboard Errors anche selezionando <b data-morhtml="true">Applications</b> &gt; (applicazione) &gt; Events &gt; Errors.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Visualizzare i dettagli relativi al server applicazioni</td>
       <td data-morhtml="true"><p data-morhtml="true">Eseguire una o pi&ugrave; delle operazioni seguenti:<p data-morhtml="true">
        <ul data-morhtml="true">
          <li data-morhtml="true">Passare da una visualizzazione in tabella degli host a una dei dettagli sulle metriche di suddivisione per ogni host.</li>
          <li data-morhtml="true">Fare clic sul nome di un server specifico.</li>
          <li data-morhtml="true">Selezionare il punteggio Apdex per un server specifico.</li>
          <li data-morhtml="true">Fare clic sull'utilizzo della CPU o della memoria di un server specifico.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>


Di seguito è riportato un esempio del dashboard Applications Overview quando è selezionata la visualizzazione Browser.

![Console di Gestione pacchetti](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

Passaggi successivi
-------------------

Per ulteriori informazioni, è possibile consultare le seguenti risorse aggiuntive:

-   [Installazione dell'agente .NET in Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): procedure di installazione dell'agente .NET di New Relic
-   [Interfaccia utente di New Relic](https://newrelic.com/docs/site/the-new-relic-ui): panoramica dell'interfaccia utente di New Relic, impostazione dei diritti e dei profili utente, utilizzo delle funzioni standard e dettagli sui drill-down dei dashboard
-   [Applications Overview](https://newrelic.com/docs/site/applications-overview): funzionalità disponibili nel dashboard Applications Overview di New Relic
-   [Apdex](https://newrelic.com/docs/site/apdex): panoramica delle misurazioni Apdex della soddisfazione degli utenti finali riguardo all'applicazione
-   [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): panoramica delle modalità con cui la funzionalità RUM registra i dettagli relativi all'ubicazione degli utenti, il tempo impiegato dai browser per caricare le pagine Web e i tipi di browser utilizzati
-   [Supporto](https://newrelic.com/docs/site/finding-help): risorse disponibili tramite l'Help Center di New Relic

