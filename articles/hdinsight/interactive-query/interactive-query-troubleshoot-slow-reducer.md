---
title: Il riduttore è lento in Azure HDInsight
description: Il riduttore è lento in Azure HDInsight da possibili distorsioni dei dati
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 74e5214b304870b48e6c6f3ec34b7a5bae0e389a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288864"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]