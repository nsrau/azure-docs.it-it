---
title: Ridimensionamento di un cluster di Esplora dati di Azure per rispondere al cambiamento della domanda
description: Questo articolo descrive la procedura per aumentare e ridurre il numero di istanze di un cluster di Esplora dati di Azure in base al cambiamento della domanda.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735314"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gestire l'aumento delle prestazioni di un cluster per rispondere al cambiamento della domanda

Ridimensionare un cluster in modo appropriato è fondamentale per garantire le prestazioni di Esplora dati di Azure. Ma in un cluster non è possibile prevedere la domanda con un'accuratezza del 100%. La scelta di dimensioni statiche per un cluster può portare al suo sottoutilizzo o sovrautilizzo e nessuna delle due condizioni è ideale. Un approccio migliore consiste nel *ridimensionare* il cluster aumentandone o diminuendone la capacità e la CPU in base al cambiamento della domanda. Questo articolo illustra come gestire l'aumento delle prestazioni di un cluster.

Passare al cluster e in **Impostazioni** selezionare **Aumenta**.

Viene quindi fornito l'elenco di SKU disponibili. È possibile scegliere dall'elenco delle schede abilitate. Ad esempio, nella figura seguente è presente un solo SKU selezionabile in D14_vs.

![Aumentare le prestazioni](media/manage-cluster-scale-up/scale-up.png)

D13_v2 è disabilitato perché si tratta dello SKU corrente del cluster. L8 e L16 sono disabilitati perché non sono disponibili nell'area in cui si trova il cluster.

Per cambiare SKU, è sufficiente fare clic sullo SKU che si vuole usare e quindi sul pulsante **Seleziona**.

[!NOTE] Il processo per aumentare le prestazioni può richiedere alcuni minuti e durante tale periodo il cluster verrà sospeso. Si noti che, riducendo le prestazioni, si può compromettere l'efficienza del cluster.

Se occorre assistenza per problemi relativi al ridimensionamento di un cluster, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).