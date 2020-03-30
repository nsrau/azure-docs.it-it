---
title: Apache Spark Streaming application stops after 24 days in Azure HDInsight
description: Un'applicazione Apache Spark Streaming si arresta dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894430"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark Streaming application stops after executing for 24 days in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Un'applicazione Apache Spark Streaming si arresta dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.

## <a name="cause"></a>Causa

Il `livy.server.session.timeout` valore controlla per quanto tempo Apache Livy deve attendere il completamento di una sessione. Una volta che la `session.timeout` durata della sessione raggiunge il valore, la sessione Livy e l'applicazione vengono automaticamente eliminate.

## <a name="resolution"></a>Risoluzione

Per i processi a esecuzione `livy.server.session.timeout` prolungata, aumentare il valore dell'utilizzo dell'interfaccia utente di Ambari. È possibile accedere alla configurazione Livy dall'interfaccia `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`utente di Ambari utilizzando l'URL .

Sostituire `<yourclustername>` con il nome del cluster HDInsight come illustrato nel portale.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
