---
title: L'applicazione di streaming Apache Spark si arresta dopo 24 giorni in Azure HDInsight
description: Un'applicazione di flusso Apache Spark viene arrestata dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: c513c5df0d83eb0049683f88d85e8a1c41fd0bf0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736289"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: L'applicazione di streaming Apache Spark si interrompe dopo l'esecuzione per 24 giorni in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Un'applicazione di flusso Apache Spark viene arrestata dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.

## <a name="cause"></a>Causa

Il `livy.server.session.timeout` valore controlla per quanto tempo Apache Livio deve attendere il completamento di una sessione. Quando la lunghezza della sessione raggiunge `session.timeout` il valore, la sessione di Livio e l'applicazione vengono terminate automaticamente.

## <a name="resolution"></a>Risoluzione

Per i processi con esecuzione prolungata, `livy.server.session.timeout` aumentare il valore di usando l'interfaccia utente di Ambari. È possibile accedere alla configurazione di Livio dall'interfaccia utente di Ambari usando `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`l'URL.

Sostituire `<yourclustername>` con il nome del cluster HDInsight, come illustrato nel portale.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
