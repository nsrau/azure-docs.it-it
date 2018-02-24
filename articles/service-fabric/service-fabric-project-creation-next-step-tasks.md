---
title: Passaggi successivi per la creazione del progetto di Service Fabric | Microsoft Docs
description: Informazioni sul progetto di applicazione appena creato in Visual Studio.  Informazioni su come compilare servizi con le esercitazioni e altre informazioni sullo sviluppo di servizi per Service Fabric.
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
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 17eb1e7c2184fe9cae19685a47ea80716292b754
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>Applicazione dell'infrastruttura di servizi e fasi successive
L'applicazione Service Fabric di Azure è stata creata. In questo articolo vengono descritte alcune esercitazioni per provare la struttura del progetto, altre informazioni utili e possibili passaggi successivi.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Introduzione a esercitazioni, procedure ed esempi
Informazioni introduttive  

Usare l'esercitazione per l'applicazione .NET. Informazioni su come [compilare un'app](service-fabric-tutorial-create-dotnet-app.md) con un front-end ASP.NET Core e un back-end con stato, [distribuire l'applicazione](service-fabric-tutorial-deploy-app-to-party-cluster.md) in un cluster, [configurare CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), e [impostare monitoraggio e diagnostica](service-fabric-tutorial-monitoring-aspnet.md).

In alternativa, provare una delle procedure seguenti e creare il primo...
- [Servizio di Reliable Services C# su Windows](service-fabric-reliable-services-quick-start.md) 
- [Servizio di Reliable Actors C# su Windows](service-fabric-reliable-actors-get-started.md) 
- [Servizio eseguibile guest su Windows](quickstart-guest-app.md) 
- [Applicazione contenitore Windows](service-fabric-get-started-containers.md) 

Può essere utile provare anche le nostre [applicazioni di esempio](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Altre domande, commenti o suggerimenti?  È necessario segnalare un problema?
Leggere le [domande frequenti](service-fabric-common-questions.md) per trovare le risposte sulle operazioni eseguibili in Service Fabric e su come deve essere usato.

[Opzioni di supporto](service-fabric-support.md) elenca i forum su StackOverflow e MSDN per porre domande, oltre ad alcune opzioni per segnalare eventuali problemi, ottenere supporto e inviare di commenti e suggerimenti sul prodotto.

## <a name="the-application-project"></a>Il progetto dell'applicazione
Ogni nuova applicazione include un progetto di applicazione. Potrebbero essere presenti uno o due progetti aggiuntivi in base al tipo di servizio scelto.

Il progetto dell'applicazione è composto da:

* Un set di riferimenti ai servizi che costituiscono l'applicazione.
* Tre profili di pubblicazione (locale a 1 nodo, locale a 5 nodi e cloud) che consentono di gestire le preferenze per l'uso in ambienti diversi, ad esempio relative a un endpoint del cluster e alla scelta di eseguire o meno distribuzioni di aggiornamento per impostazione predefinita.
* Tre file di parametri dell'applicazione (identici a quelli riportati in precedenza), che consentono di gestire configurazioni dell'applicazione specifiche per ogni ambiente, ad esempio il numero di partizioni da creare per un servizio. Informazioni su come [configurare l'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).
* Uno script di distribuzione che consente di distribuire l'applicazione dalla riga di comando o nell'ambito di una pipeline di integrazione e distribuzione continua automatizzata. Altre informazioni sulla [distribuzione di applicazioni tramite PowerShell](service-fabric-deploy-remove-applications.md).
* Il manifesto dell'applicazione, che descrive l'applicazione. Il manifesto è disponibile nella cartella ApplicationPackageRoot. Altre informazioni sui [manifesti dell'applicazione e del servizio](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Altre informazioni sui modelli di programmazione
Service Fabric offre diversi modi per scrivere e gestire i servizi.  Ecco una panoramica e alcune informazioni di carattere generale su [servizio Reliable con stato e senza stato](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [contenitori](service-fabric-containers-overview.md), [eseguibili guest](service-fabric-deploy-existing-app.md) e [servizi ASP.NET Core con stato e senza stato](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Informazioni sulle comunicazioni di servizio
Un'applicazione di Service Fabric è costituita da servizi diversi, ognuno dei quali esegue un'attività specializzata. Questi servizi possono comunicare tra loro; possono essere presenti anche applicazioni client all'esterno del cluster che si connettono e comunicano con i servizi. Informazioni su come [configurare la comunicazione con e tra i servizi](service-fabric-connect-and-communicate-with-services.md) in Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Informazioni sulla configurazione della sicurezza dell'applicazione
È possibile proteggere le applicazioni in esecuzione nel cluster con account utente diversi. Service Fabric permette anche di proteggere le risorse usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.  Informazioni su come [configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md).

L'applicazione può contenere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale. Informazioni su come [gestire i segreti nell'applicazione](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Informazioni sul ciclo di vita dell'applicazione
Analogamente ad altre piattaforme, un'applicazione su Service Fabric in genere passa attraverso le fasi seguenti: progettazione, sviluppo, test, distribuzione, aggiornamento, manutenzione e rimozione. [Questo articolo](service-fabric-application-lifecycle.md) offre una panoramica delle interfacce API e del modo in cui vengono usate dai diversi ruoli nelle fasi del ciclo di vita di un'applicazione di Service Fabric.

## <a name="next-steps"></a>Passaggi successivi
- [Creare un cluster Windows in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualizzare il cluster, incluse le applicazioni distribuite e il layout fisico, con [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versioning e aggiornamento dei servizi](service-fabric-application-upgrade-tutorial.md)


