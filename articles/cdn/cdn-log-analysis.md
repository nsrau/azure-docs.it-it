---
title: Analizzare i modelli di utilizzo della rete CDN di Azure | Microsoft Docs
description: Questo articolo descrive i diversi tipi di report di analisi disponibili per i prodotti della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593677"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizzare i modelli di utilizzo della rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è possibile monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure fornisce queste funzionalità nei modi seguenti: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analisi principale tramite i log di diagnostica di Azure

Le funzionalità di analisi di base sono disponibili per tutti gli endpoint di rete CDN per tutti i piani tariffari. I log di diagnostica di Azure consentono l'esportazione dell'analisi di base in log di archiviazione, eventi o monitoraggio di Azure.Azure diagnostics logs allow core analytics to be exported to Azure storage, event hubs, or Azure Monitor logs. I log di Monitoraggio di Azure offrono una soluzione con grafici configurabili e personalizzabili dall'utente. Per altre informazioni sui log di diagnostica di Azure, vedere [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Report principali da Verizon

Come utente di rete CDN di Azure con un profilo di **rete CDN Standard di Azure con tecnologia Verizon** o di **rete CDN Premium di Azure con tecnologia Verizon**, è possibile visualizzare i report principali da Verizon nel portale supplementare di Verizon. I report principali di Verizon sono accessibili tramite l'opzione **Gestisci** dal portale di Azure e offrono una varietà di grafici e visualizzazioni. Per altre informazioni, vedere [Report principali da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Report personalizzati da Verizon

Come utente di rete CDN di Azure con un profilo di **rete CDN Standard di Azure con tecnologia Verizon** o di **rete CDN Premium di Azure con tecnologia Verizon**, è possibile visualizzare i report personalizzati da Verizon nel portale supplementare di Verizon. I report personalizzati Verizon sono accessibili tramite l'opzione Gestisci dal portale di Azure.Verizon custom reports is accessible via the **Manage** option from the Azure portal. La pagina dei report personalizzati da Verizon riporta il numero di riscontri o di dati trasferiti per ogni CNAME periferico appartenente a un profilo di rete CDN di Azure. I dati possono essere raggruppati per stato della cache o codice di risposta HTTP in qualsiasi periodo di tempo. Per altre informazioni, vedere [Custom Reports from Verizon](cdn-verizon-custom-reports.md) (Report personalizzati da Verizon).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Report di rete CDN Premium di Azure con tecnologia Verizon

Con **Rete CDN Premium di Azure fornita da Verizon** è possibile accedere ai seguenti report:
   * [Report HTTP avanzati](cdn-advanced-http-reports.md)
   * [Statistiche in tempo reale](cdn-real-time-stats.md)
   * [Prestazioni del nodo perimetrale](cdn-edge-performance.md)

