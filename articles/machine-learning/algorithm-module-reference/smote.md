---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo SMOTE in Azure Machine Learning per aumentare il numero di esempi a bassa incidenza in un set di dati usando il sovracampionamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477460"
---
# <a name="smote"></a>SMOTE

Questo articolo descrive come usare il modulo SMOTE nella finestra di progettazione di Azure Machine Learning (anteprima) per aumentare il numero di casi sottorappresentati in un set di dati usato per l'apprendimento automatico. SMOTE è un modo migliore per aumentare il numero di casi rari rispetto alla semplice duplicazione dei casi esistenti.  

Si connette il modulo SMOTE a un dataset che è *sbilanciato*. Esistono molti motivi per cui un set di dati potrebbe essere sbilanciato. Ad esempio, la categoria di destinazione potrebbe essere rara nella popolazione o i dati potrebbero essere difficili da raccogliere. In genere, si utilizza SMOTE quando la *classe* che si desidera analizzare è sottorappresentata. 
  
Il modulo restituisce un set di dati che contiene gli esempi originali. Restituisce inoltre un numero di campioni di minoranza sintetici, a seconda della percentuale specificata.  
  
## <a name="more-about-smote"></a>Più informazioni su SMOTE

Synthetic Minority Oversampling Technique (SMOTE) è una tecnica statistica per aumentare il numero di case nel set di dati in modo bilanciato. Il modulo funziona generando nuove istanze da casi di minoranza esistenti forniti come input. Questa implementazione di SMOTE *non* modifica il numero di casi maggioriarili.

I nuovi casi non sono solo copie di casi di minoranza esistenti. Al contrario, l'algoritmo preleva esempi dello *spazio delle funzionalità* per ogni classe di destinazione e i relativi vicini più vicini. L'algoritmo genera quindi nuovi esempi che combinano le caratteristiche del caso di destinazione con le caratteristiche dei suoi vicini. Questo approccio aumenta le caratteristiche disponibili per ciascuna classe e rende i campioni più generali.
  
SMOTE accetta l'intero set di dati come input, ma aumenta la percentuale di soli casi di minoranza. Si supponga, ad esempio, di disporre di un set di dati sbilanciato in cui solo l'1% dei case ha il valore di destinazione A (la classe di minoranza) e il 99% dei case ha il valore B. Per aumentare la percentuale di casi di minoranza a due volte la percentuale precedente, immettere **200** per **percentuale SMOTE** nelle proprietà del modulo.  
  
## <a name="examples"></a>Esempi  

È consigliabile provare a usare SMOTE con un set di dati di dimensioni inferiori per verificarne il funzionamento. L'esempio seguente usa il set di dati Blood Donation disponibile nella finestra di progettazione di Azure Machine Learning.The following example uses the Blood Donation dataset available in Azure Machine Learning designer.
  
Se si aggiunge il set di dati a una pipeline e si seleziona **Visualizza** nell'output del set di dati, è possibile notare che delle 748 righe o case nel set di dati, 570 casi (76%) sono della classe 0 e 178 casi (24%) sono della classe 1. Anche se questo risultato non è terribilmente squilibrato, Classe 1 rappresenta le persone che hanno donato il sangue, quindi queste righe contengono lo *spazio delle funzionalità* che si desidera modellare.
 
Per aumentare il numero di case, è possibile impostare il valore della **percentuale SMOTE**, utilizzando multipli di 100, come indicato di seguito:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Set di dati originale<br /><br /> (equivalente alla **percentuale** = SMOTE**0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**Percentuale** = SMOTE**100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Percentuale** = SMOTE**200**|570<br /><br /> 52%|534<br /><br /> 48%|1.104|  
|**Percentuale** = SMOTE**300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Non è garantito che l'aumento del numero di casi utilizzando SMOTE produca modelli più accurati. Provare il pipelining con percentuali diverse, set di feature diversi e diversi numeri di vicini più vicini per vedere in che modo l'aggiunta di casi influenza il modello.  
  
## <a name="how-to-configure-smote"></a>Come configurare SMOTE
  
1.  Aggiungere il modulo SMOTE alla pipeline. È possibile trovare il modulo in **Moduli di trasformazione dati**, nella categoria **Manipolazione.**

2. Connettere il set di dati che si desidera incrementare. Se si desidera specificare lo spazio delle funzionalità per la creazione dei nuovi casi, utilizzando solo colonne specifiche o escludendone alcune, utilizzare il modulo [Seleziona colonne nel set di dati.](select-columns-in-dataset.md) È quindi possibile isolare le colonne che si desidera utilizzare prima di utilizzare SMOTE.
  
    In caso contrario, la creazione di nuovi casi tramite SMOTE si basa su *tutte le* colonne fornite come input. Almeno una colonna delle colonne di entità geografiche è numerica.
  
3.  Assicurarsi che la colonna che contiene l'etichetta, o classe di destinazione, sia selezionata. SMOTE accetta solo etichette binarie.
  
4.  Il modulo SMOTE identifica automaticamente la classe di minoranza nella colonna label e quindi ottiene tutti gli esempi per la classe di minoranza. Tutte le colonne non possono avere valori NaN.
  
5.  Nell'opzione **Percentuale SMOTE** immettere un numero intero che indichi la percentuale di destinazione dei casi di minoranza nel set di dati di output. Ad esempio:  
  
    - Immettere **0**. Il modulo SMOTE restituisce esattamente lo stesso set di dati fornito come input. Non si aggiungono nuovi casi di minoranza. In questo set di dati, la proporzione della classe non è stata modificata.  
  
    - Immettere **100**. Il modulo SMOTE genera nuovi casi di minoranza. Aggiunge lo stesso numero di casi di minoranza che si trovavano nel set di dati originale. Poiché SMOTE non aumenta il numero di casi di maggioranza, la proporzione dei casi di ogni classe è cambiata.  
  
    - Si immette **200**. Il modulo raddoppia la percentuale di casi di minoranza rispetto al set di dati originale. Ciò *non* comporta il doppio dei casi di minoranza di prima. Piuttosto, le dimensioni del set di dati vengono aumentate in modo tale che il numero di casi maggiori. Il numero di casi di minoranza viene aumentato fino a quando non corrisponde al valore percentuale desiderato.  
  
    > [!NOTE]
    > Utilizzare solo multipli di 100 per la percentuale SMOTE.

6.  Utilizzare l'opzione **Numero di vicini più vicini** per determinare le dimensioni dello spazio delle funzionalità utilizzato dall'algoritmo SMOTE per la creazione di nuovi casi. Un vicino di casa più vicino è una riga di dati (un caso) simile a un caso di destinazione. La distanza tra i due casi viene misurata combinando i vettori di peso di tutte le funzioni.  
  
    + Aumentando il numero di vicini più vicini, si ottengono funzionalità da più casi.
    + Mantenendo basso il numero di vicini più vicini, si utilizzano funzionalità più simili a quelle del campione originale.  
  
7. Immettere un valore nella casella Valore di **serie casuale** se si desidera garantire che gli stessi risultati superino le esecuzioni della stessa pipeline, con gli stessi dati. In caso contrario, il modulo genera un valore di controllo casuale in base ai valori di clock del processore quando viene distribuita la pipeline. La generazione di un seme casuale può causare risultati leggermente diversi nelle esecuzioni.

8. Inviare la pipeline.  
  
   L'output del modulo è un set di dati che contiene le righe originali più un numero di righe aggiunte con casi di minoranza.  

## <a name="technical-notes"></a>Note tecniche

+ Quando si pubblica un modello che usa il modulo **SMOTE,** rimuovere **SMOTE** dalla pipeline predittiva prima che venga pubblicato come servizio Web. Il motivo è che SMOTE è destinato a migliorare un modello durante il training, non per il punteggio. È possibile che venga visualizzato un errore se una pipeline predittiva pubblicata contiene il modulo SMOTE.

+ Spesso è possibile ottenere risultati migliori se si puliscono i valori mancanti o si applicano altre trasformazioni per correggere i dati prima di applicare SMOTE. 

+ Alcuni ricercatori hanno studiato se SMOTE è efficace su dati ad alta dimensione o sparse, come i dati utilizzati nella classificazione del testo o nei set di dati genomici. Questo documento ha una buona sintesi degli effetti e della validità teorica dell'applicazione di SMOTE in questi casi: [Blagus e Lusa: SMOTE per dati sbilanciati di classe alta- dimensionale](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Se SMOTE non è efficace nel set di dati, altri approcci che è possibile prendere in considerazione includono:If SMOTE is not effective in your dataset, other approaches that you might consider include:
  + Metodi per sovracampionare i casi di minoranza o sottocampionare i casi maggiorionici.
  + Tecniche di adattamento che aiutano lo studente direttamente utilizzando il clustering, il bagging o l'amplificazione adattiva.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

