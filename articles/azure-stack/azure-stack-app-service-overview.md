---
title: 'Panoramica del servizio App: Azure Stack | Microsoft Docs'
description: Panoramica del servizio App in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079158"
---
# <a name="app-service-on-azure-stack-overview"></a>Panoramica del servizio app in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Servizio App di Azure in Azure Stack è un'offerta di platform-as-a-service (PaaS) di Microsoft Azure disponibile in Azure Stack. Il servizio consente a clienti - interni o esterni - creazione di web API e funzioni di Azure delle applicazioni per qualsiasi piattaforma o dispositivo. Possono integrare le tue App con le applicazioni locali e automatizzare i processi di business. Gli operatori cloud di Azure Stack è possono eseguire app di clienti in macchine virtuali (VM), completamente gestite con le scelte di risorse delle macchine Virtuali condivise o le macchine virtuali dedicate.

Servizio App di Azure consente di automatizzare i processi di business e l'API di hosting cloud. Un singolo servizio integrato, servizio App di Azure consente di combinare vari componenti: siti Web, API RESTful e processi di business, in un'unica soluzione.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Perché offre il servizio App di Azure in Azure Stack?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:

- **Più linguaggi e Framework**: servizio App ha un supporto eccellente per ASP.NET, Node. js, Java, PHP e Python. È anche possibile eseguire Windows PowerShell e altri script o eseguibili su macchine virtuali del servizio App.
- **Ottimizzazione della metodologia DevOps**: configurare l'integrazione continua e la distribuzione Git locale, GitHub o BitBucket. Alzare di livello gli aggiornamenti tramite test e ambienti di staging. Gestire le app nel servizio App usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma (CLI).
- **Integrazione di Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di creazione e distribuzione di applicazioni.

## <a name="app-types-in-app-service"></a>Tipi di app nel servizio app

Servizio App offre diversi tipi di app, ognuno dei quali è progettato per ospitare un carico di lavoro specifico:

- [App Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) per l'hosting di siti e applicazioni web.
- [App per le API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) per l'hosting di API RESTful.
- Funzioni di Azure per l'hosting aktivita typu EventDriven, i carichi di lavoro senza server.

L'app di word qui fa riferimento alle risorse di hosting dedicate all'esecuzione di un carico di lavoro. Prendendo ad esempio le "app Web", si è probabilmente abituati a considerare un'app Web come costituita dalle risorse di calcolo e dal codice dell'applicazione che insieme forniscono funzionalità a un browser. Ma nel servizio App un'app web sia le risorse di calcolo di Azure Stack offre per ospitare il codice dell'applicazione.

L'applicazione può essere composto da più App del servizio App di tipi diversi. Ad esempio, se l'applicazione è costituita da un front-end web e un'API RESTful nuovamente terminata, è possibile:

- Distribuire in un'unica app Web sia il front-end che l'API
- Distribuire il codice front-end a un'app Web e il codice back-end a un'app per le API

   ![Panoramica del servizio App con i dati di monitoraggio](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Informazioni sui piani di servizio app

Il provider di risorse del servizio App Usa lo stesso codice che Usa servizio App di Azure. Di conseguenza, alcuni concetti comuni sono la pena descrivere. Nel servizio App, il contenitore dei prezzi per le applicazioni è denominato piano di servizio App. Rappresenta il set di macchine virtuali dedicate, usato per contenere le tue app. All'interno di una determinata sottoscrizione, è possibile avere più piani di servizio App.

In Azure, sono disponibili ruoli di lavoro condivisi e dedicati. Un ruolo di lavoro condiviso supporta l'hosting di app multi-tenant ad alta densità ed è presente solo un set di ruoli di lavoro condivise. Server dedicati sono usati da un solo tenant e sono disponibili in tre dimensioni: small, medium e large. Sempre le esigenze dei clienti in locale non possono essere descritta usando questi termini. Nel servizio App in Azure Stack, gli amministratori di provider di risorse possono definire intendono rendere disponibili i livelli di lavoro. In base alle esigenze di hosting univoche, è possibile definire più set di ruoli di lavoro condivisi o diversi set di ruoli di lavoro dedicati. Usando queste definizioni di ruolo di lavoro a livelli, essi possono quindi definire la propria prezzi SKU.

## <a name="portal-features"></a>Funzionalità del portale

Servizio App in Azure Stack Usa la stessa interfaccia utente che Usa servizio App di Azure, lo stesso accade con il back-end. Tuttavia, alcune funzionalità sono disattivate e non sono funzionale in Azure Stack. I requisiti specifici di Azure o i servizi che richiedono tali funzionalità non sono attualmente disponibili in Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

- [Prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md)

È anche possibile provare altri [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md), ad esempio le [provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md) e il [provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md).
