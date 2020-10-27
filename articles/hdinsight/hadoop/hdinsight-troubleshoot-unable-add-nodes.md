---
title: Non è possibile aggiungere nodi al cluster Azure HDInsight
description: Risolvere i problemi perché non è possibile aggiungere nodi a Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b8e8bf0a8a0555b23799600c3958fbbfd5a510dd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540568"
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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).