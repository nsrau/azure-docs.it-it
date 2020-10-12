---
title: Monitorare più serie temporali in una singola regola di avviso della metrica
description: Avviso su larga scala con una sola regola di avviso per più serie temporali
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 10/04/2020
ms.subservice: alerts
ms.openlocfilehash: 81e09e6d9c6a57339f1d6f1eb5ce4f494555fa19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704481"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Monitorare più serie temporali in una singola regola di avviso della metrica

Una singola regola di avviso di metrica può essere usata per monitorare una o più serie temporali metrica, semplificando il monitoraggio delle risorse su larga scala.

## <a name="metric-time-series"></a>Serie temporale metrica

Una serie temporale metrica è una serie di misurazioni (o "valori metrici") acquisite in un periodo di tempo. 

Ad esempio:

- Utilizzo della CPU di una macchina virtuale
- Byte in ingresso (in ingresso) a un account di archiviazione
- Numero di richieste non riuscite di un'applicazione Web



## <a name="alert-rule-on-a-single-time-series"></a>Regola di avviso su una singola serie temporale
Una regola di avviso monitora una singola serie temporale quando soddisfa tutte le condizioni seguenti:
-   La regola esegue il monitoraggio di una singola risorsa di destinazione 
-   Contiene una singola condizione
-   Valuta una metrica senza scegliere le dimensioni (presupponendo che la metrica supporti le dimensioni)

Un esempio di una regola di avviso di questo tipo (con solo le proprietà pertinenti visualizzate):
-   Risorsa di destinazione: *myVM1*
-   Metrica: *percentuale CPU*
-   Operatore: *maggiore di*
-   Soglia: *70*


Per questa regola di avviso, viene monitorata una singola serie temporale della metrica:
-   Percentuale CPU in cui *Resource*=' myVM1' > 70%

![Una regola di avviso su una singola serie temporale](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Regola di avviso su più serie temporali
Una regola di avviso monitora più serie temporali se usa almeno una delle funzionalità seguenti: 
-   Più risorse
-   Più condizioni 
-   Uso di più dimensioni


## <a name="multiple-resources-multi-resource"></a>Più risorse (multi-Resource)

Una singola regola di avviso di metrica può monitorare più risorse, purché le risorse siano dello stesso tipo e esistano nella stessa area di Azure. L'utilizzo di questo tipo di regola riduce la complessità e il numero totale di regole di avviso che è necessario gestire. 

Esempio di una regola di avviso di questo tipo:
-   Risorsa di destinazione: *myVM1, myVM2*
-   Metrica: *percentuale CPU*
-   Operatore: *maggiore di*
-   Soglia: *70*

Per questa regola di avviso, due serie temporali della metrica vengono monitorate separatamente:
-   Percentuale CPU in cui *Resource*=' myVM1' > 70%
-   Percentuale CPU in cui *Resource*=' myVM2' > 70%

![Una regola di avviso per più risorse](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
In una regola di avviso per più risorse, la condizione viene valutata **separatamente** per ogni risorsa (o più precisamente, per ogni serie temporale metrica corrispondente a ogni risorsa). Ciò significa che anche gli avvisi vengono generati separatamente per ogni risorsa.

Si supponga, ad esempio, di aver impostato la regola di avviso sopra per monitorare la CPU superiore al 70%. Nel periodo di tempo valutato (ovvero negli ultimi 5 minuti)
-   La *percentuale di CPU* di *myVM1* è maggiore del 70% 
-   La *percentuale di CPU* di *myVM2* è al 50%

La regola di avviso viene attivata in *myVM1*, ma non in *myVM2*. Questi avvisi attivati sono indipendenti. Possono inoltre risolversi in momenti diversi a seconda del comportamento individuale di ogni macchina virtuale.

Per altre informazioni sulle regole di avviso per più risorse e sui tipi di risorse supportati per questa funzionalità, vedere [monitoraggio su larga scala tramite gli avvisi delle metriche in monitoraggio di Azure](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> In una regola di avviso metrica che monitora più risorse è consentita una sola condizione.

## <a name="multiple-conditions-multi-condition"></a>Più condizioni (multi-Condition)

Una singola regola di avviso di metrica può inoltre monitorare fino a cinque condizioni per ogni regola di avviso. 

Ad esempio:

- Risorsa di destinazione: *myVM1*
- Condition1
  - Metrica: *percentuale CPU*
  - Operatore: *maggiore di*
  - Soglia: *70*
- Condizione2
  - Metrica: *rete totale*
  - Operatore: *maggiore di*
  - Soglia: *20 MB*

Per questa regola di avviso, vengono monitorate due serie temporali della metrica:

- Percentuale CPU in cui *Resource*=' myVM1' > 70%
- Rete in totale dove *Resource*=' myVM1' > 20 MB

![Una regola di avviso di più condizioni](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Tra le condizioni viene usato un operatore ' AND '. La regola di avviso genera un avviso quando vengono soddisfatte **tutte** le condizioni. L'avviso attivato viene risolto se almeno una delle condizioni non viene più soddisfatta. 

> [!NOTE]
> Quando si usano le dimensioni in una regola di avviso con più condizioni, sono previste restrizioni. Per altre informazioni, vedere [restrizioni quando si usano le dimensioni in una regola di avviso per la metrica con più condizioni](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Più dimensioni (MULTIDIMENSIONE)

Una singola regola di avviso di metrica può inoltre monitorare più valori di dimensione di una metrica. Le dimensioni di una metrica sono coppie nome-valore che contengono dati aggiuntivi per descrivere il valore della metrica. Ad esempio, la metrica delle **transazioni** di un account di archiviazione ha una dimensione denominata **nome API**, che descrive il nome dell'API chiamato da ogni transazione (ad esempio, GetBlob, DeleteBlob, PutPage). L'uso delle dimensioni è facoltativo, ma consente di filtrare la metrica e di monitorare solo serie temporali specifiche, invece di monitorare la metrica come aggregazione di tutti i valori dimensionali combinati. 

Ad esempio, è possibile scegliere di generare un avviso quando il numero di transazioni è elevato in tutti i nomi di API (ovvero i dati aggregati) oppure suddividerli in un solo avviso quando il numero di transazioni è elevato per nomi di API specifici. 

Un esempio di una regola di avviso che monitora più dimensioni è:

- Risorsa di destinazione: *myStorage1*
- Metrica: *transazioni*
- Dimensioni
  * Nome API = *GetBlob, DeleteBlob, PutPage*
- Operatore: *maggiore di*
- Soglia: *70*

Per questa regola di avviso, è in corso il monitoraggio di tre serie temporali della metrica:

- Transazioni in cui *Resource*=' myStorage1' e *nome API*=' getblob ' > 70
- Transazioni in cui *Resource*=' myStorage1' e *nome API*=' DeleteBlob ' > 70
- Transazioni in cui *Resource*=' myStorage1' e *nome API*=' PutPage ' > 70

![Una regola di avviso MULTIDIMENSIONE con valori di una dimensione](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Una regola di avviso per la metrica MULTIDIMENSIONE può inoltre monitorare più valori di dimensione da **diverse** dimensioni di una metrica. In questo caso, la regola di avviso monitora **separatamente** tutte le combinazioni di valori di dimensioni dei valori della dimensione selezionati.

Esempio di questo tipo di regola di avviso:

- Risorsa di destinazione: *myStorage1*
- Metrica: *transazioni*
- Dimensioni
  * Nome API = *GetBlob, DeleteBlob, PutPage*
  * Autenticazione = *SAS, AccountKey*
- Operatore: *maggiore di*
- Soglia: *70*

Per questa regola di avviso, è in corso il monitoraggio di sei serie temporali metrica:

- Transazioni in cui *Resource*=' myStorage1' e *nome API*=' GetBlob ' e *Authentication*=' SAS ' > 70
- Transazioni in cui *Resource*=' myStorage1' e *nome API*=' GetBlob ' e *Authentication*=' AccountKey ' > 70
- Transazioni in cui *Resource*= "myStorage1" e *nome API*= "DeleteBlob" e *Authentication*= "SAS" > 70
- Transazioni in cui *Resource*= "myStorage1" e *nome API*= "DeleteBlob" e *Authentication*= "accountKey" > 70
- Transazioni in cui *Resource*= "myStorage1" e *nome API*= "PutPage" e *Authentication*= "SAS" > 70
- Transazioni in cui *Resource*= "myStorage1" e *nome API*= "PutPage" e *Authentication*= "accountKey" > 70

![Una regola di avviso MULTIDIMENSIONE con valori provenienti da più dimensioni](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Funzionalità avanzate MULTIDIMENSIONE

1.  **Selezione di tutte le dimensioni correnti e future** : è possibile scegliere di monitorare tutti i valori possibili di una dimensione, inclusi i valori futuri. Una regola di avviso di questo tipo verrà ridimensionata automaticamente per monitorare tutti i valori della dimensione senza che sia necessario modificare la regola di avviso ogni volta che un valore di dimensione viene aggiunto o rimosso.
2.  **Esclusione delle dimensioni** : la selezione dell'operatore ' ≠' (esclusione) per un valore della dimensione equivale alla selezione di tutti gli altri valori di tale dimensione, inclusi i valori futuri.
3.  **Dimensioni nuove e personalizzate** : i valori delle dimensioni visualizzati nel portale di Azure sono basati sui dati delle metriche raccolti negli ultimi tre giorni. Se il valore della dimensione che si sta cercando non è ancora stato emesso, è possibile aggiungere un valore di dimensione personalizzato.

![Funzionalità avanzate MULTIDIMENSIONE](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Prezzi avvisi metrica

Il prezzo delle regole di avviso delle metriche è disponibile nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Quando si crea una regola di avviso per la metrica, la stima del prezzo fornita è basata sulle funzionalità selezionate e sul numero di serie temporali monitorate, che è determinato dalla configurazione della regola e dai valori della metrica corrente. Tuttavia, l'addebito mensile si basa sulle valutazioni effettive delle serie temporali e può quindi differire dalla stima originale se alcune serie temporali non dispongono di dati da valutare o se la regola di avviso USA funzionalità che possono aumentare la scalabilità dinamicamente.

Una regola di avviso, ad esempio, può mostrare una stima di prezzo elevata se utilizza la funzionalità MULTIDIMENSIONE e viene selezionata una serie di valori di dimensioni elevato, con conseguente monitoraggio di molte serie temporali. Tuttavia, l'addebito effettivo per la regola di avviso può essere inferiore se non tutte le serie temporali risultanti dalle combinazioni di valori di dimensione hanno effettivamente dati da valutare.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Numero di serie temporali monitorate da una sola regola di avviso

Per evitare costi eccessivi, ogni regola di avviso può monitorare fino a 5000 serie temporali per impostazione predefinita. Per revocare questo limite dalla sottoscrizione, aprire un ticket di supporto.


## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul monitoraggio su larga scala usando gli avvisi della metrica e le [soglie dinamiche](alerts-dynamic-thresholds.md).
