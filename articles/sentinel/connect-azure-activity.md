---
title: Connetti i dati di attività di Azure a Azure Sentinel Preview | Microsoft Docs
description: Informazioni su come connettere i dati di attività di Azure a Azure Sentinel.
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e329c8efd9b0e89f5f5eae41952cda9a45a95969
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620661"
---
# <a name="connect-data-from-azure-activity-log"></a>Connetti i dati dal log attività di Azure

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log dal [log attività Azure](../azure-monitor/platform/activity-logs-overview.md) in Azure Sentinel con un solo clic. Il log attività è un log di sottoscrizione che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Uso del log attività, è possibile determinare la "cosa, chi e quando ' per qualsiasi operazione (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione di scrittura. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include le operazioni di lettura (GET) o quelle per le risorse che usano il modello di distribuzione classica / modello "RDFE". 


## <a name="prerequisites"></a>Prerequisiti

- Utenti con autorizzazioni di sicurezza amministratore o amministratore globale


## <a name="connect-to-azure-activity-log"></a>Connettersi al log attività di Azure

1. In Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **log attività di Azure** riquadro.

2. Nel riquadro dei log attività di Azure selezionare le sottoscrizioni di cui che si vuole trasmettere in Sentinel di Azure. 

3. Fare clic su **Connetti**.

4. Per usare lo schema appropriato nel Log Analitica per gli avvisi di attività di Azure, cercare **AzureActivity**.


 

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di log attività di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
