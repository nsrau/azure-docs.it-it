---
title: Connettere i dati di Alcide kAudit ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Alcide kAudit ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: cf8da1d88529a823ff4399fb955c8a5e0abbd20e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038240"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Connettere Alcide kAudit ad Azure Sentinel

> [!IMPORTANT]
> Il connettore di dati Alcide kAudit in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) consente di identificare i comportamenti anomali di Kubernetes e di concentrarsi sulle violazioni e gli eventi imprevisti di Kubernetes, riducendo al tempo stesso i tempi di rilevamento. Questo articolo illustra come connettere la soluzione Alcide kAudit ad Azure Sentinel. Il connettore di dati Alcide kAudit consente di importare facilmente i dati di log di kAudit in Sentinel di Azure, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Alcide kAudit e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro.

## <a name="configure-and-connect-alcide-kaudit"></a>Configurare e connettere Alcide kAudit

Alcide kAudit può esportare i log direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** nel menu di navigazione.

1. Selezionare **Alcide kAudit** dalla raccolta e quindi fare clic sul pulsante **Apri pagina connettore** .

1. Seguire le istruzioni dettagliate fornite nella [Guida all'installazione di Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Quando viene richiesto l'ID dell'area di lavoro e la chiave primaria, è possibile copiarli dalla pagina del connettore di dati Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ID area di lavoro e chiave primaria":::

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** con i tipi di dati seguenti in **CustomLogs**:

- rilevamento **alcide_kaudit_detections_1_CL** -Alcide kaudit 
- log attività **alcide_kaudit_activity_1_CL** -Alcide kaudit
- conteggi attività kAudit di **alcide_kaudit_selections_count_1_CL** -Alcide
- Dettagli attività **alcide_kaudit_selections_details_1_CL** -Alcide kaudit

Per usare lo schema pertinente nei log per Alcide kAudit, cercare i tipi di dati menzionati in precedenza.

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Alcide kAudit ad Azure Sentinel. Per sfruttare appieno le funzionalità integrate in questo connettore di dati, fare clic sulla scheda **passaggi successivi** della pagina connettore dati. In questa pagina sono disponibili alcune query di esempio pronte per iniziare a trovare informazioni utili.

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
