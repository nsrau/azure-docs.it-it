---
title: Non è possibile aggiungere nodi al cluster Azure HDInsight
description: Non è possibile aggiungere nodi al cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 6c2e2c7395fcc45fe74c50beb3624eabb1d395c9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828020"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: Non è possibile aggiungere nodi al cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile aggiungere nodi al cluster HDInsight di Azure.

## <a name="cause"></a>Causa

I motivi possono variare.

## <a name="resolution"></a>Risoluzione

Usando la funzionalità [dimensioni cluster](../hdinsight-scaling-best-practices.md) , calcolare il numero di core aggiuntivi necessari per il cluster. Questo valore si basa sul numero totale di core nei nuovi nodi del ruolo di lavoro. Quindi, provare uno o più dei passaggi seguenti:

* Verificare se sono presenti core disponibili nella posizione del cluster.

* Osservare il numero di core disponibili in altre località. Provare a ricreare il cluster in un'altra località con un numero di core disponibili sufficiente.

* Per aumentare la quota di core per una località specifica, aprire un ticket di supporto per un aumento della quota di core per HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
