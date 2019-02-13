---
title: Attività di valutazione di Azure Stack avanzate | Microsoft Docs
description: Questo articolo descrive le attività di valutazione avanzate Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: d00eaf0ab24fbd754ba5ad2c79e9630d69d28eb7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200514"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Attività di valutazione di Azure Stack Development Kit avanzate
Dopo che acquisite familiarità con le caratteristiche di servizio di Azure Stack Development Kit (ASDK) basic, è possibile approfondire la comprensione di Azure Stack ulteriormente verificando gli scenari più avanzati. Queste attività di valutazione più avanzate sono documentate completamente nella documentazione di Azure Stack operatore.

> [!NOTE]
> Anche se molte attività operatore sono supportate per ASDK e la produzione, le distribuzioni di Azure Stack con più nodi, non tutti gli scenari di utilizzo sono supportati per le distribuzioni ASDK. Visualizzare [ASDK e le differenze di Azure Stack con più nodi](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) per altre informazioni.

## <a name="delegate-offers-in-azure-stack"></a>Delegare offerte in Azure Stack
L'operatore di Stack di Azure, è spesso necessario inserire altri utenti responsabile della creazione di offerte e iscrizione gli utenti. Ad esempio, se sei un provider di servizi, è possibile i rivenditori a effettuare l'iscrizione ai clienti e gestirli per tuo conto. In alternativa, se si fa parte di un gruppo IT centrale in un'organizzazione, è possibile affiliate per l'iscrizione agli utenti senza l'intervento dell'utente.

[Delegare offerte in Azure Stack](../azure-stack-delegated-provider.md) aiuta con queste attività grazie alla possibilità di raggiungere e gestire più utenti rispetto alla configurazione diretta.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Rendere disponibili i database SQL per gli utenti di Azure Stack
Un operatore di Stack di Azure, è possibile creare offerte di consentono agli utenti (tenant) creare i database SQL che possono essere utilizzati con le app native del cloud, siti Web e i carichi di lavoro. Fornendo questi database personalizzati, on demand, basato sul cloud per gli utenti, è possibile salvarli tempo e risorse.

Utilizzare l'adapter di provider di risorse SQL Server per [rendere disponibili i database SQL per gli utenti di Azure Stack](../azure-stack-tutorial-sql-server.md) come un servizio di Azure Stack. Dopo aver installato il provider di risorse è connettersi a uno o più istanze di SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Rendere disponibili agli utenti di Azure Stack web e App per le API
Un operatore di Stack di Azure, è possibile creare offerte di consentono agli utenti (tenant) creare applicazioni web e funzioni di Azure e API. Fornendo l'accesso a queste App on demand, basato sul cloud per gli utenti, è possibile salvarli tempo e risorse.

Distribuire il provider di risorse del servizio App di [rendere disponibili agli utenti di Azure Stack web e App per le API](../azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'offerta di servizi con i sistemi integrati di Azure Stack](../azure-stack-offer-services-overview.md)
