---
title: Passaggi successivi per la creazione del progetto di Service Fabric | Microsoft Docs
description: "In questo articolo vengono forniti collegamenti a un set di attività di sviluppo principali per Infrastruttura di servizi"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: e04f9e57c65da42da73a5ee6a0b601dcbb318aaa
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>Applicazione dell'infrastruttura di servizi e fasi successive
L'applicazione Service Fabric di Azure è stata creata. In questo articolo si descrive la costruzione del progetto e alcuni potenziali passaggi successivi.

## <a name="your-application"></a>L'applicazione
Ogni nuova applicazione include un progetto di applicazione. Potrebbero essere presenti uno o due progetti aggiuntivi in base al tipo di servizio scelto.

### <a name="the-application-project"></a>Il progetto dell'applicazione
Il progetto dell'applicazione è composto da:

* Un set di riferimenti ai servizi che costituiscono l'applicazione.
* Tre profili di pubblicazione (locale a 1 nodo, locale a 5 nodi e cloud) che consentono di gestire le preferenze per l'uso in ambienti diversi, ad esempio relative a un endpoint del cluster e alla scelta di eseguire o meno distribuzioni di aggiornamento per impostazione predefinita.
* Tre file di parametri dell'applicazione (identici a quelli riportati in precedenza), che consentono di gestire configurazioni dell'applicazione specifiche per ogni ambiente, ad esempio il numero di partizioni da creare per un servizio.
* Uno script di distribuzione che consente di distribuire l'applicazione dalla riga di comando o nell'ambito di una pipeline di integrazione e distribuzione continua automatizzata.
* Il manifesto dell'applicazione, che descrive l'applicazione. Il manifesto è disponibile nella cartella ApplicationPackageRoot.

### <a name="stateless-service"></a>Servizio senza stato
Quando si aggiunge un nuovo servizio senza stato, Visual Studio aggiunge alla soluzione un progetto del servizio, che include un tipo proveniente da `StatelessService`. Il servizio incrementa una variabile locale in un contatore.

### <a name="stateful-service"></a>Servizio con stato
Quando si aggiunge un nuovo servizio con stato, Visual Studio aggiunge alla soluzione un progetto del servizio, che include un tipo proveniente da `StatefulService`. Il servizio incrementa un contatore nel relativo metodo `RunAsync` e archivia il risultato in un `ReliableDictionary`.

### <a name="actor-service"></a>Servizio Actor
Quando si aggiunge un nuovo Reliable Actor, Visual Studio aggiunge due progetti alla soluzione: un progetto attore e un progetto interfaccia.

Il progetto attore fornisce metodi per l'impostazione e il recupero del valore di un contatore che viene mantenuto in modo affidabile all'interno dello stato dell'attore. Il progetto di interfaccia fornisce un'interfaccia che può essere utilizzata da altri servizi per richiamare l'attore.

### <a name="stateless-web-api"></a>API Web senza stato
Il progetto API Web senza stato fornisce un servizio Web di base che è possibile usare per aprire l'applicazione ai client esterni. Per altre informazioni su come è strutturato il progetto, vedere [Introduzione ai servizi API Web di Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md).


### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric SDK offre lo stesso set di modelli ASP.NET Core disponibile per i progetti ASP.NET Core autonomi: modello vuoto, [API Web][aspnet-webapi] e [applicazione Web][aspnet-webapp].

### <a name="guest-executables-and-guest-containers"></a>Eseguibili guest e contenitori guest

Un "guest" di Service Fabric è un servizio che non è creato con i modelli di programmazione della piattaforma. È possibile comprimere i file binari per un guest [direttamente nel pacchetto dell'applicazione](service-fabric-deploy-existing-app.md) o [tramite un'immagine del contenitore](service-fabric-deploy-container.md). Visual Studio crea in entrambi i casi gli elementi necessari nella cartella **ApplicationPackageRoot** del progetto dell'applicazione. Visual Studio non creerà un nuovo progetto di servizio perché il codice esiste già in una qualche posizione. Se si desidera gestire i progetti guest insieme al progetto dell'applicazione di Service Fabric, è possibile aggiungerli alla soluzione di Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
### <a name="create-an-azure-cluster"></a>Creare un cluster di Azure
LSDK di Infrastruttura di servizi fornisce un cluster locale per lo sviluppo e il test. Per creare un cluster in Azure, vedere [Creare un cluster di Service Fabric in Azure tramite il portale di Azure][create-cluster-in-portal].

### <a name="publish-your-application-to-azure"></a>Pubblicazione dell'applicazione in Azure
È possibile pubblicare l'applicazione direttamente da Visual Studio in un cluster di Azure. Per informazioni, vedere [Publishing your application to Azure][publish-app-to-azure] (Pubblicare l'applicazione in Azure).

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Visualizzare il cluster con Service Fabric Explorer
Service Fabric Explorer offre un modo semplice per la visualizzazione del cluster, tra cui le applicazioni distribuite e il layout fisico. Per altre informazioni, vedere [Visualizzare il cluster con Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Effettuare il versioning e aggiornare i servizi
Service Fabric consente il controllo indipendente delle versioni e l'aggiornamento di servizi indipendenti in un'applicazione. Per altre informazioni, vedere [Versioning and upgrading your services][app-upgrade-tutorial] (Controllo delle versione e aggiornamento dei servizi).

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurare l'integrazione continua con Visual Studio Team Services
Per informazioni su come impostare un processo di integrazione continua per l'applicazione di Service Fabric, vedere [Configurare l'integrazione e la distribuzione continue di Service Fabric con Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-manage-application-in-visual-studio.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
