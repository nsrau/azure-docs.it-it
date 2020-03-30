---
title: Impossibile accedere al cluster di Azure HDInsight
description: Risolvere i problemi per cui non è possibile accedere al cluster Apache Hadoop in Azure HDInsightTroubleshoot why unable to log into Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894064"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: Impossibile accedere al cluster di Azure HDInsightScenario: Unable to log into Azure HDInsight cluster

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Impossibile accedere al cluster di Azure HDInsight.

## <a name="cause"></a>Causa

I motivi possono variare. Tenere presente che quando si accede ai dashboard del cluster o dell'app, usare le credenziali HTTP o di accesso al cluster. Se ci si connette in modalità remota, usare le credenziali Secure Shell (SSH) o Desktop remoto.

## <a name="resolution"></a>Risoluzione

Per risolvere i problemi comuni, provare le procedure seguenti.

* Provare ad aprire il dashboard del cluster in modalità anonima in una nuova scheda del browser.

* Se non è possibile richiamare le credenziali SSH, è possibile [reimpostare le credenziali all'interno dell'interfaccia utente di Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
