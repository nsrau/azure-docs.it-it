---
title: Connettere gli avvisi di Orca ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati degli avvisi di sicurezza di Orca ad Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531711"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Connettere gli avvisi Orca ad Azure Sentinel 

Il connettore di avvisi di sicurezza di Orca consente di portare facilmente gli avvisi della soluzione di sicurezza degli avvisi [Orca](https://orca.security/) in Sentinel di Azure, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra gli avvisi di sicurezza di Orca e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Configurare e connettere gli avvisi di sicurezza di Orca

Gli avvisi di sicurezza di Orca possono integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel, fare clic su **connettori dati** e selezionare gli **avvisi di sicurezza di Orca** , quindi aprire la pagina del **connettore**.

2. Vedere https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration per completare l'integrazione dalla piattaforma Orca.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in **CustomLogs** nella tabella **OrcaAlerts_CL** .
Per utilizzare lo schema pertinente in Log Analytics per gli avvisi Orca, cercare `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Convalidare la connettività
Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere gli avvisi di sicurezza di Orca ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

