---
title: Il riduttore è lento in Azure HDInsight
description: Il riduttore è lento in Azure HDInsight da possibili distorsioni dei dati
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5fe1b2c720bbc55e8e245f9592755e046e0b04a3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968436"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: Il riduttore è lento in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si esegue una query, `insert into table1 partition(a,b) select a,b,c from table2` ad esempio il piano di query, avvia una serie di riduttori, ma i dati di ogni partizione passano a un singolo Reducer. In questo modo la query sarà lenta quanto il tempo impiegato dal riduttore della partizione più grande.

## <a name="cause"></a>Causa

Aprire [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verificare il valore di set `hive.optimize.sort.dynamic.partition`.

Il valore di questa variabile deve essere impostato su true/false in base alla natura dei dati.

Se le partizioni nella tabella di input sono inferiori (ad eccezione di 10), quindi è il numero di partizioni di output e la variabile è impostata su `true`, in questo modo i dati vengono ordinati a livello globale e scritti usando un singolo riduttore per partizione. Anche se il numero di riduttori disponibili è più grande, è possibile che alcuni riduttori rimangano in ritardo a causa dell'asimmetria dei dati e che il parallelismo massimo non possa essere raggiunto. Quando viene modificato `false`in, più di un Reducer può gestire una singola partizione e più file più piccoli verranno scritti, causando un inserimento più rapido. Questo potrebbe influire ulteriormente sulle query, a causa della presenza di file più piccoli.

Il valore `true` è utile quando il numero di partizioni è maggiore e i dati non sono sbilanciati. In questi casi, il risultato della fase della mappa verrà scritto in modo tale che ogni partizione verrà gestita da un singolo riduttore, ottenendo migliori prestazioni di query successive.

## <a name="resolution"></a>Risoluzione

1. Provare a ripartizionare i dati per normalizzarli in più partizioni.

1. Se #1 non è possibile, impostare il valore della configurazione su false nella sessione di e riprovare a eseguire la query. [https://login.microsoftonline.com/common/](`set hive.optimize.sort.dynamic.partition=false`). Non è consigliabile impostare il valore su false a livello di cluster. Il valore di `true` è ottimale e imposta il parametro come necessario in base alla natura dei dati e della query.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
