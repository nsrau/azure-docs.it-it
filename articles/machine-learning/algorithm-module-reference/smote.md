---
title: COLPÌ
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo PERCOSse nel servizio Azure Machine Learning per aumentare il numero di esempi a bassa incidenza in un set di dati usando il sovracampionamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516188"
---
# <a name="smote"></a>COLPÌ

Questo articolo descrive come usare il modulo **percosse** in Azure Machine Learning Designer (anteprima) per aumentare il numero di case sottorappresentati in un set di dati usato per Machine Learning. PERCOSse è un modo migliore per aumentare il numero di casi rari rispetto alla semplice duplicazione dei case esistenti.  

 Il modulo PERCOSse viene connesso a un set di dati *sbilanciato*. Esistono molti motivi per cui un set di dati potrebbe essere sbilanciato: la categoria di destinazione potrebbe essere rara nella popolazione oppure i dati potrebbero essere difficili da raccogliere. In genere, si usa PERCOSse quando la *classe* che si vuole analizzare è sotto-rappresentata. 
  
 Il modulo restituisce un set di dati che contiene gli esempi originali, oltre a un numero aggiuntivo di campioni di minoranza sintetica, a seconda della percentuale specificata.  
  
### <a name="more-about-smote"></a>Altre informazioni su PERCOSse

**Percossa** sta per la *tecnica di sovracampionamento della minoranza sintetica*. Si tratta di una tecnica statistica per aumentare il numero di case nel set di dati in modo bilanciato.  Il modulo funziona generando nuove istanze da casi di minoranza esistenti forniti come input. Questa implementazione di COLPÌ non **modifica il** numero di case di maggioranza.

Le nuove istanze non sono solo copie di casi di minoranza esistenti; al contrario, l'algoritmo accetta esempi dello *spazio delle funzionalità* per ogni classe di destinazione e i relativi adiacenti più vicini e genera nuovi esempi che combinano le funzionalità del case di destinazione con le funzionalità degli elementi adiacenti. Questo approccio aumenta le funzionalità disponibili per ogni classe e rende più generali gli esempi.
  
PERCOSsa accetta l'intero set di dati come input, ma aumenta la percentuale dei soli casi di minoranza. Si supponga, ad esempio, di disporre di un set di dati sbilanciato in cui solo l'1% dei case dispone del valore di destinazione A (classe di minoranza) e il valore B del 99% dei case. Per aumentare la percentuale di casi di minoranza al doppio della percentuale precedente, immettere 200 per la **percentuale di percosse** nelle proprietà del modulo.  
  
## <a name="examples"></a>Esempi  

Si consiglia di provare a usare **percosse** con un set di dati di piccole dimensioni per verificarne il funzionamento. Nell'esempio seguente viene usato il set di dati della donazione di sangue disponibile in Azure Machine Learning Designer.
  
Se si aggiunge il set di dati a una pipeline e si fa clic su **Visualizza** nell'output del set di dati, si noterà che, in 748 righe o case del set di dati, sono presenti 570 casi (76%) della classe 0 e 178 casi (24%) della classe 1. Sebbene questo non sia terribilmente sbilanciato, la classe 1 rappresenta le persone che hanno donato sangue, quindi queste righe contengono lo *spazio delle funzionalità* che si desidera modellare.
 
Per aumentare il numero di case, è possibile impostare il valore di **colpì la percentuale**, usando multipli di 100, come indicato di seguito:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Set di dati originale<br /><br /> (equivalente a **percosse percentuale** = **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Percentuale di percosse** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Percentuale di percosse** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**Percentuale di percosse** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  L'aumento del numero di case con COLPÌ non garantisce la produzione di modelli più accurati. È consigliabile provare a eseguire la pipeline con percentuali diverse, set di funzionalità diversi e numeri diversi di adiacenti più vicini per vedere in che modo l'aggiunta dei case influisce sul modello.  
  
## <a name="how-to-configure-smote"></a>Come configurare PERCOSse
  
1.  Aggiungere il modulo PERCOSd alla pipeline. È possibile trovare il modulo in moduli di trasformazione dei dati nella categoria manipolazione.

2. Connettere il set di dati che si desidera aumentare. Se si desidera specificare lo spazio di funzionalità per la compilazione dei nuovi casi, utilizzando solo colonne specifiche o escludendo alcuni, utilizzare il modulo [Select Columns in DataSet](select-columns-in-dataset.md) per isolare le colonne che si desidera utilizzare prima di utilizzare **percosse**.
  
    In caso contrario, la creazione di nuovi case con **percosse** si basa su **tutte** le colonne fornite come input. Almeno una colonna delle colonne della funzionalità è numerica.
  
3.  Verificare che sia selezionata la colonna contenente l'etichetta o la classe di destinazione.  **Percosse** accetta solo etichette binarie.
  
4.  Il modulo **percosd** identifica automaticamente la classe minoritaria nella colonna Label, quindi ottiene tutti gli esempi per la classe di minoranza. Tutte le colonne non possono avere NaN.
  
5.  Nell'opzione **percentuale di percosse** , digitare un numero intero che indichi la percentuale di destinazione di casi di minoranza nel set di dati di output. Ad esempio:  
  
    - Digitare **0** (%). Il modulo PERCOSse restituisce esattamente lo stesso set di dati fornito come input, non aggiungendo nuovi casi di minoranza. In questo set di dati la proporzione della classe non è stata modificata.  
  
    -   Digitare **100** (%). Il modulo PERCOSso genera nuovi casi di minoranza, aggiungendo lo stesso numero di casi di minoranza nel set di dati originale. Poiché non aumenta il numero di case di maggioranza, la percentuale di case di ogni classe è stata modificata.  
  
    -   Digitare **200** (%). Il modulo raddoppia la percentuale di casi di minoranza rispetto al set di dati originale. Questa operazione **non** comporta la presenza di un doppio numero di casi di minoranza come prima.  Piuttosto, le dimensioni del set di dati vengono aumentate in modo che il numero di case di maggioranza rimanga invariato e il numero di case minoritarie venga aumentato fino a quando non corrisponde al valore percentuale desiderato.  
  
    > [!NOTE]
    > Usare solo multipli di 100 per la percentuale di PERCOSse.

6.  Usare l'opzione **numero di adiacenti più vicini** per determinare la dimensione dello spazio di funzionalità usato dall'algoritmo percosse quando si crea un nuovo case. Un *router adiacente più vicino* è una riga di dati (un caso) simile a un case di destinazione. La distanza tra due casi viene misurata combinando i vettori ponderati di tutte le funzionalità.  
  
     + Aumentando il numero di adiacenti più vicini, si ottengono funzionalità da più case.
     + Mantenendo il numero degli adiacenti più vicini, è possibile utilizzare le funzionalità più simili a quelle dell'esempio originale.  
  
7. Digitare un valore nella casella di testo **seed casuale** se si desidera garantire gli stessi risultati rispetto alle esecuzioni della stessa pipeline con gli stessi dati. In caso contrario, il modulo genera un valore di inizializzazione casuale basato sui valori di clock del processore quando viene distribuita la pipeline, che può causare risultati leggermente diversi rispetto alle esecuzioni.

8. Eseguire la pipeline.  
  
     L'output del modulo è un set di dati contenente le righe originali più un numero di righe aggiunte con i casi di minoranza.  

## <a name="technical-notes"></a>Note tecniche

+ Quando si pubblica un modello che usa il modulo **percosse** , rimuovere **percosse** dalla pipeline predittiva prima che venga pubblicato come servizio Web.  Il motivo è che il PERCOSso è progettato per migliorare un modello durante il training e non è destinato all'assegnazione dei punteggi.  Se una pipeline predittiva pubblicata contiene il modulo PERCOSse, potrebbe essere presente un errore.

+ È spesso possibile ottenere risultati migliori se si applica la pulizia del valore mancante o altre trasformazioni per correggere i dati prima di applicare il sistema di PERCOSse. 

+ Alcuni ricercatori hanno verificato se il PERCOSso è efficace sui dati ad alta dimensionalità o di tipo sparse, ad esempio quelli usati nella classificazione di testo o nei set di dati di genomica. Questo documento contiene un riepilogo degli effetti e della validità teorica dell'applicazione di PERCOSse in questi casi: [Blagus e Lusa: percosse per i dati di classe altamente dimensionali non bilanciati](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Se il set di dati non è efficace, altri approcci che è possibile considerare includono diversi metodi per sovracampionare i casi di minoranza o sottocampionare la maggior parte dei casi, oltre a tecniche di insieme che consentono direttamente allo strumenti di apprendimento, tramite il clustering, insaccamento o boosting adattivo.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 

