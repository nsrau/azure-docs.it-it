---
title: Apache Ambari UI shows down hosts and services in Azure HDInsight
description: Risoluzione di un problema dell'interfaccia utente Apache Ambari quando vengono visualizzati gli host e i servizi in Azure HDInsightTroubleshooting an Apache Ambari UI issue when it shows down hosts and services in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895643"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: l'interfaccia utente di Apache Ambari mostra gli host e i servizi in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

L'interfaccia utente di Apache Ambari è accessibile, ma l'interfaccia utente mostra quasi tutti i servizi inattivo, tutti gli host che mostrano il battito cardiaco perso.

## <a name="cause"></a>Causa

Nella maggior parte degli scenari, si tratta di un problema con il server Ambari non in esecuzione sul nodo head attivo. Controllare quale headnode è il nodo head attivo e assicurarsi che l'ambari-server venga eseguito su quello giusto. Non avviare manualmente ambari-server, lasciare che il servizio del controller di failover sia responsabile dell'avvio di ambari-server sul nodo head destro. Riavviare il nodo head attivo per forzare un failover.

Problemi di rete possono anche causare questo problema. Da ogni nodo del cluster, `headnodehost`verificare se è possibile eseguire il ping di . Esiste una situazione rara in cui `headnodehost`nessun nodo del cluster può connettersi a:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Risoluzione

Di solito il riavvio del nodo head attivo ridurrà questo problema. In caso contrario, contattare il team di supporto HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
