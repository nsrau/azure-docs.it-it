---
title: Connettere i dati del punto di controllo ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati del punto di controllo ad Azure Sentinel.
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
# <a name="connect-check-point-to-azure-sentinel"></a>Connetti punto di controllo ad Azure Sentinel



Questo articolo illustra come connettere l'appliance Check Point ad Azure Sentinel. Il connettore Check Point Data consente di connettere facilmente i log di check point con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di check point in Azure Sentinel ti offre un maggior numero di informazioni sull'uso di Internet dell'organizzazione e ne migliora le funzionalità di sicurezza. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Inviare i log del punto di controllo all'agente syslog

Configurare l'appliance del punto di controllo per l'invio dei messaggi syslog in formato CEF all'area di lavoro di Azure tramite l'agente syslog.

1. Passare a [Check Point Export Log](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Scorrere fino alla **distribuzione di base** e seguire le istruzioni per configurare la connessione, attenendosi alle linee guida seguenti:
   - Impostare la **porta syslog** su **514** o la porta impostata per l'agente.
     - Sostituire il **nome** e l' **indirizzo IP del server di destinazione** nell'interfaccia della riga di comando con il nome e l'indirizzo IP dell'agente syslog.
     - Impostare il formato su **CEF**.
1. Se si usa la versione R 77.30 o R 80.10, scorrere fino a **installazioni** e seguire le istruzioni per installare un esportatore di log per la versione.
1. Continuare con il [passaggio 3: convalidare la connettività](connect-cef-verify.md).
 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le appliance di check point ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- [Convalidare la connettività](connect-cef-verify.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.


