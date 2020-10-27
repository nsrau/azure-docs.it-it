---
title: API REST per eseguire query su Apache HBase in Azure HDInsight
description: Questo articolo descrive i passaggi per la risoluzione dei problemi quando si interagisce con i componenti di Apache HBase nei cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 1091366ac4d0445a465bc75913baf0f812e6a635
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629290"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>API REST per eseguire query su Apache HBase in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

L'uso dell'interfaccia REST di Apache HBase per eseguire una query su una tabella in uno spazio dei nomi diverso da quello predefinito restituisce un errore di runtime (stato HTTP 500).

## <a name="cause"></a>Causa

L'API REST di HBase è supportata solo quando si usa lo spazio dei nomi predefinito. Si tratta di un problema noto per quanto riguarda l'uso di spazi dei nomi HBase o l'esecuzione di chiamate che fanno riferimento a un valore specifico per le colonne con le famiglie di colonne con server REST in HDInsight. Questo è dovuto a un problema di sicurezza con il gateway HDInsight. Quando si usa l'API per creare una tabella con uno spazio dei nomi, per accedere alle colonne tramite le famiglie di colonne, è necessario specificare il `:` carattere, che è considerato un problema di sicurezza nel modulo Gateway IIS.

## <a name="mitigation"></a>Strategia di riduzione del rischio

Ignorare il server gateway/REST distribuendo l'applicazione in una macchina virtuale che si trova nella stessa VNet di Azure del cluster HDInsight. È quindi possibile comunicare con HBase direttamente tramite RPC (ignorando completamente il server REST) o nei singoli server REST in esecuzione nei nodi di lavoro che ignorano i gateway HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).