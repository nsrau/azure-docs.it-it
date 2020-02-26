---
title: Convalidare la connettività a Sentinel di Azure | Microsoft Docs
description: Convalidare la connettività della soluzione di sicurezza per assicurarsi che i messaggi CEF vengano inviati a Sentinel di Azure.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588434"
---
# <a name="step-3-validate-connectivity"></a>PASSAGGIO 3: convalidare la connettività



Dopo aver distribuito l'agente e configurato la soluzione di sicurezza per l'invio dei messaggi CEF, usare questo articolo per informazioni su come verificare la connettività tra Azure Sentinel e la soluzione di sicurezza. 

## <a name="how-to-validate-connectivity"></a>Come convalidare la connettività

1. Aprire Log Analytics per assicurarsi che i log vengano ricevuti con lo schema CommonSecurityLog.<br> Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

1. Prima di eseguire lo script, è consigliabile inviare messaggi dalla soluzione di sicurezza per assicurarsi che vengano inoltrati al computer proxy syslog configurato. 
1. È necessario disporre di autorizzazioni elevate (sudo) nel computer. Assicurarsi di avere Python nel computer usando il comando seguente: `python –version`
1. Eseguire lo script seguente per verificare la connettività tra l'agente, Azure Sentinel e la soluzione di sicurezza. Verifica che l'invio del daemon sia configurato correttamente, sia in ascolto sulle porte corrette e che nulla blocchi la comunicazione tra il daemon e l'agente di Log Analytics. Lo script invia anche i messaggi fittizi ' TestCommonEventFormat ' per controllare la connettività end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

