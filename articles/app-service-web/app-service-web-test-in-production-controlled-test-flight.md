<properties
	pageTitle="Distribuzione di versioni di anteprima (test beta) nel servizio app di Azure"
	description="Apprendere come distribuire nuove funzionalità nell'app o eseguire test beta degli aggiornamenti in questa esercitazione end-to-end. Questa soluzione combina le funzionalità del servizio app, come pubblicazione continua, slot, routing del traffico e l'integrazione di Application Insights."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cephalin"/>
# Distribuzione di versioni di anteprima (test beta) nel servizio app di Azure

Questa esercitazione illustra come eseguire *distribuzioni di versioni di anteprima* integrando le diverse funzionalità del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e di [Azure Application Insights](/services/application-insights/).

La *distribuzione di versioni di anteprima* è un processo che convalida una nuova funzionalità o una modifica con un numero limitato di clienti reali ed è un test importante in uno scenario di produzione. È simile al test di versioni beta e viene talvolta definita come "anteprima di test controllato". Molte aziende di grandi dimensioni con una presenza Web usano questo approccio per ottenere la convalida preventiva degli aggiornamenti di app nella proprià attività di [sviluppo Agile](https://en.wikipedia.org/wiki/Agile_software_development). Il servizio app di Azure consente di integrare il test nell'ambiente di produzione con la pubblicazione continua e Application Insights per implementare lo stesso scenario DevOps. I vantaggi di questo approccio includono:

- **Ottenere commenti e suggerimenti reali _prima_ del rilascio di aggiornamenti in produzione**: avere commenti e suggerimenti prima di rilasciare un aggiornamento è un vantaggio unico rispetto a riceverli dopo averlo rilasciato. È possibile testare gli aggiornamenti con traffico utente e comportamenti reali con la tempestività desiderata nel ciclo di vita del prodotto.
- **Migliorare lo [sviluppo continuo basato su test](https://en.wikipedia.org/wiki/Continuous_test-driven_development)** (CTDD, Continuous test-driven development): grazie all'integrazione di test nell'ambiente di produzione con l'integrazione continua e la strumentazione con Application Insights, la convalida dell'utente avviene tempestivamente e automaticamente nel ciclo di vita del prodotto. Questo approccio consente di ridurre gli investimenti in termini di tempo per l'esecuzione di test manuali.
- **Ottimizzare il flusso di lavoro di test**: automatizzando i test nell'ambiente di produzione con la strumentazione di monitoraggio continuo, è potenzialmente possibile raggiungere gli obiettivi dei vari tipi di test in un singolo processo, ad esempio [integrazione](https://en.wikipedia.org/wiki/Integration_testing), [regressione](https://en.wikipedia.org/wiki/Regression_testing), [usabilità](https://en.wikipedia.org/wiki/Usability_testing), accessibilità, localizzazione, [prestazioni](https://en.wikipedia.org/wiki/Software_performance_testing), [sicurezza](https://en.wikipedia.org/wiki/Security_testing) e [accettazione](https://en.wikipedia.org/wiki/Acceptance_testing).

Una distribuzione di versioni di anteprima non riguarda semplicemente il routing del traffico live. In questo tipo di distribuzione si vogliono ottenere informazioni il più rapidamente possibile, sia nel caso di un bug imprevisto, di una riduzione del livello delle prestazioni o di problemi relativi all'esperienza utente. Tenere presente che si sta trattando con clienti reali. Per procedere quindi correttamente, è necessario assicurarsi di avere configurato la distribuzione di versioni di anteprima per raccogliere tutti i dati necessari per prendere una decisione basata su informazioni aggiornate per il passaggio successivo. Questa esercitazione illustra come raccogliere i dati con Application Insights, ma è possibile usare New Relic o altre tecnologie adatte al proprio scenario.

## Contenuto dell'esercitazione

In questa esercitazione si apprenderà come combinare gli scenari seguenti per testare l'app del servizio app nell'ambiente di produzione:

- [Instradare il traffico di produzione](app-service-web-test-in-production-get-start.md) all'app beta
- [Instrumentare l'app](../application-insights/app-insights-web-track-usage.md) per ottenere metrica utile
- Distribuire continuamente l'app beta e tenere traccia della metrica dell'app attiva
- Confrontare la metrica tra l'app di produzione e l'app beta per vedere in che modo le modifiche al codice vengono convertite in risultati

## Prerequisiti

-	Un account Azure
-	Account [GitHub](https://github.com/)
- Visual Studio 2015: è possibile scaricare la [Community Edition](https://www.visualstudio.com/it-IT/products/visual-studio-express-vs.aspx).
-	Git Shell (installato con [GitHub per Windows](https://windows.github.com/)): consente di eseguire sia comandi Git che PowerShell nella stessa sessione
-	Ultimi bit di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)
-	Conoscenza di base degli argomenti seguenti:
	-	Distribuzione di modelli di [Gestione risorse di Azure](../resource-group-overview.md); vedere [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure: è possibile [aprire un account Azure gratuitamente](/pricing/free-trial/) - si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio le app Web. È possibile [attivare i benefici della sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/): con la sottoscrizione Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
>
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Configurare l'app Web di produzione

>[AZURE.NOTE] Lo script usato in questa esercitazione configurerà automaticamente la pubblicazione continua dal repository GitHub. A questo scopo, è necessario che le credenziali GitHub siano già archiviate in Azure, altrimenti la distribuzione con script ha esito negativo quando si tenta di configurare le impostazioni di controllo del codice sorgente per le app Web.
>
>Per archiviare le credenziali GitHub in Azure, creare un'app Web nel [Portale di Azure](https://portal.azure.com/) e [configurare la distribuzione GitHub](web-sites-publish-source-control.md#Step7). È necessario eseguire questa operazione una sola volta.

In uno scenario tipico di sviluppo, in Azure è in esecuzione un'applicazione a cui si desidera apportare modifiche tramite la pubblicazione continua. In questo scenario si distribuirà nell'ambiente di produzione un modello sviluppato e testato.

1.	Creare il fork personalizzato del repository [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Per informazioni sulla creazione della biforcazione, vedere la pagina relativa alla [biforcazione di un repository](https://help.github.com/articles/fork-a-repo/). Una volta creata la biforcazione, è possibile visualizzarla nel browser.

	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Aprire una sessione di Git Shell. Se non si ha ancora Git Shell, installare [GitHub per Windows](https://windows.github.com/).
3.	Creare un clone locale della biforcazione eseguendo il comando seguente:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.	Una volta creato il clone locale, passare a *&lt;radice\_repository>*\\ARMTemplates ed eseguire lo script deploy.ps1 con un suffisso univoco, come illustrato di seguito:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.	Quando richiesto, digitare il nome utente e la password desiderati per l'accesso al database. Prendere nota delle credenziali del database perché sarà necessario specificarle di nuovo quando si aggiorna il gruppo di risorse.

	Verrà visualizzato l'avanzamento del provisioning delle varie risorse di Azure. Al termine della distribuzione, lo script avvierà l'applicazione nel browser ed emetterà un breve segnale acustico. ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.	Tornare alla sessione di Git Shell ed eseguire:

        .\swap –Name ToDoApp<your_suffix>

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.	Al termine dello script, tornare all'indirizzo del front-end (http://ToDoApp*&lt;your_suffix>*master.azurewebsites.net/) per visualizzare l'applicazione in esecuzione nell'ambiente di produzione.
5.	Accedere al [Portale di Azure](https://portal.azure.com/) per vedere gli elementi creati.

	Dovrebbero essere visibili due app Web nello stesso gruppo di risorse, una con il suffisso `Api` nel nome. Osservando il gruppo di risorse, si noteranno anche il server e il database SQL, il piano del servizio app e gli slot di gestione temporanea per le app Web. Esplorare le diverse risorse e confrontarle con *&lt;radice\_repository>*\\ARMTemplates\\ProdAndStage.json per verificare come sono configurate nel modello.

	![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

L'app di produzione è così configurata. A questo punto, si immagini di ricevere commenti e suggerimenti dai quali risulta che l'usabilità dell'app è limitata. Si decide quindi di esaminare il problema. L'app verrà instrumentata per ottenere commenti e suggerimenti.

## Procedere all'approfondimento: instrumentare l'app client per il monitoraggio e la metrica

5. Aprire *& lt;radice\_repository >*\\src\\MultiChannelToDo.sln in Visual Studio.
6. Ripristinare tutti i pacchetti Nuget facendo clic con il pulsante destro del mouse sulla soluzione > **Gestisci pacchetti NuGet** per la soluzione > **Ripristina**.
6. Fare clic con il pulsante destro del mouse su **MultiChannelToDo.Web** > **Aggiungi Application Insights Telemetry** > **Configura impostazioni** > Modificare il gruppo di risorse in ToDoApp*& lt;suffisso\_personalizzato>* > **Aggiungi Application Insights al progetto**.
7. Nel portale di Azure, aprire il pannello per la risorsa **MultiChannelToDo.Web** di Application Insights. Quindi nella parte **Integrità applicazione** fare clic su **Informazioni su come raccogliere i dati relativi ai caricamenti di pagina del browser** > copiare il codice.
7. Aggiungere il codice di strumentazione JS copiato a *& lt; radice\_repository >*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml, appena prima del tag `<heading>` di chiusura. Dovrebbe contenere la chiave di strumentazione univoca della risorsa di Application Insights.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Inviare gli eventi personalizzati ad Application Insights per i clic del mouse, aggiungendo il codice seguente alla fine del corpo:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Questo frammento di codice JavaScript invia un evento personalizzato ad Application Insights ogni volta che un utente fa clic in un punto qualsiasi dell'app Web.

12. In Git Shell eseguire il commit e il push delle modifiche al fork in GitHub. Attendere quindi che i client aggiornino il browser.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.	Trasferire le modifiche all'app distribuita nell'ambiente di produzione:

        .\swap –Name ToDoApp<your_suffix>

13. Passare alla risorsa di Application Insights configurata. Fare clic su Eventi personalizzati.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Se non viene visualizzata la metrica per gli eventi personalizzati, attendere alcuni minuti e fare clic su **Aggiorna**.

Si supponga di visualizzare un grafico come il seguente:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

e la griglia di eventi sottostante:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

In base al codice dell'app ToDoApp, l'evento **BUTTON** corrisponde al pulsante di invio e l'evento **INPUT** corrisponde alla casella di testo. Fino a questo punto, è tutto chiaro. Sembra tuttavia che ci sia una buona quantità di clic e pochissimi clic sugli elementi relativi alle attività (gli eventi **LI**).

In base a questo risultato, si concretizza l'ipotesi che alcuni utenti non sono sicuri di quale sia la parte dell'interfaccia utente selezionabile. Questo è dovuto al fatto che lo stile del cursore, quando lo si passa sugli elementi dell'elenco e sulle relative icone, è quello per la selezione del testo.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Potrebbe trattarsi di un esempio improbabile. Tuttavia, si apporterà un miglioramento all'app e quindi si eseguirà una distribuzione di versioni di anteprima per ottenere commenti e suggerimenti sull'usabilità da parte di clienti reali.

### Instrumentare l'app server per il monitoraggio e la metrica
Questa è una digressione, perché lo scenario illustrato in questa esercitazione riguarda solo l'app client. Per completezza, si configurerà tuttavia l'app sul lato server.

6. Fare clic con il pulsante destro del mouse su **MultiChannelToDo** > **Aggiungi Application Insights Telemetry** > **Configura impostazioni** > Modificare il gruppo di risorse in ToDoApp*& lt;suffisso\_personalizzato>* > **Aggiungi Application Insights al progetto**.
12. In Git Shell eseguire il commit e il push delle modifiche al fork in GitHub. Attendere quindi che i client aggiornino il browser.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.	Trasferire le modifiche all'app distribuita nell'ambiente di produzione:

        .\swap –Name ToDoApp<your_suffix>

L'operazione è terminata.

## Procedere all'approfondimento: aggiungere tag specifici dello slot alla metrica dell'app client

In questa sezione si configureranno i diversi slot di distribuzione per inviare dati di telemetria specifici dello slot alla stessa risorsa di Application Insights. In questo modo, è possibile confrontare i dati di telemetria nel traffico proveniente da diversi slot (ambienti di distribuzione) per vedere facilmente l'effetto delle modifiche apportate all'app. Allo stesso tempo, è possibile separare il traffico di produzione dal resto, per poter continuare a monitorare l'app di produzione secondo le esigenze.

Poiché si raccolgono dati sul comportamento del client, si dovrà [aggiungere un inizializzatore di telemetria al codice JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) in index.cshtml. Se si vuole ad esempio eseguire il test delle prestazioni sul lato server, è possibile procedere in modo analogo anche nel codice del server. Vedere [API di Application Insights per metriche ed eventi personalizzati](../application-insights/app-insights-api-custom-events-metrics.md).

1. Aggiungere prima di tutto il codice tra i due commenti `//` seguenti nel blocco JavaScript aggiunto in precedenza al tag `<heading>`.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Con questo codice inizializzatore l'oggetto `appInsights` aggiunge una proprietà personalizzata denominata `Environment` ai singoli dati di telemetria inviati.

2. Aggiungere quindi questa proprietà personalizzata come [impostazione dello slot](web-sites-staged-publishing.md#AboutConfiguration) per l'app Web in Azure. A questo scopo, eseguire i comandi seguenti nella sessione Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Il file Web.config del progetto definisce già l'impostazione dell'app `environment`. Con questa impostazione, durante il test dell'app in locale, la metrica verrà contrassegnata con `VS Debugger`. Tuttavia, quando si effettua il push delle modifiche in Azure, Azure troverà e userà invece l'impostazione dell'app `environment` nella configurazione dell'app Web e la metrica sarà contrassegnata con `Production`.

3. Eseguire il commit e il push delle modifiche al codice al fork su GitHub e quindi attendere che gli utenti usino la nuova app mediante l'aggiornamento del browser. La visualizzazione della nuova proprietà nella risorsa `MultiChannelToDo.Web` di Application Insights richiede circa 15 minuti.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. A questo punto, passare di nuovo al pannello **Eventi personalizzati** e filtrare la metrica in `Environment=Production`. Con questo filtro dovrebbe essere possibile vedere tutti i nuovi eventi personalizzati nello slot di produzione.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Fare clic sul pulsante **Preferiti** per salvare le impostazioni correnti di Esplora metriche, che dovrebbero essere simili a **Eventi personalizzati: Produzione**. È possibile passare facilmente tra questa visualizzazione e una visualizzazione dello slot di distribuzione in un secondo momento.

    > [AZURE.TIP] Per analisi ancora più avanzate, prendere in considerazione l'[l'integrazione della risorsa di Application Insights con Power BI](../application-insights/app-insights-export-power-bi.md).

### Aggiungere tag specifici dello slot alla metrica dell'app server
Di nuovo per completezza, si configurerà l'app sul lato server. A differenza dell'app client che è stata instrumentata in JavaScript, i tag specifici dello slot per l'app server sono instrumentati con codice .NET.

1. Aprire *& lt;radice\_repository>*\\src\\MultiChannelToDo\\Global.asax.cs. Aggiungere il blocco di codice seguente, subito prima della parentesi graffa chiusa dello spazio dei nomi.

		namespace MultiChannelToDo
		{
				...

				// Begin new code
		    public class ConfigInitializer
		    : ITelemetryInitializer
		    {
		        void ITelemetryInitializer.Initialize(ITelemetry telemetry)
		        {
		            telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
		        }
		    }
				// End new code
		}

2. Correggere gli errori relativi alla risoluzione dei nomi aggiungendo le istruzioni `using` seguenti all'inizio del file:

		using Microsoft.ApplicationInsights.Channel;
		using Microsoft.ApplicationInsights.Extensibility;

3. Aggiungere il codice seguente all'inizio del metodo `Application_Start()`:

		TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Eseguire il commit e il push delle modifiche al codice al fork su GitHub e quindi attendere che gli utenti usino la nuova app mediante l'aggiornamento del browser. La visualizzazione della nuova proprietà nella risorsa `MultiChannelToDo` di Application Insights richiede circa 15 minuti.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## Procedere all'aggiornamento: configurare il ramo beta

2. Aprire *& lt;radice\_repository>*\\ARMTemplates\\ProdAndStagetest.json e trovare le risorse `appsettings` (cercare `"name": "appsettings"`). Ne sono disponibili 4, una per ogni slot. 

2. Per ogni risorsa `appsettings` aggiungere un'impostazione dell'app `"environment": "[parameters('slotName')]"` alla fine della matrice `properties`. Ricordare di terminare la riga precedente con una virgola.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Si è appena aggiunta un'impostazione dell'app `environment` a tutti gli slot del modello.
    
2. Nello stesso file trovare le risorse `slotconfignames` (cercare `"name": "slotconfignames"`). Ne sono disponibili 2, una per ogni app.

2. Per ogni risorsa `slotconfignames` aggiungere `"environment"` alla fine della matrice `appSettingNames`. Ricordare di terminare la riga precedente con una virgola.

    Sono state associate le impostazioni dell'app `environment` al rispettivo slot di distribuzione per entrambe le app.

3. Nella sessione di Git Shell eseguire i comandi seguenti con lo stesso suffisso del gruppo di risorse usato in precedenza.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Quando richiesto, specificare le stesse credenziali del database SQL come prima. Quando poi viene richiesto di aggiornare il gruppo di risorse, digitare `Y`, quindi `ENTER`.

    Al termine dell'esecuzione dello script tutte le risorse nel gruppo di risorse originale vengono mantenute, ma viene creato un nuovo slot denominato "beta" con la stessa configurazione dello slot di "staging" creato all'inizio.

    >[AZURE.NOTE] Questo metodo per la creazione di diversi ambienti di distribuzione è diverso dal metodo descritto in [Agile Software Development con il servizio app di Azure](app-service-agile-software-development.md). Con questo metodo si creano ambienti di distribuzione con slot di distribuzione, mentre con l'altro si creano ambienti di distribuzione con gruppi di risorse. La gestione di ambienti di distribuzione con gruppi di risorse consente di mantenere l'ambiente di produzione inaccessibile per gli sviluppatori, ma non è facile eseguire test nell'ambiente di produzione, mentre possono essere eseguiti facilmente con gli slot.

Se necessario, è anche possibile creare un'app alfa con il codice seguente

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Per questa esercitazione si continuerà a usare l'app beta.

## Procedere all'aggiornamento: effettuare il push degli aggiornamenti all'app beta

Ritornare all'app che si vuole migliorare.

1. Verificare di trovarsi nel ramo beta

        git checkout beta

2. In *& lt;radice\_repository>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml trovare il tag `<li>` e aggiungere l'attributo `style="cursor:pointer"`, come illustrato di seguito.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. Eseguire il commit e il push in Azure.

4. Verificare che la modifica venga riflessa nello slot beta passando a http://todoapp*&lt;your_suffix>*-beta.azurewebsites.net/. Se la modifica non viene ancora visualizzata, aggiornare il browser per ottenere il nuovo codice javascript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Una volta che la modifica sarà in esecuzione nello slot beta, si potrà eseguire una distribuzione di versioni di anteprima.

## Eseguire la convalida: instradare il traffico all'app beta

In questa sezione si instraderà il traffico all'app beta. Per chiarezza di dimostrazione, si instraderà all'app beta una parte significativa del traffico utente. In realtà, la quantità di traffico da instradare dipenderà dalla situazione specifica. Ad esempio, se le dimensioni del sito sono a livello di quelle di microsoft.com, potrebbe essere necessario meno dell'1% del traffico totale per ottenere dati utili.

1. Nella sessione di Git Shell eseguire i comandi seguenti per instradare metà del traffico di produzione allo slot beta:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  La proprietà `ReroutePercentage=50` specifica che il 50% del traffico di produzione verrà instradato all'URL dell'app beta, specificato dalla proprietà `ActionHostName`.

2. Passare ora a http://ToDoApp*&lt;your_suffix>*.azurewebsites.net. 50% del traffico dovrebbe essere reindirizzato allo slot beta.

3. Nella risorsa di Application Insights filtrare la metrica per ambiente="beta".

    > [AZURE.NOTE] Se si salva questa visualizzazione filtrata come un'altra preferita, è possibile passare facilmente tra le visualizzazioni beta e di produzione di Esplora metriche.

Si supponga che in Application Insights venga visualizzata una schermata simile alla seguente:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Questa visualizzazione mostra non solo che sono presenti molti più clic sui tag `<li>`, ma sembra esserci un picco dei clic sui tag `<li>`. È quindi possibile concludere che le persone hanno scoperto i nuovi tag `<li>` sono selezionabili e ora stanno cancellando tutte le attività già completate nell'app.

In base ai dati della distribuzione di versioni di anteprima, si decide che la nuova interfaccia utente è pronta per la produzione.

## Passare alla fase operativa: spostare di nuovo codice nell'ambiente di produzione

Ora si può spostare l'aggiornamento nell'ambiente di produzione. L'aspetto interessante è che ora si ha la certezza che l'aggiornamento sia stato convalidato _prima_ di essere inviato all'ambiente di produzione. A questo punto è possibile distribuirlo. Poiché è stato apportato un aggiornamento all'app client AngularJS, è stato convalidato solo il codice sul lato client. Se fosse stato necessario apportare modifiche all'app per le API Web di back-end, si sarebbero potute convalidare le modifiche in modo altrettanto semplice e rapido.

1. In Git Shell rimuovere la regola di routing del traffico eseguendo il comando seguente:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Eseguire i comandi Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Attendere alcuni minuti che venga distribuito il nuovo codice allo slot di staging, quindi avviare http://ToDoApp*&lt;your_suffix>*-staging.azurewebsites.net per verificare che il nuovo aggiornamento sia stato eseguito nello slot di staging. Tenere presente che il ramo master del fork è collegato allo slot di staging dell'app.

3. A questo punto, trasferire lo slot di staging nell'ambiente di produzione

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## Riepilogo ##

Il servizio app di Azure consente alle piccole e medie imprese di testare le proprie app per i clienti nell'ambiente di produzione, un'operazione che tradizionalmente veniva effettuata nelle grandi imprese. È auspicabile che in questa esercitazione si siano state le conoscenze necessarie per combinare il servizio app e Application Insights per rendere possibili la distribuzione di versioni di anteprima e anche altri scenari di test in produzione nell'ambiente DevOps.

## Altre risorse ##

-   [Agile Software Development con il servizio app di Azure](app-service-agile-software-development.md)
-   [Configurare ambienti di staging per le app Web nel servizio app di Azure](web-sites-staged-publishing.md)
-	[Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
-	[Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md)
-	[JSONLint - Il validator JSON](http://jsonlint.com/)
-	[Diramazione Git - Diramazione e unione di base](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Azure PowerShell](../powershell-install-configure.md)
-	[Wiki del progetto Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0211_2016-->