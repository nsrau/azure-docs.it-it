<properties
   pageTitle="Microsoft Azure Service Fabric - Come eseguire il monitoraggio e la diagnosi dei servizi localmente"
   description="Questo articolo illustra come eseguire il monitoraggio e la diagnosi dei servizi scritti usando Microsoft Azure Service Fabric in un computer di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/04/2015"
   ms.author="kunalds"/>


# Monitoraggio e diagnosi dei servizi in una configurazione di sviluppo con computer locale
Il monitoraggio, il rilevamento, la diagnosi e la risoluzione dei problemi consentono ai servizi di continuare a funzionare con un'interruzione minima dell'esperienza utente. Questo aspetto è essenziale in un ambiente di produzione distribuito effettivo, ma l'efficacia dipenderà dall'adozione (durante lo sviluppo dei servizi) di un modello analogo che assicuri il funzionamento anche nel mondo reale. Service Fabric facilita gli sviluppatori di servizi nell'implementazione di una diagnostica in grado di operare senza problemi sia in ambienti di sviluppo costituiti da un unico computer in locale sia in configurazioni con cluster di produzione veri e propri.

## Perché usare ETW per il tracciamento e la registrazione
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) è la tecnologia consigliata per il tracciamento dei messaggi in Service Fabric. I motivi di questa scelta sono i seguenti:

* ETW è veloce. È stata sviluppata come tecnologia di tracciamento con un impatto minimo sui tempi di esecuzione del codice.
* Il tracciamento ETW è in grado di funzionare facilmente in ambienti di sviluppo locali, ma anche in configurazioni cluster reali. Questo significa che non è necessario riscrivere il codice di tracciamento quando si è pronti per distribuire il codice in un vero cluster.
* Anche il codice di sistema di Service Fabric usa ETW per il tracciamento interno. Ciò consente di visualizzare le tracce dell’applicazione con interfoliazione delle tracce di sistema di infrastruttura di servizi, facilitando la comprensione delle sequenze e delle correlazioni tra il codice dell'applicazione e gli eventi nel sistema sottostante.
* Negli strumenti di Visual Studio per Service Fabric è incorporato il supporto per la visualizzazione degli eventi ETW.


## Visualizzare gli eventi di sistema di Service Fabric in Visual Studio

Service Fabric emette eventi ETW per aiutare gli sviluppatori di applicazioni a comprendere cosa accade nella piattaforma. Nel caso in cui non sia ancora stato fatto, andare avanti e seguire la procedura in [Creazione della prima applicazione in Visual Studio](./service-fabric-create-your-first-application-in-visual-studio.md) per ottenere un'applicazione in esecuzione con il Visualizzatore eventi di diagnostica che mostra i messaggi di traccia.

1. Se la finestra degli eventi di diagnostica non viene visualizzata automaticamente, passare alla scheda Esplora server in Visual Studio, fare clic con il pulsante destro del mouse sul cluster di Service Fabric e scegliere "View Diagnostic Events" dal menu di scelta rapida.

  ![Aprire il visualizzatore eventi di diagnostica di Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

2. Per ogni evento sono disponibili informazioni di metadati standard che indicano il nodo, l'applicazione e il servizio da cui proviene l'evento. È anche possibile filtrare l'elenco degli eventi usando la casella del filtro eventi nella parte superiore della finestra. Ad esempio è possibile filtrare in base al nome del nodo o al nome del servizio. E quando si cercano le informazioni dettagliate di un evento è possibile anche sospendere utilizzando il pulsante sopra la finestra e riprendere senza alcuna perdita di eventi in un secondo momento.

  ![Visualizzatore eventi di diagnostica di Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## Aggiungere tracce personalizzate al codice dell'applicazione
I modelli di progetto di Visual Studio per Service Fabric contengono codice di esempio. Il codice mostra come aggiungere tracce ETW personalizzate per il codice dell'applicazione da visualizzare nel visualizzatore ETW di Visual Studio insieme alle tracce di sistema di Service Fabric. Questa soluzione ha il vantaggio di consentire l'aggiunta automatica dei metadati alle tracce e inoltre il visualizzatore diagnostico di Visual Studio è già configurato per visualizzarli.

Per i progetti creati dai **i modelli di servizio** (con o senza stato) è sufficiente cercare l’implementazione `RunAsync`:

1. La chiamata a `ServiceEventSource.Current.ServiceMessage` nel metodo `RunAsync` mostra un esempio di una traccia ETW personalizzata del codice dell'applicazione.
2. Nel file **ServiceEventSource.cs**, si noterà un overload per il metodo `ServiceEventSource.ServiceMessage` che deve essere utilizzato per eventi ad alta frequenza dovuti a motivi di prestazioni.

Per i progetti creati da **modelli di attore** (con o senza stato):

1. Aprire il file **"NomeProgetto".cs**, dove *NomeProgetto* è il nome scelto per il progetto di Visual Studio.  
2. Trovare il codice `ActorEventSource.Current.ActorMessage(this, "Doing Work");` nel metodo *DoWorkAsync*. Questo è un esempio di una traccia ETW personalizzata scritta dal codice dell'applicazione.  
3. Nel file **ActorEventSource.cs**, si noterà un overload per il metodo `ActorEventSource.ActorMessage` che deve essere utilizzato per eventi ad alta frequenza dovuti a motivi di prestazioni.

Dopo aver aggiunto il tracciamento ETW personalizzato al codice del servizio, sarà possibile compilare, distribuire ed eseguire nuovamente l'applicazione per vedere gli eventi personalizzati nel visualizzatore diagnostico. Se si esegue il debug dell'applicazione con F5, tale visualizzatore verrà aperto automaticamente.

## Passaggi successivi
Lo stesso codice di traccia che è stato aggiunto all'applicazione in precedenza per la diagnostica locale funzionerà con gli strumenti che è possibile usare per visualizzare questi eventi quando si esegue l'applicazione in un cluster di Azure, consultare questi articoli in cui si discute sulle diverse opzioni per gli strumenti e viene descritto come è possibile impostarle. * [Raccolta di log da un cluster dell'infrastruttura di servizi in Azure mediante WAD (diagnostica Microsoft Azure) e Operational Insights](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) * [Uso di ElasticSearch come archivio di traccia dell’applicazione di infrastruttura di servizi](service-fabric-diagnostic-how-to-use-elasticsearch.md)

<!---HONumber=Nov15_HO4-->