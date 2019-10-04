---
title: Connettere i dati dell'attività di Azure ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati delle attività di Azure a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240784"
---
# <a name="connect-data-from-azure-activity-log"></a>Connettere i dati dal log attività di Azure



È possibile eseguire lo streaming dei log da [log attività di Azure](../azure-monitor/platform/activity-logs-overview.md) in Sentinel di Azure con un solo clic. Il log attività è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Utilizzando il log attività, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include operazioni di lettura (GET) o operazioni per le risorse che usano il modello classico/"RDFE". 


## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza


## <a name="connect-to-azure-activity-log"></a>Connettersi al log attività di Azure

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **log attività di Azure** .

2. Nel riquadro Log attività di Azure selezionare le sottoscrizioni di cui si vuole eseguire lo streaming in Sentinel di Azure. 

3. Fare clic su **Connetti**.

4. Per usare lo schema pertinente in Log Analytics per gli avvisi di attività di Azure, cercare **AzureActivity**.


 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il log attività di Azure a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
