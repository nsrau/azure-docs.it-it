---
title: 'Implementazioni dei clienti del database SQL di Azure: dettagli tecnici | Microsoft Docs'
description: Dettagli tecnici sulle implementazioni dei clienti del database SQL di Azure per risolvere i problemi aziendali
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b05b24bf9a14570b07c2c1ab3076835c7167b647
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017


---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Studi tecnici sulle implementazioni dei clienti del database SQL di Azure

- [Daxko](sql-database-implementation-daxko.md): Daxko/CSI Software ha affrontato una sfida. La base di clienti dei centri fitness e di ricreazione ha registrato un aumento rapido grazie alla soluzione software aziendale onnicomprensiva. Il supporto delle esigenze dell'infrastruttura IT per la crescita della base di clienti metteva tuttavia a dura prova lo staff IT aziendale. La società era sempre più vincolata alle crescenti spese operative, in particolare per la gestione dei database in continua crescita. Ancor peggio, le spese operative venivano tagliate dalle risorse di sviluppo impegnate su nuove iniziative, tra cui le funzionalità di mobilità per il software aziendale.

- [GEP](sql-database-implementation-gep.md): GEP offre software e servizi che consentono ai responsabili dell'approvvigionamento in tutto il mondo di massimizzare l'impatto sulle operazioni, sulle strategie e sulle prestazioni finanziarie dell'azienda. Oltre ai servizi gestiti e di consulenza, l'azienda offre SMART by GEP®, una piattaforma software completa per l'approvvigionamento basata su cloud. GEP ha tuttavia riscontrato problemi nel tentativo di supportare soluzioni come SMART by GEP con il proprio data center locale: sono stati necessari importanti investimenti e i requisiti normativi in altri paesi hanno reso gli investimenti necessari un ostacolo in più. Con la migrazione al cloud, GEP ha liberato risorse IT che potevano trascurare le operazioni IT e concentrarsi maggiormente sullo sviluppo di nuove fonti di valore per i clienti in tutto il mondo.

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart produce rinomati software di gestione aziendale e finanziaria per le piccole e medie imprese nei Paesi Bassi. I 55.000 clienti sono serviti da un organico composto da 110 dipendenti, tra cui 35 esperti del reparto IT. Grazie al passaggio dal software desktop a un'offerta Software come servizio (SaaS) compilata in Azure, SnelStart ha sfruttato al massimo i servizi incorporati, l'automazione della gestione tramite l'ambiente familiare in C# e l'ottimizzazione delle prestazioni e della scalabilità con provisioning eccessivo o insufficiente delle aziende, usando pool elastici. L'uso di Azure garantisce a SnelStart fluidità nello spostamento dei clienti tra ambiente locale e cloud.

- [Umbraco](sql-database-implementation-umbraco.md): per semplificare le distribuzioni ai clienti, Umbraco ha aggiunto Umbraco-as-a-Service (UaaS), un'offerta di Software-as-a-Service (SaaS) che esclude la necessità di distribuzioni locali, fornisce un ridimensionamento predefinito ed elimina il sovraccarico di gestione, consentendo agli sviluppatori di concentrarsi sull'innovazione dei prodotti invece che sulla gestione della soluzione. Umbraco offre tutti questi vantaggi basandosi sul modello Platform-as-a-Service (PaaS) flessibile offerto da Microsoft Azure.
