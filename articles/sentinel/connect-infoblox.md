---
title: Connettere i dati di Infoblox Network Identity Operating System (NIOS) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Infoblox Network Identity Operating System (NIOS) ad Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531639"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Connettere il NIOS di Infoblox ad Azure Sentinel

Questo articolo illustra come connettere il [dispositivo Infoblox Network Identity Operating System (Nios)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ad Azure Sentinel. Il connettore dati Infoblox NIOS consente di connettere facilmente i log di Infoblox con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Infoblox NIOS e Azure Sentinel usa syslog.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Inviare i log Infoblox all'agente syslog  

Configurare Infoblox per l'invio dei messaggi syslog all'area di lavoro di Azure tramite l'agente syslog.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare connettore **Infoblox Nios** .

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina **INFOBLOX Nios** .

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in syslog.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Infoblox NIOS a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.