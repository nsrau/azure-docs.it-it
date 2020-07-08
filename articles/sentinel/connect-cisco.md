---
title: Connettere i dati Cisco ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere il dispositivo Cisco ASA ad Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi.
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
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85566035"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Connettere Cisco ASA ad Azure Sentinel



Questo articolo illustra come connettere l'appliance Cisco ASA ad Azure Sentinel. Cisco ASA Data Connector consente di connettere facilmente i log Cisco ASA con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Cisco ASA in Azure Sentinel ti offre informazioni più dettagliate sull'uso di Internet dell'organizzazione e ne migliora le funzionalità di sicurezza. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Inviare i log Cisco ASA all'agente syslog

Cisco ASA non supporta CEF, quindi i log vengono inviati come syslog e l'agente Sentinel di Azure sa come analizzarli come se fossero log CEF. Configurare Cisco ASA per l'invio dei messaggi syslog all'area di lavoro di Azure tramite l'agente syslog:

1. Passare a [inviare messaggi syslog a un server syslog esterno](https://aka.ms/asi-syslog-cisco-forwarding)e seguire le istruzioni per configurare la connessione. Usare questi parametri quando richiesto:
    - Impostare la **porta** su 514 o la porta impostata nell'agente.
    - Impostare **syslog_ip** sull'indirizzo IP dell'agente.

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi Cisco, cercare `CommonSecurityLog` .

1. Continuare con il [PASSAGGIO 3: Convalidare la connettività](connect-cef-verify.md).




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance Cisco ASA ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


