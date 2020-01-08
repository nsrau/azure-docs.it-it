---
title: Connettere i dati F5 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati F5 a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: cf6dc6977ff066b646beac4db5562ae8d97ab066
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610557"
---
# <a name="connect-f5-to-azure-sentinel"></a>Connettere F5 ad Azure Sentinel

Questo articolo illustra come connettere il dispositivo F5 a Sentinel di Azure. F5 Data Connector consente di connettere facilmente i log F5 con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di F5 in Sentinel di Azure offrirà un maggior numero di informazioni sull'uso di Internet dell'organizzazione e migliorerà le sue funzionalità di sicurezza. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configurare F5 per l'invio di messaggi CEF

1. Passare a [F5 configurazione della registrazione degli eventi di sicurezza dell'applicazione](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)e seguire le istruzioni per configurare la registrazione remota, usando le linee guida seguenti:
   - Impostare il **tipo di archiviazione remota** su **CEF**.
   - Impostare il **protocollo** su **TCP**.
   - Impostare l' **indirizzo IP** sull'indirizzo IP del server syslog.
   - Impostare il **numero di porta** su **514**o la porta impostata per l'uso da parte dell'agente.
   - È possibile impostare le **dimensioni massime della stringa di query** sulle dimensioni impostate nell'agente.

1. Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

1. Continuare con il [passaggio 3: convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere F5 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

