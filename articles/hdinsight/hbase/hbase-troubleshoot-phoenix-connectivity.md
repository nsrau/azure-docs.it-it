---
title: Problemi di connettività Apache Phoenix in Azure HDInsight
description: Problemi di connettività Apache Phoenix in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887040"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: Problemi di connettività Apache Phoenix in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile connettersi ad Apache HBase con Apache Phoenix. I motivi possono variare.

## <a name="cause-incorrect-ip"></a>Causa: IP non corretto

L'IP del nodo Zookeeper attivo non è corretto.

### <a name="resolution"></a>Risoluzione

L'IP del nodo Zookeeper attivo può essere identificato dall'interfaccia utente di Ambariri, seguendo i collegamenti ai **collegamenti rapidi HBase->-> ZK***  **(attivo) > informazioni Zookeeper**. Correggere le esigenze.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: Sistema. Tabella del catalogo offline

Quando si eseguono comandi come `!tables`, viene visualizzato un messaggio di errore simile al seguente:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Quando si eseguono comandi come `count 'SYSTEM.CATALOG'`, viene visualizzato un messaggio di errore simile al seguente:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Risoluzione

Riavviare il servizio HMaster in tutti i nodi Zookeeper dall'interfaccia utente di Ambari.

1. Passare a **HBase-> collegamento attivo HBase Master** nella sezione di riepilogo di HBase.

1. Nella sezione **componenti** riavviare il servizio HBASE master.

1. Ripetere i passaggi precedenti per i servizi di **HBase Master standby** rimanenti.

Possono essere necessari fino a 5 minuti affinché il servizio HBase Master stabilizzi e completi il ripristino. Quando la `SYSTEM.CATALOG` tabella torna al normale, il problema di connettività a Apache Phoenix dovrebbe essere risolto automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
