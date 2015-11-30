<properties
   pageTitle="Passaggi successivi alla creazione del progetto di Infrastruttura di Servizi | Microsoft Azure"
   description="In questo articolo vengono forniti collegamenti a un set di attività di sviluppo principali per Infrastruttura di servizi"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/16/2015"
   ms.author="seanmck"/>

# Applicazione di Infrastruttura di servizi e fasi successive
L'applicazione di Infrastruttura di servizi è stata creata. In questo articolo si descrive la costruzione del progetto e alcuni potenziali passaggi successivi.

## L'applicazione
Ogni nuova applicazione include un progetto di applicazione. Potrebbero esserci uno o due progetti aggiuntivi in base al tipo di servizio scelto.

### Il progetto dell’applicazione
Il progetto dell’applicazione è costituito da: - un set di riferimenti ai servizi che costituiscono l'applicazione. - Due profili di pubblicazione (locale e Cloud) che è possibile utilizzare per gestire le preferenze per l'utilizzo di ambienti diversi, ad esempio un endpoint del cluster e la scelta di eseguire le distribuzioni degli aggiornamenti per impostazione predefinita. - Due file dei parametri dell'applicazione (locale e Cloud) che è possibile utilizzare per gestire le configurazioni di applicazioni specifiche per l'ambiente, ad esempio il numero di partizioni da creare per un servizio. -Uno script di distribuzione che è possibile utilizzare per distribuire l'applicazione dalla riga di comando o come parte di una pipeline di integrazione continua automatizzata. -Il manifesto dell'applicazione, che descrive l'applicazione.

### Reliable Services
Quando si aggiunge un nuovo Reliable Service, Visual Studio aggiunge un progetto del servizio alla soluzione. Il progetto del servizio contiene una classe che estende dal `StatelessService` o `StatefulService` a seconda del tipo scelto.

### Reliable Actors
Quando si aggiunge un nuovo Reliable Actor, Visual Studio aggiunge due progetti alla soluzione: un progetto attore e un progetto di interfaccia.

Il progetto attore definisce il tipo di attore e (per attori con stato) il relativo stato. Il progetto di interfaccia fornisce un'interfaccia che può essere utilizzata da altri servizi per richiamare l'attore.

Si noti che i progetti attore non contengono alcun comportamento di avvio predefinito poichè gli attori devono essere attivati da altri servizi. È possibile aggiungere un servizio affidabile o un progetto ASP.NET per creare e interagire con gli attori.

### ASP.NET 5
I modelli ASP.NET 5 forniti per le applicazioni di Infrastruttura di servizi sono quasi identici a quelli disponibili per i progetti ASP.NET 5 creati in modo indipendente. Le uniche differenze sono: - il progetto contiene una cartella **PackageRoot** per la memorizzazione del ServiceManifest con pacchetti dati e config. - Il pacchetto fa riferimento a un altro pacchetto NuGet aggiuntivo (Microsoft.ServiceFabric.AspNet.Hosting), che funge da ponte tra DNX e Infrastruttura di servizi.

## Passaggi successivi
### Aggiungere un front-end web all'applicazione
Infrastruttura di servizi fornisce l'integrazione con ASP.NET 5 per la creazione di punti di ingresso basati sul web per l'applicazione. Vedere [Aggiunta di un front-end web all'applicazione][add-web-frontend] per imparare a creare un'interfaccia REST basata su WebAPI ASP.NET.

### Creare un cluster di Azure
L’SDK di Infrastruttura di servizi fornisce un cluster locale per lo sviluppo e il test. Per creare un cluster in Azure, vedere [Configurazione di cluster dell'infrastruttura di servizi dal portale di Azure][create-cluster-in-portal]

### Pubblicazione dell'applicazione in Azure
È possibile pubblicare l'applicazione direttamente da Visual Studio in un cluster di Azure. Per ulteriori informazioni, vedere [Pubblicare l'applicazione in Azure][publish-app-to-azure].

### Visualizzare il cluster con Service Fabric Explorer
Service Fabric Explorer offre un modo semplice per la visualizzazione del cluster, tra cui le applicazioni distribuite e layout fisico. Per informazioni, vedere [Visualizzazione del cluster con Server Fabric Explorer][visualize-with-sfx].

### Effettuare il versioning e aggiornare i servizi
Infrastruttura di servizi consente il controllo indipendente delle versioni e l'aggiornamento di servizi indipendenti in un'applicazione. Per ulteriori informazioni, vedere [Effettuare il versioning e l’aggiornamento dei servizi][app-upgrade-tutorial].

### Configurare l'integrazione continua con Visual Studio Online
Per informazioni su come impostare un processo di integrazione continua per l'applicazione di Infrastruttura di servizi, vedere [Configurare l'integrazione continua con Visual Studio Online][ci-with-vso].


<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-configure-continuous-integration-with-vso.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=Nov15_HO4-->