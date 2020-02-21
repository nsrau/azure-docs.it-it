---
title: Connettere i prodotti Forcepoint ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i prodotti Forcepoint a Sentinel di Azure.
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: 66b6b7e7c9b3fec1a6252077519710452f37312a
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500965"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Connetti i tuoi prodotti Forcepoint ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati di prodotti Forcepoint in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questo articolo illustra come connettere i prodotti Forcepoint a Sentinel di Azure. 

I connettori di dati Forcepoint consentono di connettere Forcepoint cloud Access Security broker e Forcepoint log del firewall di nuova generazione con Sentinel di Azure. In questo modo è possibile esportare automaticamente i log definiti dall'utente in Sentinel di Azure in tempo reale. Il connettore offre visibilità arricchita delle attività degli utenti registrate dai prodotti Forcepoint. Consente anche una maggiore correlazione con i dati dei carichi di lavoro di Azure e di altri feed e migliora la funzionalità di monitoraggio con le cartelle di lavoro all'interno di Sentinel di Azure.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Inviare i log del prodotto Forcepoint all'agente syslog 

Configurare il prodotto Forcepoint per l'invio dei messaggi syslog in formato CEF nell'area di lavoro di Azure tramite l'agente syslog.

1. Configurare il prodotto Forcepoint nell'integrazione di Sentinel di Azure, come descritto nelle guide all'installazione seguenti:
 - [Guida all'integrazione di Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Guida all'integrazione di Forcepoint nuova generazione](https://frcpnt.com/ngfw-sentinel)

2. Cercare CommonSecurityLog per usare lo schema pertinente in Log Analytics con il nome DeviceVendor contiene ' Forcepoint '. 

3. Continuare con il [passaggio 3: convalidare la connettività](connect-cef-verify.md).



## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere i prodotti Forcepoint ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).

- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.