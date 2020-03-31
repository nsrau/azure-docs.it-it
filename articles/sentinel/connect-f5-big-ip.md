---
title: Connettere i dati DI F5 BIG-IP ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati F5 BIG-IP ad Azure Sentinel.Learn how to connect F5 BIG-IP data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588281"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Collegare l'appliance F5 BIG-IP 

> [!IMPORTANT]
> Il connettore dati F5 BIG-IP in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore F5 BIG-IP consente di connettere facilmente tutti i log DiF5 BIG-IP con Azure Sentinel, per visualizzare le cartelle di lavoro, creare avvisi personalizzati e migliorare l'analisi. Ciò offre maggiori informazioni sulla rete dell'organizzazione e migliora le funzionalità di gestione della sicurezza. L'integrazione tra F5 BIG-IP e Azure Sentinel fa uso dell'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configurare e connettere F5 BIG-IP 

F5 BIG-IP può integrare ed esportare i log direttamente in Azure Sentinel.

1. Nel portale di Azure Sentinel fare clic su **Connettori dati** e selezionare **F5 BIG-IP** e quindi **Apri pagina connettore**. 
1. Per connettere il tuo F5 BIG-IP, devi inviare una dichiarazione JSON all'endpoint API del sistema. Per istruzioni su come eseguire questa operazione, vedere [Integrazione di F5 BIG-IP con Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Dalla pagina Connettore IP BIG F5 copiare l'ID area di lavoro e la chiave primaria e incollarli come indicato in [Streaming data to Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Dopo aver completato le istruzioni di F5 BIG-IP, nella pagina del connettore di Azure Sentinel vengono visualizzati i tipi di dati connessi.
1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi F5 BIG-IP, cercare **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect F5 BIG-IP to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


