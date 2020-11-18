---
title: Connettere i dati di zScaler ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di zScaler a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655256"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connettere l'accesso a Internet di zScaler ad Azure Sentinel

Questo articolo illustra come connettere il dispositivo zScaler Internet Access ad Azure Sentinel. ZScaler Data Connector consente di connettere facilmente i log di zScaler Internet Access (ZIA) con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di zScaler in Sentinel di Azure offrirà maggiori informazioni sull'uso di Internet dell'organizzazione e migliorerà le funzionalità di sicurezza. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurare zScaler per l'invio di messaggi CEF

1. Nell'appliance zScaler è necessario impostare questi valori in modo che l'appliance invii i log necessari nel formato necessario all'agente syslog di Azure Sentinel, in base all'agente Log Analytics. È possibile modificare questi parametri nel dispositivo, purché vengano modificati anche nel daemon syslog nell'agente Sentinel di Azure.
    - Protocollo = TCP
    - Porta = 514
    - Formato = CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.
 Per altre informazioni, vedere la [Guida alla distribuzione di zScaler e Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Questa soluzione supporta syslog RFC 3164 o RFC 5424.


1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog` .
1. Continuare con il [PASSAGGIO 3: Convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere zScaler Internet Access ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.