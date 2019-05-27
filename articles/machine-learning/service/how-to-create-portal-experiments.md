---
title: Creare ed esplorare gli esperimenti nel portale
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare e gestire automatizzati esperimenti di machine learning nel portale
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989958"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Creazione e l'esplorazione automatizzati esperimenti di machine learning nel portale di Azure (anteprima)

 In questo articolo descrive come creare, eseguire ed esplorare automatizzati esperimenti di machine learning nel portale di Azure senza una singola riga di codice. Apprendimento automatico consente di automatizzare il processo di selezione dell'algoritmo più appropriato da usare per i dati specifici, in modo che è possibile generare rapidamente un modello di machine learning. [Altre informazioni sull'apprendimento automatico](concept-automated-ml.md).

 Se si preferisce un'esperienza maggiormente basata su codice, è anche possibile [configurare automatizzati esperimenti di machine learning in Python](how-to-configure-auto-train.md) con il [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Visualizzare [creare un'area di lavoro del servizio di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Attività iniziali

Passare al riquadro sinistro dell'area di lavoro. Selezionare automatizzati di Machine Learning nella sezione Creazione e modifica (anteprima).

![Riquadro di spostamento del portale di Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Se si tratta di eseguire qualsiasi esperimenti di apprendimento automatico per la prima volta, si noterà quanto segue:

![Pagina di destinazione esperimento del portale di Azure](media/how-to-create-portal-experiments/landing-page.png)

In caso contrario, si noterà dashboard Automated machine learning con una panoramica di tutti automatizzati esperimenti di machine learning, inclusi quelli creati con il SDK. Qui è possibile filtrare ed esplorare le esecuzioni per data, provare a nome e lo stato di esecuzione.

![Dashboard di esperimento del portale di Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Creare un esperimento

Selezionare il pulsante Crea esperimento per popolare il modulo seguente.

![Creare una forma di esperimento](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Immettere il nome dell'esperimento.

1. Seleziona un calcolo per il processo di training e il profiling dati. Un elenco delle risorse di calcolo esistente è disponibile nell'elenco a discesa. Per creare un nuovo calcolo, seguire le istruzioni nel passaggio 3.

1. Selezionare il pulsante Crea un nuovo calcolo per aprire il riquadro, di sotto e configurare il contesto di calcolo per questo esperimento.

    ![Creare nuove risorse di calcolo per esperimento](media/how-to-create-portal-experiments/create-new-compute.png)

    Campo|Descrizione
    ---|---
    Nome dell'ambiente di calcolo| Immettere un nome univoco che identifica il contesto di calcolo.
    Dimensioni delle macchine virtuali| Selezionare le dimensioni di macchina virtuale per le risorse di calcolo.
    Impostazioni aggiuntive| *Nodo min*: Immettere il numero minimo di nodi per le risorse di calcolo. Il numero minimo di nodi di calcolo di Azure Machine Learning è 0. Per abilitare la profilatura dei dati, è necessario disporre di 1 o più nodi. <br> *Nodo max*: Immettere il numero massimo di nodi per le risorse di calcolo. Il valore predefinito è 6 nodi per un calcolo di Azure Machine Learning.

      Per avviare la creazione delle nuove risorse di calcolo, selezionare **Create**. Questa operazione può richiedere alcuni istanti.

      >[!NOTE]
      > Indica se le risorse di calcolo è selezionare o creare è il nome del calcolo *profilatura abilitata*. (Vedere 7b per altre informazioni dettagliate sul profiling dati).

1. Selezionare un account di archiviazione per i dati. Versione di anteprima pubblica supporta solo il caricamento di file locale e gli account di archiviazione Blob di Azure.

1. Selezionare un contenitore di archiviazione.

1. Selezionare un file di dati dal contenitore di archiviazione oppure caricare un file dal computer locale al contenitore.

    ![Selezionare il file di dati per esperimento](media/how-to-create-portal-experiments/select-file.png)

1. Usare le schede anteprima e il profilo per configurare ulteriormente i dati per questo esperimento.

    1. Nella scheda Anteprima, indicare se i dati includono le intestazioni e selezionare le funzionalità (colonne) per l'uso di formazione il **inclusi** passare i pulsanti in ogni colonna di funzionalità.

        ![Anteprima dati](media/how-to-create-portal-experiments/data-preview.png)

    1. Nella scheda del profilo, è possibile visualizzare il [profilo dati](#profile) funzionalità, nonché la distribuzione, tipo e statistiche summary (Media, mediana, max/min e così via) della ognuno.

        ![Scheda profilo dati](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > Verrà visualizzato il seguente messaggio di errore se il contesto di calcolo **non** profilatura abilitata: *Profiling dati è disponibile solo per le destinazioni di calcolo che eseguono già*.

1. Selezionare il tipo di processo di training: classificazione, regressione o previsione.

1. Selezionare una colonna di destinazione. La colonna a cui si desidera eseguire le stime su.

1. Per la previsione:
    1. Selezionare la colonna di tempo: Questa colonna contiene i dati di ora da utilizzare.
    1. Selezionare orizzonte di previsione: Indicare il numero di unità di tempo (minuti/ore/giorni o settimane o mesi/anni) il modello sarà in grado di prevedere il futuro. Ogni ulteriore il modello è necessario per prevedere il futuro, meno precisa diventerà. [Informazioni su previsione e previsione orizzonte](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Facoltativo) Impostazioni avanzate: impostazioni aggiuntive, è possibile usare per controllare meglio il processo di training.

    Impostazioni avanzate|Descrizione
    ------|------
    Metrica primaria| Metrica principale usato per la valutazione del modello. [Altre informazioni sulle metriche del modello](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Criteri uscita| Quando uno di questi criteri vengono soddisfatti, il processo di training termina prima del completamento completo. <br> *Ora (minuti) del processo di training*: Il tempo per consentire l'esecuzione del processo di training.  <br> *Numero massimo di iterazioni*: Numero massimo di pipeline (iterazioni) per eseguire il test nel processo di training. Il processo non verrà eseguito maggiore del numero specificato di iterazioni. <br> *Metrica di soglia del punteggio*:  Punteggio minimo delle metriche per tutte le pipeline. Ciò garantisce che se si dispone di una metrica definita destinazione che si vuole raggiungere, non dedicare più tempo nel processo di training più necessario.
    Pre-elaborazione| Selezionare questa opzione per abilitare o disabilitare la pre-elaborazione eseguita per l'apprendimento automatico. Pre-elaborazione include le operazioni di pulizia automatica dei dati, preparazione e la trasformazione per generare funzionalità sintetica. [Altre informazioni sulla pre-elaborazione](#preprocess).
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida incrociata](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Concorrenza| Selezionare i limiti di multi-core da usare quando si usa multi-core calcolo.
    Algoritmo bloccata| Selezionare gli algoritmi che si desidera escludere dal processo di training.

   ![Modulo delle impostazioni avanzate](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Per altre informazioni sui campi, fare clic su descrizione comando informazioni.

<a name="profile"></a>

### <a name="data-profiling"></a>Profiling dati

È possibile ottenere una vasta gamma di statistiche di riepilogo del set di dati per verificare se il set di dati è pronto per Machine Learning. Per le colonne numeriche, includono le statistiche di base solo, ad esempio min, max e numero di errori. Per le colonne numeriche, è anche possibile esaminare i momenti statistici e i quantili stimati. In particolare, il profilo dati include:

* **Funzionalità**: nome della colonna che viene viene riepilogata.

* **Profilo**: una visualizzazione in linea in base al tipo derivato. Ad esempio, valori booleani, stringhe e date avrà i conteggi di valore, mentre i decimali (dati numerici) sono approssimati a istogrammi. In questo modo è possibile ottenere una rapida comprensione della distribuzione dei dati.

* **Tipo di distribuzione**: un conteggio dei valori nella riga dei tipi all'interno di una colonna. I valori null sono proprio tipo, pertanto questa visualizzazione è utile per rilevare valori mancanti pari o dispari.

* **Tipo**: il tipo dedotto della colonna. I valori possibili includono: le stringhe, valori booleani, date e numeri decimali.

* **Min**: il valore minimo della colonna. Voci vuote vengono visualizzati per le funzionalità il cui tipo non dispone di un ordine intrinseco (ad esempio, valori booleani).

* **Max**: il valore massimo della colonna. Ad esempio "min", voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Conteggio**: il numero totale di voci mancanti e non mancante nella colonna.

* **Non mancano conteggio**: il numero di voci nella colonna che non sono mancanti. Si noti che gli errori e le stringhe vuote vengono trattati come valori, in modo che non contribuiscono al "count non mancante".

* **I quantili** (a intervalli 0,1, 1, 5, 25, 50, 75, 95, 99 e per il 99,9%): i valori approssimati al ogni quantile per fornire un'idea della distribuzione dei dati. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Significa**: la media aritmetica della colonna. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Deviazione standard**: la deviazione standard della colonna. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Varianza**: la varianza della colonna. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Asimmetria**: l'asimmetria della colonna. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

* **Curtosi**: curtosi della colonna. Voci vuote vengono visualizzati per le funzionalità con tipi non pertinenti.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Advanced pre-elaborazione

Quando si configurano gli esperimenti, è possibile abilitare l'impostazione avanzata `Preprocess`. Ciò significa quindi che i seguenti passaggi pre-elaborazione e definizione delle funzionalità di dati vengono eseguiti automaticamente.

|Pre-elaborazione&nbsp;passaggi| Descrizione |
| ------------- | ------------- |
|Eliminazione delle caratteristiche con elevata cardinalità o senza varianza|Eliminare queste informazioni dal set di training e convalida, incluse le funzionalità con tutti i valori mancanti, stesso valore per tutte le righe o con una cardinalità molto elevata (ad esempio, gli hash, ID o GUID).|
|Attribuire i valori mancanti|Per funzionalità numeriche, attribuire con Media dei valori nella colonna.<br/><br/>Per funzionalità categoriche, attribuire con valore più frequente.|
|Generazione di caratteristiche aggiuntive|Per le caratteristiche di tipo DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuti, secondi.<br/><br/>Per le caratteristiche di tipo Text: Frequenza del termine basata su unigrammi digrammi e tri-carattere-grammi.|
|Trasformare e codifica |Funzioni numeriche con pochi valori univoci vengono trasformati in funzionalità categoriche.<br/><br/>Viene eseguita la codifica One-hot per cardinalità bassa categoriche; un'elevata cardinalità, la codifica hash hot uno.|
|Rappresentazioni distribuite delle parole|Testo featurizer che converte i vettori di token di testo in vettori di frase usando un modello con training preliminare. Vettore di incorporamento di ogni parola in un documento è aggregato in modo da produrre un vettore di funzionalità del documento.|
|Codifiche di destinazione|Per funzionalità categoriche, esegue il mapping di ogni categoria con il valore di destinazione Media per i problemi di regressione e la probabilità di classe per ogni classe per i problemi di classificazione. Ponderazione basata sulla frequenza e k-piega la convalida incrociata viene applicata in modo da ridurre tramite l'adattamento del mapping e rumore dovuta a categorie di dati di tipo sparse.|
|Codifica di destinazione del testo|Per l'input di testo, viene usato un modello lineare in pila con bag-of-words per generare la probabilità di ogni classe.|
|Peso di prova (rapidamente)|Calcola rapidamente come misura di correlazione di colonne di categoria per la colonna di destinazione. Viene calcolato come il registro del rapporto tra le probabilità di out-of-class-classe in Visual Studio. Questo passaggio genera una colonna di funzionalità numerico per ogni classe e rimuove la necessità di attribuire in modo esplicito i valori mancanti e una gestione degli outlier.|
|Distanza del cluster|Esegue il training di un modello di clustering k-means per tutte le colonne numeriche.  Output k nuove funzionalità, una nuova funzionalità numerica per ogni cluster, che contiene la distanza tra ogni campione del centro di ogni cluster.|

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Per eseguire l'esperimento, fare clic su Avvia. L'esperimento di preparazione del processo richiede alcuni minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli di esperimento

Al termine, la fase di preparazione esperimento si noterà la schermata dei dettagli di esecuzione. Ciò offre un elenco completo dei modelli creati. Per impostazione predefinita, il modello che assegna punteggi la massima in base i parametri nella parte superiore dell'elenco. Durante il processo di training tentativo out più modelli, essi vengono aggiunti all'elenco di iterazione e grafico. Usare il grafico di iterazione per ottenere un rapido confronto delle metriche per i modelli di prodotto finora.

I processi di training può richiedere un po' di tempo per ogni termine dell'esecuzione della pipeline.

![Dashboard dettagli delle esecuzioni](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Formazione di visualizzazione Dettagli esecuzione

Eseguire il drill-in uno qualsiasi dei modelli di output per visualizzare i dettagli dell'esecuzione, come i grafici di metriche e la distribuzione delle prestazioni di training. [Altre informazioni sui grafici](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Dettagli iterazione](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Distribuzione del modello

Dopo aver creato il modello migliore in questione, è possibile distribuirlo come servizio web per eseguire previsioni su nuovi dati.

ML automatizzata consente di distribuire il modello senza scrivere codice:

1. Sono disponibili alcune opzioni per la distribuzione. 
    1. Se si vuole distribuire il modello migliore in base ai criteri delle metriche è impostato per l'esperimento, seleziona **distribuire il modello migliore** dalle **Dettagli esecuzione** pagina.

        ![Pulsante modello di distribuzione](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Se si desidera distribuire un'iterazione di modello specifico, eseguire il drill-sul modello per aprire la pagina dei dettagli di esecuzione specifico e selezionare **Distribuisci Model**.

        ![Pulsante modello di distribuzione](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Primo passaggio consiste nel registrare il modello nel servizio. Selezionare "Register model" e attendere il completamento del processo di registrazione.

    ![Distribuire il pannello modello](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Dopo aver registrato il modello, è possibile scaricare lo script di assegnazione dei punteggi (scoring.py) e lo script di ambiente (condaEnv.yml) da utilizzare durante la distribuzione.

1. Quando vengono scaricati il script di assegnazione dei punteggi e l'ambiente, passare al **Assets** pannello del riquadro di spostamento a sinistra e selezionare **modelli**.

    ![Modelli di riquadro di spostamento](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Selezionare il modello che è stato registrato e selezionare "Crea immagine".

    È possibile identificare il modello tramite la relativa descrizione, che includerà l'ID di esecuzione, numero di iterazione, nel formato seguente: *modello < Iteration_number > _ < Run_ID >*

    ![Modelli: Crea immagine](media/how-to-create-portal-experiments/model-create-image.png)

1. Immettere un nome per l'immagine. 
1. Selezionare il **esplorare** pulsante accanto alla casella "File di assegnazione dei punteggi" per caricare il file di assegnazione dei punteggi (scoring.py) scaricati in precedenza.

1. Selezionare il **esplorare** pulsante accanto alla casella "File Conda" per caricare il file di ambiente (condaEnv.yml) scaricati in precedenza.

    È possibile usare il proprio script di assegnazione dei punteggi e il file conda, nonché caricare i file aggiuntivi. [Altre informazioni su script di assegnazione dei punteggi](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script).

      >[!Important]
      > I nomi di file devono sotto i 32 caratteri e deve iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici tra. Non sono consentiti spazi.

    ![Crea immagine](media/how-to-create-portal-experiments/create-image.png)

1. Selezionare il pulsante "Crea" per avviare la creazione dell'immagine. Questo richiederà alcuni minuti, al termine, verrà visualizzato un messaggio nella barra superiore.
1. Passare alla scheda "Immagini", selezionare la casella di controllo accanto l'immagine da distribuire e selezionare "Crea distribuzione". [Altre informazioni sulle distribuzioni](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

    Sono disponibili 2 opzioni per la distribuzione.
     + Istanza di contenitore di Azure (ACI) - questa viene usata più di test scopo anziché operational distribuzione su larga scala. Assicurarsi di specificare i valori per almeno una base per _capacità di riserva della CPU_e almeno un gigabyte (GB) per _capacità di riserva della memoria_
     + Azure Kubernetes Service (AKS)) - questa opzione è per la distribuzione su larga scala. È necessario disporre di un calcolo basato su servizio contenitore di AZURE pronto.

     ![Immagini: Creare una distribuzione](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Al termine, scegliere **Crea**. Distribuzione del modello può richiedere alcuni minuti per ogni termine dell'esecuzione della pipeline.

1. L'operazione è terminata. Si dispone di un servizio web operativo per generare stime.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sull'apprendimento automatico](concept-automated-ml.md) e Azure Machine Learning.
* [Informazioni su come utilizzare un servizio web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
