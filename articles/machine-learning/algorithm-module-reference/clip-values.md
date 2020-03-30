---
title: Ritagliare valori
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Valori clip in Azure Machine Learning per rilevare gli outlier e ritagliare o sostituire i relativi valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456608"
---
# <a name="clip-values"></a>Ritagliare valori

Questo articolo descrive un modulo della finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo Valori clip per identificare e sostituire facoltativamente i valori dei dati che si trovano al di sopra o al di sotto di una soglia specificata con una media, una costante o un altro valore sostitutivo.  

Collegare il modulo a un set di dati che contiene i numeri che si desidera ritagliare, scegliere le colonne con cui lavorare e quindi impostare una soglia o un intervallo di valori e un metodo di sostituzione. Il modulo può restituire solo i risultati o i valori modificati aggiunti al set di dati originale.

## <a name="how-to-configure-clip-values"></a>Come configurare i valori di clip

Prima di iniziare, identificate le colonne da ritagliare e il metodo da utilizzare. Si consiglia di testare prima qualsiasi metodo di ritaglio su un piccolo sottoinsieme di dati.

Il modulo applica gli stessi criteri e lo stesso metodo di sostituzione a **tutte le** colonne incluse nella selezione. Pertanto, assicurarsi di escludere le colonne che non si desidera modificare.

Se è necessario applicare metodi di ritaglio o criteri diversi ad alcune colonne, è necessario utilizzare una nuova istanza di Valori di **ritaglio** per ogni set di colonne simili.

1.  Aggiungere il modulo **Valori clip** alla pipeline e connetterlo al set di dati che si desidera modificare. Questo modulo è disponibili in **Trasformazione dati**, nella categoria **Scala e riduci.** 
  
1.  In **Elenco di colonne**, utilizzare il Selettore colonne per scegliere le colonne a cui verranno applicati i valori di **clip.**  
  
1.  Per **Imposta soglie**, scegliere una delle seguenti opzioni dall'elenco a discesa. Queste opzioni determinano la modalità di impostazione dei limiti superiore e inferiore per i valori accettabili e i valori che devono essere ritagliati.  
  
    - **ClipPeaks**: Quando si ritagliano i valori in base ai picchi, si specifica solo un contorno superiore. I valori maggiori di tale valore limite vengono sostituiti.
  
    -  **ClipSubpeaks**: Quando si ritagliano i valori in base ai sottopicchi, si specifica solo un contorno inferiore. I valori inferiori a tale valore limite vengono sostituiti.  
  
    - **ClipPeaksAndSubpeaks**: Quando si ritagliano i valori in base a picchi e sottopicchi, è possibile specificare sia i limiti superiore che uno inferiore. I valori non rientrati in tale intervallo vengono sostituiti. I valori che corrispondono ai valori limite non vengono modificati.
  
1.  A seconda della selezione effettuata nel passaggio precedente, è possibile impostare i seguenti valori di soglia: 

    + **Soglia inferiore**: visualizzata solo se si sceglie **ClipSubPeaks**
    + **Soglia superiore**: Visualizzata solo se si sceglie **ClipPeaks**
    + **Soglia**: Visualizzata solo se si sceglie **ClipPeaksAndSubPeaks**

    Per ogni tipo di soglia, scegliere **Costante** o **Percentile**.

1. Se si seleziona **Costante**, digitare il valore massimo o minimo nella casella di testo. Si supponga, ad esempio, di sapere che il valore 999 è stato utilizzato come valore segnaposto. È possibile scegliere **Costante** per la soglia superiore e digitare 999 in **Valore costante per soglia superiore**.
  
1. Se si sceglie **Percentile**, i valori delle colonne vengono vincolati a un intervallo percentile. 

    Si supponga, ad esempio, di voler mantenere solo i valori compresi nell'intervallo di 10-80 percentili e di sostituirne tutti gli altri. Scegliere **Percentile**, quindi digitare 10 per **Valore percentile per soglia inferiore**e 80 per Valore **percentile per la soglia superiore**. 

    Vedere la sezione sui [percentili](#examples-for-clipping-using-percentiles) per alcuni esempi di come utilizzare gli intervalli percentili.  
  
1.  Definire un valore sostitutivo.

    I numeri che corrispondono esattamente ai limiti specificati vengono considerati all'interno dell'intervallo di valori consentito e pertanto non vengono sostituiti. Tutti i numeri che non rientrano nell'intervallo specificato vengono sostituiti con il valore sostitutivo. 
  
    + **Valore sostitutivo per picchi**: Definisce il valore da sostituire per tutti i valori di colonna che sono maggiori della soglia specificata.  
    + **Valore sostitutivo per i sottopicchi**: Definisce il valore da utilizzare in sostituzione di tutti i valori di colonna inferiori alla soglia specificata.  
    + Se si utilizza l'opzione **ClipPeaksAndSubpeaks,** è possibile specificare valori di sostituzione separati per i valori ritagliati superiore e inferiore.  

    Sono supportati i seguenti valori di sostituzione:  
  
    -   **Soglia**: Sostituisce i valori ritagliati con il valore di soglia specificato.  
  
    -   **Media**: Sostituisce i valori ritagliati con la media dei valori della colonna. La media viene calcolata prima che i valori vengano ritagliati.  
  
    -   **Median**: Sostituisce i valori ritagliati con la mediana dei valori della colonna. La mediana viene calcolata prima che i valori vengano ritagliati.   
  
    -   **Mancante**. Sostituisce i valori ritagliati con il valore mancante (vuoto).  
  
1.  **Aggiungi colonne indicatore**: Selezionare questa opzione se si desidera generare una nuova colonna che indica se l'operazione di ritaglio specificata è stata applicata ai dati in tale riga. Questa opzione è utile quando si sta testando un nuovo set di valori di ritaglio e sostituzione.  
  
1. **Flag di sovrascrittura**: Indicare la modalità di generazione dei nuovi valori. Per impostazione predefinita, **Valori clip** costruisce una nuova colonna con i valori di picco ritagliati alla soglia desiderata. I nuovi valori sovrascrivono la colonna originale.  
  
    Per mantenere la colonna originale e aggiungere una nuova colonna con i valori ritagliati, deselezionate questa opzione.  
  
1.  Inviare la pipeline.  
  
    Fare clic con il pulsante destro del mouse sul modulo **Valori clip** e selezionare **Visualizza** oppure selezionare il modulo e passare alla scheda **Uscite** nel pannello di destra, fare clic sull'icona dell'istogramma negli **output delle**porte , per esaminare i valori e assicurarsi che l'operazione di ritaglio abbia soddisfatto le aspettative.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Esempi per il ritaglio con i percentili

Per comprendere il funzionamento dell'operazione di taglio con i percentili, prendere in considerazione un set di dati con 10 righe, contenenti ognuna un'istanza dei valori da 1 a 10.  
  
- Se si usa il percentile come soglia superiore, impostando un valore pari al 90° percentile, il 90% di tutti i valori nel set di dati deve essere minore di tale valore.  
  
- Se si usa il percentile come soglia inferiore, impostando un valore pari al 10° percentile, il 10% di tutti i valori nel set di dati deve essere minore di tale valore.  
  
1.  Per **Imposta soglie**, scegliere **ClipPeaksAndSubPeaks**.  
  
1.  Per **Soglia superiore**scegliere **Percentile**e Per **Numero percentile digitare**90.  
  
1.  Per **Valore sostitutivo superiore**, scegliere Valore **mancante**.  
  
1.  Per **Lower threshold** scegliere **Percentile** e per **Percentile number** digitare 10.  
  
1.  Per **Valore sostitutivo inferiore**, scegliere Valore **mancante**.  
  
1.  Deselezionare l'opzione **Sovrascrivi flag**e selezionare l'opzione **Aggiungi colonna indicatore**.  
  
Provare ora la stessa pipeline usando 60 come soglia percentile superiore e 30 come soglia percentile inferiore e utilizzare il valore di soglia come valore di sostituzione. La tabella seguente confronta questi due risultati:  
  
1.  Sostituire con mancante; Soglia superiore : 90; Soglia inferiore : 20  
  
1.  Sostituire con soglia; Percentile superiore - 60; Percentile inferiore - 40  
  
|Dati originali|Sostituire con valore mancante|Sostituire con soglia|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSO<br /><br /> 4, FALSO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, FALSO<br /><br /> 8, FALSO<br /><br /> 9, FALSO<br /><br /> TRUE|4, VERO<br /><br /> 4, VERO<br /><br /> 4, VERO<br /><br /> 4, VERO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, VERO<br /><br /> 7, VERO<br /><br /> 7, VERO<br /><br /> 7, VERO| 
 
## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
