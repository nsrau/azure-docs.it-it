---
title: Usare l'interfaccia ML automatizzata di Azure per eseguire il training dei modelli di distribuzione &
titleSuffix: Azure Machine Learning
description: Creazione, gestione e distribuzione di esperimenti automatici di Machine Learning nella pagina di destinazione dell'area di lavoro di Azure Machine Learning (anteprima).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 3a47977f2589227347582dc6fcaff25120e380d7
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034822"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Crea, Esplora e Distribuisci esperimenti di Machine Learning automatici con la pagina di destinazione dell'area di lavoro di Azure Machine Learning (anteprima)

 Questo articolo illustra come creare, esplorare e distribuire esperimenti automatici di Machine Learning nella pagina di destinazione dell'area di lavoro Azure Machine Learning senza una sola riga di codice. Automatizzato Machine Learning consente di automatizzare il processo di selezione dell'algoritmo migliore da usare per i dati specifici, in modo da poter generare rapidamente un modello di machine learning. [Altre informazioni su Machine Learning automatizzato](concept-automated-ml.md).

 Se si preferisce un'esperienza più basata sul codice, è anche possibile [configurare gli esperimenti di Machine Learning automatici in Python](how-to-configure-auto-train.md) con l' [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree) .

* Un'area di lavoro di Azure Machine Learning. Vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

## <a name="get-started"></a>Attività iniziali


1. Accedere alla [pagina di destinazione dell'area di lavoro](https://ml.azure.com/workspaceportal/). 

1. Selezionare la sottoscrizione e l'area di lavoro. 

1. Passare al riquadro sinistro. Selezionare **automatizzato ml** nella sezione **creazione e modifica** .

[![Riquadro di spostamento portale di Azure](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Se è la prima volta che si esegue un esperimento, verrà **visualizzata la schermata iniziale.** 

In caso contrario, verrà visualizzato il dashboard di **Machine Learning automatizzato** con una panoramica di tutti gli esperimenti automatici di Machine Learning, inclusi quelli creati con l'SDK. Qui è possibile filtrare ed esplorare le esecuzioni in base alla data, al nome dell'esperimento e allo stato di esecuzione.

## <a name="create-and-run-experiment"></a>Creazione ed esecuzione dell'esperimento

1. Selezionare **Crea esperimento** e popolare il modulo.

1. Immettere un nome di esperimento univoco.

1. Selezionare un calcolo per il processo di profiling dei dati e di training. Un elenco di calcoli esistenti è disponibile nell'elenco a discesa. Per creare un nuovo calcolo, seguire le istruzioni riportate nel passaggio 4.

1. Selezionare **Crea nuovo calcolo** per configurare il contesto di calcolo per questo esperimento.

    Campo|Descrizione
    ---|---
    Nome dell'ambiente di calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni delle macchine virtuali| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.
    Impostazioni aggiuntive| *Min node* (Nodi minimi): Immettere il numero minimo di nodi per il calcolo. Il numero minimo di nodi per il calcolo di AML è 0. Per abilitare la profilatura dei dati, è necessario disporre di uno o più nodi. <br> *Max node* (Nodi max): Immettere il numero massimo di nodi per il calcolo. Il valore predefinito è 6 nodi per un calcolo AML.
    
    Selezionare **Create**. La creazione di un nuovo calcolo può richiedere alcuni minuti.

    >[!NOTE]
    > Il nome di calcolo indicherà se la profilatura del calcolo selezionato/creato è abilitata. Per altri dettagli, vedere la sezione [profiling dei dati](#profile) .

1. Selezionare un set di dati dal contenitore di archiviazione oppure crearne uno caricando un file dal computer locale al contenitore. L'anteprima pubblica supporta solo i caricamenti di file locali e gli account di archiviazione BLOB di Azure.

    >[!Important]
    > Requisiti per i dati di training:
    >* I dati devono essere in formato tabulare.
    >* Il valore che si desidera stimare (colonna di destinazione) deve essere presente nei dati.

    1. Per creare un nuovo set di dati da un file nel calcolo locale, selezionare **Sfoglia** e quindi selezionare il file. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Next (avanti** ) per caricarlo nel contenitore di archiviazione predefinito creato automaticamente con l'area di lavoro o scegliere un contenitore di archiviazione da usare per l'esperimento. 

    1. Esaminare le **Impostazioni e** il modulo di anteprima per verificarne l'accuratezza. Il modulo viene popolato in modo intelligente in base al tipo di file. 

        Campo| Descrizione
        ----|----
        Formato file| Definisce il layout e il tipo di dati archiviati in un file.
        Delimitatore| Uno o più caratteri per specificare il limite tra aree separate indipendenti in testo normale o altri flussi di dati.
        Codifica| Identifica la tabella dello schema da utilizzare per la lettura del set di dati.
        Intestazioni di colonna| Indica il modo in cui verranno gestite le intestazioni del set di dati.
        Ignora righe | Indica il numero di righe, se presenti, ignorate nel set di dati.
    
        Selezionare **Avanti**.

    1. Il form **dello schema** viene popolato in modo intelligente in base alle selezioni nelle **Impostazioni e** nel modulo di anteprima. Qui configurare il tipo di dati per ogni colonna, rivedere i nomi delle colonne e selezionare le colonne da **non includere** per l'esperimento. 
            
        Selezionare **Avanti.**

1. Selezionare il tipo di processo di training: classificazione, regressione o previsione.

1. Selezionare la colonna di destinazione. si tratta della colonna su cui si desidera eseguire stime.

1. Per la previsione:
    1. Selezionare la colonna Time: Questa colonna contiene i dati relativi all'ora da usare.

    1. Selezionare l'orizzonte di previsione: Indica il numero di unità di tempo (minuti/ore/giorni/settimane/mesi/anni) che il modello sarà in grado di stimare in futuro. Maggiore è il modello necessario per prevedere il futuro, minore sarà il livello di precisione. [Altre informazioni sulla previsione e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. Opzionale Impostazioni avanzate: impostazioni aggiuntive che è possibile usare per controllare meglio il processo di training. In caso contrario, vengono applicati i valori predefiniti in base alla selezione dell'esperimento e ai dati. 

    Impostazioni avanzate|Descrizione
    ------|------
    Primary metric (Metrica principale)| Metrica principale usata per assegnare un punteggio al modello. [Altre informazioni sulle metriche del modello](how-to-configure-auto-train.md#explore-model-metrics).
    Criteri uscita| Quando uno di questi criteri viene soddisfatto, il processo di training termina prima del completamento completo. <br> *Training job time (minutes)* (Durata processo di training - minuti): Per quanto tempo consentire l'esecuzione del processo di training.  <br> *Max number of iterations* (Numero max di iterazioni): Numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non viene eseguito più del numero specificato di iterazioni. <br> *Soglia Punteggio metrica*:  Punteggio di metrica minimo per tutte le pipeline. In questo modo si garantisce che, se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si dedica più tempo al processo di training del necessario.
    Pre-elaborazione| Selezionare questa impostazione per abilitare o disabilitare la pre-elaborazione eseguita da Automatic Machine Learning. La pre-elaborazione include la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche. [Altre informazioni sulla pre-elaborazione](#preprocess).
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida](how-to-configure-auto-train.md)incrociata.
    Concorrenza| Selezionare i limiti multicore da usare quando si usa il calcolo multicore.
    Algoritmo bloccato| Selezionare gli algoritmi che si desidera escludere dal processo di training.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiche di riepilogo & Profiling dati

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è pronto per ML. Per le colonne non numeriche, includono solo le statistiche di base come min, Max e il numero di errori. Per le colonne numeriche, è anche possibile esaminare i relativi momenti statistici e stimare quantili. In particolare, il profilo dati include:

>[!NOTE]
> Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

Statistica|Descrizione
------|------
Funzionalità| Nome della colonna riepilogata.
Profilo| Visualizzazione inline basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi dei valori, mentre i decimali (numerici) hanno istogrammi approssimati. In questo modo è possibile acquisire una rapida conoscenza della distribuzione dei dati.
Distribuzione del tipo| Conteggio dei valori in linea di tipi all'interno di una colonna. I valori null sono di tipo, quindi questa visualizzazione è utile per rilevare valori dispari o mancanti.
Type|Tipo dedotto della colonna. I valori possibili sono: stringhe, valori booleani, date e decimali.
Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.
Max| Valore massimo della colonna. 
Conteggio| Numero totale di voci mancanti e non mancanti nella colonna.
Totale non mancanti| Numero di voci nella colonna mancanti. Le stringhe e gli errori vuoti vengono considerati come valori, quindi non contribuiscono al "conteggio mancante".
Quantili| Valori approssimati in ogni quantile per fornire un senso della distribuzione dei dati.
Media| Media aritmetica o media della colonna.
Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
Variance| Misura della diffusione dei dati di questa colonna dal relativo valore medio. 
Asimmetria| Misura del modo in cui i dati della colonna sono diversi da una distribuzione normale.
Curtosi| Misura della quantità di dati di questa colonna rispetto a una distribuzione normale.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Opzioni di pre-elaborazione avanzate

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

Selezionare **Start (avvia** ) per eseguire l'esperimento. L'esperimento di preparazione del processo richiede alcuni minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

Al termine della fase di preparazione dell'esperimento, si noterà che la schermata dei dettagli di esecuzione inizia a popolare. Questa schermata fornisce un elenco completo dei modelli creati. Per impostazione predefinita, il modello che assegna un punteggio al più alto in base alla metrica scelta si trova nella parte superiore dell'elenco. Quando il processo di training tenta un maggior numero di modelli, questi vengono aggiunti all'elenco di iterazioni e al grafico. Usare il grafico di iterazione per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere tempo.

[![Dashboard dei dettagli di esecuzione](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visualizza i dettagli dell'esecuzione di training

Eseguire il drill-down su uno dei modelli di output per visualizzare i dettagli delle esecuzioni di training, come le metriche delle prestazioni e i grafici di distribuzione. [Altre informazioni sui grafici](how-to-understand-automated-ml.md).

[![Dettagli iterazione](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Una volta ottenuto il modello migliore, è necessario distribuirlo come servizio Web per prevedere i nuovi dati.

Automatizzato ML semplifica la distribuzione del modello senza scrivere codice:

1. Sono disponibili due opzioni per la distribuzione. 

    + Opzione 1: Per distribuire il modello migliore (in base ai criteri di metrica definiti), selezionare Distribuisci modello migliore dalla pagina Dettagli esecuzione.

    + Opzione 2: Per distribuire un'iterazione del modello specifica da questo esperimento, eseguire il drill-down sul modello per aprire la pagina dei dettagli dell'esecuzione e selezionare Distribuisci modello.

1. Popolare il riquadro **Distribuisci modello** .

    Campo| Value
    ----|----
    Nome distribuzione| Immettere un nome univoco per la distribuzione.
    Descrizione della distribuzione| Immettere una descrizione per identificare meglio le finalità della distribuzione.
    Scoring script (Script di assegnazione punteggi)| Generare automaticamente o caricare il proprio file di assegnazione dei punteggi. [Scopri di più sullo script di assegnazione dei punteggi](how-to-deploy-and-where.md#script)
    Environment script (Script ambiente)| Generare automaticamente o caricare il file dell'ambiente.
    >[!Important]
    > Il nome file deve essere inferiore a 32 caratteri e deve iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici tra. Gli spazi non sono consentiti.

1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.

    Quando la distribuzione viene completata correttamente, viene visualizzato il messaggio seguente.

    ![Distribuzione completata](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

A questo punto si dispone di un servizio Web operativo per generare stime.

## <a name="next-steps"></a>Passaggi successivi

* Provare l'esercitazione end-to-end [per creare il primo esperimento di Machine Learning automatizzato con Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Scopri di più su Machine Learning](concept-automated-ml.md) e Azure Machine Learning automatizzati.
* [Informazioni sui risultati automatici di Machine Learning](how-to-understand-automated-ml.md).
* [Informazioni su come utilizzare un servizio Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
