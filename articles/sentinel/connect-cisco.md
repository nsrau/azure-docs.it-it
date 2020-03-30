---
title: Connettere i dati Cisco ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati Cisco ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588400"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Connettere Cisco ASA ad Azure Sentinel



Questo articolo illustra come connettere l'appliance Cisco ASA ad Azure Sentinel.This article explains how to connect your Cisco ASA appliance to Azure Sentinel. Il connettore dati Cisco ASA consente di connettere facilmente i log Cisco ASA con Azure Sentinel, per visualizzare dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Cisco ASA in Azure Sentinel fornirà maggiori informazioni sull'utilizzo di Internet dell'organizzazione e ne migliorerà le funzionalità operative di sicurezza. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Inoltrare i registri Cisco ASA all'agente Syslog

Cisco ASA non supporta CEF, pertanto i log vengono inviati come Syslog e l'agente di Azure Sentinel sa come analizzarli come se fossero log CEF. Configurare Cisco ASA per inoltrare i messaggi Syslog all'area di lavoro di Azure tramite l'agente Syslog:Configure Cisco ASA to forward Syslog messages to your Azure workspace via the Syslog agent:

1. Passare a [Inviare messaggi Syslog a un server Syslog esterno](https://aka.ms/asi-syslog-cisco-forwarding)e seguire le istruzioni per configurare la connessione. Utilizzare questi parametri quando richiesto:
    - Impostare **la porta** su 514 o sulla porta impostata nell'agente.
    - Impostare **syslog_ip** sull'indirizzo IP dell'agente.

1. Per utilizzare lo schema pertinente in `CommonSecurityLog`Log Analytics per gli eventi Cisco, cercare .

1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).




## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Cisco ASA appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


