---
title: Connettere i dati dell'attività di Azure ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati delle attività di Azure a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124995"
---
# <a name="connect-data-from-azure-activity-log"></a>Connettere i dati dal log attività di Azure

È possibile eseguire lo streaming dei log da [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md) in Sentinel di Azure con un solo clic. Il log attività è un log di sottoscrizione che registra e visualizza gli eventi a livello di sottoscrizione in Azure, da Azure Resource Manager dati operativi agli aggiornamenti sugli eventi di integrità del servizio. Utilizzando il log attività, è possibile determinare le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse nella sottoscrizione, ovvero quali, chi e quando. È anche possibile conoscere lo stato dell'operazione e altre proprietà rilevanti. Il log attività non include operazioni di lettura (GET) o operazioni per le risorse che usano il modello classico/"RDFE". 

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve disporre delle autorizzazioni di collaboratore per l'area di lavoro Log Analytics.
- L'utente deve disporre delle autorizzazioni di lettura per qualsiasi sottoscrizione i cui log si vuole trasmettere in Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configurare il connettore attività di Azure

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**. Dall'elenco dei connettori, fare clic su **attività di Azure**e quindi sul pulsante **Apri pagina connettore** in basso a destra.

2. Nella scheda **istruzioni** fare clic sul collegamento **configurare i log attività di Azure >** .

3. Nel riquadro **log attività di Azure** selezionare le sottoscrizioni di cui si vuole eseguire lo streaming dei log in Sentinel di Azure. 

4. Nel riquadro sottoscrizione visualizzato a destra fare clic su **Connetti**.

5. Per usare lo schema pertinente in Log Analytics per gli avvisi di attività `AzureActivity` di Azure, digitare nella finestra query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il log attività di Azure a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .
