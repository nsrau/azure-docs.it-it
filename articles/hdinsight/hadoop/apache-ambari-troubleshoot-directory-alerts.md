---
title: Avvisi directory di Apache Ambari in Azure HDInsight
description: Discussione e analisi delle possibili cause e delle soluzioni per gli avvisi directory di Apache Ambari in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998312"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scenario: avvisi directory di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Si ricevono errori da Apache Ambari simili a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Le directory indicate dall'avviso Ambari non sono presenti nei nodi del ruolo di lavoro interessati.

## <a name="resolution"></a>Risoluzione

Creare manualmente directory mancanti nei nodi di lavoro interessati.

1. Eseguire SSH sul nodo del ruolo di lavoro pertinente.

1. Ottenere l'utente root: `sudo su` .

1. Creare le directory necessarie in modo ricorsivo.

1. Modificare il proprietario e il gruppo per le directory.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Dall'interfaccia utente di Apache Ambari disabilitare e quindi abilitare Alert.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]