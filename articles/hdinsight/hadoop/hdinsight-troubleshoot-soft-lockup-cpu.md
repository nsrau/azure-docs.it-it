---
title: Errore di utilizzo della CPU del blocco software del watchdog dal cluster HDInsight di Azure
description: La CPU del blocco software del Watchdog viene visualizzata nei syslog del kernel dal cluster HDInsight di Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894114"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: errore "watchdog: BUG: blocco soft-CPU" da un cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

I syslog del kernel contengono il messaggio di errore: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Causa

Un [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) nel kernel Linux causa blocchi software di CPU.

## <a name="resolution"></a>Soluzione

Applica patch del kernel. Lo script seguente aggiorna il kernel Linux e riavvia le macchine in momenti diversi in 24 ore. Eseguire l'azione script in due batch. Il primo batch si trova in tutti i nodi eccetto il nodo head. Il secondo batch si trova nel nodo head. Non eseguire nel nodo Head e in altri nodi nello stesso momento.

1. Passare al cluster HDInsight da portale di Azure.

1. Passare a azioni script.

1. Selezionare **Invia nuovo** e immettere l'input come indicato di seguito

    | Proprietà | Valore |
    | --- | --- |
    | Tipo di script | -Personalizzato |
    | Nome |Correzione per il problema di blocco soft kernel |
    | URI script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo/i di nodo |Worker, Zookeeper |
    | Parametri |N/D |

    Selezionare **Mantieni questa azione script...** se si desidera che esegua lo script quando vengono aggiunti nuovi nodi.

1. Selezionare **Crea**.

1. Attendere che l'esecuzione abbia esito positivo.

1. Eseguire l'azione script sul nodo head seguendo la stessa procedura descritta nel passaggio 3, ma questa volta con i tipi di nodo: Head.

1. Attendere che l'esecuzione abbia esito positivo.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
