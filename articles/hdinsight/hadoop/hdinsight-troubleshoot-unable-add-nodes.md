---
title: Impossibile aggiungere nodi al cluster Azure HDInsight
description: Risolvere perché non è possibile aggiungere nodi al cluster Apache Hadoop in Azure HDInsightTroubleshoot why unable to add nodes to Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894088"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: impossibile aggiungere nodi al cluster di Azure HDInsightScenario: Unable to add nodes to Azure HDInsight cluster

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Impossibile aggiungere nodi al cluster Azure HDInsight.Unable to add nodes to Azure HDInsight cluster.

## <a name="cause"></a>Causa

I motivi possono variare.

## <a name="resolution"></a>Risoluzione

Utilizzando la funzionalità [Dimensioni cluster,](../hdinsight-scaling-best-practices.md) calcolare il numero di core aggiuntivi necessari per il cluster. Questo valore si basa sul numero totale di core nei nuovi nodi del ruolo di lavoro. Provare quindi uno o più dei seguenti passaggi:

* Verificare se sono disponibili core nella posizione del cluster.

* Osservare il numero di core disponibili in altre località. Provare a ricreare il cluster in un'altra località con un numero di core disponibili sufficiente.

* Per aumentare la quota di core per una località specifica, aprire un ticket di supporto per un aumento della quota di core per HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
