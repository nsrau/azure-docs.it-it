<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Risoluzione dei problemi e domande e risposte - Application Insights in Anteprima di Microsoft Azure

-   [Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio][Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio]
-   [Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.][Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.]
-   [Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.][Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.]
-   [In Dati di analisi utilizzo non ci sono dati][In Dati di analisi utilizzo non ci sono dati]
-   [Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?][Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?]
-   [La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?][La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?]
-   [Quando si aggiunge Application Insights all'applicazione e si apre il portale di Application Insights, è tutto completamente diverso rispetto alle catture di schermata.][Quando si aggiunge Application Insights all'applicazione e si apre il portale di Application Insights, è tutto completamente diverso rispetto alle catture di schermata.]
-   [Si può usare Application Insights per monitorare un server Web Intranet?][Si può usare Application Insights per monitorare un server Web Intranet?]
-   [Come si ottengono dati per Windows Phone o Windows Store?][Come si ottengono dati per Windows Phone o Windows Store?]
-   [Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?][Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?]
-   [Perché ci sono due versioni di Application Insights?][Perché ci sono due versioni di Application Insights?]
-   [Cosa manca nella versione Azure di Application Insights?][Cosa manca nella versione Azure di Application Insights?]
-   [In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?][In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?]
-   [Quali modifiche apporta Application Insights al progetto?][Quali modifiche apporta Application Insights al progetto?]
-   [Come si fa a trovare i risultati in Application Insights?][Come si fa a trovare i risultati in Application Insights?]
-   [Passaggi successivi][Passaggi successivi]

## <a name="q01"></a>Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio

-   Assicurarsi di disporre di [Visual Studio Update 3][Visual Studio Update 3]. È preinstallato con Strumenti Application Insights per Visual Studio, che dovrebbe essere presente in Gestione estensioni.
-   Application Insights in Anteprima di Microsoft Azure attualmente è disponibile solo per progetti Web ASP.NET in C# o Visual Basic.
-   Se si dispone di un progetto esistente, passare a Esplora soluzioni e fare clic sul progetto Web (non su un altro progetto o sulla soluzione). Dovrebbe essere presente una voce di menu "Aggiungi telemetria di Application Insights al progetto".
-   Se si crea un nuovo progetto, in Visual Studio scegliere File \> Nuovo progetto e selezionare {Visual C#|Visual Basic} \> Web \> Applicazione Web ASP.NET. Dovrebbe essere presente un'opzione Aggiungi Application Insights al progetto.

## <a name="q02"></a>Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.

Può succedere se la comunicazione con il portale di Application Insights non è riuscita o in caso di problemi con l'account.

-   Verificare di avere specificato le credenziali di accesso per l'account Azure appropriato. Le credenziali di Microsoft Azure, visualizzate nella finestra di dialogo Nuovo progetto, possono essere diverse da quelle di Visual Studio Online visualizzate nella parte superiore destra di Visual Studio.
-   Attendere qualche istante, poi [aggiungere Application Insights al progetto esistente][aggiungere Application Insights al progetto esistente].
-   Passare alle impostazioni dell'account Microsoft Azure e verificare la presenza di eventuali limitazioni. Provare ad aggiungere manualmente un'applicazione di Application Insights.

## <a name="q03"></a>Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.

-   È necessario chiudere e riaprire un pannello nel quale si attende la comparsa di dati. Nella versione corrente il contenuto di un pannello non viene aggiornato automaticamente.
-   Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
-   Nel firewall del server Web può essere necessario aprire la porta 443 per il traffico in uscita.

## <a name="q04"></a>In Dati di analisi utilizzo non ci sono dati

-   I dati provengono da script nelle pagine Web. Se Application Insights è stato aggiunto a un progetto Web esistente, [è necessario aggiungere manualmente gli script][aggiungere Application Insights al progetto esistente].

## <a name="q05"></a>Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?

È possibile procedere in due modi:

-   Scegliere Sfoglia, Application Insights, quindi il nome del progetto. Se non è presente alcun progetto, è necessario [aggiungere Application Insights al progetto Web in Visual Studio][aggiungere Application Insights al progetto esistente].

-   In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto Web e scegliere Apri portale Application Insights.

## <a name="q06"></a>La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?

No. Mostra lo stato del servizio di Azure. Per vedere i risultati del test Web, scegliere Sfoglia \> Application Insights \> (applicazione) e quindi osservare i risultati del test Web.

## <a name="q07"></a>Quando si aggiunge Application Insights all'applicazione e si apre il portale di Application Insights, è tutto completamente diverso rispetto alle catture di schermata.

È possibile che sia in uso una versione precedente di Strumenti Application Insights, che si connette alla versione Visual Studio Online.

Queste pagine della guida si riferiscono ad Application Insights per Anteprima di Microsoft Azure, in dotazione con Visual Studio Update 3.

## <a name="q08"></a>Si può usare Application Insights per monitorare un server Web Intranet?

Sì, è possibile monitorare l'integrità e l'utilizzo se il server è in grado di inviare dati alla rete Internet pubblica.

Se però si vuole eseguire test Web per il servizio, è necessario che sia accessibile dalla rete Internet pubblica.

## <a name="q10"></a>Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?

Nella versione Microsoft Azure non è ancora supportato. Sarà disponibile a breve. Per il momento, è possibile provare la [versione precedente][versione precedente].

## <a name="q11"></a>Perché ci sono due versioni di Application Insights?

Il portale precedente fa parte di Visual Studio Online. Non saranno apportate altre modifiche sostanziali a quella versione. Se si usa una versione precedente degli strumenti Application Insights per Visual Studio, questi si connettono alla versione Visual Studio Online.

Visual Studio Update 3 include una versione preinstallata dei nuovi strumenti Application Insights. Questi si connettono al nuovo portale di Application Insights, che è un componente di Anteprima di Microsoft Azure. È in corso il trasferimento di Application Insights in questo nuovo ambiente. Il processo non è ancora completato e ci sono diverse limitazioni.

## <a name="q12"></a>Cosa manca nella versione Azure di Application Insights?

Sono in arrivo grandi novità.

Attualmente, però, le principali funzionalità mancanti sono: supporto di app per dispositivi come Windows Phone e Windows Store e rapporti per la telemetria personalizzata come `trackEvent()` e `trackPageView()`.

## <a name="q13"></a>In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?

1.  Passare a Gestione estensioni di Visual Studio.
2.  Disinstallare Strumenti Application Insights.
3.  Eseguire il [programma di installazione per la versione precedente degli strumenti][programma di installazione per la versione precedente degli strumenti] e leggere la relativa [guida introduttiva][versione precedente].

## <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?

-   Aggiunge tre file al progetto:

  * ApplicationInsights.config.
  * ai.js

-   Installa i pacchetti NuGet seguenti:

  * Application Insights API*, ovvero l'API principale

  * Application Insights API for Web Applications*, che consente di inviare i dati di telemetria dal server

  * Application Insights API for JavaScript Applications*, che consente di inviare i dati di telemetria dal client

    I pacchetti includono gli assembly seguenti:

  * Microsoft.ApplicationInsights

  * Microsoft.ApplicationInsights.Platform

-   (Solo per i nuovi progetti, se si [aggiunge Application Insights a un progetto esistente][aggiungere Application Insights al progetto esistente], l'operazione deve essere eseguita manualmente.) Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. Ad esempio, in un'app MVC il codice viene inserito in:

-   pagina principale Views/Shared/\_Layout.cshtml

-   Web.config

-   packages.config

## <a name="q15"></a>Come si fa a trovare i risultati in Application Insights?

1.  Aprire Microsoft Azure:

  * In Visual Studio fare clic con il pulsante destro del mouse sul progetto di applicazione Web e scegliere **Apri portale di anteprima di Azure**.
  * In alternativa, in un Web browser è possibile aprire l'account in Anteprima di Microsoft Azure.

1.  Scegliere Sfoglia, Application Insights, quindi selezionare il progetto.

## <a name="next"></a>Altre informazioni

-   [Application Insights][Application Insights]
-   [Aggiungere Application Insights al progetto][aggiungere Application Insights al progetto esistente]
-   [Monitorare un server Web attivo][Monitorare un server Web attivo]
-   [Esaminare le metriche in Application Insights][Esaminare le metriche in Application Insights]
-   [Ricerche nei log di diagnostica][Ricerche nei log di diagnostica]
-   [Verifica della disponibilità mediante test Web][Verifica della disponibilità mediante test Web]
-   [Verifica dell'utilizzo con eventi e metriche][Verifica dell'utilizzo con eventi e metriche]
-   [Domande e risposte e risoluzione dei problemi][Domande e risposte e risoluzione dei problemi]

<!--Link references-->

  [Non è presente un'opzione per aggiungere Application Insights al progetto in Visual Studio]: #q01
  [Il nuovo progetto Web è stato creato ma l'aggiunta di Application Insights ha avuto esito negativo.]: #q02
  [Dopo avere aggiunto Application Insights ed eseguito l'app, nel portale non sono presenti dati.]: #q03
  [In Dati di analisi utilizzo non ci sono dati]: #q04
  [Dalla schermata iniziale di Anteprima di Microsoft Azure come si fa a trovare i dati in Application Insights?]: #q05
  [La mappa nella schermata iniziale di Anteprima di Microsoft Azure mostra lo stato dell'applicazione?]: #q06
  [Quando si aggiunge Application Insights all'applicazione e si apre il portale di Application Insights, è tutto completamente diverso rispetto alle catture di schermata.]: #q07
  [Si può usare Application Insights per monitorare un server Web Intranet?]: #q08
  [Come si ottengono dati per Windows Phone o Windows Store?]: #q09
  [Come è possibile vedere gli eventi e le visualizzazioni di pagina registrati nel codice?]: #q10
  [Perché ci sono due versioni di Application Insights?]: #q11
  [Cosa manca nella versione Azure di Application Insights?]: #q12
  [In che modo è possibile riavere tutte le funzionalità presenti nella versione Visual Studio Online di Application Insights?]: #q13
  [Quali modifiche apporta Application Insights al progetto?]: #q14
  [Come si fa a trovare i risultati in Application Insights?]: #q15
  [Passaggi successivi]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [aggiungere Application Insights al progetto esistente]: ../app-insights-monitor-application-health-usage/
  [versione precedente]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [programma di installazione per la versione precedente degli strumenti]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Monitorare un server Web attivo]: ../app-insights-monitor-performance-live-website-now/
  [Esaminare le metriche in Application Insights]: ../app-insights-explore-metrics/
  [Ricerche nei log di diagnostica]: ../app-insights-search-diagnostic-logs/
  [Verifica della disponibilità mediante test Web]: ../app-insights-monitor-web-app-availability/
  [Verifica dell'utilizzo con eventi e metriche]: ../app-insights-track-usage-custom-events-metrics/
  [Domande e risposte e risoluzione dei problemi]: ../app-insights-troubleshoot-faq/
