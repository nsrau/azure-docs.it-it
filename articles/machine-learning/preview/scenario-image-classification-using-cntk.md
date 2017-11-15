---
title: Classificazione delle immagini con CNTK in Azure Machine Learning Workbench | Microsoft Docs
description: Eseguire il training, valutare e distribuire un modello di classificazione delle immagini personalizzate usando Azure ML Workbench.
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
ms.reviewer: mawah, marhamil, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: eefede6196bedf208d9b14cee63632922223a6d6
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Classificazione delle immagini con Azure Machine Learning Workbench

Gli approcci alla classificazione delle immagini consentono di risolvere numerosi problemi di Visione artificiale.
Questi approcci includono i modelli di compilazione, che rispondono a domande come: *L'immagine contiene un OGGETTO?* in cui OGGETTO potrebbe essere, per esempio, *cane*, *macchina* o *nave*. Oppure a domande più complesse, come: *Quale classe di gravità di malattia oculare si evince dalla scansione della retina di questo paziente?*.

Questa esercitazione ha l'obiettivo di risolvere problemi di questo tipo. Viene illustrato come eseguire il training, valutare e distribuire il proprio modello di classificazione delle immagini usando [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) per l'apprendimento avanzato.
Sono fornite immagini di esempio, ma il lettore può anche importare i propri set di dati ed eseguire il training dei propri modelli personalizzati.

Le soluzioni tradizionali di Visione artificiale richiedono una conoscenza esperta per identificare manualmente e implementare le cosiddette *funzionalità* che evidenziano le informazioni desiderate nelle immagini.
Tuttavia, questo approccio manuale è cambiato nel 2012 con il famoso articolo sull'apprendimento avanzato di [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] e, attualmente, le reti neurali profonde sono usate per trovare automaticamente queste funzionalità.
Le reti neurali profonde hanno portato a un miglioramento considerevole, non solo nel campo della classificazione delle immagini, ma anche per altri problemi di Visione artificiale, come il rilevamento di oggetti e la somiglianza tra immagini.


## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Panoramica

L'esercitazione è suddivisa in tre parti:

- Parte 1: viene illustrato come eseguire il training, valutare e distribuire un sistema di classificazione delle immagini. Tale sistema usa una rete neurale profonda con training preliminare come utilità di funzioni ed esegue il training di un modello SVM nel suo output.
- Parte 2: viene illustrato come migliorare l'accuratezza, ad esempio, perfezionando la rete neurale profonda invece di usarla come utilità di funzioni predefinita.
- Parte 3: viene illustrato come usare il set di dati invece delle immagini di esempio fornite e, se necessario, come produrre il set di dati analizzando le immagini dalla rete.

Sebbene non sia necessario avere esperienza di machine learning e CNTK, risulta utile per comprendere i principi di base. I numeri di precisione, il tempo del training e così via, presenti nell'esercitazione, sono solo di riferimento e i valori effettivi visualizzati quando si esegue il codice sono quasi sicuramente diversi.


## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per eseguire questo esempio sono i seguenti:

1. Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
2. [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md), installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) del programma e alla creazione di un'area di lavoro.  
3. Un computer Windows. Il sistema operativo Windows è necessario poiché Workbench supporta solo Windows e MacOS, mentre Microsoft Cognitive Toolkit, usato come raccolta di apprendimento avanzato, supporta solo Windows e Linux.
4. Una GPU dedicata non è necessaria per eseguire il training SVM nella parte 1. Tuttavia, è richiesta per il perfezionamento della rete neurale profonda descritto nella parte 2. Se non si dispone di una GPU potente, si desidera eseguire il training su più GPU o non si dispone di un computer Windows, considerare l'uso della macchina virtuale per l'apprendimento avanzato di Azure con sistema operativo Windows. Vedere [qui](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) per una guida alla distribuzione con un solo clic. Dopo aver eseguito la distribuzione, connettersi alla macchina virtuale tramite una connessione desktop remoto, installarvi Workbench ed eseguire il codice localmente dalla macchina virtuale.
5. È necessario installare diverse librerie Python, come OpenCV. Scegliere *Open Command Prompt* (Apri prompt dei comandi) dal menu *File* in Workbench ed eseguire i comandi seguenti per installare queste dipendenze:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.0-cp35-cp35m-win_amd64.whl` dopo aver scaricato il selettore OpenCV da http://www.lfd.uci.edu/~gohlke/pythonlibs/ (il nome del file e la versione esatti possono cambiare)
    - `conda install matplotlib numpy pillow`
    - `conda install -c conda-forge bqplot`

### <a name="troubleshooting--known-bugs"></a>Risoluzione dei problemi/Bug noti
- Per la parte 2 è necessaria una GPU altrimenti, quando si prova a perfezionare la rete neurale profonda, viene generato l'errore "Batch normalization training on CPU is not yet implemented" (Training normalizzazione batch sulle CPU non ancora implementato).
- Durante il training della rete neurale profonda, è possibile evitare gli errori di memoria insufficiente riducendo le dimensioni del mini-batch (variabile `cntk_mb_size` in `PARAMETERS.py`).
- Il codice è stato testato con CNTK 2.0 e 2.1 e dovrebbe essere eseguito anche nelle versioni più recenti senza (o con minori) modifiche.
- Al momento della scrittura, Azure Machine Learning Workbench ha riscontrato problemi con la visualizzazione di blocchi appunti superiori a 5 MB. Blocchi appunti di così grandi dimensioni si possono verificare se vengono salvati con l'output di tutte le celle visualizzato. Se si verifica questo errore, aprire il prompt dei comandi dal menu File all'interno di Workbench, eseguire `jupyter notebook`, aprire il blocco appunti, deselezionare tutto l'output e salvare il blocco appunti. Dopo aver eseguito questi passaggi, il blocco appunti verrà nuovamente aperto correttamente all'interno di Azure Machine Learning Workbench.


## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Per creare un nuovo progetto usare questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench.
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto).
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto.
4.  Nella casella di ricerca **Cerca modelli di progetto** digitare "Classificazione delle immagini" e selezionare il modello.
5.  Fare clic su **Crea**.

Eseguendo questi passaggi viene creata la struttura del progetto illustrata di seguito. La directory del progetto è limitata a meno di 25 MB poiché Azure Machine Learning Workbench crea una copia di questa cartella dopo ogni esecuzione (per abilitare la cronologia di esecuzione). Di conseguenza, tutte le immagini e i file temporanei vengono salvati da e verso la directory *~/Desktop/imgClassificationUsingCntk_data* (indicata come *DATA_DIR* in questo documento).

  Cartella| Descrizione
  ---|---
  aml_config/|                           Directory contenente i file di configurazione di Azure Machine Learning Workbench
  libraries/|                              Directory contenente tutte le funzioni di supporto Python e Jupyter
  notebooks/|                              Directory contenente tutti i blocchi appunti
  resources/|                              Directory contenente tutte le risorse (ad esempio l'URL di immagini di moda)
  scripts/|                              Directory contenente tutti gli script
  PARAMETERS.py|                       Script Python che specifica tutti i parametri
  readme.md|                           Questo file Readme


## <a name="data-description"></a>Descrizione dei dati

Questa esercitazione usa come esempio di esecuzione un set di dati della texture di abbigliamento per la parte superiore del corpo costituito da un massimo di 428 immagini. Ogni immagine è annotata come una delle tre diverse texture (a pois, a righe, leopardata). Il numero delle immagini è stato mantenuto ridotto in modo che questa esercitazione possa essere eseguita rapidamente. Tuttavia, il codice è ben collaudato e funziona con decine di migliaia di immagini o più. Tutte le immagini sono state ottenute usando la Ricerca immagini di Bing e annotate manualmente come spiegato nella [Parte 3](#using-a-custom-dataset). Gli URL delle immagini con i relativi attributi sono elencati nel file */resources/fashionTextureUrls.tsv*.

Lo script `0_downloadData.py` scarica tutte le immagini nella directory *DATA_DIR/immagini/fashionTexture/*. Alcuni dei 428 URL potrebbero essere danneggiati. Questo non è un problema e significa semplicemente che si ha un numero leggermente inferiore di immagini per il training e il test.

Nella figura seguente vengono illustrati esempi per gli attributi a pois (sinistra), a righe (centro) e leopardato (destra). Le annotazioni sono state eseguite in base al capo di abbigliamento per la parte superiore del corpo.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_dotted.jpg"  alt="alt text" height="200">
<img src="media/scenario-image-classification-using-cntk/examples_striped.jpg" alt="alt text" height="200">
<img src="media/scenario-image-classification-using-cntk/examples_leopard.jpg" alt="alt text" height="200">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Parte 1: Training e valutazione del modello

Nella prima parte di questa esercitazione viene eseguito il training di un sistema che usa, ma non modifica, una rete neurale profonda con training preliminare. Questa rete neurale profonda con training preliminare è usata come utilità di funzioni e viene eseguito il training di una SVM lineare per predire l'attributo (a pois, a righe o leopardato) di una determinata immagine.

Viene ora descritto questo approccio nel dettaglio, passaggio dopo passaggio, e gli script che è necessario eseguire. Dopo ogni passaggio, è consigliabile esaminare quali file vengono scritti e dove vengono scritti.

Tutti i parametri importanti sono specificati, insieme a una breve spiegazione, in un'unica posizione: il file `PARAMETERS.py`.




### <a name="step-1-data-preparation"></a>Passaggio 1: Preparazione dei dati
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Il blocco appunti `showImages.ipynb` può essere usato per visualizzare le immagini e per correggerne le annotazioni in base alle esigenze. Per eseguire il blocco note, aprirlo in Azure Machine Learning Workbench, fare clic su "Start Server Notebook" (Avvia server notebook) se questa opzione viene visualizzata, quindi eseguire tutte le celle del blocco note. Se si verifica un errore che segnala che il blocco note è troppo grande per essere visualizzato, vedere la sezione sulla risoluzione dei problemi in questo documento.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Eseguire lo script denominato `1_prepareData.py`, che assegna tutte le immagini al set di training o al set di test. Tali assegnazioni si escludono a vicenda: nessuna immagine per il training viene usata anche per il test o viceversa. Per impostazione predefinita, un 75% casuale delle immagini di ogni classe Attribute viene assegnato al training, mentre il restante 25% è assegnato al test. Tutti i dati generati dallo script vengono salvati nella cartella *DATA_DIR/proc/fashionTexture/*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Passaggio 2: Perfezionamento della rete neurale profonda
`Script: 2_refineDNN.py`

Come illustrato nella parte 1 di questa esercitazione, la rete neurale profonda con training preliminare rimane fissa (ovvero, non è perfezionata). Tuttavia, lo script denominato `2_refineDNN.py` viene comunque eseguito nella parte 1, mentre carica un modello [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] con training preliminare e lo modifica, ad esempio, per consentire maggiore risoluzione dell'immagine di input. Questo passaggio è veloce (pochi secondi) e non richiede una GPU.

Nella parte 2 dell'esercitazione, una modifica al file PARAMETERS.py consente allo script `2_refineDNN.py` di perfezionare anche la rete neurale profonda con training preliminare. Per impostazione predefinita, vengono eseguiti 45 periodi di training durante il perfezionamento.

In entrambi i casi, il modello finale viene successivamente scritto nel file *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Passaggio 3: Valutare la rete neurale profonda per tutte le immagini
`Script: 3_runDNN.py`

È ora possibile usare la rete neurale profonda (preferibilmente perfezionata), partendo dall'ultimo passaggio, per assegnare una funzione alle immagini. Data un'immagine come input alla rete neurale profonda, l'output è il vettore a 512 float dal penultimo livello del modello. Le dimensioni di questo vettore sono molto più piccole di quelle dell'immagine stessa. Tuttavia, deve contenere ed evidenziare tutte le informazioni rilevanti per riconoscere l'attributo dell'immagine, vale a dire, se il capo di abbigliamento presenta una texture a pois, a righe o leopardata.

Tutte le rappresentazioni delle immagini della rete neurale profonda vengono salvate nel file *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Passaggio 4: Training della SVM
`Script: 4_trainSVM.py`

Le rappresentazioni dei 512 float illustrate nel passaggio precedente vengono ora usate per eseguire il training di un classificatore SVM: data un'immagine come input, la SVM restituisce un punteggio per ogni attributo presente. Nel set di dati dell'esempio usato finora, ciò significa un punteggio per "a pois ", per "a righe" e per "leopardato".

Lo script `4_trainSVM.py` carica le immagini di training, esegue il training di una SVM per diversi valori del parametro C della regolarizzazione (slack) e permette che la SVM mantenga la precisione più elevata. L'accuratezza della classificazione viene visualizzata nella console e tracciata in Workbench. Per i dati sulla texture forniti, questi valori devono essere rispettivamente intorno al 100% e all'88%. Infine, la SVM su cui è stato eseguito il training viene trascritta nel file *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Passaggio 5: Valutazione e visualizzazione
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

È possibile misurare l'accuratezza del classificatore di immagini sottoposto a training usando lo script `5_evaluate.py`. Lo script elabora un punteggio per tutte le immagini di test usando il classificatore SVM con training, assegna ad ogni immagine l'attributo con il punteggio più alto e confronta gli attributi stimati con le annotazioni dei risultati di base.

L'output dello script `5_evaluate.py` è illustrato di seguito. Viene calcolata la precisione della classificazione di ogni singola classe, così come la precisione dell'intero set di test (''precisione complessiva'') e la media sulle precisioni singole (''precisione complessiva delle medie delle classi''). Il 100% corrisponde alla massima precisione possibile, mentre lo 0% alla minima. Un'ipotesi casuale produrrebbe in generale una precisione delle medie delle classi nel rapporto di 1 al numero degli attributi: nel caso preso in esempio, questa precisione sarebbe del 33,33%. Questi risultati migliorano in modo significativo quando si usa una risoluzione di input maggiore come `rf_inputResoluton = 1000`, a scapito, però, di tempi di calcolo della rete neurale profonda più lunghi.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Oltre la precisione, viene tracciata la curva ROC con la rispettiva area sotto curva (a sinistra) e viene visualizzata la matrice di confusione (destra):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Infine, viene fornito il blocco appunti `showResults.py` per scorrere tra le immagini di test e visualizzare i rispettivi punteggi di classificazione:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Passaggio 6: Distribuzione
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Il sistema sottoposto a training può ora essere pubblicato come un'API REST. La distribuzione viene illustrata nel blocco appunti `deploy.ipynb` ed è basata sulla funzionalità all'interno di Azure Machine Learning Workbench. Vedere anche l'eccellente sezione sulla distribuzione dell'esercitazione [Classificare i dati Iris - Parte 3: Distribuire un modello](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3).

Una volta distribuito, il servizio Web può essere chiamato tramite lo script `6_callWebservice.py`. Si noti che l'indirizzo IP, locale o nel cloud, del servizio Web deve essere impostato prima nello script. Il blocco appunti `deploy.ipynb` illustra come trovare questo indirizzo IP.








## <a name="part-2---accuracy-improvements"></a>Parte 2: Miglioramenti della precisione

Nella parte 1 è stato illustrato come classificare un'immagine eseguendo il training di una Support Vector Machine (SVM) lineare nell'output a 512 float di una rete neurale profonda. Quest'ultima è stata sottoposta a un training preliminare di milioni di immagini e il penultimo livello è stato restituito come vettore delle funzionalità. Questo approccio è veloce poiché la rete neurale profonda viene usata così com'è, ma ciononostante spesso offre buoni risultati.

Vengono ora presentati diversi modi per migliorare la precisione del modello della parte 1. In particolare la rete neurale profonda viene perfezionata invece di essere mantenuta fissa.

### <a name="dnn-refinement"></a>Perfezionamento della rete neurale profonda

Invece di usare una SVM, è possibile eseguire la classificazione direttamente nella rete neurale. Ciò è possibile aggiungendo un nuovo ultimo livello alla rete neurale profonda con training preliminare, che prende i 512 float dal penultimo livello come input. Il vantaggio di eseguire la classificazione nella rete neurale profonda è che ora l'intera rete può essere sottoposta nuovamente a training usando la retropropagazione. Questo approccio porta a precisioni di classificazione di gran lunga maggiori rispetto all'uso della rete neurale profonda con training preliminare così com'è, a scapito però di tempi di training molto più lunghi (anche con GPU).

Il training della rete neurale invece di una SVM viene eseguito modificando la variabile `classifier` in `PARAMETERS.py` da `svm` a `dnn`. Quindi, come descritto nella parte 1, è necessario che tutti gli script, ad eccezione della preparazione dei dati (passaggio 1) e del training della SVM (passaggio 3), vengano eseguiti di nuovo. Il perfezionamento della rete neurale profonda richiede una GPU. Se la GPU non è stata rilevata o è bloccata, ad esempio da una precedente esecuzione di CNTK, lo script `2_refineDNN.py` genera un errore. Il training della rete neurale profonda può generare errori di memoria insufficiente in alcune GPU e ciò può essere evitato riducendo le dimensioni del mini-batch (variabile `cntk_mb_size` in `PARAMETERS.py`).

Al termine del processo di training, il modello perfezionato viene salvato in *DATA_DIR/proc/fashionTexture/cntk_refined.model* e viene elaborato un tracciato che mostra i cambiamenti degli errori di classificazione del training e del test nel corso del training. Si noti che in tale tracciato l'errore nel set di training è molto inferiore rispetto al set di test. È possibile ridurre questo cosiddetto comportamento di sovradattamento usando, per esempio, un valore maggiore per il tasso di drop-out `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Come è possibile osservare nel tracciato seguente, usando il perfezionamento della rete neurale profonda sul set di dati fornito, la precisione è del 92,35% contro l'88,92% visto in precedenza (parte 1). In particolare, le immagini ''a pois'' migliorano in modo significativo con un'area sotto curva ROC di 0,98 con perfezionamento contro lo 0,94 precedente. Il set di dati in questione è di piccole dimensioni e, pertanto, le precisioni effettive nell'esecuzione del codice sono diverse. Questa discrepanza è dovuta a effetti stocastici come la divisione casuale delle immagini in set di training e di test.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Rilevamento della cronologia di esecuzione

Azure Machine Learning Workbench archivia la cronologia di ogni esecuzione in Azure per consentire il confronto di due o più esecuzioni a distanza anche di settimane l'una dall'altra. Per una spiegazione dettagliata vedere [Classificare i dati Iris - Parte 2: Creare un modello](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). Ciò è anche illustrato nelle schermate seguenti, in cui vengono confrontate due esecuzioni dello script `5_evaluate.py`, usando il perfezionamento della rete neurale profonda, cioè `classifier = "dnn"` (esecuzione numero 148), o il training della SVM, cioè `classifier = "svm"` (esecuzione numero 150).

Nella prima schermata il perfezionamento della rete neurale profonda comporta maggiori precisioni rispetto al training della SVM per tutte le classi. La seconda schermata illustra tutte le metriche tracciate, incluso il classificatore. Questa verifica viene eseguita nello script `5_evaluate.py` chiamando il logger di Azure Machine Learning Workbench. In aggiunta, lo script salva anche la curva ROC e la matrice di confusione nella cartella *outputs*. Questa cartella *outputs* è speciale in quanto il suo contenuto è anche rilevato dalla funzionalità di cronologia di Workbench e quindi è possibile accedere ai file di output in ogni momento, indipendentemente se le copie locali siano state sovrascritte.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Regolazione del parametro
Come per la maggior parte dei progetti di machine learning, ottenere buoni risultati per un nuovo set di dati richiede un'accurata regolazione del parametro, nonché la valutazione di diverse decisioni di progettazione. Per supportare queste attività, sono specificati tutti i parametri importanti, insieme a una breve spiegazione, in un'unica posizione: il file `PARAMETERS.py`.

Le strategie più promettenti per ottenere miglioramenti sono:

- Qualità dei dati: assicura una qualità elevata dei set di training e di test. Ciò significa che le immagini sono annotate correttamente, quelle ambigue vengono rimosse (ad esempio capi di abbigliamento sia a pois che a strisce) e gli attributi si escludono a vicenda (ovvero, sono scelti in modo che ogni immagine appartenga esattamente a un solo attributo).
- Nel caso in cui l'oggetto di interesse all'interno dell'immagine sia di piccole dimensioni, è risaputo che gli approcci di classificazione delle immagini non siano una valida soluzione. In questi casi è consigliabile usare un approccio di rilevamento di oggetti, come descritto in questa [esercitazione](https://github.com/Azure/ObjectDetectionUsingCntk).
- Perfezionamento della rete neurale profonda: senza dubbio il parametro più importante per un'analisi accurata delle immagini è la velocità di apprendimento `rf_lrPerMb`. Se la precisione del set di training (prima figura nella parte 2) non è compresa tra 0 e 5%, probabilmente si tratta di un errore della velocità di apprendimento. Gli altri parametri che iniziano con `rf_` sono meno importanti. In genere, l'errore di training deve diminuire in modo esponenziale ed essere vicino allo 0% dopo il training.
- Risoluzione di input: la risoluzione predefinita delle immagini è di 224 x 224 pixel. L'uso di una risoluzione delle immagini superiore (parametro: `rf_inputResoluton`), ad esempio, di 448 x 448 o 896 x 896 pixel, spesso comporta un miglioramento significativo della precisione, ma rallenta il perfezionamento della rete neurale profonda. **Una risoluzione superiore delle immagini ha un avvio quasi libero e quasi sempre incrementa la precisione**.
- Overfitting della rete neurale profonda: evitare un ampio divario tra la precisione del training e del test durante il perfezionamento della rete neurale profonda (prima figura nella parte 2). Questo divario può essere ridotto tramite tassi di dropout `rf_dropoutRate` pari a 0,5 o più e aumentando il peso normalizzatore `rf_l2RegWeight`. L'uso di un elevato tasso di dropout può essere particolarmente utile se la risoluzione dell'immagine di input della rete neurale profonda è superiore.
- Provare a usare reti neurali più profonde cambiando `rf_pretrainedModelFilename` da `ResNet_18.model` a `ResNet_34.model` o `ResNet_50.model`. Il modello Resnet-50 non solo è più profondo, ma il suo output del penultimo livello è di 2048 float (contro i 512 float dei modelli ResNet-18 e ResNet 34). Questa maggiore dimensione può essere particolarmente utile durante il training di un classificatore SVM.

## <a name="part-3---custom-dataset"></a>Parte 3: Set di dati personalizzato

Nella parte 1 e 2 un modello di classificazione delle immagini è stato sottoposto a training e valutazione usando le immagini fornite delle texture dei capi d'abbigliamento per la parte superiore del corpo. È ora illustrato come usare, invece, un set di dati personalizzato fornito dall'utente. In alternativa, se non è disponibile, come generare e annotare tale set di dati tramite Ricerca immagini di Bing.

### <a name="using-a-custom-dataset"></a>Uso di un set di dati personalizzato

In primo luogo, si guardi alla struttura di cartelle per i dati delle texture dei capi di abbigliamento. Notare come tutte le immagini per i diversi attributi sono nelle rispettive sottocartelle *a pois*, *leopardato e *a righe* in *DATA_DIR/images/fashionTexture/*. Si noti anche come il nome della cartella di immagini si trovi anche nel file `PARAMETERS.py`:
```python
datasetName = "fashionTexture"
```

L'uso di un set di dati personalizzato è tanto semplice quanto riprodurre questa struttura di cartelle in cui tutte le immagini si trovano nelle sottocartelle in base ai relativi attributi e copiare queste sottocartelle nella nuova directory specificata dall'utente *DATA_DIR/immagini/newDataSetName/*. L'unica modifica al codice necessaria è impostare la variabile `datasetName` su *newDataSetName*. Gli script da 1 a 5 possono essere quindi eseguiti in ordine e tutti i file intermedi vengono scritti in *proc/DATA_DIR/newDataSetName/*. Non sono necessarie altre modifiche al codice.

È importante che ogni immagine sia assegnata esattamente a un solo attributo. Ad esempio, sarebbe sbagliato avere attributi per ''animale'' e ''leopardo'', poiché un'immagine di un ''leopardo'' apparterrebbe anche ad ''animale''. È consigliabile anche rimuovere le immagini ambigue che sarebbero difficili da annotare.



### <a name="image-scraping-and-annotation"></a>Analisi delle immagini e annotazione

Può essere difficile raccogliere un numero abbastanza grande di immagini con annotazioni per il training e il test. Un modo per superare questo problema è analizzare le immagini da Internet. Per esempio, vedere di seguito i risultati di una ricerca immagini di Bing per la query *t-shirt a righe*. Come previsto, la maggior parte delle immagini è effettivamente costituita da t-shirt a righe. Le poche immagini non corrette o ambigue (ad esempio, colonna 1, riga 1; oppure colonna 3, riga 2) possono essere identificate e rimosse facilmente:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Per generare un set di dati corposo e diversificato è necessario usare più query. Per esempio, 7\*3 = 21 query possono essere sintetizzate automaticamente usando tutte le combinazioni di capi d'abbigliamento (blusa, felpa, maglione, maglietta, t-shirt, gilet) e attributi (a righe, a pois, leopardato). Scaricare le prime 50 immagini per ogni query fornirebbe quindi un massimo di 50*21 = 1050 immagini.

Invece di scaricare manualmente le immagini dalla ricerca immagini di Bing, è molto più facile usare l'[API Ricerca immagini Bing dei Servizi cognitivi](https://www.microsoft.com/cognitive-services/bing-image-search-api) che, data una stringa di query, restituisce un set di URL di immagini.

Alcune delle immagini scaricate sono duplicati esatti o quasi duplicati (che, ad esempio, differiscono solo per risoluzione o artefatti jpg). È necessario rimuovere i duplicati in modo che la divisione tra training e test non contenga le stesse immagini. Tali immagini duplicate si possono eliminare usando un approccio basato su hash che richiede due passaggi: in primo luogo, viene calcolata per tutte le immagini la stringa hash; successivamente, in una seconda analisi delle immagini vengono mantenute solo quelle con una stringa hash non ancora individuata. Tutte le altre immagini vengono quindi eliminate. L'approccio `dhash`, presente nella libreria Python `imagehash`, viene descritto in questo [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) come ben funzionate, con il parametro `hash_size` impostato su 16. Qualora si rimuovesse erroneamente qualche immagine non duplicata, ciò non sarebbe un problema in quanto viene rimossa la maggior parte dei duplicati effettivi.





## <a name="conclusion"></a>Conclusioni

Ecco alcune delle principali caratteristiche di questo esempio:
- Codice per eseguire il training, valutare e distribuire i modelli di classificazione delle immagini.
- Immagini demo fornite, ma facilmente adattabili per usare un proprio set di immagini attraverso la modifica di una sola riga di codice.
- Funzionalità all'avanguardia implementate per eseguire il training di modelli di alta precisione basati sul trasferimento induttivo.
- Sviluppo di modelli interattivi con Azure Machine Learning Workbench e Notebook Jupyter.


## <a name="references"></a>Riferimenti

[1] Alex Krizhevsky, Ilya Sutskever e Geoffrey E. Hinton, [_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) (Classificazione ImageNet con reti neurali convoluzionali profonde). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren, e Jian Sun, [_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) (Apprendimento residuo profondo per il riconoscimento delle immagini). CVPR 2016.
