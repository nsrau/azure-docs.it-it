---
title: Non è possibile aggiungere nodi al cluster Azure HDInsight
description: Risolvere i problemi perché non è possibile aggiungere nodi a Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289046"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: non è possibile aggiungere nodi al cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile aggiungere nodi al cluster HDInsight di Azure.

## <a name="cause"></a>Causa

I motivi possono variare.

## <a name="resolution"></a>Soluzione

Usando la funzionalità [dimensioni cluster](../hdinsight-scaling-best-practices.md) , calcolare il numero di core aggiuntivi necessari per il cluster. Questo valore si basa sul numero totale di core nei nuovi nodi del ruolo di lavoro. Quindi, provare uno o più dei passaggi seguenti:

* Verificare se sono presenti core disponibili nella posizione del cluster.

* Osservare il numero di core disponibili in altre località. Provare a ricreare il cluster in un'altra località con un numero di core disponibili sufficiente.

* Per aumentare la quota di core per una località specifica, aprire un ticket di supporto per un aumento della quota di core per HDInsight.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]