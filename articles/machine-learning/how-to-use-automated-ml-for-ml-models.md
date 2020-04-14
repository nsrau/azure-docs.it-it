---
title: Usare autoML per creare modelli & distribuire
titleSuffix: Azure Machine Learning
description: Creare, esaminare e distribuire modelli di Machine Learning automatizzati con Azure Machine Learning.Create, review, and deploy automated machine learning models with Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257233"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Creare, esaminare e distribuire modelli di Machine Learning automatizzati con Azure Machine LearningCreate, review, and deploy automated machine learning models with Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questo articolo viene illustrato come creare, esplorare e distribuire modelli di Machine Learning automatizzati senza una singola riga di codice nell'interfaccia di studio di Azure Machine Learning.In this article, you learn how to create, explore, and deploy automated machine learning models without a single line of code in Azure Machine Learning's studio interface. L'apprendimento automatico è un processo in cui viene selezionato il miglior algoritmo di apprendimento automatico da usare per i dati specifici. Questo processo consente di generare rapidamente modelli di apprendimento automatico. [Ulteriori informazioni sull'apprendimento automatico](concept-automated-ml.md).
 
Per un esempio end-to-end, provare l'esercitazione per la creazione di un modello di [classificazione con l'interfaccia ML automatizzata](tutorial-first-experiment-automated-ml.md)di Azure Machine Learning. 

Per un'esperienza basata su codice Python, configura gli esperimenti di [apprendimento automatico automatizzati](how-to-configure-auto-train.md) con Azure Machine Learning SDK.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Area di lavoro di Azure Machine Learning con un tipo di **edizione Enterprise**. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).  Per aggiornare un'area di lavoro esistente all'edizione Enterprise, vedere [Upgrade to Enterprise edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introduzione

1. Accedere ad Azure Machine https://ml.azure.comLearning all'indirizzo . 

1. Selezionare l'abbonamento e l'area di lavoro. 

1. Passare al riquadro sinistro. Selezionare ML automatizzato nella sezione **Autore.Select** **Automated ML** under the Author section.

[![Riquadro di spostamento di Azure Machine Learning StudioAzure Machine Learning studio navigation pane](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se è la prima volta che fai degli esperimenti, vedrai un elenco vuoto e link alla documentazione. 

In caso contrario, verrà visualizzato un elenco dei recenti esperimenti di apprendimento automatico automatizzati, inclusi quelli creati con l'SDK. 

## <a name="create-and-run-experiment"></a>Creare ed eseguire esperimenti

1. Selezionare **: Nuova esecuzione automatica di ML** e popolare il modulo.

1. Selezionare un set di dati dal contenitore di archiviazione o creare un nuovo set di dati. I set di dati possono essere creati da file locali, URL Web, datastore o set di dati aperti di Azure.Datasets can be created from local files, web urls, datastores, or Azure open datasets. 

    >[!Important]
    > Requisiti per i dati di training:
    >* I dati devono essere in formato tabulare.
    >* Il valore che si desidera stimare (colonna di destinazione) deve essere presente nei dati.

    1. Per creare un nuovo set di dati da un file nel computer locale, selezionare **Sfoglia** e quindi selezionare il file. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Avanti** per aprire il **modulo Datastore e selezione file.** In questo modulo è possibile selezionare dove caricare il set di dati; il contenitore di archiviazione predefinito creato automaticamente con l'area di lavoro oppure scegliere un contenitore di archiviazione da usare per l'esperimento. 

    1. Rivedere il modulo **Impostazioni e anteprima** per verificarne l'accuratezza. Il modulo viene popolato in modo intelligente in base al tipo di file. 

        Campo| Descrizione
        ----|----
        Formato file| Definisce il layout e il tipo di dati archiviati in un file.
        Delimitatore| Uno o più caratteri per specificare il limite tra aree distinte indipendenti in testo normale o altri flussi di dati.
        Codifica| Identifica la tabella dello schema bit-carattere da usare per leggere il set di dati.
        Intestazioni di colonna| Indica come verranno considerate le intestazioni del set di dati, se presenti.
        Ignora righe | Indica quante righe vengono eventualmente ignorate nel set di dati.
    
        Selezionare **Avanti**.

    1. Il modulo **Schema** viene popolato in modo intelligente in base alle selezioni nel modulo **Impostazioni e anteprima.** Qui configurare il tipo di dati per ogni colonna, esaminare i nomi delle colonne e selezionare le colonne **da non includere** per l'esperimento. 
            
        Selezionare **Avanti.Select Next.**

    1. Il modulo **Conferma dettagli** è un riepilogo delle informazioni precedentemente inserite nei moduli Informazioni **di base,** **Impostazioni e anteprima.** È anche possibile creare un profilo dati per il set di dati usando un calcolo abilitato per la profilatura. Altre informazioni sulla [profilatura dei dati](#profile).

        Selezionare **Avanti**.
1. Selezionare il set di dati appena creato una volta visualizzato. È inoltre possibile visualizzare un'anteprima del set di dati e delle statistiche di esempio. 

1. Nel modulo **Configura esecuzione** immettere un nome univoco per l'esperimento.

1. Selezionare una colonna di destinazione; questa è la colonna su cui si desidera eseguire le stime.

1. Selezionare un calcolo per la profilatura dei dati e il processo di formazione. Nell'elenco a discesa è disponibile un elenco dei calcoli esistenti. Per creare un nuovo calcolo, seguire le istruzioni nel passaggio 7.To create a new compute, follow the instructions in step 7.

1. Selezionare **Crea un nuovo calcolo** per configurare il contesto di calcolo per questo esperimento.

    Campo|Descrizione
    ---|---
    Nome del calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.
    Numero minimo/massimo di nodi (in Impostazioni avanzate)| Per profilare i dati, è necessario specificare almeno un nodo. Immettere il numero massimo di nodi per il calcolo. Il valore predefinito è 6 nodi per un calcolo AML.
    
    Selezionare **Create** (Crea). La creazione di un nuovo calcolo può richiedere alcuni minuti.

    >[!NOTE]
    > Il nome del calcolo indicherà se il calcolo selezionato/creare è *abilitato per la profilatura.* (Vedere la sezione [Profiling dei dati](#profile) per ulteriori dettagli).

    Selezionare **Avanti**.

1. Nel modulo **Tipo di attività e impostazioni** selezionare il tipo di attività: classificazione, regressione o previsione. Per ulteriori informazioni, vedere come definire i [tipi di attività.](how-to-define-task-type.md)

    1. Per la classificazione, è anche possibile abilitare il deep learning che viene utilizzato per le imprese di testo.

    1. Per le previsioni:
        1. Colonna Seleziona ora: questa colonna contiene i dati temporali da utilizzare.

        1. Selezionare l'orizzonte di previsione: indicare quante unità di tempo (minuti/ore/giorni/settimane/mesi/anni) il modello sarà in grado di prevedere in futuro. Quanto più il modello è necessario per prevedere in futuro, meno preciso diventerà. [Ulteriori informazioni sulle previsioni e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. (Facoltativo) Visualizzare le impostazioni di configurazione dell'addizione: impostazioni aggiuntive che è possibile utilizzare per controllare meglio il processo di formazione. Altrimenti, vengono applicate le impostazioni predefinite in base alla selezione dell'esperimento e ai dati. 

    Configurazioni aggiuntive|Descrizione
    ------|------
    Primary metric (Metrica principale)| Metrica principale utilizzata per il punteggio del modello. [Ulteriori informazioni sulle metriche del modello](how-to-configure-auto-train.md#explore-model-metrics).
    Automatic featurization (Definizione automatica funzionalità)| Selezionare questa opzione per abilitare o disabilitare la pre-elaborazione eseguita dall'apprendimento automatico automatico. La pre-elaborazione include la pulizia, la preparazione e la trasformazione automatiche dei dati per generare feature sintetiche. Non supportato per il tipo di attività di previsione della serie temporale. [Ulteriori informazioni sulla pre-elaborazione](#featurization). 
    Spiegare il modello migliore | Selezionare questa opzione per abilitare o disabilitare la spiegazione del modello migliore consigliato
    Algoritmo bloccato| Selezionare gli algoritmi che si desidera escludere dal processo di training.
    Exit criterion (Esci da criterio)| Quando uno di questi criteri viene soddisfatto, il lavoro di formazione viene interrotto. <br> Tempo di lavoro di *formazione (ore):* per quanto tempo consentire l'esecuzione del processo di formazione. <br> *Soglia punteggio metrica:* punteggio metrico minimo per tutte le pipeline. In questo modo si garantisce che se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si spende più tempo sul lavoro di formazione del necessario.
    Convalida| Selezionare una delle opzioni di convalida incrociata da utilizzare nel processo di training. [Ulteriori informazioni sulla convalida incrociata](how-to-configure-auto-train.md).
    Concorrenza| Numero massimo di *iterazioni simultanee:* numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non verrà eseguito per un numero di iterazioni superiore a quello specificato.

1. (Facoltativo) Visualizzare le impostazioni di featurizzazione: se si sceglie di abilitare la **featurizzazione automatica** nel modulo Impostazioni di **configurazione aggiuntive,** è possibile specificare le colonne in cui eseguire tali featurizzazioni e selezionare il valore statistico da utilizzare per le imputazioni di valori mancanti.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Profilazione dei dati & statistiche di riepilogoData profiling & summary stats

È possibile ottenere una vasta gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è mL-ready. Per le colonne non numeriche, includono solo statistiche di base come min, max e count error. Per le colonne numeriche, è inoltre possibile esaminare i momenti statistici e i quantili stimati. In particolare, il nostro profilo dati include:

>[!NOTE]
> Le voci vuote vengono visualizzate per le entità geografiche con tipi irrilevanti.

Statistiche|Descrizione
------|------
Funzionalità| Nome della colonna che viene riepilogata.
Profilo| Visualizzazione in linea in base al tipo dedotto. Ad esempio, stringhe, valori booleani e date avranno conteggi dei valori, mentre i decimali (numerici) hanno approssimato gli istogrammi. Ciò consente di ottenere una rapida comprensione della distribuzione dei dati.
Distribuzione dei tipi| Numero di valori in linea di tipi all'interno di una colonna. I valori Null sono il proprio tipo, pertanto questa visualizzazione è utile per rilevare valori dispari o mancanti.
Type|Tipo dedotto della colonna. I valori possibili includono: stringhe, valori booleani, date e decimali.
Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le feature il cui tipo non dispone di un ordinamento intrinseco (ad esempio i valori booleani).
Max| Valore massimo della colonna. 
Conteggio| Numero totale di voci mancanti e non mancanti nella colonna.
Totale non mancanti| Numero di voci nella colonna che non sono mancanti. Le stringhe e gli errori vuoti vengono considerati come valori, pertanto non contribuiranno al "conteggio non mancante".
Quantiles| Valori approssimativi ad ogni quantile per fornire un senso della distribuzione dei dati.
Media| Media aritmetica o media della colonna.
Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
Variance| Misura della distanza di distribuzione dei dati di questa colonna dal valore medio. 
Asimmetria| Misura della differenza dei dati di questa colonna da una distribuzione normale.
Curtosi| Misura di come i dati di questa colonna vengono confrontati con una distribuzione normale.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opzioni avanzate di featurizzazione

L'apprendimento automatico offre automaticamente la pre-elaborazione e i guardrail dei dati, che consentono di identificare e gestire potenziali problemi con i dati. 

### <a name="preprocessing"></a>Pre-elaborazione

> [!NOTE]
> Se si prevede di esportare i modelli creati da ML auto in un [modello ONNX,](concept-onnx.md)nel formato ONNX sono supportate solo le opzioni di featurization indicate con un simbolo . Ulteriori informazioni sulla [conversione dei modelli in ONNX](concept-automated-ml.md#use-with-onnx). 

|Passaggi&nbsp;di pre-elaborazione| Descrizione |
| ------------- | ------------- |
|Eliminare l'elevata cardinalità o nessuna feature di varianza |Eliminare questi dai set di training e convalida, incluse le funzionalità con tutti i valori mancanti, lo stesso valore in tutte le righe o con cardinalità estremamente elevata (ad esempio, istre, ID o GUID).|
|Impute valori mancanti |Per le feature numeriche, imputare con la media dei valori nella colonna.<br/><br/>Per le funzionalità categoriche, impute con il valore più frequente.|
|Genera funzionalità aggiuntive |Per le caratteristiche DateTime: Anno, Mese, Giorno, Giorno della settimana, Giorno dell'anno, Trimestre, Settimana dell'anno, Ora, Minuto, Secondo.<br/><br/>Per le funzioni di testo: frequenza dei termini basata su unigrammi, bigrammi e tri-grammi.|
|Trasformare e codificare|Le caratteristiche numeriche con pochi valori univoci vengono trasformate in caratteristiche categoriche.<br/><br/>La codifica one-hot viene eseguita per la classificazione a bassa cardinalità; per una cardinalità elevata, codifica one-hot-hash.|
|Incorporamenti di parole|Featurizzatore di testo che converte vettori di token di testo in vettori di frasi utilizzando un modello con training preliminare. Il vettore di incorporamento di ogni parola in un documento viene aggregato insieme per produrre un vettore di funzionalità del documento.|
|Codifiche di destinazione|Per le funzionalità di categoria, esegue il mapping di ogni categoria con un valore di destinazione medio per i problemi di regressione e alla probabilità di classe per ogni classe per i problemi di classificazione. La ponderazione basata sulla frequenza e la convalida trasversale k-fold vengono applicate per ridurre l'adattamento della mappatura e del rumore causato da categorie di dati sparse.|
|Codifica della destinazione testuale|Per l'input di testo, un modello lineare impilato con sacchetto di parole viene utilizzato per generare la probabilità di ogni classe.|
|Peso delle prove (WoE)|Calcola WoE come misura di correlazione delle colonne di categoria alla colonna di destinazione. Viene calcolato come log del rapporto tra probabilità in-class e fuori classe. Questo passaggio restituisce una colonna numerica della funzionalità per classe ed elimina la necessità di imputare in modo esplicito i valori mancanti e un trattamento outlier.|
|Distanza cluster|Forma ad un modello di clustering k-means su tutte le colonne numeriche.  Uscite k nuove funzionalità, una nuova caratteristica numerica per cluster, contenente la distanza di ogni campione per la centroide di ogni cluster.|

### <a name="data-guardrails"></a>Guardrail per i dati

I guardrail dati vengono applicati quando la featurizzazione automatica è abilitata o la convalida è impostata su auto. I guardrail di dati consentono di identificare potenziali problemi con i dati (ad esempio, valori mancanti, squilibrio di classe) e aiutano a intraprendere azioni correttive per migliorare i risultati. 

Gli utenti possono esaminare i guardrail dei dati nello studio all'interno della ```show_output=True``` scheda **Guardrail di dati** di un'esecuzione automatica di ML o impostando quando si invia un esperimento utilizzando Python SDK. 

#### <a name="data-guardrail-states"></a>Stati di Guardrail dei dati

I guardrail di dati visualizzeranno uno dei tre stati seguenti: **Superato**, **Fatto**o **Avviso**.

State| Descrizione
----|----
Passed| Non sono stati rilevati problemi relativi ai dati e non è richiesta alcuna azione da parte dell'utente. 
Operazione completata| Le modifiche sono state applicate ai dati. Incoraggiamo gli utenti a rivedere le azioni correttive che m/ML automatica ha eseguito per garantire che le modifiche siano in linea con i risultati previsti. 
Avvisato| È stato rilevato un problema di dati che non è stato possibile risolto. Incoraggiamo gli utenti a rivedere e risolvere il problema. 

>[!NOTE]
> Le versioni precedenti degli esperimenti mL automatizzati mostravano un quarto stato: **Fixed**. Gli esperimenti più recenti non visualizzeranno questo stato e tutti i guardrail che visualizzano lo stato **Fisso** ora visualizzeranno **Fatto**.   

Nella tabella seguente vengono descritti i guardrail di dati attualmente supportati e gli stati associati che gli utenti possono incontrare durante l'invio dell'esperimento.

Guardrail|Stato|Condizione&nbsp;&nbsp;per trigger
---|---|---
Imputazione dei valori di funzionalità mancanti |**Passed** <br><br><br> **Operazione completata**| Non sono stati rilevati valori di feature mancanti nei dati di training. Ulteriori informazioni [sull'imputazione dei valori mancanti.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> I valori delle funzionalità mancanti sono stati rilevati nei dati di training e non sono stati calcolati.
Gestione delle funzionalità ad alta cardinalitàHigh cardinality feature handling |**Passed** <br><br><br> **Operazione completata**| Gli input sono stati analizzati e non sono state rilevate funzionalità di cardinalità elevata. Ulteriori informazioni sul rilevamento delle funzionalità di [cardinalità elevata.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Le funzionalità di cardinalità elevata sono state rilevate negli input e sono state gestite.
Gestione divisione convalida |**Operazione completata**| *La configurazione di convalida è stata impostata su 'auto' e i dati di training contenevano **meno** di 20.000 righe.* <br> Ogni iterazione del modello sottoposto a training è stata convalidata tramite la convalida incrociata. Ulteriori informazioni sui dati di [convalida.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *La configurazione di convalida è stata impostata **more** su 'auto' e i dati di training contenevano più di 20.000 righe.* <br> I dati di input sono stati suddivisi in un set di dati di training e un set di dati di convalida per la convalida del modello.
Rilevamento del bilanciamento delle classi |**Passed** <br><br><br><br> **Avvisato** | Gli input sono stati analizzati e tutte le classi sono bilanciate nei dati di training. Un set di dati viene considerato bilanciato se ogni classe dispone di una buona rappresentazione nel set di dati, misurata in base al numero e al rapporto dei campioni. <br><br><br> Le classi sbilanciate sono state rilevate negli input. Per risolvere la distorsione del modello, risolvere il problema di bilanciamento. Ulteriori informazioni sui [dati sbilanciati.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Rilevamento dei problemi di memoria |**Passed** <br><br><br><br> **Operazione completata** |<br> Sono stati analizzati i valori selezionati per l'orizzonte, il ritardo e la finestra di rotolamento e non sono stati rilevati potenziali problemi di memoria insufficiente. Ulteriori informazioni sulle [configurazioni](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) di previsione delle serie temporali. <br><br><br>I valori selezionati di "Horizon, lag, rolling window" sono stati analizzati e potrebbero causare l'esaurimento della memoria dell'esperimento. Le configurazioni delle finestre di ritardo o di rotolamento sono state disattivate.
Rilevamento della frequenza |**Passed** <br><br><br><br> **Operazione completata** |<br> La serie temporale è stata analizzata e tutti i punti dati sono allineati con la frequenza rilevata. <br> <br> La serie temporale è stata analizzata e sono stati rilevati punti dati che non sono allineati con la frequenza rilevata. Questi punti dati sono stati rimossi dal set di dati. Ulteriori informazioni sulla preparazione dei dati per le [previsioni di serie temporali.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Selezionare **Fine** per eseguire l'esperimento. L'esperimento di preparazione del processo può richiedere fino a 10 minuti. Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere altri 2-3 minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

La schermata **Dettagli esecuzione** si apre nella scheda **Dettagli.** Questa schermata mostra un riepilogo dell'esecuzione dell'esperimento, inclusa una barra di stato nella parte superiore accanto al numero di esecuzione. 

La scheda **Modelli** contiene un elenco dei modelli creati, ordinati in base al punteggio della metrica. Per impostazione predefinita, il modello che riceve il punteggio più alto in base alla metrica scelta si trova all'inizio dell'elenco. Man mano che il processo di training prova altri modelli, questi vengono aggiunti all'elenco. Usare questa procedura per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

[![Dashboard dei dettagli di esecuzione](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visualizzare i dettagli dell'esecuzione del training

Eseguire il drill-down su uno dei modelli completati per visualizzare i dettagli dell'esecuzione [Learn more about charts](how-to-understand-automated-ml.md)del training, ad esempio le metriche di esecuzione nella scheda **Dettagli modello** o i grafici delle prestazioni nella scheda **Visualizzazioni.**

[![Dettagli iterazione](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Una volta che si dispone del modello migliore a portata di mano, è il momento di distribuirlo come servizio web per prevedere i nuovi dati.

L'autenticazione a più file automatico consente di distribuire il modello senza scrivere codice:Automated ML helps ing deploying the model without writing code:

1. Sono disponibili un paio di opzioni per la distribuzione. 

    + Opzione 1: per distribuire il modello migliore (in base ai criteri di metrica definiti), selezionare il pulsante **Distribuisci modello migliore** nella scheda **Dettagli.**

    + Opzione 2: per distribuire un'iterazione del modello specifica da questo esperimento, eseguire il drill-down del modello per aprire la scheda **Dettagli modello** e selezionare **Distribuisci modello**.

1. Popolare il riquadro **Distribuisci modello.**

    Campo| valore
    ----|----
    Nome| Immettere un nome univoco per la distribuzione.
    Descrizione| Immettere una descrizione per identificare meglio la distribuzione a cui è destinata la distribuzione.
    Tipo di calcolo| Selezionare il tipo di endpoint da distribuire: *Servizio Azure Kubernetes (AKS)* o *Istanza contenitore di Azure (ACI)*.
    Nome del calcolo| *Si applica solo ad AKS:* Selezionare il nome del cluster AKS in cui si desidera eseguire la distribuzione.
    Abilita autenticazione | Selezionare questa opzione per consentire l'autenticazione basata su token o chiave.
    Usa asset di distribuzione personalizzati| Abilitare questa funzionalità se si desidera caricare lo script di punteggio e il file di ambiente personalizzati. [Ulteriori informazioni sugli script di assegnazione dei punteggi](how-to-deploy-and-where.md#script).

    >[!Important]
    > I nomi di file devono essere al di sotto di 32 caratteri e devono iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e alfanumerici tra. Gli spazi non sono consentiti.

    Il menu *Avanzate* offre funzionalità di distribuzione predefinite, ad esempio la raccolta dei [dati](how-to-enable-app-insights.md) e le impostazioni di utilizzo delle risorse. Se si desidera ignorare queste impostazioni predefinite, eseguire questa operazione in questo menu.

1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.

Ora hai un servizio web operativo per generare previsioni! È possibile testare le stime eseguendo una query sul servizio dal supporto di [Power BI incorporato in Azure Machine Learning.](how-to-consume-web-service.md#consume-the-service-from-power-bi)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come utilizzare un servizio Web.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
* [Comprendere i risultati dell'apprendimento automatico](how-to-understand-automated-ml.md).
* [Altre informazioni sull'apprendimento automatico](concept-automated-ml.md) e Azure Machine Learning.Learn more about automated machine learning and Azure Machine Learning.
