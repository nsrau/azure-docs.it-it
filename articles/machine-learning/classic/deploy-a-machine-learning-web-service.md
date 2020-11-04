---
title: 'ML Studio (classico): distribuire un servizio Web-Azure'
description: Come convertire un esperimento di training in un esperimento predittivo, prepararlo per la distribuzione e quindi distribuirlo come servizio Web Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 5a588195f2095b2d0cb261e1573eeb9ec881f2fd
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322839"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Distribuire un servizio Web di Azure Machine Learning Studio (classico)

**SI APPLICA A:**  ![sì](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica)   ![no ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (classico) consente di compilare e testare una soluzione analitica predittiva. È quindi possibile distribuire la soluzione come servizio Web.

I servizi Web di Machine Learning Studio (classico) forniscono un'interfaccia tra un'applicazione e un modello di valutazione del flusso di lavoro Machine Learning Studio (classico). Un'applicazione esterna può comunicare con un modello di punteggio del flusso di lavoro Machine Learning Studio (classico) in tempo reale. Una chiamata a un servizio Web di Machine Learning Studio (classico) restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Un servizio Web di Machine Learning Studio (classico) è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning Studio (versione classica) dispone di due tipi di servizi Web:

* Servizio Request-Response (RRS): servizio A bassa latenza e scalabilità elevata che assegna un punteggio a un singolo record di dati.
* Servizio esecuzione batch (BES): un servizio asincrono che assegna un punteggio a un batch di record di dati.

L'input per BES è simile all'input di dati usato da RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio un archivio BLOB di Azure, un archivio tabelle di Azure, un database SQL di Azure, HDInsight (query Hive) e origini HTTP.

In generale, la distribuzione del modello avviene in tre passaggi:

* **[Creare un esperimento di training]** : in studio (classico), è possibile eseguire il training e il test di un modello di analisi predittiva usando i dati di training forniti, usando un ampio set di algoritmi di Machine Learning predefiniti.
* **[Eseguire la conversione in un esperimento predittivo]** - Dopo avere eseguito il training del modello con i dati esistenti, preparare e ottimizzare l'esperimento per la stima quando si è pronti a usarlo per l'assegnazione dei punteggi ai nuovi dati.
* **Eseguire la distribuzione** come **[nuovo servizio Web]** o come **[servizio Web classico]** : quando si distribuisce l'esperimento predittivo come servizio Web di Azure, gli utenti possono inviare dati al modello e ricevere le stime del modello.

## <a name="create-a-training-experiment"></a>Creare un esperimento di training

Per eseguire il training di un modello di analisi predittiva, è possibile usare Azure Machine Learning Studio (classico) per creare un esperimento di training in cui si includono diversi moduli per caricare i dati di training, preparare i dati in modo necessario, applicare algoritmi di machine learning e valutare i risultati. È possibile eseguire l'iterazione dell'esperimento e provare diversi algoritmi di apprendimento automatico per confrontare e valutare i risultati.

Il processo di creazione e gestione degli esperimenti di training è trattato in modo più approfondito altrove. Per altre informazioni, vedere questi articoli:

* [Creare un esperimento semplice in Azure Machine Learning Studio (classico)](create-experiment.md)
* [Sviluppare una soluzione predittiva con Azure Machine Learning Studio (versione classica)](tutorial-part1-credit-risk.md)
* [Importare i dati di training in Azure Machine Learning Studio (versione classica)](import-data.md)
* [Gestire le iterazioni dell'esperimento in Azure Machine Learning Studio (versione classica)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo

Dopo avere eseguito il training del modello, si è pronti per convertire l'esperimento di training in un esperimento predittivo per assegnare i punteggi ai nuovi dati.

Effettuando la conversione in un esperimento predittivo, si prepara il modello sottoposto a training per la distribuzione come servizio Web di classificazione. Gli utenti del servizio Web possono inviare i dati di input al modello, che restituirà i risultati della previsione. Quando si esegue la conversione in un esperimento predittivo, tenere presente il modo in cui si prevede che il modello verrà usato da altri utenti.

Il processo di conversione di un esperimento di training in un esperimento predittivo prevede tre passaggi:

1. Sostituire i moduli di algoritmi di Machine Learning con il modello di training.
2. Organizzare l'esperimento solo per i moduli necessari alla valutazione. Un esperimento di training include alcuni moduli che sono necessari per il training, ma che non lo saranno più al termine del training.
3. Definire in che modo il modello accetterà i dati dall'utente del servizio Web e quali dati restituirà.

> [!TIP]
> Durante l'esperimento di training, è stato eseguito il training ed è stato assegnato un punteggio al modello utilizzando i propri dati. In seguito alla distribuzione, tuttavia, gli utenti invieranno nuovi dati al modello che, a sua volta, restituirà i risultati della previsione. Quando dunque si converte l'esperimento di training in un esperimento predittivo per prepararlo per la distribuzione, è importante considerare in che modo il modello verrà utilizzato da altri utenti.

![Eseguire la conversione a un esperimento di assegnazione dei punteggi](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Pulsante Set Up Web Service
Dopo aver eseguito l'esperimento (fare clic su **RUN** (ESEGUI) nella parte inferiore dell'area di disegno dell'esperimento), fare clic sul pulsante **Set Up Web Service** (Configura servizio Web) scegliendo l'opzione **Predictive Web Service** (Servizio Web predittivo). **Set Up Web Service** (Configura servizio Web) esegue automaticamente i tre passaggi necessari per convertire l'esperimento di training in un esperimento predittivo:

1. Salva il modello di training nella sezione dei **modelli di training** della tavolozza del modulo (a sinistra dell'area di disegno dell'esperimento). Sostituisce quindi l'algoritmo di Machine Learning e i moduli [Train Model][train-model] (Modello di training) con il modello di training salvato.
2. Analizza l'esperimento e rimuove i moduli chiaramente usati solo per il training e non più necessari.
3. Inserisce moduli di _input_ e di _output del servizio Web_ in posizioni predefinite nell'esperimento (questi moduli accettano e restituiscono i dati utente).

Ad esempio, il seguente esperimento esegue il training di un modello di albero delle decisioni incrementato a due classi utilizzando dati di classificazione di esempio:

![esperimento di training](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

I moduli nell'esperimento eseguono fondamentalmente quattro diverse funzioni:

![Funzioni del modulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando si converte l'esperimento di training in un esperimento predittivo, alcuni di questi moduli non sono più necessari o hanno a questo punto uno scopo diverso:

* **Data** (Dati): i dati in questo set di dati di esempio non vengono usati durante l'assegnazione di un punteggio. L'utente del servizio Web fornisce i dati da classificare. Tuttavia, i metadati di questo set di dati, ad esempio i tipi di dati, vengono usati dal modello sottoposto a training. È quindi necessario mantenere il set di dati nell'esperimento predittivo perché possa fornire questi metadati.

* **Prep** : in base ai dati utente che verranno inviati per la classificazione, i moduli potrebbero non essere necessari per elaborare i dati in ingresso. Il pulsante **Set Up Web Service** (Configura servizio Web) non agisce su questi moduli, pertanto è necessario decidere come gestirli.
  
    In questo esempio alcuni valori nel set di dati di esempio potrebbero essere mancanti, pertanto è stato aggiunto un modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) per gestirli. Il set di dati di esempio include, inoltre, colonne che non sono necessarie per il training del modello. È stato pertanto incluso un modulo [Select columns in Dataset][select-columns] (Seleziona colonne nel set di dati) per escludere le colonne aggiuntive dal flusso di dati. Se si è certi che i dati inviati per la classificazione tramite il servizio Web non presentino valori mancanti, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data]. Poiché tuttavia il modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati) consente di definire le colonne di dati previste dal modello di training, è necessario mantenerlo.

* **Train** (Training): questi moduli vengono usati per eseguire il training del modello. Quando si fa clic su **Set Up Web Service** (Configura servizio Web), questi moduli vengono sostituiti con un singolo modulo contenente il modello sottoposto a training. Il nuovo modulo viene salvato nella sezione **Trained Models** (Modelli sottoposti a training) della tavolozza dei moduli.

* **Score** (Punteggio): in questo esempio il modulo [Split Data][split] (Dividi dati) viene usato per suddividere il flusso di dati in dati di test e dati di training. Poiché nell'esperimento predittivo non si esegue più il training, è possibile rimuovere [Split Data][split] (Dividi dati). In modo analogo, il secondo modulo [Score Model][score-model] (Assegna punteggio al modello) e il modulo [Evaluate Model][evaluate-model] (Valuta modello) vengono usati per confrontare i risultati dei dati di test, pertanto questi moduli non sono necessari nell'esperimento predittivo. Il modulo rimanente [Score Model][score-model] è però necessario per restituire un risultato relativo all'assegnazione del punteggio tramite il servizio Web.

Ecco come appare l'esempio dopo aver fatto clic su **Set Up Web Service** :

![Esperimento predittivo convertito](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Le operazioni eseguite da **Set Up Web Service** (Configura sevizio Web) potrebbero essere sufficienti per preparare l'esperimento per la distribuzione come servizio Web. Tuttavia, potrebbe essere necessario eseguire ulteriori operazioni specifiche per l'esperimento.

#### <a name="adjust-input-and-output-modules"></a>Regolazione dei moduli di input e output
Nell'esperimento di training è stato utilizzato un set di dati di training e quindi sono state eseguite delle operazioni di elaborazione per ottenere i dati in un formato utile all'algoritmo Machine Learning necessario. Se i dati che si prevede di ricevere tramite il servizio Web non richiedono questo tipo di elaborazione, è possibile ignorarli. A questo scopo, connettere l'output del modulo **Web service input** (Input servizio Web) a un altro modulo nell'esperimento. I dati dell'utente verranno inviati in questa posizione nel modello.

Per impostazione predefinita, ad esempio, **Set Up Web Service** (Configura servizio Web) inserisce il modulo **Web service input** (Input del servizio Web) all'inizio del flusso di dati, come illustrato nella figura precedente. È tuttavia possibile posizionare manualmente **Web service input** (Input servizio Web) dopo i moduli di elaborazione dei dati:

![Spostamento del modulo web service input](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

I dati di input forniti tramite il servizio Web ora passano direttamente al modulo Score Model senza pre-elaborazione.

In modo analogo, per impostazione predefinita, **Set Up Web Service** inserisce il modulo di output del servizio Web in fondo al flusso di dati. In questo esempio il servizio Web restituisce all'utente l'output del modulo [Score Model][score-model] (Assegna punteggio al modello) che include il vettore dei dati di input completo, oltre ai risultati dell'assegnazione del punteggio.
Se però si preferisce restituire un valore diverso, è possibile aggiungere moduli aggiuntivi prima del modulo **Web service output** (Output servizio Web). 

Per restituire, ad esempio, solo i risultati dell'assegnazione del punteggio e non l'intero vettore dei dati di input, aggiungere un modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati) per escludere tutte le colonne ad eccezione dei risultati dell'assegnazione del punteggio. Spostare quindi il modulo **Web service output** (Output servizio Web) nell'output del modulo [Select Columns in Dataset][select-columns] (Seleziona colonne nel set di dati). L'esperimento è simile al seguente:

![Spostamento del modulo web service output](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Aggiungere o rimuovere i moduli di elaborazione dati aggiuntivi
Se presenti nell'esperimento, è possibile rimuove i moduli di cui si è certi che non saranno utili per la classificazione. Ad esempio, poiché il modulo **Web service input** (Input servizio Web) è stato spostato in un punto successivo ai moduli di elaborazione dei dati, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) dall'esperimento predittivo.

L'esperimento predittivo ora appare come illustrato di seguito:

![Rimozione del modulo aggiuntivo](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Aggiungere parametri facoltativi al servizio Web
In alcuni casi, è possibile consentire all'utente del servizio Web di modificare il comportamento dei moduli quando si accede al servizio. *I parametri del servizio Web* consentono di eseguire questa operazione.

Un esempio comune è la configurazione di un modulo [Import Data][import-data] (Importa dati) per consentire all'utente del servizio Web distribuito di specificare un'origine dati diversa quando si accede al servizio Web oppure la configurazione di un modulo [Export Data][export-data] (Esporta dati) in modo che sia possibile specificare una destinazione differente.

È possibile definire i parametri del servizio Web e associarli a uno o più parametri di modulo e specificare se sono obbligatori o facoltativi. Effettuando l'accesso al servizio e modificando adeguatamente le azioni del modulo, l'utente del servizio Web fornisce valori per tali parametri.

Per ulteriori informazioni sui parametri del servizio Web e su come usarli, vedere [Usare i parametri del servizio Web di Azure Machine Learning][webserviceparameters].

Nei passaggi seguenti è descritta la distribuzione di un esperimento predittivo come nuovo servizio Web. È anche possibile distribuire l'esperimento come servizio Web classico.

## <a name="deploy-it-as-a-new-web-service"></a>Eseguire la distribuzione come nuovo servizio Web

Ora che l'esperimento predittivo è stato preparato, è possibile distribuirlo come nuovo servizio Web di Azure (basato su Resource Manager). Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Esegui** nella parte inferiore dell'area di disegno dell'esperimento. Al termine dell'esecuzione dell'esperimento, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service New** (Distribuisci servizio Web [Nuovo]).  Verrà visualizzata la pagina distribuzione del portale del servizio Web Machine Learning Studio (classico).

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [gestire un servizio Web usando il portale dei servizi web Azure Machine Learning](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Pagina dell'esperimento di distribuzione del portale servizi Web

Nella pagina Deploy Experiment (Sperimentazione distribuzione) immettere un nome per il servizio Web.
Selezionare un piano tariffario. Se è disponibile un piano tariffario, è possibile selezionarlo; in caso contrario è necessario creare un nuovo piano tariffario per il servizio.

1. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano esistente o l'opzione **Select new plan** (Seleziona nuovo piano).
2. In **Plan Name** (Nome piano) digitare un nome che identifica il piano di fatturazione.
3. Selezionare uno dei **livelli del piano mensile**. Per impostazione predefinita vengono usati i livelli di piano per l'area predefinita e il servizio Web viene distribuito in questa area.

Fare clic su **Distribuisci** e verrà visualizzata la pagina **avvio rapido** per il servizio Web.

La pagina Avvio rapido del servizio Web offre indicazioni e accesso alle attività più comuni che verranno eseguite dopo la creazione di un servizio Web. Da qui è possibile accedere facilmente alle pagine relative a test e utilizzo.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testare il nuovo servizio Web

Per testare il nuovo servizio Web, fare clic su **Test web service** (Test servizio Web) nelle attività comuni. Nella pagina di test è possibile testare il servizio Web come servizio Richiesta-risposta (RRS) o servizio Esecuzione batch (BES).

La pagina di test del servizio di richiesta-risposta (RRS) visualizza gli input, gli output ed eventuali parametri globali definiti per l'esperimento. Per testare il servizio Web, è possibile immettere manualmente i valori appropriati per gli input o fornire un file formattato con valori delimitati da virgole (CSV) contenente i valori di test.

Per eseguire il test usando il servizio di richiesta-risposta (RRS), nella modalità di visualizzazione elenco immettere i valori appropriati per gli input e fare clic su **Test Request-Response** (Test Richiesta-risposta). I risultati della stima verranno visualizzati nella colonna di output a sinistra.

![Immettere i valori appropriati per testare il servizio Web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Per testare il servizio BES, fare clic su **Batch**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato usando Machine Learning Studio (versione classica), è possibile scaricare il set di dati per l'esperimento predittivo e usarlo.

Per scaricare il set di dati, aprire Machine Learning Studio (classico). Aprire l'esperimento predittivo e fare clic con il pulsante destro del mouse sull'input per l'esperimento. Dal menu di scelta rapida scegliere **dataset** (set di dati) e quindi selezionare **Download** (Scarica).

![Scaricare il set di dati dall'area di disegno di studio (classico)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Fare clic su **Test**. Lo stato del processo di esecuzione del batch verrà visualizzato a destra sotto **Test Batch Jobs** (Test Processi batch).

![Testare il processo di esecuzione batch con il portale dei servizi Web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Nella pagina **configurazione** è possibile modificare la descrizione, il titolo, aggiornare la chiave dell'account di archiviazione e abilitare i dati di esempio per il servizio Web.

![Configurare il servizio Web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Accedere al nuovo servizio Web

Dopo aver distribuito il servizio Web da Machine Learning Studio (classico), è possibile inviare dati al servizio e ricevere risposte a livello di codice.

Nella pagina **consume** sono disponibili tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, viene fornita la chiave API per consentire l'accesso autorizzato al servizio.

Per ulteriori informazioni sull'accesso a un servizio Web Machine Learning Studio (classico), vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gestire il nuovo servizio Web

È possibile gestire i nuovi servizi Web usando il portale dei servizi Web Machine Learning Studio (classico). Dalla [pagina principale del portale](https://services.azureml.net/)fare clic su **servizi Web**. Nella pagina dei servizi Web è possibile eliminare o copiare un servizio. Per monitorare un servizio specifico, fare clic sul servizio e quindi su **Dashboard**. Per monitorare i processi batch associati al servizio Web, fare clic su **Batch Request Log** (Log richieste batch).

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Distribuire il nuovo servizio Web in più aree

È possibile distribuire facilmente un nuovo servizio Web in più aree, senza la necessità di più sottoscrizioni o aree di lavoro.

I prezzi sono specifici per ogni area, quindi è necessario definire un piano di fatturazione per ogni area in cui verrà distribuito il servizio Web.

#### <a name="create-a-plan-in-another-region"></a>Creare un piano in un'altra area

1. Accedere a [Servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Fare clic sull'opzione del menu **Plans** (Piani).
3. Nella pagina della panoramica Plans (Piani), fare clic su **New** (Nuovo).
4. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo piano.
5. Nell'elenco a discesa **Regione** (Area) selezionare un'area per il nuovo piano. Le opzioni del piano per l'area selezionata verranno visualizzate nella sezione **Plan Options** (Opzioni del piano) della pagina.
6. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il piano. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. In **Plan Name** (Nome piano) digitare il nome del piano.
8. In **Plan Options** (Opzioni piano) selezionare il livello di fatturazione per il nuovo piano.
9. Fare clic su **Crea**.

#### <a name="deploy-the-web-service-to-another-region"></a>Eseguire la distribuzione del servizio Web in un'altra area

1. Nella pagina de servizi Web di Microsoft Azure Machine Learning fare clic sull'opzione di menu **Servizi Web**.
2. Selezionare il servizio Web da distribuire in una nuova area.
3. Fare clic su **Copy**
4. In **Web Service Name** (Nome servizio Web) digitare un nuovo nome per il servizio Web.
5. In **Web service description** (Descrizione servizio Web) immettere una descrizione per il servizio Web.
6. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo servizio Web.
7. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il servizio Web. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. Nell'elenco a discesa **Region** (Area) selezionare l'area in cui si desidera distribuire il servizio Web.
9. Nell'elenco a discesa **Storage account** (Account di archiviazione) selezionare un account di archiviazione in cui archiviare il servizio Web.
10. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano nell'area scelta nel passaggio 8.
11. Fare clic su **Copy**

## <a name="deploy-it-as-a-classic-web-service"></a>Eseguire la distribuzione come servizio Web classico

Ora che l'esperimento predittivo è stato sufficientemente preparato, è possibile distribuirlo come servizio Web di Azure classico. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per distribuire l'esperimento predittivo, fare clic su **Run** (Esegui) nella parte inferiore dell'area di disegno dell'esperimento e quindi fare clic su **Deploy Web Service** (Distribuisci servizio Web). Il servizio Web viene configurato e viene visualizzato il dashboard del servizio Web.

![Distribuire il servizio Web da studio (classico)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testare il servizio Web classico

È possibile testare il servizio Web nel portale dei servizi Web Machine Learning Studio (classico) o Machine Learning Studio (classico).

Per testare il servizio Web Richiesta-risposta, fare clic sul collegamento **Test** nel dashboard del servizio Web. Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Si tratta delle colonne necessarie per l'esperimento di classificazione. Immettere un set di dati e quindi fare clic su **OK**. Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard.

È possibile fare clic sul collegamento anteprima **test** per testare il servizio nel portale dei servizi Web Azure Machine Learning Studio (classico), come illustrato in precedenza nella sezione relativa al nuovo servizio Web.

Per testare il servizio Esecuzione batch, fare clic sul collegamento di anteprima **Test**. Nella pagina di test Batch, fare clic su Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriati. Se non si ha un file CSV e l'esperimento predittivo è stato creato usando Machine Learning Studio (versione classica), è possibile scaricare il set di dati per l'esperimento predittivo e usarlo.

![Testare il servizio Web](./media/publish-a-machine-learning-web-service/figure-3.png)

Nella pagina **CONFIGURATION** (CONFIGURAZIONE) è possibile modificare il nome visualizzato per il servizio e assegnare una descrizione. Il nome e la descrizione vengono visualizzati nel [portale di Azure](https://portal.azure.com/), in cui è possibile gestire i propri servizi Web.

È possibile specificare una descrizione per i dati di input, i dati di output e i parametri del servizio Web immettendo una stringa per ogni colonna nello **schema di input** , **nello schema di output** e nel parametro del **servizio Web**. Queste descrizioni vengono usate nella documentazione del codice di esempio fornita per il servizio Web.

È possibile abilitare la registrazione per diagnosticare eventuali errori che si verificano quando si accede al servizio Web. Per altre informazioni, vedere [abilitare la registrazione per i servizi Web di Machine Learning Studio (classico)](web-services-logging.md).

![Abilitare la registrazione nel portale dei servizi Web](./media/publish-a-machine-learning-web-service/figure-4.png)

È anche possibile configurare gli endpoint per il servizio Web nel portale dei servizi Web di Azure Machine Learning, con una procedura simile a quella illustrata in precedenza nella sezione relativa al nuovo servizio Web. Le opzioni sono diverse: è possibile aggiungere o modificare la descrizione del servizio, abilitare la registrazione e abilitare i dati di esempio per il test.

### <a name="access-your-classic-web-service"></a>Accedere al servizio Web classico

Dopo aver distribuito il servizio Web da Azure Machine Learning Studio (classico), è possibile inviare dati al servizio e ricevere risposte a livello di codice.

Il dashboard offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, la chiave API viene offerta per consentire l'accesso autorizzato al servizio, mentre le pagine della guida API hanno lo scopo di aiutare nella scrittura di codice.

Per ulteriori informazioni sull'accesso a un servizio Web Machine Learning Studio (classico), vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gestire il servizio Web classico

Per monitorare un servizio Web, sono disponibili diverse operazioni. È possibile aggiornare il servizio ed eliminarlo. È anche possibile aggiungere altri endpoint a un servizio Web classico oltre all'endpoint predefinito creato al momento della distribuzione.

Per altre informazioni, vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md) e [gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning Studio (classico)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aggiornare il servizio Web
È possibile apportare modifiche al servizio Web, ad esempio aggiornare il modello con ulteriori dati di training e ridistribuirlo, sovrascrivendo il servizio Web originale.

Per aggiornare il servizio Web, aprire l'esperimento predittivo originale usato per distribuire il servizio Web ed effettuare una copia modificabile facendo clic su **Salva con nome**. Eseguire le modifiche e fare clic su **Distribuisci Servizio Web**.

Poiché questo esperimento è stato distribuito prima, viene chiesto se si desidera sovrascrivere (servizio Web classico) o aggiornare il servizio esistente (nuovo servizio Web). Se si fa clic su **Sì** o su **Aggiorna** , il servizio Web esistente viene arrestato e la distribuzione del nuovo esperimento predittivo viene distribuita al suo posto.

> [!NOTE]
> Se sono state apportate modifiche di configurazione al servizio Web originale, ad esempio immettendo un nuovo nome o descrizione, è necessario immettere nuovamente questi valori.

Un'opzione per l'aggiornamento del servizio Web consiste nel ripetere il training del modello a livello di codice. Per altre informazioni, vedere ripetere il [training dei modelli di Machine Learning Studio (classico) a livello di codice](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni più tecniche sul funzionamento della distribuzione, vedere [come un modello di Machine Learning Studio (classico) progredisce da un esperimento a un servizio Web operativo](model-progression-experiment-to-web-service.md).

* Per informazioni dettagliate su come preparare il modello per la distribuzione, vedere [come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)](deploy-a-machine-learning-web-service.md).

* È possibile utilizzare l'API REST e accedere al servizio Web in diversi modi. Vedere [come utilizzare un servizio Web di Azure Machine Learning Studio (classico)](consume-web-services.md).

<!-- internal links -->
[Creare un esperimento di training]: #create-a-training-experiment
[Convertirlo in un esperimento predittivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Nuovo servizio Web]: #deploy-it-as-a-new-web-service
[Servizio Web classico]: #deploy-it-as-a-classic-web-service
[Nuovo]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data