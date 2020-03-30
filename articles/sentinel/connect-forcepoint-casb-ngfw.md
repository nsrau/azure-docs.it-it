---
title: Connettere i prodotti Forcepoint ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i prodotti Forcepoint ad Azure Sentinel.Learn how to connect Forcepoint products to Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588230"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Connettere i prodotti Forcepoint ad Azure SentinelConnect your Forcepoint products to Azure Sentinel

> [!IMPORTANT]
> Il connettore dati dei prodotti Forcepoint in Azure Sentinel è attualmente in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questo articolo illustra come connettere i prodotti Forcepoint ad Azure Sentinel.This article explains how to connect your Forcepoint products to Azure Sentinel. 

I connettori dati Forcepoint consentono di connettere i log Forcepoint Cloud Access Security Broker e Forcepoint Next Generation Firewall con Azure Sentinel. In questo modo è possibile esportare automaticamente i log definiti dall'utente in Azure Sentinel in tempo reale. Il connettore offre una visibilità ottimizzata per le attività degli utenti registrate dai prodotti Forcepoint. Consente inoltre un'ulteriore correlazione con i dati dei carichi di lavoro di Azure e di altri feed e migliora la funzionalità di monitoraggio con le cartelle di lavoro all'interno di Azure Sentinel.It also enables further correlation with data from Azure workloads and other feeds, and improves monitoring capability with Workbooks inside Azure Sentinel.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Inoltrare i registri dei prodotti di Forcepoint all'agente Syslog 

Configurare il prodotto Forcepoint per inoltrare i messaggi Syslog in formato CEF all'area di lavoro di Azure tramite l'agente Syslog.

1. Configurare il prodotto Forcepoint per l'integrazione di Azure Sentinel come descritto nelle guide all'installazione seguenti:Set up the Forcepoint product to Azure Sentinel integration as described in the following installation guides:
 - [Guida all'integrazione di Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Guida all'integrazione di Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Cercare CommonSecurityLog per utilizzare lo schema pertinente in Log Analytics con DeviceVendor nome contiene 'Forcepoint'. 

3. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).



## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect Forcepoint products to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).

- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.