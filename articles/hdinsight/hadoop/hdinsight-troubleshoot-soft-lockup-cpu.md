---
title: errore di utilizzo della CPU per il blocco molle del BUG di watchdog da un cluster Azure HDInsight
description: la CPU del blocco flessibile del BUG Watchdog viene visualizzata nei syslog del kernel
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 9278c174d96cb6b1823c8dbfdcba197b7a3c05cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829177"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: "watchdog: BUG: errore di blocco software-CPU da un cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

I syslog del kernel contengono il messaggio di errore `watchdog: BUG: soft lockup - CPU`:.

## <a name="cause"></a>Causa

Un [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) nel kernel Linux causa blocchi software di CPU.

## <a name="resolution"></a>Risoluzione

Applica patch del kernel. Lo script seguente aggiorna il kernel Linux e riavvia le macchine in momenti diversi in 24 ore. Eseguire l'azione script in due batch. Il primo batch si trova in tutti i nodi eccetto il nodo head. Il secondo batch si trova nel nodo head. Non eseguire nel nodo Head e in altri nodi nello stesso momento.

1. Passare al cluster HDInsight da portale di Azure.

1. Passare a azioni script.

1. Selezionare **Invia nuovo** e immettere l'input come indicato di seguito

    | Proprietà | Valore |
    | --- | --- |
    | Tipo di script | -Personalizzato |
    | Name |Correzione per il problema di blocco soft kernel |
    | URI script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo/i di nodo |Worker, Zookeeper |
    | Parametri |N/D |

    Selezionare **Mantieni questa azione script...** se si desidera che esegua lo script quando vengono aggiunti nuovi nodi.

1. Selezionare **Create**.

1. Attendere che l'esecuzione abbia esito positivo.

1. Eseguire l'azione script sul nodo head seguendo la stessa procedura descritta nel passaggio 3, ma questa volta con i tipi di nodo: Head.

1. Attendere che l'esecuzione abbia esito positivo.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
