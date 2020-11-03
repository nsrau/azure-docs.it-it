---
title: Non è possibile accedere al cluster Azure HDInsight
description: Risolvere i problemi perché non è possibile accedere a Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289010"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: non è possibile accedere al cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile accedere al cluster HDInsight di Azure.

## <a name="cause"></a>Causa

I motivi possono variare. Tenere presente che quando si accede al cluster o ai dashboard dell'app, usare l'account di accesso del cluster o le credenziali HTTP. Se ci si connette in modalità remota, usare le credenziali Secure Shell (SSH) o Desktop remoto.

## <a name="resolution"></a>Soluzione

Per risolvere i problemi comuni, provare le procedure seguenti.

* Provare ad aprire il dashboard del cluster in modalità anonima in una nuova scheda del browser.

* Se non è possibile richiamare le credenziali SSH, è possibile [reimpostare le credenziali all'interno dell'interfaccia utente di Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]