---
title: Ritaglia valori
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo clip values nel servizio Azure Machine Learning per rilevare gli outlier e ritagliare o sostituire i valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: a45755ca13aed6a514b548674ef424c40238fa42
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694740"
---
# <a name="clip-values"></a>Ritaglia valori

Questo articolo descrive un modulo di Azure Machine Learning interfaccia visiva.

Usare il modulo clip Values per identificare e facoltativamente sostituire i valori di dati che si trovano al di sopra o al di sotto di una soglia specificata con una media, una costante o un altro valore sostitutivo.  

Connettere il modulo a un set di dati che contiene i numeri da ritagliare, scegliere le colonne da usare, quindi impostare una soglia o un intervallo di valori e un metodo di sostituzione. Il modulo può restituire solo i risultati o i valori modificati aggiunti al set di dati originale.

## <a name="how-to-configure-clip-values"></a>Come configurare i valori di ritaglio

Prima di iniziare, identificare le colonne che si vuole ritagliare e il metodo da usare. È consigliabile testare prima qualsiasi metodo di ritaglio su un piccolo subset di dati.

Il modulo applica gli stessi criteri e il metodo di sostituzione a **tutte** le colonne incluse nella selezione. Assicurarsi pertanto di escludere le colonne che non si desidera modificare.

Se è necessario applicare metodi di ritaglio o criteri diversi ad alcune colonne, è necessario utilizzare una nuova istanza di **clip values** per ogni set di colonne analoghe.

1.  Aggiungere il modulo **clip values** alla pipeline e connetterlo al set di dati che si vuole modificare. È possibile trovare questo modulo in **trasformazione dati**nella categoria **Ridimensiona e Riduci** . 
  
1.  In **elenco di colonne**utilizzare il selettore di colonna per scegliere le colonne a cui verranno applicati i **valori di ritaglio** .  
  
1.  Per **set di soglie**, scegliere una delle opzioni seguenti dall'elenco a discesa. Queste opzioni determinano la modalità di impostazione dei limiti superiore e inferiore per i valori accettabili rispetto a quelli che devono essere ritagliati.  
  
    - **ClipPeaks**: quando si ritagliano i valori per i picchi, si specifica solo un limite superiore. I valori maggiori di tale valore limite vengono sostituiti.
  
    -  **ClipSubpeaks**: quando si ritagliano i valori in base ai picchi, si specifica solo un limite inferiore. I valori minori di tale valore limite vengono sostituiti.  
  
    - **ClipPeaksAndSubpeaks**: quando si ritagliano i valori in base a picchi e sottopicchi, è possibile specificare i limiti superiore e inferiore. I valori esterni a tale intervallo vengono sostituiti. I valori che corrispondono ai valori limite non vengono modificati.
  
1.  A seconda della selezione effettuata nel passaggio precedente, è possibile impostare i valori soglia seguenti: 

    + **Soglia inferiore**: visualizzata solo se si sceglie **ClipSubPeaks**
    + **Soglia superiore**: visualizzata solo se si sceglie **ClipPeaks**
    + **Soglia**: visualizzata solo se si sceglie **ClipPeaksAndSubPeaks**

    Per ogni tipo di soglia, scegliere **costante** o **percentile**.

1. Se si seleziona **costante**, digitare il valore massimo o minimo nella casella di testo. Si supponga, ad esempio, di avere la conoscenza del valore 999 usato come valore segnaposto. È possibile scegliere **costante** per la soglia superiore e digitare 999 in **valore costante per la soglia superiore**.
  
1. Se si sceglie **percentile**, i valori della colonna vengono vincolati a un intervallo percentile. 

    Si supponga, ad esempio, di voler tenere solo i valori nell'intervallo 10-80 percentile e sostituire tutti gli altri. Scegliere **percentile**, quindi digitare 10 per **valore percentile per soglia inferiore**e digitare 80 per **valore percentile per soglia superiore**. 

    Per alcuni esempi su come usare gli intervalli percentile, vedere la sezione relativa ai [percentile](#examples-for-clipping-using-percentiles) .  
  
1.  Definire un valore sostitutivo.

    I numeri che corrispondono esattamente ai limiti specificati sono considerati all'interno dell'intervallo di valori consentiti e pertanto non vengono sostituiti. Tutti i numeri che non rientrano nell'intervallo specificato vengono sostituiti con il valore sostitutivo. 
  
    + **Valore sostitutivo per i picchi**: definisce il valore da sostituire con tutti i valori di colonna maggiori della soglia specificata.  
    + **Valore sostitutivo per i sottopicchi**: definisce il valore da usare come sostituto per tutti i valori di colonna minori della soglia specificata.  
    + Se si usa l'opzione **ClipPeaksAndSubpeaks** , è possibile specificare valori di sostituzione distinti per i valori di ritaglio superiore e inferiore.  

    Sono supportati i valori di sostituzione seguenti:  
  
    -   **Threshold**: sostituisce i valori ritagliati con il valore di soglia specificato.  
  
    -   **Mean**: sostituisce i valori ritagliati con la media dei valori della colonna. La media viene calcolata prima che i valori vengano ritagliati.  
  
    -   **Mediana**: sostituisce i valori ritagliati con la mediana dei valori della colonna. Il valore mediano viene calcolato prima che i valori vengano ritagliati.   
  
    -   **Mancante**. Sostituisce i valori ritagliati con il valore mancante (vuoto).  
  
1.  **Aggiungi colonne indicatore**: selezionare questa opzione se si desidera generare una nuova colonna che indica se l'operazione di ritaglio specificata è stata applicata ai dati della riga. Questa opzione è utile quando si esegue il test di un nuovo set di valori di ritaglio e sostituzione.  
  
1. **Overwrite flag**: indica come si desidera che vengano generati i nuovi valori. Per impostazione predefinita, **clip values** costruisce una nuova colonna con i valori di picco tagliati alla soglia desiderata. I nuovi valori sovrascrivono la colonna originale.  
  
    Per salvare la colonna originale e aggiungere una nuova colonna con i valori ritagliati, deselezionare questa opzione.  
  
1.  Eseguire la pipeline.  
  
    Fare clic con il pulsante destro del mouse sull'output del modulo **clip values** e selezionare **Visualizza** per esaminare i valori e verificare che l'operazione di ritaglio soddisfi le aspettative.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Esempi per il ritaglio con percentile

Per comprendere il funzionamento del ritaglio per percentile, prendere in considerazione un set di dati con 10 righe, che hanno un'istanza ciascuno dei valori 1-10.  
  
- Se si utilizza percentile come soglia superiore, con il valore per il 90 ° percentile, il 90% di tutti i valori nel set di dati deve essere minore di tale valore.  
  
- Se si utilizza percentile come soglia inferiore, al valore per il 10 ° percentile, il 10% di tutti i valori nel set di dati deve essere minore di tale valore.  
  
1.  Per **set di soglie**, scegliere **ClipPeaksAndSubPeaks**.  
  
1.  Per la **soglia superiore**scegliere **percentile**e per il **numero percentile**digitare 90.  
  
1.  Per **valore sostitutivo superiore**scegliere **valore mancante**.  
  
1.  Per la **soglia inferiore**, scegliere **percentile**e per il **numero percentile**digitare 10.  
  
1.  Per **valore sostitutivo inferiore**, scegliere **valore mancante**.  
  
1.  Deselezionare l'opzione **Sovrascrivi flag**, quindi selezionare l'opzione **Aggiungi colonna indicatore**.  
  
A questo punto, provare la stessa pipeline usando 60 come soglia percentile superiore e 30 come soglia percentile inferiore e usare il valore soglia come valore di sostituzione. Nella tabella seguente vengono confrontati questi due risultati:  
  
1.  Sostituisci con Missing; Soglia superiore = 90; Soglia inferiore = 20  
  
1.  Sostituisci con soglia; Percentile superiore = 60; Percentile inferiore = 40  
  
|Dati originali|Sostituisci con Missing|Sostituisci con soglia|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
