<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="New Relic" pageTitle="Using New Relic with Azure - Azure feature guides" metaKeywords="" description="Learn how to use the New Relic service to manage and monitor your Azure application." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure

Questa guida descrive come aggiungere New Relic per un monitoraggio
di qualità superiore delle prestazioni delle applicazioni ospitate in Azure. Verrà illustrato un processo semplice
e rapido per aggiungere New Relic all'applicazione e ne
verranno presentate alcune funzionalità. Per ulteriori informazioni sull'utilizzo di New Relic, vedere [Utilizzo di New Relic][Utilizzo di New Relic].

## Informazioni su New Relic

New Relic è uno strumento destinato agli sviluppatori per il monitoraggio delle applicazioni
di produzione, in grado di fornire informazioni molto approfondite su prestazioni e affidabilità. Lo strumento
è progettato per consentire di risparmiare tempo quando si identificano o si diagnosticano problemi di prestazioni
ed è in grado di mettere alla portata dell'utente tutte le informazioni necessarie per la risoluzione di tali problemi.

New Relic tiene traccia del tempo di caricamento e della velocità effettiva della transazione Web,
sia dal server che dai browser degli utenti. Visualizza il tempo trascorso nel database,
analizza le query e le richieste Web più lente, fornisce il monitoraggio del tempo di attività
e l'invio di avvisi, tiene traccia delle eccezioni delle applicazioni e molto altro ancora.

## New Relic a un prezzo speciale tramite Azure Store

New Relic Standard è gratuito per gli utenti di Azure
New Relic Pro viene offerto in base alle dimensioni delle istanze per i servizi cloud di Azure

Per informazioni sui prezzi, vedere la [pagina dedicata a New Relic in Azure Store][pagina dedicata a New Relic in Azure Store].

<div class="dev-callout"> 
<strong>Nota:</strong>
<p>Vengono indicati i prezzi solo fino a 10 istanze di elaborazione. Per un numero di istanze superiore a 10, contattare New Relic (sales@newrelic.com) per informazioni sui prezzi per volume.</p>
</div>

I clienti di Azure che distribuiscono l'agente New Relic hanno diritto a una sottoscrizione di valutazione di New Relic Pro della durata di 2 settimane.

## Iscrizione a New Relic utilizzando Azure Store

New Relic si integra facilmente con i ruoli Web e di lavoro di Azure.

Per iscriversi a New Relic direttamente da Azure Store, attenersi alla procedura seguente, che consiste in tre semplici passaggi.

### Passaggio 1. Effettuare l'iscrizione tramite Azure Store

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nel riquadro inferiore del portale di gestione fare clic su **New**.
3.  Fare clic su **Store**.
4.  Nella finestra di dialogo **Choose an Add-on** selezionare **New Relic** e fare clic su **Next**.
5.  Nella finestra di dialogo **Personalize Add-on** selezionare il piano New Relic a cui si desidera iscriversi.
6.  Se disponibile, immettere un codice di promozione.
7.  Immettere il nome con il quale si desidera che il servizio New Relic venga
    visualizzato nelle impostazioni di Azure oppure usare il valore predefinito **NewRelic**. Il nome deve essere univoco
    nell'elenco degli elementi di Azure Store sottoscritti.
8.  Scegliere un valore per l'area, ad esempio **West US**.
9.  Fare clic su **Avanti**.
10. Nella finestra di dialogo **Rivedi acquisto** rivedere le informazioni sul piano e sui prezzi
    e consultare le note legali. Se si accettano le condizioni, fare clic su **Purchase**.
11. Dopo avere fatto clic su **Purchase**, verrà avviata la creazione del nuovo account New Relic. È possibile monitorare lo stato nel portale di gestione di Azure.
12. Per recuperare il codice di licenza di New Relic, fare clic su **Output Values**.
13. Copiare il codice di licenza visualizzato. Sarà necessario immetterlo durante l'installazione del pacchetto New Relic Nuget.

### Passaggio 2. Installare il pacchetto Nuget

1.  Aprire la soluzione Visual Studio o crearne una nuova selezionando
     **File \> Nuovo \> Progetto**.

    ![Visual Studio][Visual Studio]

2.  Se nella soluzione non esiste ancora un progetto di servizio cloud di Azure,
    aggiungerne uno facendo clic con il pulsante destro del mouse sull'applicazione in Esplora soluzioni,
    quindi selezionando selecting **Add Azure Cloud Service Project**.

    ![Creazione servizio cloud][Creazione servizio cloud]

3.  Aprire la console di Gestione pacchetti selezionando **Strumenti \> Gestione pacchetti libreria \>
     Console di Gestione pacchetti**. Impostare il progetto come predefinito nella parte
    superiore della finestra della console di Gestione pacchetti.

    ![Console di Gestione pacchetti][Console di Gestione pacchetti]

4.  Al prompt dei comandi di Gestione pacchetti, digitare `Install-Package    NewRelicWindowsAzure` e premere **INVIO**.

    ![Installazione in Gestione pacchetti][Installazione in Gestione pacchetti]

5.  Al prompt del codice di licenza, immettere il codice di licenza ottenuto da Azure Store.

    ![Immettere il codice di licenza][Immettere il codice di licenza]

6.  Facoltativo: al prompt del nome dell'applicazione, immettere il nome dell'applicazione
    come verrà visualizzato nel dashboard di New Relic. In alternativa, è possibile utilizzare il nome della soluzione come nome predefinito.

    ![Immettere il nome dell'applicazione][Immettere il nome dell'applicazione]

7.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio cloud di Azure, quindi selezionare **Pubblica**.

    ![Pubblicare il progetto cloud][Pubblicare il progetto cloud]

**Nota:** se si sta distribuendo l'applicazione in Azure per la prima volta, verrà richiesta l'immissione
delle credenziali di Azure. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure][distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure].

![Impostazioni di pubblicazione][Impostazioni di pubblicazione]

### Passaggio 3. Verificare le prestazioni dell'applicazione in New Relic.

Per visualizzare il dashboard di New Relic:

1.  Nel portale di Azure fare clic sul pulsante **Manage**.
2.  Accedere con l'account di New Relic sotto forma di indirizzo di posta elettronica e password.
3.  Nella barra dei menu di New Relic selezionare **Applications \> (nome dell'applicazione)**.

    Verrà automaticamente visualizzato il dashboard **Monitoring \> Overview**.

    ![Dashboard di monitoraggio di New Relics][Dashboard di monitoraggio di New Relics]

    Dopo avere selezionato un'applicazione dall'elenco nel menu **Applications**, nel dashboard **Overview** verranno visualizzate le informazioni relative al server applicazioni e al browser.

### <span id="using-new-relic"></span></a>Utilizzare New Relic

Dopo avere selezionato l'applicazione dall'elenco nel menu Applications, nel dashboard Overview verranno visualizzate le informazioni relative al server applicazioni e al browser. Per passare da una visualizzazione all'altra, fare clic sul pulsante **App server** o **Browser**.

Oltre alle funzioni fornite dall'[interfaccia utente standard di New Relic][interfaccia utente standard di New Relic] e dal [drill-down dei dashboard][drill-down dei dashboard], nel dashboard Applications Overview sono disponibili funzioni aggiuntive.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Per...</strong></th>
<th align="left"><strong>Effettuare l'operazione seguente:</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Visualizzare informazioni del dashboard per il server applicazioni o browser selezionato</td>
<td align="left">Fare clic sul pulsante <strong>App Server</strong> o <strong>Browser</strong>.</td>
</tr>
<tr class="even">
<td align="left">Visualizzare i livelli di soglia per il punteggio <a href="https://newrelic.com/docs/site/apdex">Apdex</a> dell'applicazione</td>
<td align="left">Selezionare l'icona del punteggio Apdex <strong>?<strong>.</strong></strong></td>
</tr>
<tr class="odd">
<td align="left">Visualizzare dettagli Apdex a livello globale</td>
<td align="left">Nella visualizzazione <strong>Browser</strong> della schermata Overview, selezionare un punto qualsiasi nella mappa Global Apdex.<br /><strong>Suggerimento:</strong> per passare direttamente al dashboard <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard">Geography</a> per l'applicazione selezionata, fare clic sul titolo <strong>Global Apdex</strong> oppure fare clic in un punto qualsiasi della mappa Global Apdex.</td>
</tr>
<tr class="even">
<td align="left">Visualizzare il dashboard <a href="https://newrelic.com/docs/applications-dashboards/web-transactions">Web Transactions</a></td>
<td align="left">Fare clic sulla tabella Web Transactions nel dashboard Applications Overview. Oppure per visualizzare dettagli relativi a una transazione Web specifica (comprese quelle del gruppo <a href="https://newrelic.com/docs/site/key-transactions">Key Transactions</a>) fare clic sul nome della transazione.</td>
</tr>
<tr class="odd">
<td align="left">Visualizzare il dashboard <a href="https://newrelic.com/docs/site/errors">Errors</a></td>
<td align="left">Fare clic sul titolo del grafico Error rate nel dashboard Applications Overview.<br /><strong>Suggerimento:</strong> è possibile visualizzare il dashboard Errors anche selezionando <strong>Applications</strong> &gt; (applicazione) &gt; Events &gt; Errors.</td>
</tr>
<tr class="even">
<td align="left">Visualizzare i dettagli relativi al server applicazioni</td>
<td align="left"><p>Eseguire una o più delle operazioni seguenti:</p>
<p></p>
<ul>
<li>Passare da una visualizzazione in tabella degli host a una dei dettagli sulle metriche di suddivisione per ogni host.</li>
<li>Fare clic sul nome di un server specifico.</li>
<li>Selezionare il punteggio Apdex per un server specifico.</li>
<li>Fare clic sull'utilizzo della CPU o della memoria di un server specifico.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

Di seguito è riportato un esempio del dashboard Applications Overview quando è selezionata la visualizzazione Browser.

![Console di Gestione pacchetti][1]

## Passaggi successivi

Per ulteriori informazioni, è possibile consultare le seguenti risorse aggiuntive:

-   [Installazione dell'agente .NET in Azure][Installazione dell'agente .NET in Azure]: procedure di installazione dell'agente .NET di New Relic
-   [Interfaccia utente di New Relic][Interfaccia utente di New Relic]:
    panoramica dell'interfaccia utente di New Relic, impostazione dei diritti e dei profili utente, uso delle funzioni standard e dettagli sui drill-down dei dashboard
-   [Applications Overview][Applications Overview]: funzionalità disponibili nel dashboard Applications Overview di New Relic
-   [Apdex][Apdex]: panoramica delle misurazioni Apdex della soddisfazione degli utenti finali riguardo all'applicazione
-   [Real User Monitoring][Real User Monitoring]: presentazione del modo in cui la funzionalità RUM registra i dettagli
    relativi all'ubicazione degli utenti, al tempo impiegato dai browser per caricare le pagine Web e ai tipi di browser usati
-   [Supporto][Supporto]: risorse disponibili tramite l'Help Center di New Relic

  [Utilizzo di New Relic]: #using-new-relic
  [pagina dedicata a New Relic in Azure Store]: http://www.windowsazure.com/it-it/gallery/store/new-relic/new-relic/
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Visual Studio]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png
  [Creazione servizio cloud]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png
  [Console di Gestione pacchetti]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [Installazione in Gestione pacchetti]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png
  [Immettere il codice di licenza]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png
  [Immettere il nome dell'applicazione]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png
  [Pubblicare il progetto cloud]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png
  [distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure]: /it-it/develop/net/tutorials/get-started/
  [Impostazioni di pubblicazione]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png
  [Dashboard di monitoraggio di New Relics]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png
  [interfaccia utente standard di New Relic]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [drill-down dei dashboard]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [Geography]: https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard
  [Web Transactions]: https://newrelic.com/docs/applications-dashboards/web-transactions
  [Key Transactions]: https://newrelic.com/docs/site/key-transactions
  [Errors]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png
  [Installazione dell'agente .NET in Azure]: https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure
  [Interfaccia utente di New Relic]: https://newrelic.com/docs/site/the-new-relic-ui
  [Applications Overview]: https://newrelic.com/docs/site/applications-overview
  [Real User Monitoring]: https://newrelic.com/docs/features/real-user-monitoring
  [Supporto]: https://newrelic.com/docs/site/finding-help
