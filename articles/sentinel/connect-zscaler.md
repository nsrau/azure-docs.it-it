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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587992"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connettere l'accesso a Internet di zScaler ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati zScaler in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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


1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.
1. Continuare con il [passaggio 3: convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere zScaler Internet Access ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.


