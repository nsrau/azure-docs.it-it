---
title: Introduzione alla preparazione dati per Azure Machine Learning | Microsoft Docs
description: "Questa è la guida introduttiva della sezione relativa alla preparazione dati del Workbench di Azure Machine Learning"
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: faf6e557914a33454229b5830419b9ef6f200a8c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---


# <a name="getting-started-with-data-preparation"></a>Introduzione alla preparazione dati

Benvenuti alla guida introduttiva sulla preparazione dati. 

La preparazione dati offre un set di strumenti per esplorare, comprendere e risolvere i problemi dei dati in modo efficiente. Consente di usare i dati in molte forme e di trasformarli in dati puliti più appropriati all'uso in downstream.

La preparazione dati viene installata come parte dell'esperienza di Azure Machine Learning Workbench.  Usare la preparazione dati in locale o distribuirla in un cluster di destinazione e in un cloud come ambiente di runtime o di esecuzione.

Il runtime della fase di progettazione usa Python per l'estensibilità e dipende da diverse librerie di Python, ad esempio Pandas. Così come per altri componenti di Azure Machine Learning Workbench, non è necessario installare Python perché viene installato automaticamente. Tuttavia se è necessario installare librerie aggiuntive, queste devono essere installate nella directory di Python di Azure Machine Learning Workbench, non nella normale directory di Python. Altre informazioni su come installare i pacchetti sono disponibili [qui](data-prep-python-extensibility-overview.md).

Prima di usare la preparazione dati è necessario un progetto; dopo averlo creato è possibile preparare i dati. 

Passare alla sezione Dati del progetto selezionando l'icona Dati ![icona dell'origine dati](media/data-prep-getting-started/data-source-icon.png) nella parte sinistra della schermata.  Fare clic di nuovo su "+" per **aggiungere un'origine dati**. Si avvia la creazione guidata dell'origine dati che aggiunge un file **Origine dati** (.dsource) al progetto dopo aver completato la procedura guidata. Per impostazione predefinita, la visualizzazione dei dati è una griglia. Sulla griglia è anche possibile selezionare la visualizzazione Metrica. Nella visualizzazione Metrica vengono visualizzate le statistiche di riepilogo.  Dopo aver esaminato le statistiche di riepilogo, fare clic su **Prepara** nella parte superiore dello schermo. [Altre informazioni sulla creazione guidata dell'origine dati](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Blocchi predefiniti di preparazione dati ##
### <a name="the-package"></a>Pacchetto ###

Il pacchetto è il contenitore primario del proprio lavoro, è l'elemento che viene salvato e caricato dal disco. Quando lo si usa all'interno del client, il pacchetto viene sempre salvato automaticamente in background. 

L'output o i risultati di un pacchetto possono essere esaminati in Python o tramite un blocco appunti Jupyter.

Un pacchetto può essere eseguito in più runtime tra cui Python, Spark che include Docker, e HDInsight locali.

Un pacchetto contiene uno o più flussi di dati che rappresentano i passaggi e le trasformazioni applicati ai dati.

Un pacchetto può usare un altro pacchetto come origine dati, definito come un flusso di dati di riferimento.

### <a name="the-dataflow"></a>Flusso di dati ###
Un flusso di dati dispone di un'origine e di trasformazioni facoltative che vengono disposte attraverso una serie di passaggi e destinazioni facoltative. Facendo clic su un passaggio si eseguono nuovamente tutte le origini e le trasformazioni prima del passaggio selezionato e includendo lo stesso passaggio.  I dati trasformati tramite questo passaggio vengono visualizzati all'interno della griglia. È possibile aggiungere, spostare ed eliminare passaggi all'interno di un flusso di dati tramite l'elenco dei passaggi.

L'elenco dei passaggi sul lato destro del client può essere aperto e chiuso per avere più spazio sullo schermo.

Possono esistere più flussi di dati per volta nell'interfaccia utente in cui ogni flusso di dati è rappresentato come una scheda.

### <a name="the-source"></a>Origine
Un'origine rappresenta il luogo di provenienza e il formato dei dati. Un pacchetto di preparazione dati preleva sempre i dati da un altro flusso di dati, ovvero da un'altra origine dati. È il flusso di dati di riferimento che contiene le informazioni. Ogni origine ha un'esperienza utente diversa per consentirne la configurazione. L'origine produce una visualizzazione tabulare o "rettangolare" dei dati. Se i dati di origine inizialmente "non sono allineati a destra", la struttura viene normalizzata in modo da essere "rettangolare". [L'appendice 2 contiene l'elenco corrente delle origini supportate](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Trasformazione ###
Le trasformazioni usano i dati in un formato specifico, eseguono alcune operazioni sui dati, ad esempio modificano il tipo di dati, e quindi producono i dati nel nuovo formato. Ogni trasformazione ha la propria interfaccia utente e i propri comportamenti. Il concatenamento di più trasformazioni tramite i passaggi nel flusso di dati è alla base della funzionalità di preparazione dati. [L'appendice 3 contiene l'elenco corrente delle trasformazioni supportate](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Controllo ###

I controlli sono visualizzazioni dei dati e sono disponibili per ottimizzare la comprensione dei dati.  Comprendere i dati e i relativi problemi di qualità consente di decidere quali operazioni, ovvero trasformazioni, eseguire. Alcuni controlli supportano le azioni che generano le trasformazioni. Ad esempio, il controllo Conteggio valori consente di selezionare un valore e di applicare un filtro per includere o escludere tale valore. I controlli possono anche indicare il contesto delle trasformazioni. Se si selezionano una o più colonne, ad esempio, cambiano le trasformazioni possibili che possono essere applicate.

Una colonna può contenere più controlli in qualsiasi punto del tempo, ad esempio le statistiche di colonna e un istogramma. Le istanze di un controllo possono anche essere presenti in più colonne. Ad esempio, tutte le colonne numeriche possono avere gli istogrammi nello stesso momento.

I controlli vengono visualizzati anche nell'area di profilatura nella parte inferiore della schermata.  Ingrandire i controlli per visualizzarli ingranditi all'interno dell'area del contenuto principale. Considerare la griglia dei dati come il controllo predefinito. È possibile espandere qualsiasi controllo nell'area del contenuto principale. I controlli all'interno dell'area del contenuto principale vengono ridotti al minimo nell'area di profilatura. Personalizzare un controllo facendo clic sull'icona della matita all'interno del controllo. Riordinare i controlli nell'area di profilatura usando il trascinamento della selezione.

Alcuni controlli supportano la modalità "Halo". Questa modalità mostra il valore o lo stato prima dell'applicazione dell'ultima trasformazione. Il valore precedente viene visualizzato in grigio, mentre il valore corrente è in primo piano e mostra l'impatto di una trasformazione. [L'appendice 4 contiene l'elenco corrente dei controlli supportati](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Destinazione
 Una destinazione è la posizione in cui si scrivono/esportano i dati dopo averli preparati in un flusso di dati. Un determinato flusso di dati può avere più destinazioni. Ogni destinazione ha un'esperienza utente diversa per consentirne la configurazione. La destinazione usa i dati in un formato tabulare o "rettangolare" e li scrive in una posizione che abbia un formato specifico. [L'appendice 5 contiene l'elenco corrente delle destinazioni supportate](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Uso della preparazione dati ###
La preparazione dati presuppone una metodologia o un approccio di base composto da cinque passaggi.

#### <a name="step-1-ingestion"></a>Passaggio 1: Inserimento ####
Importare i dati per la preparazione dati usando l'opzione **Aggiungi origine dati** all'interno della visualizzazione del progetto.  L'inserimento iniziale dei dati viene gestito tramite la creazione guidata dell'origine dati.

#### <a name="step-2-understandprofile-the-data"></a>Passaggio 2: Comprendere o profilare i dati ####

Innanzitutto, osservare la barra relativa al Data Quality nella parte superiore di ogni colonna. Il verde rappresenta le righe contenenti valori. Il grigio rappresenta le righe con un valore mancante, null e così via. Il rosso indica i valori di errore. Passare il mouse sulla barra per ottenere una descrizione comando con il numero esatto di righe in ognuno dei tre bucket. La barra relativa al Data Quality usa una scala logaritmica, pertanto è consigliabile controllare sempre il numero effettivo per avere un'idea approssimativa del volume dei dati mancanti.

![columns](media/data-prep-getting-started/columns.png)

Successivamente, usare una combinazione di altri controlli e la griglia per meglio comprendere le caratteristiche dei dati.  Avviare la formulazione di ipotesi sulla preparazione dei dati necessari per un'altra analisi. La maggior parte dei controlli usa una sola colonna o un numero limitato di colonne.  

È probabile che siano necessari più controlli su diverse colonne per comprendere i dati. È possibile scorrere i vari controlli nell'area di profilatura. All'interno di quest'area, è anche possibile spostare i controlli nella parte più alta dell'elenco per visualizzarli nell'area visualizzabile subito.

![controlli](media/data-prep-getting-started/inspectors.PNG)

Per variabili o colonne di categoria e continui vengono offerti diversi controlli. Il menu di controllo abilita e disabilita le opzioni a seconda del tipo di variabili o di colonne cui si dispone.

Quando si usano set di dati di grandi dimensioni con molte colonne, è consigliabile usare un approccio pragmatico all'uso dei subset. Questo approccio prevede la concentrazione dell'attenzione su un piccolo numero di colonne, ad esempio 5-10, la loro preparazione e quindi l'uso delle restanti colonne. Il controllo della griglia supporta il partizionamento verticale delle colonne e, se sono presenti più di 300 colonne, è necessario "sfogliarle".
 

#### <a name="step-3-transform-the-data"></a>Passaggio 3: Trasformare i dati ####
Le trasformazioni modificano i dati e consentono l'esecuzione dei dati per supportare l'ipotesi di lavoro corrente. Le trasformazioni vengono visualizzate come passaggi nell'elenco dei passaggi sul lato destro. È possibile "spostarsi cronologicamente" nell'elenco dei passaggi facendo clic su un punto arbitrario nell'elenco dei passaggi.

Un'icona verde a sinistra di un passaggio specifico indica che questo è stato eseguito e che i dati riflettono l'esecuzione della trasformazione. Una barra verticale a sinistra del passaggio indica lo stato corrente dei dati nei controlli.

![steps](media/data-prep-getting-started/steps.PNG)

Provare ad apportare piccole modifiche frequenti ai dati e a convalidarli (passaggio 4) dopo ogni modifica man mano che l'ipotesi si evolve.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Passaggio 4: Verificare l'impatto della trasformazione. 
Decidere se l'ipotesi è corretta. Se è corretta, sviluppare l'ipotesi successiva e ripetere i passaggi 2 e 3 per quella nuova. Se non lo è, annullare l'ultima trasformazione e sviluppare una nuova ipotesi e ripetere i passaggi 2 e 3.

La modalità principale per determinare se la trasformazione ha il giusto impatto consiste nell'usare i controlli. Usare i controlli esistenti. Usare i controlli con l'effetto Halo abilitato o avviare più controlli per visualizzare i dati in determinati punti nel tempo.

![controllo Halo](media/data-prep-getting-started/halo1.PNG) ![controllo Halo](media/data-prep-getting-started/halo2.PNG)

Per annullare una trasformazione, passare all'elenco di passaggi a destra dell'interfaccia utente. È possibile che sia necessario richiamare il pannello dell'elenco dei passaggi. Per aprirlo, fare clic sulla doppia freccia di espansione che punta a sinistra. Nel pannello selezionare la trasformazione che è stata eseguita e che si desidera annullare. Selezionare l'elenco a discesa a destra del blocco dell'interfaccia utente. Selezionare **Modifica** per apportare modifiche o **Elimina** per rimuovere la trasformazione dall'elenco dei passaggi e dal flusso di dati.

#### <a name="step-5-output"></a>Passaggio 5: Output 
Al termine della preparazione dati, è possibile scrivere il flusso di dati in un output. Un flusso di dati può avere più output. Dal menu Trasformazioni è possibile selezionare l'output in cui si desidera scrivere il set di dati. È anche possibile selezionare la destinazione dell'output. 

## <a name="list-of-appendices"></a>Elenco delle appendici 
[Appendix 2 - Origini dati supportate](data-prep-appendix2-supported-data-sources.md)  
[Appendix 3 - Trasformazioni supportate](data-prep-appendix3-supported-transforms.md)  
[Appendice 4 - Controlli supportati](data-prep-appendix4-supported-inspectors.md)  
[Appendice 5 - Destinazioni supportate](data-prep-appendix5-supported-destinations.md)  
[Appendice 6 - Esempio di espressioni filtro in Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Appendice 7 - Esempio di trasformazione di espressioni del flusso di dati in Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Appendice 8 - Esempio di origini dati in Python](data-prep-appendix8-sample-source-connections-python.md)  
[Appendice 9 - Esempio di connessioni di destinazione in Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Appendice 10 - Esempio di trasformazioni di colonna in Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Vedere anche

[Esercitazione sulla preparazione avanzata dei dati](tutorial-bikeshare-dataprep.md)
