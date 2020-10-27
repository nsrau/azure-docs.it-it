---
title: Il riduttore è lento in Azure HDInsight
description: Il riduttore è lento in Azure HDInsight da possibili distorsioni dei dati
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2fbe68c5dfe9b0d5b60e1e3eeabc95b201b878c6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532817"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: il riduttore è lento in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si esegue una query, ad esempio `insert into table1 partition(a,b) select a,b,c from table2` il piano di query, avvia una serie di riduttori, ma i dati di ogni partizione passano a un singolo Reducer. In questo modo la query sarà lenta quanto il tempo impiegato dal riduttore della partizione più grande.

## <a name="cause"></a>Causa

Aprire [deeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verificare il valore di set `hive.optimize.sort.dynamic.partition` .

Il valore di questa variabile deve essere impostato su true/false in base alla natura dei dati.

Se le partizioni nella tabella di input sono inferiori (ad eccezione di 10), quindi è il numero di partizioni di output e la variabile è impostata su `true` , in questo modo i dati vengono ordinati a livello globale e scritti usando un singolo riduttore per partizione. Anche se il numero di riduttori disponibili è più grande, è possibile che alcuni riduttori rimangano in ritardo a causa dell'asimmetria dei dati e che il parallelismo massimo non possa essere raggiunto. Quando viene modificato in `false` , più di un Reducer può gestire una singola partizione e più file più piccoli verranno scritti, causando un inserimento più rapido. Questo potrebbe influire ulteriormente sulle query, a causa della presenza di file più piccoli.

Il valore è `true` utile quando il numero di partizioni è maggiore e i dati non sono sbilanciati. In questi casi, il risultato della fase della mappa verrà scritto in modo tale che ogni partizione verrà gestita da un singolo riduttore, ottenendo migliori prestazioni di query successive.

## <a name="resolution"></a>Soluzione

1. Provare a ripartizionare i dati per normalizzarli in più partizioni.

1. Se #1 non è possibile, impostare il valore della configurazione su false nella sessione di e riprovare a eseguire la query. `set hive.optimize.sort.dynamic.partition=false`. Non è consigliabile impostare il valore su false a livello di cluster. Il valore di `true` è ottimale e imposta il parametro come necessario in base alla natura dei dati e della query.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).