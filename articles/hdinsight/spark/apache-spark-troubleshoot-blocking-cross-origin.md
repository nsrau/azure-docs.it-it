---
title: Errore 404 del server Jupyter a causa di un blocco dell'API Cross Origin in Azure HDInsight
description: Errore del server Jupyter 404 "non trovato" a causa di un blocco dell'API Cross Origin in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f5ac70381f0879a896e96cb37a70c1b1a1f0b5da
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090999"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: Errore del server Jupyter 404 "non trovato" a causa di un blocco dell'API Cross Origin in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si accede al servizio Jupyter in HDInsight, viene visualizzata una casella di errore che indica che non è stato trovato. Se si controllano i registri di Jupyter, verrà visualizzato un risultato simile al seguente:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

È anche possibile visualizzare un indirizzo IP nel campo "Origin" nel registro Jupyter.

## <a name="cause"></a>Causa

Questo errore può essere causato da un paio di operazioni:

- Se sono state configurate regole del gruppo di sicurezza di rete (NSG) per limitare l'accesso al cluster. La limitazione dell'accesso con regole NSG consente comunque di accedere direttamente ad Apache Ambari e ad altri servizi usando l'indirizzo IP anziché il nome del cluster. Tuttavia, quando si accede a Jupyter, è possibile che venga visualizzato l'errore 404 "non trovato".

- Se il gateway HDInsight è stato assegnato a un nome DNS personalizzato diverso da quello `xxx.azurehdinsight.net`standard.

## <a name="resolution"></a>Risoluzione

1. Modificare i file jupyter.py in queste due posizioni:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Trovare la riga che indica: `NotebookApp.allow_origin='\"https://{2}.{3}\"'`E modificarlo in: `NotebookApp.allow_origin='\"*\"'`.

1. Riavviare il servizio Jupyter da Ambari.

1. Digitando `ps aux | grep jupyter` al prompt dei comandi viene visualizzato che consente la connessione di qualsiasi URL.

Si tratta di un livello di protezione inferiore rispetto all'impostazione già disponibile. Tuttavia, si presuppone che l'accesso al cluster sia limitato e che uno dall'esterno sia autorizzato a connettersi al cluster come NSG.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
