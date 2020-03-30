---
title: Connettere i dati di scaler a Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di scalare a Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587992"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connettere l'accesso a Internet con scaler ad Azure SentinelConnect sscaler Internet Access to Azure Sentinel

> [!IMPORTANT]
> Il connettore dati di scalare z in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questo articolo viene illustrato come connettere l'appliance di accesso a Internet con scalare all'area di Azure Sentinel. Il connettore dati con scalare la barra verticale consente di connettere facilmente i log di accesso a Internet con Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di scalare in Azure Sentinel fornirà maggiori informazioni sull'utilizzo di Internet dell'organizzazione e ne migliorerà le funzionalità operative di sicurezza. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurare la scalare per l'invio di messaggi CEF

1. Nell'appliance di scala che è necessario impostare questi valori in modo che l'appliance invii i log necessari nel formato necessario all'agente Syslog di Azure Sentinel, in base all'agente log Analytics. È possibile modificare questi parametri nell'appliance, purché vengano modificati anche nel daemon Syslog nell'agente di Azure Sentinel.You can modify these parameters in your appliance, as long as you also modify them in the Syslog daemon on the Azure Sentinel agent.
    - Protocollo - TCP
    - Porta - 514
    - Formato: CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.
 Per altre informazioni, vedere la Guida alla distribuzione di [Azure Sentinel e di Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Questa soluzione supporta Syslog RFC 3164 o RFC 5424.


1. Per utilizzare lo schema pertinente in Log `CommonSecurityLog`Analytics per gli eventi CEF, cercare .
1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come connettere l'accesso A Internet ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


