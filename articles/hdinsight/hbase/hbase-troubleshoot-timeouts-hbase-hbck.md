---
title: Timeout con il comando ' HBase hbck ' in Azure HDInsight
description: Si è verificata una situazione di timeout con il comando ' HBase hbck ' durante la correzione delle assegnazioni
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737927"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: Timeout con il comando ' HBase hbck ' in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Rilevare i timeout con `hbase hbck` il comando quando si fissano le assegnazioni di aree.

## <a name="cause"></a>Causa

La possibile causa di questo problema può essere dovuta al fatto che diverse aree sono in stato "in transizione" per un lungo periodo. Tali aree possono essere considerate offline dall'interfaccia utente di Apache HBase Master. A causa del numero elevato di aree che tentano di eseguire la transizione, HBase Master possibile timeout e non sarà possibile riportare tali aree allo stato online.

## <a name="resolution"></a>Risoluzione

1. Accedere al cluster HDInsight HBase tramite SSH.

1. Eseguire `hbase zkcli` il comando per connettersi con la shell Zookeeper.

1. Eseguire `rmr /hbase/regions-in-transition` il `rmr /hbase-unsecure/regions-in-transition` comando o.

1. Uscire dalla `hbase zkcli` shell `exit` tramite il comando.

1. Aprire l'interfaccia utente di Ambari e riavviare il servizio Active HBase Master da Ambari.

1. Monitorare la sezione "area in transizione" dell'interfaccia utente HBase Master per assicurarsi che nessuna area venga bloccata.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
