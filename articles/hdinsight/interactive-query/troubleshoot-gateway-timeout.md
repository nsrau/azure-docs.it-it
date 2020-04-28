---
title: Eccezione durante l'esecuzione di query dalla vista hive di Apache Ambari in Azure HDInsight
description: Procedura di risoluzione dei problemi durante l'esecuzione di query Apache Hive tramite la vista hive di Apache Ambari in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895040"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Eccezione durante l'esecuzione di query dalla vista hive di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si esegue una query Apache Hive dalla vista hive di Apache Ambari, viene visualizzato il messaggio di errore seguente in modo intermittente:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Timeout del gateway.

Il valore di timeout del gateway è 2 minuti. Le query dalla vista hive di Ambari vengono inviate `/hive2` all'endpoint tramite il gateway. Una volta che la query viene compilata e accettata correttamente `queryid`, del restituisce. I client continuano quindi a eseguire il polling per lo stato della query. Durante questo processo, se il del non restituisce una risposta HTTP entro 2 minuti, il gateway HDI genera un errore di timeout del gateway 502,3 al chiamante. Gli errori possono verificarsi quando la query viene inviata per l'elaborazione (più probabile) e anche nella chiamata Get Status (meno probabile). Gli utenti potrebbero visualizzarne uno.

Il thread del gestore HTTP dovrebbe essere rapido: preparare il processo e restituire un oggetto `queryid`. Tuttavia, a causa di diversi motivi, tutti i thread del gestore potrebbero essere occupati, causando timeout per le nuove query e le chiamate di stato Get.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilità del thread del gestore HTTP

Quando il client invia una query a del, esegue le operazioni seguenti nel thread in primo piano:

* Analizza la richiesta, Esegui la verifica semantica
* Acquisisci blocco
* Ricerca Metastore se necessario
* Compilare la query (DDL o DML)
* Preparare un piano di query
* Eseguire l'autorizzazione (eseguire tutti i criteri Ranger applicabili in cluster sicuri)

## <a name="resolution"></a>Risoluzione

Di seguito sono riportate alcune indicazioni generali per migliorare la situazione:

* Se si usa un Metastore hive esterno, controllare le metriche del database e verificare che il database non sia sovraccarico. Valutare la possibilità di ridimensionare il livello di database del Metastore.

* Assicurarsi che le operazioni parallele siano attivate, in modo da consentire l'esecuzione in parallelo dei thread del gestore HTTP. Per verificare il valore, avviare [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e passare a **hive** > **configs** > **Advanced** > **Custom hive-site**. Il valore di `hive.server2.parallel.ops.in.session` deve essere `true`.

* Verificare che lo SKU della macchina virtuale del cluster non sia troppo piccolo per il carico. Si consiglia di suddividere il lavoro tra più cluster. Per ulteriori informazioni, vedere [scegliere un tipo di cluster](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Se Ranger è installato nel cluster, verificare se sono presenti troppi criteri Ranger che devono essere valutati per ogni query. Cercare i criteri duplicati o non necessari.

* Verificare il valore delle **dimensioni heap HiveServer2** da Ambari. Passare a **Hive** > **configurazione** > di hive**Impostazioni** > **ottimizzazione**. Verificare che il valore sia maggiore di 10 GB. Modificare in base alle esigenze per ottimizzare le prestazioni.

* Assicurarsi che la query hive sia ben ottimizzata. Per altre informazioni, vedere [optimize Apache hive queries in Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
