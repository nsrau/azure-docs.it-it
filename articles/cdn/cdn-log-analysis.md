---
title: Analizzare i modelli di utilizzo della rete CDN di Azure
description: Questo articolo descrive i diversi tipi di report di analisi disponibili per i prodotti della rete CDN di Azure.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483989"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizzare i modelli di utilizzo della rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è possibile monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure fornisce queste funzionalità nei modi seguenti: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Log non elaborati per la rete CDN di Azure da Microsoft
Con un profilo Microsoft standard, è possibile abilitare i log non elaborati e selezionare per eseguire lo streaming dei log in:

* Archiviazione di Azure
* Hub eventi
* Azure Log Analytics

Con Azure Log Analytics è possibile visualizzare le metriche di monitoraggio e configurare gli avvisi. 

Per altre informazioni, vedere la pagina relativa ai [log Raw http](monitoring-and-access-log.md)della rete CDN di Azure.


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analisi principale tramite i log di diagnostica di Azure

Le funzionalità di analisi di base sono disponibili per tutti gli endpoint di rete CDN per tutti i piani tariffari. I log di diagnostica di Azure consentono di esportare l'analisi principale in archiviazione di Azure, Hub eventi o log di monitoraggio di Azure. Log di monitoraggio di Azure offre una soluzione con grafici che possono essere configurati e personalizzabili dall'utente. Per altre informazioni sui log di diagnostica di Azure, vedere [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Report principali da Verizon

I profili di rete **CDN standard di Azure fornita da Verizon** o **Azure CDN Premium di Verizon** forniscono report di base. È possibile visualizzare i report principali nel portale supplementare di Verizon. I report di base di Verizon sono accessibili tramite l'opzione **Gestisci** dal portale di Azure e offrono tipi diversi di grafici e visualizzazioni. Per altre informazioni, vedere [Report principali da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Report personalizzati da Verizon

I profili della rete **CDN standard di Azure di Verizon** o **della rete CDN Premium di Azure** offrono report personalizzati. È possibile visualizzare i report personalizzati nel portale supplementare di Verizon. I report personalizzati di Verizon sono accessibili tramite l'opzione **Gestisci** dal portale di Azure. 

I report personalizzati visualizzano il numero di riscontri o di dati trasferiti per ogni CNAME perimetrale. I dati vengono raggruppati in base al codice di risposta HTTP o allo stato della cache nel periodo di tempo. Per altre informazioni, vedere [Custom Reports from Verizon](cdn-verizon-custom-reports.md) (Report personalizzati da Verizon).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Report di rete CDN Premium di Azure con tecnologia Verizon

Con **Rete CDN Premium di Azure fornita da Verizon** è possibile accedere ai seguenti report:
   * [Report HTTP avanzati](cdn-advanced-http-reports.md)
   * [Statistiche in tempo reale](cdn-real-time-stats.md)
   * [Prestazioni del nodo perimetrale della rete CDN di Azure](cdn-edge-performance.md)

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono state illustrate le diverse opzioni per i report di analisi per la rete CDN di Azure.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:

* [Che cos'è la rete CDN di Azure?](cdn-overview.md)
* [Log non elaborati HTTP della rete CDN di Azure](monitoring-and-access-log.md)
