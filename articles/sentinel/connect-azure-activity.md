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
ms.openlocfilehash: 107bbed8aea692a76f6d5bf6bbf1da26277b2c85
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156574"
---
# <a name="connect-data-from-azure-activity-log"></a>Connettere i dati dal log attività di Azure



È possibile eseguire lo streaming dei log da [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md) in Sentinel di Azure con un solo clic. Il log attività è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Utilizzando il log attività, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include operazioni di lettura (GET) o operazioni per le risorse che usano il modello classico/"RDFE". 


## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di collaboratore per Log Analytics area di lavoro 


## <a name="connect-to-azure-activity-log"></a>Connettersi al log attività di Azure

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **log attività di Azure** .

2. Nel riquadro Log attività di Azure selezionare le sottoscrizioni di cui si vuole eseguire lo streaming in Sentinel di Azure. 

3. Fare clic su **Connetti**.

4. Per usare lo schema pertinente in Log Analytics per gli avvisi di attività di Azure, cercare **AzureActivity**.


 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il log attività di Azure a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
