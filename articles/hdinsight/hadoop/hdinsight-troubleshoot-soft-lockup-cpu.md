---
title: Errore della CPU di controllo SOFT lockup BUG Watchdog BUG soft lockup CPU from Azure HDInsight cluster
description: La CPU soft lockup di Watchdog BUG viene visualizzata nei syslog del kernel dal cluster HDInsight di AzureWatchdog BUG soft lockup CPU appears in kernel syslogs from Azure HDInsight cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894114"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: errore "watchdog: BUG: soft lockup - CPU" da un cluster Azure HDInsightScenario: "watchdog: BUG: soft lockup - CPU" error from an Azure HDInsight cluster

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Il kernel syslogs contiene `watchdog: BUG: soft lockup - CPU`il messaggio di errore: .

## <a name="cause"></a>Causa

Un [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) nel kernel Linux sta causando i soft lockup della CPU.

## <a name="resolution"></a>Risoluzione

Applicare la patch del kernel. Lo script seguente aggiorna il kernel linux e riavvia i computer in tempi diversi in 24 ore. Eseguire l'azione di script in due batch. Il primo batch si trova in tutti i nodi ad eccezione del nodo head. Il secondo batch si trova nel nodo head. Non eseguire contemporaneamente sul nodo head e su altri nodi.

1. Passare al cluster HDInsight dal portale di Azure.Navigate to your HDInsight cluster from Azure portal.

1. Passare alle azioni di script.

1. Selezionare **Invia nuovo** e immettere l'input come segue

    | Proprietà | valore |
    | --- | --- |
    | Tipo di script | -Personalizzato |
    | Nome |Correzione del problema di soft lock del kernelFix for kernel soft lock issue |
    | URI script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo/i di nodo |Lavoratore, custode dello zoo |
    | Parametri |N/D |

    Selezionare **Rendere persistente questa azione dello script...** se si desidera eseguire lo script quando vengono aggiunti nuovi nodi.

1. Selezionare **Crea**.

1. Attendere che l'esecuzione abbia esito positivo.

1. Eseguire l'azione di script sul nodo Head seguendo la stessa procedura del passaggio 3, ma questa volta con i tipi di nodo: Head.

1. Attendere che l'esecuzione abbia esito positivo.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
