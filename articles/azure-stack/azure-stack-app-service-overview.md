---
title: 'Panoramica di servizio App: Stack Azure | Documenti Microsoft'
description: Panoramica di servizio App di Azure stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: fd2d355b2556faddb06acf2998b54ffcc9aa7919
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="app-service-on-azure-stack-overview"></a>Panoramica del servizio app in Azure Stack

Servizio App di Azure nello Stack di Azure è un'offerta platform-as-a-service (PaaS) di Microsoft Azure disponibili allo Stack di Azure. Il servizio consente di clienti interni o esterni - - creazione di web API e le funzioni di Azure per qualsiasi piattaforma o dispositivo le applicazioni. Possono integrare le app con applicazioni locali e automatizzare i processi di business. Gli operatori cloud di Azure Stack è possono eseguire applicazioni cliente in completamente gestite macchine virtuali (VM), con la scelta della risorse VM condivise o le macchine virtuali dedicate.

Servizio App di Azure include funzionalità per l'automatizzazione dei processi di business e il cloud le API di hosting. Come un unico servizio integrato, servizio App di Azure consente di combinare i vari componenti - siti Web, le API REST e i processi di business, in un'unica soluzione.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Perché offre il servizio App di Azure nello Stack di Azure?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:
- **Più linguaggi e altri framework**: il servizio App deve prima classe supporto per ASP.NET, Node.js, Java, PHP e Python. È anche possibile eseguire Windows PowerShell e altri script o file eseguibili in macchine virtuali di servizio App.
- **Ottimizzazione di DevOps**: configurare l'integrazione continua e distribuzione con GitHub, Git locale o BitBucket. Alzare di livello gli aggiornamenti tramite test e ambienti di gestione temporanea. Gestire le app nel servizio App usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma (CLI).
- **Integrazione di Visual Studio**: dedicati strumenti in Visual Studio semplificano il lavoro di creazione e distribuzione di applicazioni.

## <a name="app-types-in-app-service"></a>Tipi di app nel servizio app

Servizio App offre numerosi tipi di app, ognuno dei quali deve ospitare un carico di lavoro specifico:

- [Le app Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) per l'hosting di siti e applicazioni web.
- [App per le API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) per API REST per l'hosting.
- Funzioni di Azure per ospitare i carichi di lavoro senza guidata dagli eventi.

L'app word qui si riferisce alle risorse host dedicate all'esecuzione di un carico di lavoro. Prendendo ad esempio le "app Web", si è probabilmente abituati a considerare un'app Web come costituita dalle risorse di calcolo e dal codice dell'applicazione che insieme forniscono funzionalità a un browser. Ma nel servizio App un'app web le risorse di calcolo che fornisce dello Stack di Azure per ospitare il codice dell'applicazione.

L'applicazione può essere costituito da più applicazioni di servizio App di tipi diversi. Ad esempio, se l'applicazione è costituito da un front-end web e di fine di un'API RESTful nuovamente, è possibile:
- Distribuire in un'unica app Web sia il front-end che l'API
- Distribuire il codice front-end a un'app Web e il codice back-end a un'app per le API

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Informazioni sui piani di servizio app

Il provider di risorse del servizio App Usa lo stesso codice che utilizza il servizio App di Azure. Di conseguenza, alcuni concetti comuni si ritiene opportuno che descrive. Nel servizio App, il contenitore dei prezzi per le applicazioni viene chiamato il piano di servizio App. Rappresenta il set di macchine virtuali dedicate utilizzato per contenere le app. All'interno di una determinata sottoscrizione, è possibile avere più piani di servizio App.

In Azure, sono presenti processi di lavoro condivisi e dedicati. Un thread di lavoro condiviso supporta l'hosting di applicazioni multi-tenant ad alta densità ed è presente un solo set di lavoro condivisi. Server dedicati vengono utilizzati da un solo tenant e possono essere di tre dimensioni: piccole, medie e grandi dimensioni. Le esigenze degli utenti locali non possono essere descritte sempre utilizzando tali condizioni. Nel servizio App nello Stack di Azure, gli amministratori di provider di risorse possono definire piani di lavoro di che cui desidera rendere disponibili. In base alle proprie esigenze di hosting, è possibile definire più set di processi di lavoro condivise o diversi set di ruoli di lavoro dedicati. Utilizzando tali definizioni di livello di lavoro, essi possono quindi definire la propria prezzi SKU.

## <a name="portal-features"></a>Funzionalità del portale

Servizio App nello Stack di Azure Usa la stessa interfaccia utente che utilizza Azure App Service, come accade con il back-end. Alcune funzionalità sono disabilitate e non sono funzionale nello Stack di Azure. Le aspettative specifici di Azure o i servizi che richiedono tali funzionalità non sono ancora disponibili nello Stack di Azure.

## <a name="next-steps"></a>Passaggi successivi


- [Prima di iniziare con il servizio App nello Stack di Azure](azure-stack-app-service-before-you-get-started.md)
- [Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md)

È anche possibile provare altre [piattaforma come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md), ad esempio il [il provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md) e [provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md).
