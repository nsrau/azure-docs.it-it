---
title: Connettere i dati F5 ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati F5 ad Azure Sentinel.Learn how to connect F5 data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588264"
---
# <a name="connect-f5-to-azure-sentinel"></a>Connettere F5 ad Azure SentinelConnect F5 to Azure Sentinel

Questo articolo illustra come connettere l'appliance F5 ad Azure Sentinel.This article explains how to connect your F5 appliance to Azure Sentinel. Il connettore dati F5 consente di connettere facilmente i log F5 con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di F5 in Azure Sentinel fornirà maggiori informazioni sull'utilizzo di Internet dell'organizzazione e ne migliorerà le funzionalità operative di sicurezza. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configurare l'F5 per l'invio di messaggi CEF

1. Passare a [F5 Configurazione](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)della registrazione eventi di protezione delle applicazioni e seguire le istruzioni per configurare la registrazione remota, utilizzando le linee guida seguenti:
   - Impostare il **tipo di archiviazione remota** su **CEF**.
   - Impostare il **protocollo** su **TCP**.
   - Impostare **l'indirizzo IP** sull'indirizzo IP del server Syslog.
   - Impostare il numero di **porta** su **514**o la porta impostata dall'agente per l'utilizzo.
   - È possibile impostare la **dimensione massima** della stringa di query sulla dimensione impostata nell'agente.

1. Per utilizzare lo schema pertinente in Log `CommonSecurityLog`Analytics per gli eventi CEF, cercare .

1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect F5 to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

