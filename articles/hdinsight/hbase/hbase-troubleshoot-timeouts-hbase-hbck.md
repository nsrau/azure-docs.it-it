---
title: Timeout con comando "hbase hbck" in Azure HDInsight
description: Problema di timeout con il comando 'hbase hbck' durante la correzione delle assegnazioni di areaTime out issue with 'hbase hbck' command when fixing region assignments
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887190"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: Timeouts with 'hbase hbck' command in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Timeout di `hbase hbck` 00 00 con il comando durante la correzione delle assegnazioni delle aree.

## <a name="cause"></a>Causa

Un possibile causa dei problemi di timeout quando si usa il comando `hbck` potrebbe essere che diverse aree restano a lungo in stato di transizione. Queste aree vengono visualizzate come offline nell'interfaccia utente di HBase Master. Poiché un numero elevato di aree sta tentando di eseguire la transizione, HBase Master potrebbe scivolersi e non essere in grado di riportare in linea tali aree.

## <a name="resolution"></a>Risoluzione

1. Accedere al cluster HDInsight HBase utilizzando SSH.

1. Eseguire `hbase zkcli` il comando per connettersi con la shell Apache .

1. Eseguire `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` o eseguire il comando.

1. Uscire `hbase zkcli` dalla shell `exit` utilizzando il comando.

1. Dall'interfaccia utente di Apache Ambari, riavviare il servizio Active HBase Master.

1. Eseguire il comando `hbase hbck -fixAssignments`.

1. Monitorare l'interfaccia utente master HBase "regione in transizione" quella sezione per assicurarsi che nessuna area si blocca.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

- Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

- Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
