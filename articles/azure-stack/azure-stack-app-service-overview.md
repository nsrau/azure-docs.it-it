---
title: Servizio App in panoramica di Azure Stack | Microsoft Docs
description: Panoramica del servizio App in Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: a638d5cdfbd3af46335cfb8e4970306534fc1c3b
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445985"
---
# <a name="app-service-on-azure-stack-overview"></a>Panoramica del servizio app in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Servizio App di Azure in Azure Stack è un'offerta di platform-as-a-service (PaaS) di Microsoft Azure disponibile in Azure Stack. Il servizio consente ai clienti interni o esterni creare web, API e funzioni di Azure delle applicazioni per qualsiasi piattaforma o dispositivo. È possibile integrare le app con applicazioni locali e automatizzare i processi aziendali. Gli operatori cloud di Azure Stack possono eseguire le app dei clienti in macchine virtuali completamente gestite, con le relative risorse condivise o le macchine virtuali dedicate desiderate.

Servizio App di Azure consente di automatizzare i processi aziendali e ospitare API cloud. Un singolo servizio integrato, servizio App di Azure consente di combinare vari componenti, ad esempio siti Web, API REST e i processi di business, in un'unica soluzione.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Perché offre il servizio App di Azure in Azure Stack?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:

- **Più linguaggi e Framework**: Servizio App ha un supporto eccellente per ASP.NET, Node. js, Java, PHP e Python. È anche possibile eseguire Windows PowerShell e altri script o eseguibili su macchine virtuali del servizio App.
- **Ottimizzazione della metodologia DevOps**: Configurare l'integrazione continua e distribuzione con l'archivio Git locale, GitHub o BitBucket. È possibile alzare di livello gli aggiornamenti tramite test e ambienti di staging e gestire le app nel servizio App usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma (CLI).
- **Integrazione di Visual Studio**: Gli strumenti dedicati in Visual Studio semplificano il processo di creazione e distribuzione di applicazioni.

## <a name="app-types-in-app-service"></a>Tipi di app nel servizio app

Servizio App offre diversi tipi di app, ognuno dei quali è progettato per ospitare un carico di lavoro specifico:

- [App Web](../app-service/overview.md) per l'hosting di siti e applicazioni web.
- [App per le API](../app-service/overview.md) per l'hosting di API REST.
- Funzioni di Azure per l'hosting aktivita typu EventDriven, i carichi di lavoro senza server.

La parola *app* fa riferimento a risorse di hosting dedicate all'esecuzione di un carico di lavoro. Prendendo *app web* ad esempio, si è probabilmente abituati a pensare di un'app web come se tale funzionalità insieme offrono un browser del codice sia le risorse di calcolo e dell'applicazione. Nel servizio App, un'app web è la risorsa di calcolo di Azure Stack offre per ospitare il codice dell'applicazione.

L'applicazione può essere composto da più App del servizio App di tipi diversi. Ad esempio, se l'applicazione è costituita da un front-end web e di fine nuovamente un'API REST, è possibile:

- Distribuire in un'unica app Web sia il front-end che l'API
- Distribuire il codice front-end a un'app Web e il codice back-end a un'app per le API

   [ ![Panoramica del servizio App con i dati di monitoraggio](media/azure-stack-app-service-overview/image01.png "Panoramica del servizio App con i dati di monitoraggio") ](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Informazioni sui piani di servizio app

Il provider di risorse del servizio App Usa lo stesso codice che Usa servizio App di Azure e quindi condivide alcuni concetti comuni. Nel servizio App, il contenitore dei prezzi per le applicazioni è denominato il *piano di servizio App*. Rappresenta il set di macchine virtuali dedicate, usato per contenere le tue app. All'interno di una determinata sottoscrizione, è possibile avere più piani di servizio App.

In Azure, sono disponibili ruoli di lavoro condivisi e dedicati. Un ruolo di lavoro condiviso supporta l'hosting di app multi-tenant ad alta densità ed è presente solo un set di ruoli di lavoro condivise. Server dedicati sono usati da un solo tenant e sono disponibili in tre dimensioni: small, medium e large. Sempre le esigenze dei clienti in locale non possono essere descritta usando questi termini. Nel servizio App in Azure Stack, gli amministratori di provider di risorse possono definire intendono rendere disponibili i livelli di lavoro. In base alle esigenze di hosting univoche, è possibile definire più set di ruoli di lavoro condivisi o diversi set di ruoli di lavoro dedicati. Usando queste definizioni di ruolo di lavoro a livelli, essi possono quindi definire la propria prezzi SKU.

## <a name="portal-features"></a>Funzionalità del portale

Servizio App in Azure Stack Usa la stessa interfaccia utente che Usa servizio App di Azure. Lo stesso accade con il back-end. Tuttavia, alcune funzionalità sono disabilitate in Azure Stack. I requisiti specifici di Azure o i servizi che richiedono tali funzionalità non sono attualmente disponibili in Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

- [Prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md)

È anche possibile provare altri [piattaforma distribuita come un servizio (PaaS) di servizi](azure-stack-tools-paas-services.md), ad esempio le [provider di risorse di SQL Server](azure-stack-sql-resource-provider-deploy.md) e il [provider di risorse MySQL](azure-stack-mysql-resource-provider-deploy.md).
