---
title: 'Panoramica di servizio App: Stack Azure | Documenti Microsoft'
description: Panoramica di servizio App di Azure stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 42258a352b05d97341f20c13bf837e1398dd8979
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099963"
---
# <a name="app-service-on-azure-stack-overview"></a>Panoramica del servizio app in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Servizio App di Azure nello Stack di Azure è un'offerta platform-as-a-service (PaaS) di Microsoft Azure disponibili allo Stack di Azure. Il servizio consente i clienti - interni o esterni - creazione di web API e le funzioni di Azure le applicazioni per qualsiasi piattaforma o dispositivo. Possono integrare le app con applicazioni locali e automatizzare i processi di business. Gli operatori cloud di Azure Stack possono eseguire App cliente nel completamente gestite macchine virtuali (VM), con preferiscono risorse VM condivise o le macchine virtuali dedicate.

Servizio App di Azure consente di automatizzare i processi di business e le API di cloud di hosting. Come singolo servizio integrato, Azure App Service consente di combinare i vari componenti: siti Web, le API REST e i processi di business - in un'unica soluzione.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Perché offre Azure App Service nello Stack di Azure?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:

- **Più linguaggi e altri framework**: il servizio App deve prima classe supporto per ASP.NET, Node. js, Java, PHP e Python. È anche possibile eseguire Windows PowerShell e altri script o file eseguibili nelle macchine virtuali di servizio App.
- **Ottimizzazione DevOps**: impostare integrazione continua e distribuzione con GitHub, Git locale o BitBucket. Alzare di livello gli aggiornamenti tramite test e ambienti di gestione temporanea. Gestire le app nel servizio App usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma (CLI).
- **Integrazione di Visual Studio**: dedicati strumenti in Visual Studio semplificano il lavoro di creazione e distribuzione di applicazioni.

## <a name="app-types-in-app-service"></a>Tipi di app nel servizio app

Servizio App offre numerosi tipi di app, ognuno dei quali è destinata a ospitare un carico di lavoro specifico:

- [Le app Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) per l'hosting di siti e applicazioni web.
- [App per le API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) per API REST per l'hosting.
- Funzioni di Azure per l'hosting basate su eventi, i carichi di lavoro senza server.

L'app di word fa riferimento alle risorse host dedicate all'esecuzione di un carico di lavoro. Prendendo ad esempio le "app Web", si è probabilmente abituati a considerare un'app Web come costituita dalle risorse di calcolo e dal codice dell'applicazione che insieme forniscono funzionalità a un browser. Ma nel servizio App di un'app web le risorse di calcolo offerti dello Stack di Azure per ospitare il codice dell'applicazione.

L'applicazione può essere composta da più applicazioni di servizio App di tipi diversi. Ad esempio, se l'applicazione è composta da un front-end web e di fine nuovamente un'API RESTful, è possibile:

- Distribuire in un'unica app Web sia il front-end che l'API
- Distribuire il codice front-end a un'app Web e il codice back-end a un'app per le API

   ![Panoramica di servizio App con i dati di monitoraggio](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Informazioni sui piani di servizio app

Il provider di risorse di servizio App utilizza lo stesso codice che usa Azure App Service. Di conseguenza, alcuni concetti comuni sono la pena che descrive. Nel servizio App, il contenitore dei prezzi per le applicazioni viene chiamato il piano di servizio App. Rappresenta il set di macchine virtuali dedicate utilizzato per contenere le app. All'interno di una determinata sottoscrizione, è possibile avere più piani di servizio App.

In Azure, sono presenti processi di lavoro condivisi e dedicati. Un thread di lavoro condiviso supporta l'hosting di applicazioni multi-tenant e ad alta densità ed è presente solo un set di processi di lavoro condivise. Utilizzate dal un solo tenant server dedicati e sono disponibili in tre dimensioni: small, medium e grandi dimensioni. Le esigenze dei clienti in locale non possono essere descritta sempre utilizzando tali condizioni. Nel servizio App nello Stack di Azure, gli amministratori di provider di risorse possono definire i piani di lavoro, da rendere disponibile. In base alle proprie esigenze di hosting univoche, è possibile definire più set di processi di lavoro condivise o diversi set di processi di lavoro dedicati. Utilizzando tali definizioni di ruolo di lavoro a livello, essi possono quindi definire la propria prezzi SKU.

## <a name="portal-features"></a>Funzionalità del portale

Servizio App nello Stack di Azure Usa la stessa interfaccia utente che utilizza Azure App Service, lo stesso vale nel back-end. Tuttavia, alcune funzionalità sono disabilitate e non sono funzionale nello Stack di Azure. Le aspettative specifici di Azure o i servizi che richiedono tali funzionalità non sono attualmente disponibili nello Stack di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Prima di iniziare a con il servizio App nello Stack di Azure](azure-stack-app-service-before-you-get-started.md)
- [Installare il provider di risorse di servizio App](azure-stack-app-service-deploy.md)

È anche possibile provare altre [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md), ad esempio il [provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md) e il [provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md).
