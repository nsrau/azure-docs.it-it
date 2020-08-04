---
title: 'Esercitazione: Previsione della domanda e AutoML'
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e la distribuzione di un modello di previsione della domanda con Machine Learning automatizzato in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 07/10/2020
ms.openlocfilehash: a244372168cb34f190bd584634bf108f2b5215a5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092288"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Esercitazione: Prevedere la domanda con Machine Learning automatizzato
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questa esercitazione si usa Machine Learning automatizzato, o ML automatizzato, in Azure Machine Learning Studio per creare un modello di previsione delle serie temporali e prevedere la domanda di noleggio per un servizio di bike sharing.

>[!IMPORTANT]
> L'esperienza di ML automatizzato in Azure Machine Learning Studio è in anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero avere funzionalità limitate.

Per un esempio di modello di classificazione, vedere [Esercitazione: Creare un modello di classificazione con ML automatizzato in Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare e caricare un set di dati.
> * Configurare ed eseguire un esperimento di ML automatizzato.
> * Specificare le impostazioni di previsione.
> * Esplorare i risultati dell'esperimento.
> * Distribuire il modello migliore.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning Enterprise Edition. Se non è disponibile, [creare un'area di lavoro Enterprise Edition](how-to-manage-workspace.md). 
    * Machine Learning automatizzato in Azure Machine Learning Studio è disponibile solo per le aree di lavoro Enterprise Edition. 
* Scaricare il file di dati [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Iniziare con Azure Machine Learning Studio

Per questa esercitazione, viene creato un esperimento di ML automatizzato eseguito in Azure Machine Learning Studio, un'interfaccia Web consolidata che include strumenti di Machine Learning per l'esecuzione di scenari di data science per esperti della materia con qualsiasi livello di competenza. Studio non è supportato nei browser Internet Explorer.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare la sottoscrizione e l'area di lavoro create.

1. Selezionare **Attività iniziali**.

1. Nel riquadro sinistro selezionare **Automated ML** (Machine Learning automatizzato) nella sezione **Creazione**.

1. Selezionare **Nuova esecuzione di ML automatizzato**. 

## <a name="create-and-load-dataset"></a>Creare e caricare il set di dati

Prima di configurare l'esperimento, caricare il file di dati nell'area di lavoro sotto forma di set di dati Azure Machine Learning. In questo modo, è possibile assicurarsi che i dati siano correttamente formattati per l'esperimento.

1. Nel modulo **Seleziona set di dati** scegliere **Da file locali** dall'elenco a discesa **+Crea set di dati**. 

    1. Nel modulo **Informazioni di base** assegnare un nome al set di dati e specificare una descrizione facoltativa. Il tipo di set di dati dovrebbe essere **Tabulare** per impostazione predefinita, perché il servizio ML automatizzato in Azure Machine Learning Studio attualmente supporta solo set di dati in questo formato.
    
    1. Selezionare **Avanti** in basso a sinistra

    1. Nel modulo **Selezione archivio dati e file** selezionare l'archivio dati predefinito che è stato configurato automaticamente durante la creazione dell'area di lavoro, **workspaceblobstore (archiviazione BLOB di Azure)** . Si tratta del percorso di archiviazione in cui verrà caricato il file di dati. 

    1. Selezionare **Sfoglia**. 
    
    1. Scegliere il file **bike-no.csv** nel computer locale. Si tratta del file scaricato come [prerequisito](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Selezionare **Avanti**

       Al termine del caricamento, il modulo Settings and preview (Impostazioni e anteprima) viene prepopolato in base al tipo di file. 
       
    1. Verificare che il modulo **Settings and preview** (Impostazioni e anteprima) venga popolato come segue, quindi selezionare **Avanti**.
        
        Campo|Descrizione| Valore per l'esercitazione
        ---|---|---
        Formato file|Definisce il layout e il tipo di dati archiviati in un file.| Delimitato
        Delimitatore|Uno o più caratteri per specificare il limite tra&nbsp; aree distinte indipendenti in testo normale o altri flussi di dati. |Virgola
        Codifica|Identifica la tabella dello schema bit-carattere da usare per leggere il set di dati.| UTF-8
        Intestazioni di colonna| Indica come verranno considerate le intestazioni del set di dati, se presenti.| Usa intestazioni del primo file
        Ignora righe | Indica quante righe vengono eventualmente ignorate nel set di dati.| nessuno

    1. Il modulo **Schema** consente di configurare ulteriormente i dati per questo esperimento. 
    
        1. Per questo esempio, scegliere di ignorare le colonne **casual** e **registered**. Queste colonne sono una scomposizione della colonna **cnt**, quindi non verranno incluse.

        1. Sempre per questo esempio, lasciare i valori predefiniti per **Proprietà** e **Tipo**. 
        
        1. Selezionare **Avanti**.

    1. Nel modulo **Confirm details** (Conferma dettagli) verificare che le informazioni corrispondano a quelle con cui erano stati precedentemente popolati i moduli **Basic info** (informazioni di base) e **Settings and preview** (Impostazioni e anteprima).

    1. Selezionare **Crea** per completare la creazione del set di dati.

    1. Selezionare il set di dati quando viene visualizzato nell'elenco.

    1. Selezionare **Avanti**.

## <a name="configure-experiment-run"></a>Configurare l'esecuzione dell'esperimento

Dopo aver caricato e configurato i dati, configurare la destinazione di calcolo remota e selezionare la colonna di dati per cui eseguire la previsione.

1. Popolare il modulo **Configura esecuzione** come segue:
    1. Immettere un nome di esperimento: `automl-bikeshare`

    1. Selezionare **cnt** come colonna di destinazione, che indica la previsione da eseguire. Questa colonna include il numero totale di noleggi di bike sharing.

    1. Selezionare **Create a new compute** (Crea nuovo calcolo) e configurare la destinazione di calcolo. ML automatizzato supporta solo destinazioni di calcolo di Azure Machine Learning. 

        Campo | Descrizione | Valore per l'esercitazione
        ----|---|---
        Nome del calcolo |Un nome univoco che identifica il contesto di calcolo.|bike-compute
        Tipo&nbsp;di macchina&nbsp;virtuale|Selezionare il tipo di macchina virtuale per il contesto di calcolo.|CPU (Central Processing Unit)
        Dimensioni&nbsp;della macchina&nbsp;virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.|Standard_DS12_V2
        Nodi min/max| Per profilare i dati, è necessario specificare almeno un nodo.|Numero minimo di nodi: 1<br>Numero massimo di nodi: 6
        Secondi di inattività prima della riduzione | Tempo di inattività prima che il cluster venga ridotto automaticamente al numero minimo di nodi.|120 (impostazione predefinita)
        Impostazioni avanzate | Impostazioni per la configurazione e l'autorizzazione di una rete virtuale per l'esperimento.| nessuno
  
        1. Selezionare **Crea** per ottenere la destinazione di calcolo. 

            **Il completamento dell'operazione richiede alcuni minuti.** 

        1. Al termine della creazione, selezionare la nuova destinazione di calcolo dall'elenco a discesa.

    1. Selezionare **Avanti**.

## <a name="select-forecast-settings"></a>Selezionare le impostazioni per la previsione

Per completare l'esperimento di ML automatizzato, specificare il tipo di attività di Machine Learning e le impostazioni di configurazione.

1. Nel modulo **Tipo di attività e impostazioni** selezionare **previsione** come tipo di attività di Machine Learning.

1. Selezionare **data** come **Colonna data/ora** e lasciare vuoto il campo **Time series identifiers** (Identificatori serie temporale). 

1. **Orizzonte di previsione** è il periodo di tempo futuro per cui eseguire la previsione.  Deselezionare Rilevamento automatico e digitare 14 nel campo. 

1. Selezionare **View additional configuration settings** (Visualizza altre impostazioni di configurazione) e popolare i campi come indicato di seguito. Queste impostazioni consentono di controllare meglio il processo di training e di specificare le impostazioni per la previsione. Altrimenti, vengono applicate le impostazioni predefinite in base alla selezione dell'esperimento e ai dati.

    Configurazioni&nbsp;aggiuntive|Descrizione|Valore&nbsp;per&nbsp;l'esercitazione
    ------|---------|---
    Primary metric (Metrica principale)| Metrica di valutazione in base a cui verrà misurato l'algoritmo di Machine Learning.|Radice normalizzata dell'errore quadratico medio
    Modello esplicativo migliore| Mostra automaticamente il modello esplicativo migliore creato da ML automatizzato.| Abilitare
    Blocked algorithms (Algoritmi bloccati) | Algoritmi da escludere dal processo di training| Extreme Random Trees
    Altre impostazioni della previsione| Queste impostazioni consentono di migliorare l'accuratezza del modello <br><br> _**Prevedere ritardi di destinazione:**_ quanto indietro nel tempo si vogliono creare i ritardi di una variabile di destinazione <br> _**Dimensioni della finestra mobile di destinazione**_: specifica le dimensioni della finestra mobile in cui verranno generate funzionalità come *max, min* e *sum*. | <br><br>Prevedere&nbsp;i ritardi&nbsp;di destinazione: nessuno <br> Dimensioni&nbsp;della finestra&nbsp;mobile&nbsp;di destinazione: nessuno
    Exit criterion (Esci da criterio)| Se viene soddisfatto un criterio, il processo di training viene arrestato. |Durata del&nbsp;processo&nbsp;di training (ore): 3 <br> Soglia&nbsp;punteggio&nbsp;metrica: nessuno
    Convalida | Scegliere un tipo di convalida incrociata e un numero di test.|Tipo di convalida:<br>Convalida incrociata &nbsp;k-fold&nbsp; <br> <br> Numero di convalide: 5
    Concorrenza| Il numero massimo di iterazioni parallele eseguite per ogni iterazione| Numero massimo di&nbsp;iterazioni&nbsp;simultanee: 6
    
    Selezionare **Salva**.

## <a name="run-experiment"></a>Eseguire esperimento

Per eseguire l'esperimento, selezionare **Fine**. Viene visualizzata la schermata **Dettagli esecuzione** con l'indicazione **Stato dell'esecuzione** nella parte superiore accanto al numero di esecuzione. Questo stato viene aggiornato man mano che l'esperimento procede.

>[!IMPORTANT]
> La preparazione dell'esecuzione dell'esperimento richiede **10-15 minuti**.
> Dopo l'avvio, **ogni iterazione richiede almeno 2-3 minuti**.  <br> <br>
> In produzione, questo processo richiede del tempo. Durante l'attesa, è consigliabile iniziare a esplorare gli algoritmi testati nella scheda **Modelli** non appena vengono completati. 

##  <a name="explore-models"></a>Esplorare i modelli

Passare alla scheda **Modelli** per visualizzare gli algoritmi (modelli) testati. Per impostazione predefinita, i modelli sono ordinati in base al punteggio della metrica man mano che vengono completati. Per questa esercitazione, il modello che riceve il punteggio più alto in base alla metrica **Radice normalizzata dell'errore quadratico medio** scelta si trova all'inizio dell'elenco.

Mentre si aspetta il completamento di tutti i modelli dell'esperimento, selezionare il **nome di algoritmo** di un modello completato per esplorare i dettagli delle relative prestazioni. 

Nell'esempio seguente vengono esaminate le schede **Dettagli** e **Metriche** per visualizzare le proprietà, le metriche e i grafici delle prestazioni del modello selezionato. 

![Dettagli esecuzione](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Distribuire il modello

Machine Learning automatizzato in Azure Machine Learning Studio consente di distribuire il modello migliore come servizio Web con pochi passaggi. La distribuzione è l'integrazione del modello per consentire la stima su nuovi dati e identificare le potenziali aree di opportunità. 

Per questo esperimento, la distribuzione in un servizio Web implica che l'azienda di bike sharing ha ora una soluzione Web iterativa e scalabile per prevedere la domanda di noleggio. 

Una volta completata l'esecuzione, tornare nella pagina di esecuzione padre selezionando **Esecuzione 1** nella parte superiore della schermata.

Nella sezione **Riepilogo del modello migliore**, nel contesto di questo esperimento, viene considerato come migliore il modello **StackEnsemble**, in base alla metrica **Radice normalizzata dell'errore quadratico medio**.  

Viene distribuito questo modello, ma tenere presente che il completamento della distribuzione richiede circa 20 minuti. Il processo di distribuzione comporta diversi passaggi, tra cui la registrazione del modello, la generazione delle risorse e la relativa configurazione per il servizio Web.

1. Selezionare **StackEnsemble** per aprire la pagina specifica del modello.

1. Selezionare il pulsante **Distribuisci** nell'area in alto a sinistra della schermata.

1. Immettere i dati nel riquadro **Deploy a model** (Distribuisci un modello) in questo modo:

    Campo| valore
    ----|----
    Nome distribuzione| bikeshare-deploy
    Descrizione della distribuzione| distribuzione della domanda di bike sharing
    Tipo di calcolo | Selezionare un'istanza di calcolo di Azure
    Abilita autenticazione| Disabilitare. 
    Usa asset di distribuzione personalizzati| Disabilitare. La disabilitazione consente di generare automaticamente il file di driver predefinito (script di assegnazione dei punteggi) e il file dell'ambiente. 
    
    Per questo esempio, vengono usate le impostazioni predefinite disponibili nel menu *Avanzate*. 

1. Selezionare **Distribuisci**.  

    Nella parte superiore della schermata **Esecuzione** viene visualizzato un messaggio verde che indica che la distribuzione è stata avviata correttamente. Lo stato di avanzamento della distribuzione è disponibile nel riquadro **Riepilogo modelli** in **Stato distribuzione**.
    
Una colta completata la distribuzione, è disponibile un servizio Web operativo per generare previsioni. 

Vedere [**Passaggi successivi**](#next-steps) per altre informazioni su come usare il nuovo servizio Web e testare le previsioni tramite il supporto di Azure Machine Learning incorporato in Power BI.

## <a name="clean-up-resources"></a>Pulire le risorse

I file di distribuzione sono più grandi dei file di dati e di esperimento e di conseguenza più costosi da archiviare. Eliminare solo i file di distribuzione per ridurre al minimo i costi per l'account o se si vuole conservare i file dell'area di lavoro e degli esperimenti. In caso contrario, eliminare l'intero gruppo di risorse, se non si prevede di usare alcun file.  

### <a name="delete-the-deployment-instance"></a>Eliminare l'istanza di distribuzione

Eliminare solo l'istanza di distribuzione da Azure Machine Learning Studio se si intende mantenere il gruppo di risorse e l'area di lavoro per altre esercitazioni e attività di esplorazione. 

1. Passare ad [Azure Machine Learning Studio](https://ml.azure.com/). Passare all'area di lavoro e nel riquadro di sinistra **Risorse** selezionare **Endpoint**. 

1. Selezionare la distribuzione che si vuole eliminare e scegliere **Elimina**. 

1. Selezionare **Continua**.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il servizio Machine Learning automatizzato in Azure Machine Learning Studio per creare un modello di previsione delle serie temporali e prevedere la domanda di noleggio per un servizio di bike sharing. 

Per la procedura di creazione di uno schema supportato da Power BI che facilita l'utilizzo del servizio Web appena distribuito, vedere questo articolo:

> [!div class="nextstepaction"]
> [Utilizzare un servizio Web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ [Funzionalità automatizzate di Machine Learning](concept-automated-ml.md).
+ Per altre informazioni sulle metriche e sui grafici di classificazione, vedere l'articolo [Informazioni sui risultati di Machine Learning automatizzato](how-to-understand-automated-ml.md#classification).
+ Altre informazioni sullo [sviluppo di funzionalità](how-to-configure-auto-features.md#featurization).
+ Altre informazioni sulla [profilatura dei dati](how-to-use-automated-ml-for-ml-models.md#profile).

>[!NOTE]
> Il set di dati bike share è stato modificato per questa esercitazione. Questo set di dati è stato reso disponibile nell'ambito di un [concorso Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) e originariamente era reperibile tramite [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). È disponibile anche all'interno del [database Machine Learning UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Origine: Fanaee-T, Hadi, and Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.
