---
title: Connect Check Point data to Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati del punto di controllo ad Azure Sentinel.Learn how to connect Check Point data to Azure Sentinel.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588417"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Punto di controllo di connessione ad Azure SentinelConnect Check Point to Azure Sentinel



Questo articolo illustra come connettere l'appliance Check Point ad Azure Sentinel.This article explains how to connect your Check Point appliance to Azure Sentinel. Il connettore dati Check Point consente di connettere facilmente i log del punto di controllo con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Check Point in Azure Sentinel ti fornirà maggiori informazioni sull'utilizzo di Internet della tua organizzazione e ne migliorerà le capacità operative di sicurezza. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Registri del punto di controllo di inoltro all'agente Syslog

Configurare l'appliance Check Point per inoltrare i messaggi Syslog in formato CEF all'area di lavoro di Azure tramite l'agente Syslog.

1. Vai a [Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Scorrere verso il basso fino a **Distribuzione di base** e seguire le istruzioni per configurare la connessione, utilizzando le linee guida seguenti:
   - Impostare la **porta Syslog** su **514** o sulla porta impostata sull'agente.
     - Sostituire il **nome** e l'indirizzo IP del **server di destinazione** nell'interfaccia della riga di comando con il nome e l'indirizzo IP dell'agente Syslog.
     - Impostare il formato **su CEF**.
1. Se si utilizza la versione R77.30 o R80.10, scorrere fino a **Installazioni** e seguire le istruzioni per installare un'esportazione log per la versione in uso.
1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).
 

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Check Point appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- [Convalidare la connettività](connect-cef-verify.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


