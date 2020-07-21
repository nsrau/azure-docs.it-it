---
title: Connettere i dati di Symantec ProxySG ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Symantec ProxySG ad Azure Sentinel.
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
ms.openlocfilehash: 51a5c5de7bce07aa6a54b9ff81e957c38cfffdd2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531522"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Connettere Symantec ProxySG ad Azure Sentinel

Questo articolo illustra come connettere il dispositivo [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) ad Azure Sentinel. Symantec ProxySG Data Connector consente di connettere facilmente i log di Symantec ProxySG con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Symantec ProxySG e Azure Sentinel usa syslog.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Inviare i log di Symantec ProxySG all'agente syslog  

Configurare Symantec ProxySG per l'invio dei messaggi syslog all'area di lavoro di Azure tramite l'agente syslog.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **Symantec ProxySG** Connector.

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **Symantec ProxySG** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in syslog.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Symantec ProxySG ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.