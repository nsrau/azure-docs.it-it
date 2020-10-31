---
title: Connettere i dati di Citrix WAF ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati Citrix WAF per eseguire il pull dei log in Sentinel di Azure. Visualizzare i dati di Citrix WAF nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102979"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Connettere Citrix WAF ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati Citrix Web Application Firewall (WAF) in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come connettere il dispositivo Citrix Web Application Firewall (WAF) a Sentinel di Azure. Il connettore di dati Citrix WAF consente di connettere facilmente i log Citrix WAF con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Connettendo i log di Citrix WAF CEF ad Azure Sentinel, è possibile sfruttare le funzionalità di ricerca, correlazione, avviso e Intelligence per le minacce per ogni log.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Inviare i log Citrix WAF all'agente syslog  

Citrix WAF invia i messaggi syslog in formato CEF a un server di inoltramento di log basato su Linux (che esegue rsyslog o syslog-ng) con l'agente di Log Analytics installato, che inoltra i log all'area di lavoro di Azure Sentinel.

1. Se non si dispone di un server di inoltri di log, vedere [queste istruzioni](connect-cef-agent.md) per ottenerne uno operativo.

1. Seguire le istruzioni fornite da Citrix per [configurare il WAF](https://support.citrix.com/article/CTX234174), [configurare la registrazione CEF](https://support.citrix.com/article/CTX136146)e [configurare l'invio dei log al server d'inoltro di log](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Assicurarsi di inviare i log alla porta TCP 514 nell'indirizzo IP del computer in cui è installato.

1. Convalidare la connessione e verificare l'inserimento dei dati seguendo [queste istruzioni](connect-cef-verify.md). Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** , nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Per eseguire una query sui log di Citrix WAF in Log Analytics, immettere nella `CommonSecurityLog` parte superiore della finestra della query.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Citrix WAF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.