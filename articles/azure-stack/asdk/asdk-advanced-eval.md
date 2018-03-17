---
title: "Attività di valutazione di Azure Stack avanzate | Documenti Microsoft"
description: "In questo articolo descrive le attività di valutazione avanzate dello Stack di Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Attività di valutazione avanzate Kit di sviluppo di Azure Stack
Una volta acquisite una certa familiarità con le funzionalità di servizio basic Azure Stack Development Kit (ASDK), è possibile approfondire la comprensione dello Stack Azure ulteriormente da testare gli scenari più avanzati. Queste attività di valutazione più avanzate sono documentate completamente nella documentazione di Azure Stack operatore.

> [!NOTE]
> Anche se molte attività di operatore sono supportate per ASDK e produzione, le distribuzioni di Azure Stack a più nodi, non tutti gli scenari di utilizzo sono supportati per le distribuzioni ASDK. Vedere [ASDK e le differenze dello Stack di Azure multi-node](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) per altre informazioni.

## <a name="delegate-offers-in-azure-stack"></a>Delegare offerte in Azure Stack
Come l'operatore di Stack di Azure, è spesso necessario inserire altri utenti responsabile della creazione di offerte e iscrizione gli utenti. Ad esempio, se si è un provider di servizi, è possibile rivenditori per iscriversi a clienti e gestirli per conto dell'utente. O se si fa parte di un gruppo IT centrale in un'organizzazione, potrebbe essere controllate da effettuare l'iscrizione gli utenti senza l'intervento dell'utente.

[Delega dell'offerta in Azure Stack](.\.\azure-stack-delegated-provider.md) consente di queste attività grazie alla possibilità di accedere e gestire più utenti, è possibile direttamente. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti dello Stack di Azure SQL database
Un operatore di Stack di Azure, è possibile creare offerte che consentono agli utenti di database SQL che possono essere utilizzati con le app cloud nativo, i siti Web e i carichi di lavoro di creare (tenant). Grazie agli utenti i database personalizzati, su richiesta, basato su cloud, è possibile salvarli tempo e risorse. 

Utilizzare l'adapter di provider di risorse SQL Server per [rendere disponibili agli utenti dello Stack di Azure SQL database](.\.\azure-stack-tutorial-sql-server.md) come servizio dello Stack di Azure. Dopo aver installato il provider di risorse è connettersi a una o più istanze di SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti di Azure Stack App web e API
Un operatore di Stack di Azure, è possibile creare offerte che consentono agli utenti di Azure funzioni e API di applicazioni web di creare (tenant). Fornendo l'accesso a queste App su richiesta, basato su cloud agli utenti, è possibile salvarli tempo e risorse.

Distribuire il provider di risorse di servizio App a [rendere web e App per le API disponibili agli utenti di Azure Stack](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sull'offerta di servizi con i sistemi Azure Stack integrato](.\.\azure-stack-offer-services-overview.md)