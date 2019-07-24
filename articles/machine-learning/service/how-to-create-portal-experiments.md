---
title: Creare ed esplorare gli esperimenti nel portale
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare e gestire esperimenti automatici di machine learning nel portale
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 5eb3e94ff65e8a8b74f357a4cb8a517fd3837c5a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871820"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Creare ed esplorare esperimenti automatici di machine learning nel portale di Azure (anteprima)

 Questo articolo illustra come creare, eseguire ed esplorare gli esperimenti automatici di machine learning nel portale di Azure senza una singola riga di codice. Automatizzato Machine Learning consente di automatizzare il processo di selezione dell'algoritmo migliore da usare per i dati specifici, in modo da poter generare rapidamente un modello di machine learning. [Altre informazioni su Machine Learning automatizzato](concept-automated-ml.md).

 Se si preferisce un'esperienza più basata sul codice, è anche possibile [configurare gli esperimenti di Machine Learning automatici in Python](how-to-configure-auto-train.md) con l' [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Vedere [creare un'area di lavoro del servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Attività iniziali

Passare al riquadro sinistro dell'area di lavoro. Selezionare Machine Learning automatizzati nella sezione Creazione e modifica (anteprima).

![Riquadro di spostamento portale di Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Se è la prima volta che si esegue un esperimento con Machine Learning automatizzati, verrà visualizzato quanto segue:

![Pagina di destinazione dell'esperimento di portale di Azure](media/how-to-create-portal-experiments/landing-page.png)

In caso contrario, viene visualizzato il dashboard di Machine Learning automatizzato con una panoramica di tutti gli esperimenti di Machine Learning automatizzati, inclusi quelli creati con l'SDK. Qui è possibile filtrare ed esplorare le esecuzioni in base alla data, al nome dell'esperimento e allo stato di esecuzione.

![Dashboard dell'esperimento di portale di Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Creare un esperimento

Selezionare il pulsante Crea esperimento per popolare il formato seguente.

![Crea modulo esperimento](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Immettere il nome dell'esperimento.

1. Selezionare un calcolo per il processo di profiling dei dati e di training. Un elenco di calcoli esistenti è disponibile nell'elenco a discesa. Per creare un nuovo calcolo, seguire le istruzioni riportate nel passaggio 3.

1. Selezionare il pulsante Crea un nuovo calcolo per aprire il riquadro sottostante e configurare il contesto di calcolo per l'esperimento.

    ![Crea nuovo calcolo per l'esperimento](media/how-to-create-portal-experiments/create-new-compute.png)

    Campo|DESCRIZIONE
    ---|---
    Nome calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il calcolo.
    Impostazioni aggiuntive| *Nodo minimo*: Immettere il numero minimo di nodi per il calcolo. Il numero minimo di nodi per il calcolo di AML è 0. Per abilitare la profilatura dei dati, è necessario disporre di uno o più nodi. <br> *Nodo massimo*: Immettere il numero massimo di nodi per il calcolo. Il valore predefinito è 6 nodi per un calcolo AML.

      Per avviare la creazione del nuovo calcolo, fare clic su **Crea**. Questa operazione può richiedere alcuni minuti.

      >[!NOTE]
      > Il nome di calcolo indicherà se la profilatura del calcolo selezionato/ creato è abilitata. (Per altre informazioni sul profiling dei dati, vedere 7B).

1. Selezionare un account di archiviazione per i dati. L'anteprima pubblica supporta solo i caricamenti di file locali e gli account di archiviazione BLOB di Azure.

1. Selezionare un contenitore di archiviazione.

1. Selezionare un file di dati dal contenitore di archiviazione o caricare un file dal computer locale nel contenitore.

    ![Selezionare il file di dati per l'esperimento](media/how-to-create-portal-experiments/select-file.png)

1. Usare le schede anteprima e profilo per configurare ulteriormente i dati per questo esperimento.

    1. Nella scheda Anteprima indicare se i dati includono intestazioni e selezionare le funzionalità (colonne) per il training usando i pulsanti di opzione **inclusi** in ogni colonna della funzionalità.

        ![Anteprima dati](media/how-to-create-portal-experiments/data-preview.png)

    1. Nella scheda profilo è possibile visualizzare il [profilo dati](#profile) per funzionalità, nonché la distribuzione, il tipo e le statistiche di riepilogo (media, mediana, max/min e così via) di ognuno.

        ![Scheda profilo dati](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Se il contesto di calcolo **non** è abilitato per la profilatura, verrà visualizzato il seguente messaggio di errore: *Il profiling dei dati è disponibile solo per le destinazioni di calcolo già in esecuzione*.

1. Selezionare il tipo di processo di training: classificazione, regressione o previsione.

1. Selezionare la colonna di destinazione. Colonna su cui si desidera eseguire le stime.

1. Per la previsione:
    1. Selezionare la colonna Time: Questa colonna contiene i dati relativi all'ora da usare.
    1. Selezionare l'orizzonte di previsione: Indica il numero di unità di tempo (minuti/ore/giorni/settimane/mesi/anni) che il modello sarà in grado di stimare in futuro. Maggiore è il modello necessario per prevedere il futuro, minore sarà il livello di precisione. [Altre informazioni sulla previsione e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. Opzionale Impostazioni avanzate: impostazioni aggiuntive che è possibile usare per controllare meglio il processo di training.

    Impostazioni avanzate|Descrizione
    ------|------
    Metrica primaria| Metrica principale usata per assegnare un punteggio al modello. [Altre informazioni sulle metriche del modello](how-to-configure-auto-train.md#explore-model-metrics).
    Criteri di uscita| Quando uno di questi criteri viene soddisfatto, il processo di training termina prima del completamento completo. <br> *Tempo del processo di training (minuti)* : Per quanto tempo consentire l'esecuzione del processo di training.  <br> *Numero massimo di iterazioni*: Numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non viene eseguito più del numero specificato di iterazioni. <br> *Soglia Punteggio metrica*:  Punteggio di metrica minimo per tutte le pipeline. In questo modo si garantisce che, se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si dedica più tempo al processo di training del necessario.
    Preelaborazione| Selezionare questa impostazione per abilitare o disabilitare la pre-elaborazione eseguita da Automatic Machine Learning. La pre-elaborazione include la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche. [Altre informazioni sulla pre-elaborazione](#preprocess).
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida](how-to-configure-auto-train.md)incrociata.
    Concorrenza| Selezionare i limiti multicore da usare quando si usa il calcolo multicore.
    Algoritmo bloccato| Selezionare gli algoritmi che si desidera escludere dal processo di training.

   ![Modulo Impostazioni avanzate](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Per ulteriori informazioni sui campi, fare clic sulla descrizione comando informazioni.

<a name="profile"></a>

### <a name="data-profiling"></a>Profiling dati

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è pronto per ML. Per le colonne non numeriche, includono solo le statistiche di base come min, Max e il numero di errori. Per le colonne numeriche, è anche possibile esaminare i relativi momenti statistici e stimare quantili. In particolare, il profilo dati include:

* **Feature**: nome della colonna da riepilogare.

* **Profile**: Visualizzazione inline basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi dei valori, mentre i decimali (numerici) hanno istogrammi approssimati. In questo modo è possibile acquisire una rapida conoscenza della distribuzione dei dati.

* **Distribuzione**dei tipi: numero di valori in linea di tipi all'interno di una colonna. I valori null sono di tipo, quindi questa visualizzazione è utile per rilevare valori dispari o mancanti.

* **Tipo**: tipo dedotto della colonna. I valori possibili sono: stringhe, valori booleani, date e decimali.

* **Min**: valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.

* **Max**: valore massimo della colonna. Come "min", vengono visualizzate le voci vuote per le funzionalità con tipi irrilevanti.

* **Count**: numero totale di voci mancanti e non mancanti nella colonna.

* **Conteggio non mancante**: numero di voci nella colonna non mancanti. Si noti che le stringhe e gli errori vuoti vengono considerati come valori, quindi non contribuiscono al "numero mancante".

* **Quantili** (a intervalli di 0,1, 1, 5, 25, 50, 75, 95, 99 e 99,9%): i valori approssimati in ogni quantile per fornire un senso della distribuzione dei dati. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

* **Mean**: media aritmetica della colonna. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

* **Deviazione standard**: deviazione standard della colonna. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

* **Varianza**: varianza della colonna. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

* **Asimmetria**: inclinazione della colonna. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

* **Curtosi**: curtosi della colonna. Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Pre-elaborazione avanzata

Quando si configurano gli esperimenti, è possibile abilitare `Preprocess`l'impostazione avanzate. In questo modo, vengono eseguiti automaticamente i passaggi di pre-elaborazione e di conteggi dei dati seguenti.

|&nbsp;Passaggi di pre-elaborazione| Descrizione |
| ------------- | ------------- |
|Eliminazione delle caratteristiche con elevata cardinalità o senza varianza|Eliminarli dai set di training e di convalida, incluse le funzionalità con tutti i valori mancanti, lo stesso valore in tutte le righe o con cardinalità estremamente elevata (ad esempio, hash, ID o GUID).|
|Attribuire i valori mancanti|Per le funzionalità numeriche, imputare alla media dei valori nella colonna.<br/><br/>Per le funzionalità categoriche, imputare il valore più frequente.|
|Generazione di caratteristiche aggiuntive|Per le caratteristiche di tipo DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuti, secondi.<br/><br/>Per le caratteristiche di tipo Text: Frequenza termini basata su unigrammi, bi-grammi e Tri-caratteri-grammi.|
|Trasformare e codificare |Le funzionalità numeriche con pochi valori univoci vengono trasformate in funzionalità categoriche.<br/><br/>La codifica One-Hot viene eseguita per una cardinalità categorica bassa; per la cardinalità elevata, codifica One-Hot-hash.|
|Incorporamenti di Word|Featurizer di testo che converte i vettori di token di testo in vettori di frase usando un modello con training preliminare. Il vettore di incorporamento di ogni parola in un documento viene aggregato insieme per produrre un vettore di funzionalità del documento.|
|Codifiche di destinazione|Per le funzionalità categoriche, esegue il mapping di ogni categoria con il valore di destinazione medio per i problemi di regressione e la probabilità della classe per ogni classe per i problemi di classificazione. Viene applicata la ponderazione basata sulla frequenza e la convalida incrociata k-fold per ridurre l'adattamento del mapping e del rumore causato da categorie di dati di tipo sparse.|
|Codifica della destinazione del testo|Per l'input di testo, viene usato un modello lineare in pila con il contenitore di parole per generare la probabilità di ogni classe.|
|Peso delle evidenze (guai)|Calcola il dolore come misura della correlazione delle colonne categoriche alla colonna di destinazione. Viene calcolato come log del rapporto tra le probabilità out-of-Class di Visual Studio. Questo passaggio restituisce una colonna di funzionalità numerica per classe e rimuove la necessità di imputare in modo esplicito i valori mancanti e il trattamento degli outlier.|
|Distanza del cluster|Addestra un modello di clustering k-means su tutte le colonne numeriche.  Restituisce k nuove funzionalità, una nuova funzionalità numerica per ogni cluster, che contiene la distanza di ogni campione al centro di ogni cluster.|

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Per eseguire l'esperimento, fare clic su Avvia. L'esperimento di preparazione del processo richiede alcuni minuti.

### <a name="view-experiment-details"></a>Visualizza i dettagli dell'esperimento

Al termine della fase di preparazione dell'esperimento, verrà visualizzata la schermata Esegui dettagli. In questo modo si ottiene un elenco completo dei modelli creati. Per impostazione predefinita, il modello che assegna un punteggio al più alto in base ai parametri si trova nella parte superiore dell'elenco. Quando il processo di training tenta un maggior numero di modelli, questi vengono aggiunti all'elenco di iterazioni e al grafico. Usare il grafico di iterazione per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere tempo.

![Dashboard Dettagli esecuzione](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Visualizza i dettagli dell'esecuzione di training

Eseguire il drill-down su uno dei modelli di output per visualizzare i dettagli delle esecuzioni di training, come le metriche delle prestazioni e i grafici di distribuzione. [Altre informazioni sui grafici](how-to-track-experiments.md#understanding-automated-ml-charts).

![Dettagli iterazione](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Distribuzione del modello

Una volta ottenuto il modello migliore, è necessario distribuirlo come servizio Web per prevedere i nuovi dati.

Automatizzato ML semplifica la distribuzione del modello senza scrivere codice:

1. Sono disponibili alcune opzioni per la distribuzione. 
    1. Se si vuole distribuire il modello migliore in base ai criteri della metrica impostati per l'esperimento, selezionare **Distribuisci modello migliore** dalla pagina **Dettagli esecuzione** .

        ![Pulsante Distribuisci modello](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Se si desidera distribuire un'iterazione del modello specifica, eseguire il drill-down sul modello per aprire la pagina dei dettagli di esecuzione specifica e selezionare **Distribuisci modello**.

        ![Pulsante Distribuisci modello](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Il primo passaggio consiste nel registrare il modello nel servizio. Selezionare "Registra modello" e attendere il completamento del processo di registrazione.

    ![Pannello Distribuisci modello](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Una volta registrato il modello, sarà possibile scaricare lo script di assegnazione dei punteggi (scoring.py) e lo script dell'ambiente (condaEnv. yml) da usare durante la distribuzione.

1. Quando lo script di assegnazione dei punteggi e lo script dell'ambiente vengono scaricati, passare al pannello **Asset** del riquadro di spostamento a sinistra e selezionare **modelli**.

    ![Modelli del riquadro di spostamento](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Selezionare il modello registrato e selezionare "Crea immagine".

    È possibile identificare il modello in base alla relativa descrizione, che includerà l'ID esecuzione, il numero di iterazione, nel formato seguente: *< Run_ID > _ < Iteration_number > _Model*

    ![Modelli Crea immagine](media/how-to-create-portal-experiments/model-create-image.png)

1. Immettere un nome per l'immagine. 
1. Selezionare il pulsante **Sfoglia** accanto alla casella "file di punteggio" per caricare il file di assegnazione dei punteggi (Scoring.py) precedentemente scaricato.

1. Selezionare il pulsante **Sfoglia** accanto alla casella "conda file" per caricare il file dell'ambiente (condaEnv. yml) precedentemente scaricato.

    È possibile usare uno script di assegnazione dei punteggi e un file conda, oltre a caricare file aggiuntivi. [Altre informazioni sugli script per il Punteggio](how-to-deploy-and-where.md#script).

      >[!Important]
      > Il nome file deve essere inferiore a 32 caratteri e deve iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici tra. Gli spazi non sono consentiti.

    ![Crea immagine](media/how-to-create-portal-experiments/create-image.png)

1. Selezionare il pulsante "Crea" per avviare la creazione dell'immagine. L'operazione potrebbe richiedere alcuni minuti, al termine dell'operazione verrà visualizzato un messaggio nella barra superiore.
1. Passare alla scheda "immagini", selezionare la casella di controllo accanto all'immagine che si vuole distribuire e selezionare "Crea distribuzione". [Altre informazioni sulle distribuzioni](how-to-deploy-and-where.md).

    Sono disponibili 2 opzioni per la distribuzione.
     + Istanza di contenitore di Azure (ACI): questo viene usato più a scopo di test anziché come distribuzione operativa su larga scala. Assicurarsi di inserire i valori per almeno un core per la capacità di _riserva della CPU_e almeno un gigabyte (GB) per la _capacità di riserva di memoria_
     + Azure Kubernetes Service (AKS)): questa opzione è destinata alla distribuzione su larga scala. Sarà necessario disporre di un calcolo basato su AKS.

     ![Immagini Creare una distribuzione](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Al termine, scegliere **Crea**. La distribuzione del modello può richiedere diversi minuti per il completamento dell'esecuzione di ogni pipeline.

1. La procedura è terminata. Per generare stime è necessario un servizio Web operativo.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più su Machine Learning](concept-automated-ml.md) e Azure Machine Learning automatizzati.
* [Informazioni su come utilizzare un servizio Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
