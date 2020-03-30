---
title: Connettere i dati delle attività di Azure ad Azure Sentinel. Documenti Microsoft
description: Informazioni su come connettere i dati delle attività di Azure ad Azure Sentinel.Learn how to connect Azure Activity data to Azure Sentinel.
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
# <a name="connect-data-from-azure-activity-log"></a>Connettere i dati dal log attività di AzureConnect data from Azure Activity log

È possibile trasmettere i log dal [log attività](../azure-monitor/platform/platform-logs-overview.md) di Azure in Azure Sentinel con un solo clic. Il log attività è un log di sottoscrizione che registra e visualizza eventi a livello di sottoscrizione in Azure, dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. Usando il log attività, è possibile determinare il 'cosa, chi e quando' per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. È inoltre possibile apprendere lo stato dell'operazione e altre proprietà rilevanti. Il log attività non include operazioni di lettura (GET) o operazioni per le risorse che utilizzano il modello Classic/"RDFE". 

## <a name="prerequisites"></a>Prerequisiti

- L'utente deve disporre delle autorizzazioni di collaboratore per l'area di lavoro di Log Analytics.
- L'utente deve disporre delle autorizzazioni di lettura per qualsiasi sottoscrizione i cui log si desidera trasmettere in Azure Sentinel.Your user must have Reader permissions to any subscription whose logs you want to stream into Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Configurare il connettore di attività di AzureSet up the Azure Activity connector

1. Nel menu di spostamento di Azure Sentinel selezionare **Connettori dati**. Nell'elenco dei connettori fare clic su **Attività di Azure**e quindi sul pulsante Apri pagina **connettore** in basso a destra.

2. Nella scheda Istruzioni fare clic sul collegamento Configura log attività di Azure >.Under the **Instructions** tab, click the **Configure Azure Activity logs >link.**

3. Nel riquadro Log attività di Azure selezionare le sottoscrizioni di cui si vuole trasmettere i log in Azure Sentinel.In the **Azure Activity log** pane, select the subscriptions whose logs you want to stream into Azure Sentinel. 

4. Nel riquadro della sottoscrizione che si apre a destra fare clic su **Connetti**.

5. Per usare lo schema pertinente in `AzureActivity` Log Analytics per gli avvisi attività di Azure, digitare nella finestra della query.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Azure Activity log to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate.](tutorial-detect-threats-custom.md)
