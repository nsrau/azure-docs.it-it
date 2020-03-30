---
title: Riduzione lenta in Azure HDInsightReducer is slow in Azure HDInsight
description: Il riduttore è lento in Azure HDInsight da possibili inclinazioni dei dati
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895158"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: Reducer is slow in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti di query interattivi nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues when using Interactive Query components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si esegue `insert into table1 partition(a,b) select a,b,c from table2` una query, ad esempio il piano di query, viene avviato un gruppo di riduttori, ma i dati di ogni partizione vengono riprodotti in un singolo riduttore. In questo modo la query è lenta come il tempo impiegato dal riduttore della partizione più grande.

## <a name="cause"></a>Causa

Aprire [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verificare `hive.optimize.sort.dynamic.partition`il valore di set .

Il valore di questa variabile deve essere impostato su true/false in base alla natura dei dati.

Se le partizioni nella tabella di input sono minori (ad esempio meno di 10) e `true`così è il numero di partizioni di output e la variabile è impostata su , i dati vengono ordinati a livello globale e scritti utilizzando un singolo riduttore per partizione. Anche se il numero di riduttori disponibili è maggiore, alcuni riduttori potrebbero essere in ritardo a causa dell'inclinazione dei dati e non è possibile raggiungere il parallelismo massimo. Quando viene `false`modificato in , più di un riduttore può gestire una singola partizione e più file più piccoli verranno scritti, con conseguente inserimento più veloce. Ciò potrebbe influire su ulteriori query a causa della presenza di file più piccoli.

Un valore `true` di ha senso quando il numero di partizioni è maggiore e i dati non sono distorti. In questi casi il risultato della fase della mappa verrà scritto in modo che ogni partizione verrà gestita da un singolo riduttore, ottenendo migliori prestazioni successive delle query.

## <a name="resolution"></a>Risoluzione

1. Provare a ripartizionare i dati per normalizzare in più partizioni.

1. Se #1 non è possibile, impostare il valore della configurazione su false nella sessione beeline e riprovare la query. `set hive.optimize.sort.dynamic.partition=false`. L'impostazione del valore su false a livello di cluster non è consigliata. Il valore `true` di è ottimale e impostare il parametro in base alla natura dei dati e delle query.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
