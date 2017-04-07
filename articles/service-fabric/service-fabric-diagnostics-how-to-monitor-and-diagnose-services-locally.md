---
title: Eseguire il debug di microservizi di Azure in Windows | Documentazione Microsoft
description: Informazioni su come eseguire il monitoraggio e la diagnosi dei servizi scritti usando Microsoft Azure Service Fabric in un computer di sviluppo locale.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: cf8f717d5343ae27faefdc10f81b4feaccaa53b9
ms.openlocfilehash: 5421cf66449892bb7bbc46cd8727a0642b7d66f3
ms.lasthandoff: 01/24/2017


---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Le operazioni di monitoraggio, rilevamento, diagnosi e risoluzione dei problemi consentono ai servizi di continuare a funzionare con un'interruzione minima dell'esperienza utente. L'efficienza delle operazioni di monitoraggio e diagnostica, essenziali in un ambiente di produzione distribuito reale, dipenderà tuttavia dall'adozione nella fase di sviluppo dei servizi di un modello analogo che ne assicuri il funzionamento anche nel mondo reale. Service Fabric consente agli sviluppatori di servizi di implementare facilmente un sistema di diagnostica in grado di operare senza problemi sia in ambienti di sviluppo costituiti da un unico computer locale sia in configurazioni con cluster di produzione veri e propri.

## <a name="the-benefits-of-event-tracing-for-windows"></a>Vantaggi offerti da Event Tracing for Windows
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) è la tecnologia consigliata per il tracciamento dei messaggi in Service Fabric. I motivi di questa scelta sono i seguenti:

* **ETW è veloce.** È stata sviluppata come tecnologia di tracciamento con un impatto minimo sui tempi di esecuzione del codice.
* **ETW funziona perfettamente non solo in ambienti di sviluppo locali, ma anche in configurazioni cluster reali.** Questo significa che non è necessario riscrivere il codice di tracciamento quando si è pronti per distribuire il codice in un vero cluster.
* **Anche il codice di sistema di Service Fabric usa ETW per il tracciamento interno.** In questo modo, è possibile visualizzare le tracce dell'applicazione interfoliate con le tracce di sistema di Service Fabric ed è più semplice comprendere le sequenze e le correlazioni tra il codice dell'applicazione e gli eventi nel sistema sottostante.
* **Negli strumenti di Visual Studio per Service Fabric è incorporato il supporto per la visualizzazione degli eventi ETW.**

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visualizzare gli eventi di sistema di Service Fabric in Visual Studio
Service Fabric emette eventi ETW per aiutare gli sviluppatori di applicazioni a capire cosa sta accadendo nella piattaforma. Se necessario, andare avanti e seguire la procedura descritta in [Creare la prima applicazione in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). per creare ed eseguire un'applicazione in cui il visualizzatore degli eventi di diagnostica mostra i messaggi di traccia.

1. Se la finestra degli eventi di diagnostica non viene visualizzata automaticamente, passare alla scheda **Visualizza** in Visual Studio, scegliere **Altre finestre** e quindi **Visualizzatore eventi di diagnostica**.
2. Per ogni evento sono disponibili informazioni di metadati standard che indicano il nodo, l'applicazione e il servizio da cui proviene l'evento. È anche possibile filtrare l'elenco degli eventi usando la casella **Filtra eventi** nella parte superiore della finestra. Ad esempio, è possibile filtrare in base al **nome del nodo** o al **nome del servizio**. Se si cercano informazioni dettagliate su un evento, è anche possibile sospendere la sessione tramite il pulsante **Pausa** nella parte superiore della finestra degli eventi e riprendere in un secondo momento senza alcuna perdita di eventi.
   
   ![Visualizzatore eventi di diagnostica di Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Aggiungere tracce personalizzate al codice dell'applicazione
I modelli di progetto di Visual Studio per Service Fabric contengono codice di esempio. Il codice mostra come aggiungere tracce ETW personalizzate al codice dell'applicazione, visibili nel visualizzatore ETW di Visual Studio insieme alle tracce di sistema di Service Fabric. Adottando questo metodo, i metadati vengono automaticamente aggiunti alle tracce e il visualizzatore degli eventi di diagnostica di Visual Studio è già configurato per visualizzarli.

Per i progetti creati a partire da **modelli di servizio** (con o senza stato), è sufficiente cercare l'implementazione `RunAsync`:

1. La chiamata a `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` mostra un esempio di una traccia ETW personalizzata del codice dell'applicazione.
2. Nel file **ServiceEventSource.cs** si noterà un overload per il metodo `ServiceEventSource.ServiceMessage`, che è opportuno usare per eventi ad alta frequenza dovuti a motivi di prestazioni.

Per i progetti creati da **modelli di attore** (con o senza stato):

1. Aprire il file **"NomeProgetto".cs** , dove *NomeProgetto* è il nome scelto per il progetto di Visual Studio.  
2. Trovare il codice `ActorEventSource.Current.ActorMessage(this, "Doing Work");` nel metodo *DoWorkAsync* .  Questo è un esempio di una traccia ETW personalizzata scritta dal codice dell'applicazione.  
3. Nel file **ActorEventSource.cs** si noterà un overload per il metodo `ActorEventSource.ActorMessage`, che è opportuno usare per eventi ad alta frequenza dovuti a motivi di prestazioni.

Dopo aver aggiunto il tracciamento ETW personalizzato al codice del servizio, sarà possibile compilare, distribuire ed eseguire nuovamente l'applicazione in modo che gli eventi risultino visibili nel visualizzatore degli eventi di diagnostica. Se si esegue il debug dell'applicazione con **F5**, il visualizzatore verrà aperto automaticamente.

## <a name="next-steps"></a>Passaggi successivi
Lo stesso codice di traccia aggiunto all'applicazione precedente per la diagnostica locale potrà essere usato con gli stessi strumenti di cui è possibile avvalersi per visualizzare gli eventi quando si esegue l'applicazione in un cluster di Azure. Leggere questi articoli che illustrano le diverse opzioni per gli strumenti e descrivono come configurarle.

* [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Raccogliere log direttamente da un processo di servizio](service-fabric-diagnostic-collect-logs-without-an-agent.md)


