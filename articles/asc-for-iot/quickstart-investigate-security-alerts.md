---
title: 'Avvio rapido: Esaminare gli avvisi di sicurezza'
description: Comprendere, eseguire il drill-down ed esaminare gli avvisi di sicurezza del Centro sicurezza di Azure per IoT nei dispositivi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: a42b8f64c59126f08f09cd7c167e7aa133040a0e
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068419"
---
# <a name="quickstart-investigate-security-alerts"></a>Avvio rapido: Esaminare gli avvisi di sicurezza

L'analisi e la correzione pianificate degli avvisi emessi dal Centro sicurezza di Azure per IoT è il modo migliore per garantire la conformità e la protezione di tutta la soluzione IoT.

In questa guida di avvio rapido verranno esaminate le informazioni disponibili in ogni avviso di sicurezza IoT e verrà illustrato come eseguire il drill-down e usare i dettagli di ogni avviso e dispositivo correlato, per rispondere e correggere i problemi. 

A questo punto, procedere con l'esercitazione. 


## <a name="investigate-new-security-alerts"></a>Esaminare i nuovi avvisi di sicurezza

L'elenco degli avvisi di sicurezza dell'hub IoT visualizza tutti gli avvisi di sicurezza aggregati per l'hub IoT. 

1. Nel portale di Azure aprire l'**hub IoT** per cui si vogliono esaminare i nuovi avvisi.
1. Scegliere **Avvisi** dal menu **Sicurezza**. Verranno visualizzati tutti gli avvisi di sicurezza per l'hub IoT e gli avvisi con un flag **Nuovo** indicano gli avvisi delle ultime 24 ore.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Esaminare i nuovi avvisi di sicurezza IoT con il flag per gli avvisi Nuovo":::
1. Selezionare e aprire un avviso dall'elenco per visualizzare i dettagli dell'avviso ed eseguire il drill-down delle specifiche dell'avviso. 

## <a name="security-alert-details"></a>Dettagli dell'avviso di sicurezza

L'apertura di ogni avviso aggregato consente di visualizzare la descrizione dettagliata dell'avviso, i passaggi per la correzione, l'ID del dispositivo per ogni dispositivo che ha attivato un avviso, nonché la gravità dell'avviso e l'accesso diretto all'analisi tramite Log Analytics. 

1. Selezionare e aprire un avviso di sicurezza dall'elenco in **Hub IoT** > **Sicurezza** > **Avvisi**. 
1. Esaminare la **descrizione**, la **gravità**, l'**origine del rilevamento**, i **dettagli del dispositivo** di tutti i dispositivi che hanno emesso questo avviso nel periodo di aggregazione.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Eseguire i drill-down ed esaminare i dettagli di ogni dispositivo in un avviso aggregato "::: 
1. Dopo aver esaminato le specifiche degli avvisi, utilizzare le istruzioni del **passaggio di correzione manuale** per correggere e/o risolvere il problema che ha causato l'avviso. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Seguire i passaggi di correzione manuale per risolvere o correggere gli avvisi di sicurezza del dispositivo":::

1. Se sono necessarie ulteriori indagini, **Esaminare gli avvisi in Log Analytics** usando l'apposito collegamento. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Per esaminare ulteriormente un avviso, usare il collegamento per esaminare l'avviso in Log Analytics disponibile sullo schermo":::

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per altre informazioni sui tipi di avviso del Centro sicurezza di Azure e sulle possibili personalizzazioni...

> [!div class="nextstepaction"]
> [Informazioni sugli avvisi di sicurezza per IoT](concept-security-alerts.md)
