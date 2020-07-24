---
title: Connettere i dati perimetrali 81 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati perimetrali 81 ad Azure Sentinel.
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
ms.openlocfilehash: 8c9a7a09d3085b1ec67bf29f142c6e7b205561ef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021750"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Connettere i log attività del perimetrale 81 ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati perimetrale 81 in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come connettere l'appliance dei [log attività del perimetrale 81](https://www.perimeter81.com/) ad Azure Sentinel. Il connettore perimetrale 81 attività log consente di trasferire facilmente i dati del perimetro 81 in Sentinel di Azure, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Configurare e connettere i log attività perimetrali 81

I log attività del perimetro 81 possono integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** nel menu di navigazione.

1. Selezionare **log attività perimetrale 81** dalla raccolta, quindi fare clic sul pulsante **Apri pagina connettore** .

1. Dalla pagina connettore log attività perimetrale 81 copiare l' **ID dell'area di lavoro** e la **chiave primaria** e incollarli nel perimetro 81, [come indicato di seguito](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Dopo aver completato le istruzioni, verranno visualizzati i tipi di dati connessi nella pagina del connettore di Azure Sentinel.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** in **CustomLogs**  -  **Perimeter81_CL**.

La visualizzazione dei log potrebbe richiedere fino a 20 minuti.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere i log attività del perimetro 81 ad Azure Sentinel. Per sfruttare appieno le funzionalità integrate in questo connettore di dati, fare clic sulla scheda **passaggi successivi** della pagina connettore dati. In questa pagina sono disponibili una cartella di lavoro predisposta e alcune query di esempio per iniziare a trovare informazioni utili.

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
