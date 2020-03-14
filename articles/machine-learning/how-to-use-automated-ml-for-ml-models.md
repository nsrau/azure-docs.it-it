---
title: Usare autoML per creare modelli & distribuire
titleSuffix: Azure Machine Learning
description: Creare, rivedere e distribuire modelli di Machine Learning automatici con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 9999d74bf6bef3e8351460add7efc8bdbfcd1045
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270030"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Creare, rivedere e distribuire modelli di Machine Learning automatici con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come creare, esplorare e distribuire modelli di Machine Learning automatici senza una singola riga di codice nell'interfaccia di Azure Machine Learning Studio. Machine Learning automatizzato è un processo in cui viene selezionato il migliore algoritmo di machine learning da usare per i dati specifici. Questo processo consente di generare rapidamente modelli di machine learning. [Altre informazioni su Machine Learning automatizzato](concept-automated-ml.md).
 
Per un esempio end-to-end, provare l' [esercitazione per la creazione di un modello di classificazione con l'interfaccia automatica ml di Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Per un'esperienza basata su codice Python, [configurare gli esperimenti di Machine Learning automatici](how-to-configure-auto-train.md) con l'SDK Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisites

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro di Azure Machine Learning con un tipo di **Enterprise Edition**. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).  Per aggiornare un'area di lavoro esistente a Enterprise Edition, vedere [eseguire l'aggiornamento a Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introduzione

1. Accedere a Azure Machine Learning in https://ml.azure.com. 

1. Selezionare la sottoscrizione e l'area di lavoro. 

1. Passare al riquadro sinistro. Selezionare **automatizzato ml** nella sezione **autore** .

[![Riquadro di spostamento di Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se è la prima volta che si esegue un esperimento, verrà visualizzato un elenco vuoto e i collegamenti alla documentazione. 

In caso contrario, verrà visualizzato un elenco dei recenti esperimenti automatici di Machine Learning, inclusi quelli creati con l'SDK. 

## <a name="create-and-run-experiment"></a>Creazione ed esecuzione dell'esperimento

1. Selezionare **+ nuovo automatizzato ml Run** e popolare il modulo.

1. Selezionare un set di dati dal contenitore di archiviazione o creare un nuovo set di dati. I set di dati possono essere creati da file locali, URL Web, archivi dati o set di dati aperti di Azure. 

    >[!Important]
    > Requisiti per i dati di training:
    >* I dati devono essere in formato tabulare.
    >* Il valore che si desidera stimare (colonna di destinazione) deve essere presente nei dati.

    1. Per creare un nuovo set di dati da un file nel computer locale, selezionare **Sfoglia** e quindi selezionare il file. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Avanti** per aprire il **modulo archivio dati e selezione file**. In questo modulo è possibile selezionare la posizione in cui caricare il set di dati. il contenitore di archiviazione predefinito creato automaticamente con l'area di lavoro o scegliere un contenitore di archiviazione che si vuole usare per l'esperimento. 

    1. Esaminare le **Impostazioni e** il modulo di anteprima per verificarne l'accuratezza. Il modulo viene popolato in modo intelligente in base al tipo di file. 

        Campo| Descrizione
        ----|----
        Formato file| Definisce il layout e il tipo di dati archiviati in un file.
        Delimitatore| Uno o più caratteri per specificare il limite tra aree separate indipendenti in testo normale o altri flussi di dati.
        Codifica| Identifica la tabella dello schema bit-carattere da usare per leggere il set di dati.
        Intestazioni di colonna| Indica come verranno considerate le intestazioni del set di dati, se presenti.
        Ignora righe | Indica quante righe vengono eventualmente ignorate nel set di dati.
    
        Selezionare **Avanti**.

    1. Il form **dello schema** viene popolato in modo intelligente in base alle selezioni nelle **Impostazioni e** nel modulo di anteprima. Qui configurare il tipo di dati per ogni colonna, rivedere i nomi delle colonne e selezionare le colonne da **non includere** per l'esperimento. 
            
        Selezionare **Avanti.**

    1. Il modulo **Confirm Details** è un riepilogo delle informazioni popolate in precedenza nelle **informazioni di base** e nelle **Impostazioni e** nei moduli di anteprima. È anche possibile creare un profilo dati per il set di dati usando un calcolo abilitato per la profilatura. Altre informazioni sulla [profilatura dei dati](#profile).

        Selezionare **Avanti**.
1. Selezionare il set di dati appena creato dopo che è stato visualizzato. È anche possibile visualizzare un'anteprima del set di dati e delle statistiche di esempio. 

1. Nel modulo **Configura esecuzione** , immettere un nome di esperimento univoco.

1. Selezionare una colonna di destinazione. si tratta della colonna su cui si desidera eseguire stime.

1. Selezionare un calcolo per il processo di profiling dei dati e di training. Un elenco di calcoli esistenti è disponibile nell'elenco a discesa. Per creare un nuovo calcolo, seguire le istruzioni riportate nel passaggio 7.

1. Selezionare **Crea nuovo calcolo** per configurare il contesto di calcolo per questo esperimento.

    Campo|Descrizione
    ---|---
    Nome del calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.
    Numero minimo/massimo di nodi (in Impostazioni avanzate)| Per profilare i dati, è necessario specificare almeno un nodo. Immettere il numero massimo di nodi per il calcolo. Il valore predefinito è 6 nodi per un calcolo AML.
    
    Selezionare **Create** (Crea). La creazione di un nuovo calcolo può richiedere alcuni minuti.

    >[!NOTE]
    > Il nome di calcolo indicherà se la *profilatura*del calcolo selezionato/creato è abilitata. Per altri dettagli, vedere la sezione [profiling dei dati](#profile) .

    Selezionare **Avanti**.

1. Nel modulo **tipo di attività e impostazioni** selezionare il tipo di attività, ovvero classificazione, regressione o previsione. Per ulteriori informazioni [, vedere come definire i tipi di attività](how-to-define-task-type.md) .

    1. Per la classificazione, è anche possibile abilitare l'apprendimento avanzato usato per featurizations di testo.

    1. Per la previsione:
        1. Seleziona colonna temporale: questa colonna contiene i dati relativi all'ora da usare.

        1. Select Forecast Horizon: indica il numero di unità di tempo (minuti/ore/giorni/settimane/mesi/anni) che il modello sarà in grado di stimare in futuro. Maggiore è il modello necessario per prevedere il futuro, minore sarà il livello di precisione. [Altre informazioni sulla previsione e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. Opzionale Visualizzare le impostazioni di configurazione aggiuntive: altre impostazioni che è possibile usare per controllare meglio il processo di training. Altrimenti, vengono applicate le impostazioni predefinite in base alla selezione dell'esperimento e ai dati. 

    Configurazioni aggiuntive|Descrizione
    ------|------
    Primary metric (Metrica principale)| Metrica principale usata per assegnare un punteggio al modello. [Altre informazioni sulle metriche del modello](how-to-configure-auto-train.md#explore-model-metrics).
    Automatic featurization (Definizione automatica funzionalità)| Selezionare questa impostazione per abilitare o disabilitare la pre-elaborazione eseguita da Automatic Machine Learning. La pre-elaborazione include la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche. Non supportato per il tipo di attività di previsione delle serie temporali. [Altre informazioni sulla pre-elaborazione](#featurization). 
    Descrizione del modello migliore | Selezionare questa opzione per abilitare o disabilitare per mostrare la spiegazione del modello migliore consigliato
    Algoritmo bloccato| Selezionare gli algoritmi che si desidera escludere dal processo di training.
    Exit criterion (Esci da criterio)| Quando uno di questi criteri viene soddisfatto, il processo di training viene arrestato. <br> *Tempo del processo di training (ore)* : per quanto tempo consentire l'esecuzione del processo di training. <br> *Soglia Punteggio metrica*: Punteggio metrica minimo per tutte le pipeline. In questo modo si garantisce che, se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si dedica più tempo al processo di training del necessario.
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida incrociata](how-to-configure-auto-train.md).
    Concorrenza| Numero massimo di *iterazioni simultanee*: numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non viene eseguito più del numero specificato di iterazioni.

1. Opzionale Visualizza impostazioni conteggi: se si sceglie di abilitare **conteggi automatici** nel modulo **impostazioni di configurazione aggiuntive** , questo modulo consente di specificare le colonne in cui eseguire tali featurizations e di selezionare il valore statistico da usare per le imputazioni di valori mancanti.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiche di riepilogo & Profiling dati

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è pronto per ML. Per le colonne non numeriche, includono solo le statistiche di base come min, Max e il numero di errori. Per le colonne numeriche, è anche possibile esaminare i relativi momenti statistici e stimare quantili. In particolare, il profilo dati include:

>[!NOTE]
> Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

Statistiche|Descrizione
------|------
Funzionalità| Nome della colonna riepilogata.
Profilo| Visualizzazione inline basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi dei valori, mentre i decimali (numerici) hanno istogrammi approssimati. In questo modo è possibile acquisire una rapida conoscenza della distribuzione dei dati.
Distribuzione del tipo| Conteggio dei valori in linea di tipi all'interno di una colonna. I valori null sono di tipo, quindi questa visualizzazione è utile per rilevare valori dispari o mancanti.
Type|Tipo dedotto della colonna. I valori possibili sono: stringhe, valori booleani, date e decimali.
Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.
Max| Valore massimo della colonna. 
Conteggio| Numero totale di voci mancanti e non mancanti nella colonna.
Totale non mancanti| Numero di voci nella colonna mancanti. Le stringhe e gli errori vuoti vengono considerati come valori, quindi non contribuiscono al "conteggio mancante".
Quantiles| Valori approssimati in ogni quantile per fornire un senso della distribuzione dei dati.
Media| Media aritmetica o media della colonna.
Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
Variance| Misura della diffusione dei dati di questa colonna dal relativo valore medio. 
Asimmetria| Misura del modo in cui i dati della colonna sono diversi da una distribuzione normale.
Curtosi| Misura della quantità di dati di questa colonna rispetto a una distribuzione normale.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opzioni avanzate di conteggi

Automatizzato Machine Learning offre automaticamente la pre-elaborazione e i dati Guardrails, per consentire di identificare e gestire potenziali problemi con i dati. 

### <a name="preprocessing"></a>Pre-elaborazione

|Pre-elaborazione di&nbsp;passaggi| Descrizione |
| ------------- | ------------- |
|Eliminazione delle caratteristiche con elevata cardinalità o senza varianza|Eliminarli dai set di training e di convalida, incluse le funzionalità con tutti i valori mancanti, lo stesso valore in tutte le righe o con cardinalità estremamente elevata (ad esempio, hash, ID o GUID).|
|Attribuire i valori mancanti|Per le funzionalità numeriche, imputare alla media dei valori nella colonna.<br/><br/>Per le funzionalità categoriche, imputare il valore più frequente.|
|Generazione di caratteristiche aggiuntive|Per le funzionalità DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuto, secondo.<br/><br/>Per le funzionalità di testo: frequenza dei termini basata su unigrammi, bi-grammi e Tri-caratteri-grammi.|
|Trasformare e codificare |Le funzionalità numeriche con pochi valori univoci vengono trasformate in funzionalità categoriche.<br/><br/>La codifica One-Hot viene eseguita per una cardinalità categorica bassa; per la cardinalità elevata, codifica One-Hot-hash.|
|Incorporamenti di Word|Featurizer di testo che converte i vettori di token di testo in vettori di frase usando un modello con training preliminare. Il vettore di incorporamento di ogni parola in un documento viene aggregato insieme per produrre un vettore di funzionalità del documento.|
|Codifiche di destinazione|Per le funzionalità categoriche, esegue il mapping di ogni categoria con il valore di destinazione medio per i problemi di regressione e la probabilità della classe per ogni classe per i problemi di classificazione. Viene applicata la ponderazione basata sulla frequenza e la convalida incrociata k-fold per ridurre l'adattamento del mapping e del rumore causato da categorie di dati di tipo sparse.|
|Codifica della destinazione del testo|Per l'input di testo, viene usato un modello lineare in pila con il contenitore di parole per generare la probabilità di ogni classe.|
|Peso delle evidenze (guai)|Calcola il dolore come misura della correlazione delle colonne categoriche alla colonna di destinazione. Viene calcolato come log del rapporto tra le probabilità out-of-Class di Visual Studio. Questo passaggio restituisce una colonna di funzionalità numerica per classe e rimuove la necessità di imputare in modo esplicito i valori mancanti e il trattamento degli outlier.|
|Distanza del cluster|Addestra un modello di clustering k-means su tutte le colonne numeriche.  Restituisce k nuove funzionalità, una nuova funzionalità numerica per ogni cluster, che contiene la distanza di ogni campione al centro di ogni cluster.|

### <a name="data-guardrails"></a>Guardrails dati

I dati Guardrails vengono applicati automaticamente per identificare i potenziali problemi relativi ai dati (ad esempio, valori mancanti, squilibrio della classe) e agevolare le azioni correttive per ottenere risultati migliori. Sono disponibili numerose procedure consigliate che è possibile applicare per ottenere risultati affidabili. 

La tabella seguente descrive i Guardrails di dati attualmente supportati e gli Stati associati che gli utenti possono incontrare durante l'invio dell'esperimento.

Guardrail|Stato|&nbsp;di condizione per il trigger&nbsp;
---|---|---
Valori&nbsp;mancanti&nbsp;l'imputazione |**Passato** <br> <br> **Fissa**|    Nessun valore mancante in nessuna delle colonne di input&nbsp; <br> <br> Alcune colonne contengono valori mancanti
Convalida incrociata|**Eseguita**|Se non viene fornito alcun set di convalida esplicito
&nbsp;rilevamento della funzionalità di&nbsp;della cardinalità elevata&nbsp;|    **Passato** <br> <br>**Eseguita**|    Non sono state rilevate funzionalità di cardinalità elevata <br><br> Sono state rilevate colonne di input con cardinalità elevata
Rilevamento del bilanciamento della classe    |**Passato** <br><br><br>**Avvisi** |Le classi sono bilanciate nei dati di training; Un set di dati è considerato bilanciato se ogni classe presenta una rappresentazione corretta del set di dati, misurata in base al numero e al rapporto degli esempi <br> <br> Le classi nei dati di training sono sbilanciate
Coerenza dei dati delle serie temporali|**Passato** <br><br><br><br> **Fissa** |<br> Sono stati analizzati i valori di {Horizon, lag, finestra in sequenza, ovvero la finestra in sequenza, ma non sono stati rilevati potenziali problemi di memoria insufficiente. <br> <br>Sono stati analizzati i valori di {Horizon, lag, Window in sequenza} selezionati che potrebbero causare un esaurimento della memoria da parte dell'esperimento. La finestra in ritardo o in sequenza è stata disattivata.

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Selezionare **Finish (fine** ) per eseguire l'esperimento. L'esperimento di preparazione del processo può richiedere fino a 10 minuti. Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere altri 2-3 minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

Verrà visualizzata la schermata **Esegui dettagli** nella scheda **Dettagli** . Questa schermata mostra un riepilogo dell'esecuzione dell'esperimento, inclusa una barra di stato nella parte superiore accanto al numero di esecuzione. 

La scheda **Modelli** contiene un elenco dei modelli creati, ordinati in base al punteggio della metrica. Per impostazione predefinita, il modello che riceve il punteggio più alto in base alla metrica scelta si trova all'inizio dell'elenco. Man mano che il processo di training prova altri modelli, questi vengono aggiunti all'elenco. Usare questa procedura per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

[![Dashboard dei dettagli di esecuzione](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visualizza i dettagli dell'esecuzione di training

Eseguire il drill-down su uno dei modelli completati per visualizzare i dettagli delle esecuzioni di training, ad esempio le metriche di esecuzione nella scheda **Dettagli modello** o i grafici delle prestazioni nella scheda **visualizzazioni** . [altre informazioni sui grafici](how-to-understand-automated-ml.md).

[Dettagli iterazione ![](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Una volta ottenuto il modello migliore, è necessario distribuirlo come servizio Web per prevedere i nuovi dati.

Automatizzato ML semplifica la distribuzione del modello senza scrivere codice:

1. Sono disponibili due opzioni per la distribuzione. 

    + Opzione 1: per distribuire il modello migliore (in base ai criteri della metrica definiti), selezionare il pulsante **Distribuisci modello migliore** nella scheda **Dettagli** .

    + Opzione 2: per distribuire un'iterazione del modello specifica da questo esperimento, eseguire il drill-down sul modello per aprire la scheda **Dettagli modello** e selezionare **Distribuisci modello**.

1. Popolare il riquadro **Distribuisci modello** .

    Campo| valore
    ----|----
    Nome| Immettere un nome univoco per la distribuzione.
    Descrizione| Immettere una descrizione per identificare meglio le finalità della distribuzione.
    Tipo di calcolo| Selezionare il tipo di endpoint che si vuole distribuire: *Azure Kubernetes Service (AKS)* o *Azure container instance (ACI)* .
    Nome del calcolo| *Si applica solo a AKS:* Selezionare il nome del cluster AKS in cui si vuole eseguire la distribuzione.
    Abilita autenticazione | Selezionare questa impostazione per consentire l'autenticazione basata su token o basata su chiave.
    Usa asset di distribuzione personalizzati| Abilitare questa funzionalità se si desidera caricare lo script di assegnazione dei punteggi e il file dell'ambiente. [Altre informazioni sugli script per il Punteggio](how-to-deploy-and-where.md#script).

    >[!Important]
    > Il nome file deve essere inferiore a 32 caratteri e deve iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici tra. Gli spazi non sono consentiti.

    Il menu *Avanzate* offre funzionalità di distribuzione predefinite, ad esempio le impostazioni di [raccolta dei dati](how-to-enable-app-insights.md) e di utilizzo delle risorse. Se si desidera eseguire l'override di queste impostazioni predefinite, effettuare questa operazione in questo menu.

1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.

A questo punto si dispone di un servizio Web operativo per generare stime. È possibile testare le stime eseguendo una query sul servizio dal [supporto Azure Machine Learning incorporato Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come utilizzare un servizio Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Informazioni sui risultati automatici di Machine Learning](how-to-understand-automated-ml.md).
* [Scopri di più su Machine Learning e Azure Machine Learning automatizzati](concept-automated-ml.md) .
