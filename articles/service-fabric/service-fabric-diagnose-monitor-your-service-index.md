<properties
   pageTitle="Eseguire la diagnosi e la risoluzione dei problemi di un servizio di Service Fabric"
   description="Informazioni concettuali ed esercitazioni utili per eseguire la diagnosi, il monitoraggio e la risoluzione dei problemi di un servizi di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# Eseguire la diagnosi e il monitoraggio di un servizio di Service Fabric
Il monitoraggio, il rilevamento, la diagnosi e la risoluzione dei problemi consentono ai servizi di continuare a funzionare con un'interruzione minima dell'esperienza utente. Per altre informazioni, leggere gli articoli seguenti:

- [Come eseguire il monitoraggio e la diagnosi dei servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Configurazione di Application Insights per l'applicazione di Service Fabric](service-fabric-diagnostics-application-insights-setup.md)
- [Risoluzione dei problemi relativi all'aggiornamento di un'applicazione](service-fabric-application-upgrade-troubleshooting.md)
- [Risoluzione dei problemi relativi a un aggiornamento monitorato dell'applicazione non riuscito](../service-fabric-application-monitored-upgrade-troubleshooting.md)
- [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
- [Diagnostica e monitoraggio delle prestazioni per Reliable Services](service-fabric-reliable-services-diagnostics.md)

## Risolvere i problemi relativi a un cluster
Le informazioni seguenti saranno utili per risolvere i problemi relativi al cluster di sviluppo locale:

- [Risolvere i problemi di configurazione del cluster di sviluppo locale](service-fabric-troubleshoot-local-cluster-setup.md)

## Modello di integrità
Service Fabric introduce un modello di integrità che offre una funzionalità di reporting e valutazione avanzata, flessibile ed estendibile per le entità di Service Fabric. I componenti di Service Fabric segnalano l'integrità per impostazione predefinita per tutte le entità. I servizi utente possono arricchire i dati di integrità con informazioni specifiche della relativa logica, segnalate per loro stessi o per altre entità del cluster. Per altre informazioni, leggere gli articoli seguenti:

- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)
- [Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Aggiunta di report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)
 

<!---HONumber=July15_HO2-->