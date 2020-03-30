---
title: Errore Jupyter 404 - "Blocking Cross Origin API" - Azure HDInsight
description: Jupyter server 404 errore "Non trovato" a causa di "Blocco dell'API tra origini" in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894419"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: Jupyter server 404 "Not Found" error due to "Blocking Cross Origin API" in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si accede al servizio Jupyter su HDInsight, viene visualizzata una casella di errore che indica "Non trovato". Se controlli i registri di Jupyter, vedrai qualcosa di simile a questo:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Potresti anche vedere un indirizzo IP nel campo "Origine" nel registro Jupyter.

## <a name="cause"></a>Causa

Questo errore può essere causato da un paio di cose:

- Se sono state configurate regole del gruppo di sicurezza di rete (NSG) per limitare l'accesso al cluster. Limitare l'accesso con le regole del gruppo di sicurezza di rete consentirà comunque di accedere direttamente ad Apache Ambari e ad altri servizi utilizzando l'indirizzo IP anziché il nome del cluster. Tuttavia, quando si accede Jupyter, si potrebbe vedere un errore 404 "Non trovato".

- Se il gateway HDInsight è stato assegnato un `xxx.azurehdinsight.net`nome DNS personalizzato diverso dallo standard.

## <a name="resolution"></a>Risoluzione

1. Modificare i file jupyter.py in queste due posizioni:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Trovare la riga `NotebookApp.allow_origin='\"https://{2}.{3}\"'` che dice: `NotebookApp.allow_origin='\"*\"'`E cambiarlo in: .

1. Riavviare il servizio Jupyter da Ambari.

1. Digitando `ps aux | grep jupyter` al prompt dei comandi dovrebbe mostrare che consente a qualsiasi URL di connettersi ad esso.

Questo è un meno sicuro rispetto all'impostazione che avevamo già in atto. Ma si presuppone che l'accesso al cluster sia limitato e che uno dall'esterno possa connettersi al cluster in base alle impostazioni disponibili.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
