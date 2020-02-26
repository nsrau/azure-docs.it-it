---
title: Connettere Forcepoint DLP ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Forcepoint DLP a Sentinel di Azure.
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588247"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Connettere il Forcepoint DLP ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati di prevenzione della perdita dei dati di Forcepoint in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Il connettore DLP Forcepoint consente di esportare automaticamente i dati degli eventi imprevisti DLP in Sentinel di Azure. È possibile usarlo per ottenere la visibilità delle attività degli utenti e degli eventi imprevisti per la perdita di dati. Consente anche le correlazioni con i dati dei carichi di lavoro di Azure e di altri feed e migliora la funzionalità di monitoraggio con le cartelle di lavoro all'interno di Sentinel di Azure.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Configurare e connettere Forcepoint DLP

Configurare Forcepoint DLP per l'invio dei dati degli eventi imprevisti in formato JSON nell'area di lavoro di Azure tramite l'API REST, come illustrato nella [Guida all'integrazione di FORCEPOINT DLP](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Trovare i dati

Dopo aver configurato il connettore DLP Forcepoint, i dati vengono visualizzati in Log Analytics in CustomLogs **ForcepointDLPEvents_CL**.


Per usare lo schema pertinente in Log Analytics per Forcepoint DLP, cercare **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Forcepoint DLP ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.