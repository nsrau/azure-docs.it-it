---
title: Risolvere i problemi di HBase con Azure HDInsight
description: Risposte alle domande comuni sull'uso di HBase e Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573953"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Risolvere i problemi di Apache HBase tramite Azure HDInsight

Questo articolo illustra i problemi principali che possono verificarsi quando si usano payload di Apache HBase in Apache Ambari e le relative risoluzioni.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Come eseguire report del comando hbck con più aree non assegnate

Un messaggio di errore comune che può essere visualizzato quando si esegue il comando `hbase hbck` riguarda la presenza di più aree non assegnate o difetti nella catena delle aree.

Nell'interfaccia utente di HBase Master è possibile visualizzare il numero delle aree non bilanciate tra tutti i server di area. Si può quindi eseguire il comando `hbase hbck` per visualizzare i difetti nella catena di aree.

Dato che i difetti possono essere causati dalle aree offline, per prima cosa correggere le assegnazioni. 

Per ripristinare uno stato normale delle aree non assegnate, seguire questa procedura:

1. Accedere al cluster HBase di HDInsight con SSH.
2. Per connettersi con la shell Apache ZooKeeper, eseguire il comando `hbase zkcli`.
3. Eseguire il comando `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Per uscire dalla shell `hbase zkcli`, usare il comando `exit`.
5. Aprire l'interfaccia utente di Apache Ambari e quindi riavviare il servizio HBase Master attivo.
6. Eseguire di nuovo il comando `hbase hbck`, senza opzioni. Controllare l'output del comando per verificare che tutte le aree siano assegnate.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Come risolvere i problemi di timeout con i comandi hbck per le assegnazioni di aree

### <a name="issue"></a>Problema

Un possibile causa dei problemi di timeout quando si usa il comando `hbck` potrebbe essere che diverse aree restano a lungo in stato di transizione. Queste aree vengono visualizzate come offline nell'interfaccia utente di HBase Master. A causa del numero elevato di aree che tentano la transizione, può verificarsi il timeout di HBase Master, che non riuscirà a riportare online tali aree.

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Accedere al cluster HBase di HDInsight con SSH.
2. Per connettersi con la shell Apache ZooKeeper, eseguire il comando `hbase zkcli`.
3. Eseguire il comando `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Per uscire dalla shell `hbase zkcli`, usare il comando `exit`.
5. Nell'interfaccia utente di Apache Ambari riavviare il servizio HBase Master attivo.
6. Eseguire di nuovo il comando `hbase hbck -fixAssignments`.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
