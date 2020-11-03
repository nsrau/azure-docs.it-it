---
title: Errore 404 di Jupyter-"API per il blocco di origini incrociate"-Azure HDInsight
description: Errore del server Jupyter 404 "non trovato" a causa di un blocco dell'API Cross Origin in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f2000c3938e7366dd85f36b8de9a8425e91fab8a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287946"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: errore del server Jupyter 404 "non trovato" a causa di un blocco dell'API Cross Origin in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Quando si accede al servizio Jupyter in HDInsight, viene visualizzata una casella di errore che indica che non è stato trovato. Se si controllano i registri di Jupyter, verrà visualizzato un risultato simile al seguente:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

È anche possibile visualizzare un indirizzo IP nel campo "Origin" nel registro Jupyter.

## <a name="cause"></a>Causa

Questo errore può essere causato da un paio di operazioni:

- Se sono state configurate regole del gruppo di sicurezza di rete (NSG) per limitare l'accesso al cluster. La limitazione dell'accesso con regole NSG consente comunque di accedere direttamente ad Apache Ambari e ad altri servizi usando l'indirizzo IP anziché il nome del cluster. Tuttavia, quando si accede a Jupyter, è possibile che venga visualizzato l'errore 404 "non trovato".

- Se il gateway HDInsight è stato assegnato a un nome DNS personalizzato diverso da quello standard `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Soluzione

1. Modificare i file jupyter.py in queste due posizioni:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Trovare la riga che dice: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` e modificarla in: `NotebookApp.allow_origin='\"*\"'` .

1. Riavviare il servizio Jupyter da Ambari.

1. Digitando `ps aux | grep jupyter` al prompt dei comandi viene visualizzato che consente la connessione di qualsiasi URL.

Si tratta di un livello di protezione inferiore rispetto all'impostazione già disponibile. Tuttavia, si presuppone che l'accesso al cluster sia limitato e che uno dall'esterno sia autorizzato a connettersi al cluster come NSG.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]