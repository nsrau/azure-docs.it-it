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
   ms.date="12/06/2015"
   ms.author="seanmck"/>

# Applicazione dell'infrastruttura di servizi e fasi successive
L'applicazione dell'infrastruttura di servizi è stata creata. In questo articolo si descrive la costruzione del progetto e alcuni potenziali passaggi successivi.

## L'applicazione
Ogni nuova applicazione include un progetto di applicazione. Potrebbero esserci uno o due progetti aggiuntivi in base al tipo di servizio scelto.

### Il progetto dell'applicazione
Il progetto dell'applicazione è composto da:

- Un set di riferimenti ai servizi che costituiscono l'applicazione.

- Due profili (locale e cloud), che consentono di gestire le preferenze per l'utilizzo del progetto in ambienti diversi, ad esempio un endpoint del cluster e un'opzione per stabilire se eseguire distribuzioni di aggiornamento per impostazione predefinita.

- Due file di parametri dell'applicazione (locale e cloud), utili per gestire configurazioni dell'applicazione specifiche per ogni ambiente e che consentono, ad esempio, di indicare il numero di partizioni da creare per un servizio.

- Uno script di distribuzione che consente di distribuire l'applicazione dalla riga di comando o nell'ambito di una pipeline di integrazione continua automatica.

- Il manifesto dell'applicazione, che descrive l'applicazione.

### Reliable Services
Quando si aggiunge un nuovo Reliable Service, Visual Studio aggiunge un progetto del servizio alla soluzione. Il progetto del servizio contiene una classe che si estende da `StatelessService` o `StatefulService`, a seconda del tipo scelto.

### Reliable Actors
Quando si aggiunge un nuovo Reliable Actor, Visual Studio aggiunge due progetti alla soluzione: un progetto attore e un progetto di interfaccia.

Il progetto attore definisce il tipo di attore e (per attori con stato) il relativo stato. Il progetto di interfaccia fornisce un'interfaccia che può essere utilizzata da altri servizi per richiamare l'attore.

Si noti che i progetti attore non contengono alcun comportamento di avvio predefinito poichè gli attori devono essere attivati da altri servizi. È possibile aggiungere un servizio affidabile o un progetto ASP.NET per creare e interagire con gli attori.

### ASP.NET 5
I modelli ASP.NET 5 forniti per le applicazioni dell'infrastruttura di servizi sono quasi identici a quelli disponibili per i progetti ASP.NET 5 creati in modo indipendente. Le uniche differenze sono:

- Il progetto contiene una cartella **PackageRoot**, in cui memorizzare il file ServiceManifest insieme a pacchetti dati e di configurazione.

- Il progetto fa riferimento a un pacchetto NuGet aggiuntivo (Microsoft.ServiceFabric.AspNet.Hosting), che agisce da ponte tra DNX e Service Fabric.

## Passaggi successivi
### Aggiungere un front-end web all'applicazione
L'infrastruttura di servizi fornisce l'integrazione con ASP.NET 5 per la creazione di punti di ingresso basati sul web per l'applicazione. Vedere [Aggiungere un front-end web all'applicazione][add-web-frontend] per imparare a creare un'interfaccia REST basata su WebAPI ASP.NET.

### Creare un cluster di Azure
LSDK di Infrastruttura di servizi fornisce un cluster locale per lo sviluppo e il test. Per creare un cluster in Azure, vedere [Configurazione di cluster di infrastruttura di servizi dal portale di Azure][create-cluster-in-portal]

### Provare a distribuire in Azure gratuitamente i cluster di terze parti

Se si vuole provare a distribuire e gestire applicazioni in Azure senza configurare i propri cluster, è possibile usare il [servizio gratuito per l'uso di cluster di terze parti](http://aka.ms/tryservicefabric).

### Pubblicazione dell'applicazione in Azure
È possibile pubblicare l'applicazione direttamente da Visual Studio in un cluster di Azure. Per informazioni, vedere [Pubblicare l'applicazione in Azure][publish-app-to-azure].

### Visualizzare il cluster con Service Fabric Explorer
Service Fabric Explorer offre un modo semplice per la visualizzazione del cluster, tra cui le applicazioni distribuite e layout fisico. Per informazioni, vedere [Visualizzazione del cluster con Service Fabric Explorer][visualize-with-sfx].

### Effettuare il versioning e aggiornare i servizi
Infrastruttura di servizi consente il controllo indipendente delle versioni e l'aggiornamento di servizi indipendenti in un'applicazione. Per altre informazioni, vedere l'articolo relativo all'[esecuzione del versioning e dell'aggiornamento dei servizi][app-upgrade-tutorial].

### Configurare l'integrazione continua con Visual Studio Team Services
Per informazioni su come impostare un processo di integrazione continua per l'applicazione di Service Fabric, vedere l'articolo relativo alla [configurazione dell'integrazione continua con Visual Studio Team Services][ci-with-vso].


<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-configure-continuous-integration-with-vso.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_1217_2015-->