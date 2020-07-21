---
title: Connettere Pulse Connect proteggere i dati ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati protetti con Pulse Connect a Sentinel di Azure.
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531463"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Connettere il Pulse Connect sicuro ad Azure Sentinel

Questo articolo illustra come connettere il dispositivo [Secure Pulse Connect](https://www.pulsesecure.net/products/pulse-connect-secure/) ad Azure Sentinel. Il connettore di dati protetti con Pulse Connect consente di connettere facilmente i log protetti con Pulse Connect con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Pulse Connect Secure e Azure Sentinel usa syslog.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Eseguire il rollforward dei log protetti per l'agente syslog  

Configurare Pulse Connect Secure per l'invio di messaggi syslog all'area di lavoro di Azure tramite l'agente syslog.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **Pulse Connect Secure** Connector.

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **sicurezza di Pulse Connect** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in syslog.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Pulse Connect Secure ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.