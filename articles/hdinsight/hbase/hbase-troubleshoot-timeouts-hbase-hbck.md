---
title: Timeout con comando "hbase hbck" in Azure HDInsight
description: Si è verificata una situazione di timeout con il comando ' HBase hbck ' durante la correzione delle assegnazioni
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887190"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: timeout con comando ' HBase hbck ' in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Rilevare i timeout con il `hbase hbck` comando quando si fissano le assegnazioni di aree.

## <a name="cause"></a>Causa

Un possibile causa dei problemi di timeout quando si usa il comando `hbck` potrebbe essere che diverse aree restano a lungo in stato di transizione. Queste aree vengono visualizzate come offline nell'interfaccia utente di HBase Master. Poiché un numero elevato di aree sta tentando di eseguire la transizione, HBase Master possibile timeout e non riuscire a riportare online tali aree.

## <a name="resolution"></a>Soluzione

1. Accedere al cluster HBase di HDInsight con SSH.

1. Eseguire il `hbase zkcli` comando per connettersi con Apache ZooKeeper Shell.

1. Eseguire `rmr /hbase/regions-in-transition` il `rmr /hbase-unsecure/regions-in-transition` comando o.

1. Uscire dalla `hbase zkcli` Shell tramite il `exit` comando.

1. Dall'interfaccia utente di Apache Ambari riavviare il servizio Active HBase Master.

1. Eseguire il comando `hbase hbck -fixAssignments`.

1. Monitorare la sezione "area in transizione" dell'interfaccia utente HBase Master per assicurarsi che nessuna area venga bloccata.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

- Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

- Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
