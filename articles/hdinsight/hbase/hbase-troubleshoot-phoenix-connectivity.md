---
title: Apache Phoenix connectivity issues in Azure HDInsight
description: Problemi di connettività tra Apache HBase e Apache Phoenix in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887292"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: Apache Phoenix connectivity issues in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Impossibile connettersi ad Apache HBase con Apache Phoenix. I motivi possono variare.

## <a name="cause-incorrect-ip"></a>Causa: IP errato

IP errato del nodo di  attivo.

### <a name="resolution"></a>Risoluzione

L'indirizzo IP del nodo attivo del custode dello zoo può essere identificato dall'interfaccia utente di Ambari seguendo i collegamenti a **HBase** > **Quick Links** > **(Active)** > Info dello**zoo**. Correggere l'IP in base alle esigenze.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: SISTEMA. Tabella CATALOG offline

Quando si eseguono comandi come `!tables`, viene visualizzato un messaggio di errore analogo al:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Quando si eseguono comandi come `count 'SYSTEM.CATALOG'`, viene visualizzato un messaggio di errore analogo al:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Risoluzione

Dall'interfaccia utente di Apache Ambari, completare i passaggi seguenti per riavviare il servizio HMaster su tutti i nodi di Apache Ambari:

1. Dalla sezione **Riepilogo** di HBase, accedere a **HBase** > **Active HBase Master**.

1. Nella sezione **Componenti** riavviare il servizio Master HBase.

1. Ripetere questi passaggi per tutti i rimanenti servizi **Standby HBase Master** (HBase Master in standby).

La stabilizzazione e il completamento del ripristino da parte del servizio HBase Master possono richiedere fino a cinque minuti. Dopo `SYSTEM.CATALOG` che la tabella è tornata alla normalità, il problema di connettività ad Apache Phoenix dovrebbe essere risolto automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
