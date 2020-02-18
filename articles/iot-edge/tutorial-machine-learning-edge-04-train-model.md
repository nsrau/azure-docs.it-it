---
title: 'Esercitazione: Eseguire il training e la distribuzione di un modello - Machine Learning in Azure IoT Edge'
description: Questa esercitazione mostra come eseguire il training di un modello di Machine Learning con Azure Machine Learning e quindi inserirlo in un pacchetto come immagine di contenitore che è possibile distribuire in un modulo Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5f576d28d30907f3834600d0a6a5c152025cf912
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133474"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Esercitazione: Eseguire il training e distribuire un modello di Azure Machine Learning

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per avere risultati ottimali.

In questo articolo si apprenderà come eseguire le attività seguenti:

* Usare Azure Notebooks per eseguire il training di un modello di Machine Learning.
* Assemblare il modello sottoposto a training in un'immagine del contenitore.
* Distribuire l'immagine del contenitore come modulo Azure IoT Edge.

Azure Notebooks sfrutta un'area di lavoro di Azure Machine Learning, un elemento di base usato per sperimentare, eseguire il training e distribuire modelli di Machine Learning.

I passaggi di questo articolo vengono in genere eseguiti da data scientist.

## <a name="set-up-azure-notebooks"></a>Configurare Azure Notebooks

Azure Notebooks viene usato per ospitare due notebook di Jupyter e i file di supporto. Ora verrà creato e configurato un progetto di Azure Notebooks. Se Jupyter Notebook e/o Azure Notebooks non sono mai stati usati, ecco un paio di documenti introduttivi:

* **Avvio rapido:** [Creare e condividere un notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Esercitazione:** [Creare ed eseguire un notebook di Jupyter con Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

L'uso di Azure Notebooks assicura un ambiente coerente per l'esercitazione.

> [!NOTE]
> Una volta configurato, il servizio Azure Notebooks diventa accessibile da qualsiasi computer. Durante la configurazione, è consigliabile usare la VM di sviluppo, che include tutti i file necessari.

### <a name="create-an-azure-notebooks-account"></a>Creare un account di Azure Notebooks

Per usare Azure Notebooks, è necessario creare un account. Gli account di Azure Notebooks sono indipendenti dalle sottoscrizioni di Azure.

1. Passare ad [Azure Notebooks](https://notebooks.azure.com).

1. Fare clic su **Accedi** nell'angolo in alto a destra della pagina.

1. Accedere con l'account aziendale o dell'istituto di istruzione (Azure Active Directory) oppure con l'account personale (account Microsoft).

1. Se Azure Notebooks non è mai stato usato prima, verrà chiesto di concedere l'accesso alla relativa app.

1. Creare un ID utente per Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Caricare i file del notebook di Jupyter

I file del notebook di esempio vengono caricati in un nuovo progetto di Azure Notebooks.

1. Nella pagina utente del nuovo account selezionare **My Projects** (Progetti personali) sulla barra dei menu superiore.

1. Nella finestra di dialogo **Create New Project** (Crea nuovo progetto) specificare un nome in **Project Name** (Nome progetto) che automaticamente forma anche **Project ID** (ID progetto).

1. Lasciare deselezionate le opzioni **Public** (Pubblico) e **README**, perché non è necessario che il progetto sia pubblico o includa un file readme.

1. Selezionare **Create** (Crea).

1. Selezionare **Carica** (l'icona della freccia in su) e scegliere **From Computer** (Dal computer).

1. Selezionare **Choose files** (Scegli file).

1. Passare a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selezionare tutti i file nell'elenco e fare clic su **Apri**.

1. Selezionare **Upload** (Carica) per avviare il caricamento e quindi **Done** (Fatto) al termine del processo.

### <a name="azure-notebook-files"></a>File di notebook di Azure

Esaminare i file caricati nel progetto di Azure Notebooks. Le attività in questa parte dell'esercitazione si estendono su due file di notebook, che usano alcuni file di supporto.

* **01-turbofan\_regression.ipynb:** Questo notebook usa l'area di lavoro del servizio Machine Learning per creare ed eseguire un esperimento di Machine Learning. In generale, il notebook esegue queste operazioni:

  1. Scarica i dati dall'account di archiviazione di Azure generato dall'harness del dispositivo.
  1. Esplora e prepara i dati per il training e il modello di annuncio del classificatore.
  1. Valuta il modello dell'esperimento usando un set di dati di test (Test\_FD003.txt).
  1. Pubblica il modello di classificatore migliore nell'area di lavoro del servizio Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** questo notebook usa il modello creato nel notebook precedente per creare un'immagine di contenitore da distribuire in un dispositivo Azure IoT Edge. Il notebook esegue i passaggi seguenti:

  1. Crea uno script di assegnazione di punteggi per il modello.
  1. Produce un'immagine del contenitore usando il modello di classificatore salvato nell'area di lavoro del servizio Machine Learning.
  1. Distribuisce l'immagine come servizio Web in Istanze di Azure Container.
  1. Usa il servizio Web per verificare se il modello e l'immagine funzionano come previsto. L'immagine convalidata verrà distribuita nel dispositivo IoT Edge nella sezione [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md) di questa esercitazione.

* **Test\_FD003.txt:** questo file contiene i dati che verranno usati come set di test durante la convalida del classificatore sottoposto a training. Per semplicità, vengono usati i dati di test come set di test così come vengono forniti per il contesto originale.

* **RUL\_FD003.txt:** questo file contiene la vita utile rimanente relativa all'ultimo ciclo di ogni dispositivo incluso nel file Test\_FD003.txt. Per una spiegazione dettagliata dei dati, vedere il file readme.txt e il file Damage Propagation Modeling.pdf disponibili in C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py:** contiene un set di funzioni di utilità di Python per l'uso dei dati. Il primo notebook contiene una spiegazione dettagliata delle funzioni.

* **README.md:** file che descrive l'uso dei notebook.  

## <a name="run-azure-notebooks"></a>Eseguire Azure Notebooks

Ora che il progetto è stato creato, è possibile eseguire i notebook. 

1. Nella pagina del progetto selezionare **01-turbofan\_regression.ipynb**.

    ![Selezionare il primo notebook da eseguire](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Se richiesto, scegliere il kernel Python 3.6 nella finestra di dialogo e selezionare **Set Kernel** (Imposta kernel).

1. Se il notebook è indicato come **Not Trusted** (Non attendibile), fare clic sul widget **Not Trusted** nell'angolo in alto a destra. Quando viene visualizzata la finestra di dialogo, selezionare **Trust** (Considera attendibile).

1. Nel notebook scorrere verso il basso fino alla cella che segue le istruzioni **Set global properties** (Impostare le proprietà globali) e che inizia con il codice `AZURE_SUBSCRIPTION_ID =`, quindi inserire i valori per la sottoscrizione, le impostazioni e le risorse di Azure.

    ![Impostare le proprietà globali nel notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Eseguire la cella selezionando **Esegui** sulla barra degli strumenti.

    Durante l'esecuzione, la cella visualizza un asterisco tra parentesi quadre, ([\*]). Una volta completata l'operazione della cella, l'asterisco viene sostituito con un numero e potrebbe essere visualizzato l'output appropriato. Le celle di un notebook vengono compilate in sequenza ed è possibile eseguirne solo una alla volta.

    Seguire le istruzioni contenute nel notebook. È anche possibile usare le opzioni di esecuzione nel menu **Cell** (Cella), `Ctrl` + `Enter` per eseguire una cella e `Shift` + `Enter` per eseguire una cella e passare a quella successiva.

    > [!TIP]
    > Per operazioni coerenti sulle celle, evitare di eseguire lo stesso notebook da più schede del browser.

1. Scorrere verso il basso fino alla cella che segue immediatamente il testo introduttivo **Create a workspace** (Creare un'area di lavoro) ed eseguirla. Nell'output della cella cercare il collegamento che indica di accedere per eseguire l'autenticazione. 

    ![Richiesta di accesso per l'autenticazione del dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Aprire il collegamento e immettere il codice specificato. Questa procedura di accesso autentica il notebook di Jupyter per l'accesso alle risorse di Azure usando l'interfaccia della riga di comando multipiattaforma di Microsoft Azure.  

    ![Conferma dell'autenticazione dell'applicazione nel dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. A questo punto, è possibile eseguire le altre celle. La scelta ottimale consiste nell'eseguire tutte le celle in modo che il codice al loro interno venga eseguito in sequenza. Scegliere **Run All** (Esegui tutto) dal menu **Cell** (Cella). Scorrere indietro nel notebook ed esaminare come vengono completate le operazioni delle celle.

    Nella sezione **Explore the data** (Esplorare i dati) è possibile esaminare le celle nella sottosezione **Sensor readings and RUL** (Letture del sensore e RUL), che visualizzano grafici a dispersione delle misurazioni dei sensori.

    ![Grafici a dispersione delle letture dei sensori](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Salvare il notebook e tornare nella pagina del progetto facendo clic sul relativo nome nell'angolo in alto a destra del notebook oppure tornando indietro nel browser.

1. Aprire **02-turbofan\_deploy\_model.ipynb** e ripetere i passaggi di questa procedura per eseguire il secondo notebook.

1. Salvare il notebook e tornare nella pagina del progetto facendo clic sul relativo nome nell'angolo in alto a destra del notebook oppure tornando indietro nel browser.

### <a name="verify-success"></a>Verificare l'esito positivo

Per verificare se i notebook sono stati completati correttamente, controllare che siano stati creati alcuni elementi.

1. Nella pagina del progetto di Azure Notebooks selezionare **Mostra elementi nascosti** in modo che vengano visualizzati i nomi degli elementi che iniziano con un punto.

1. Verificare che siano stati creati i file seguenti:

    | File | Descrizione |
    | --- | --- |
    | ./aml_config/.azureml/config.json | File di configurazione usato per creare l'area di lavoro di Azure Machine Learning. |
    | ./aml_config/model_config.json | File di configurazione necessario per distribuire il modello nell'area di lavoro di Machine Learning **turbofanDemo** in Azure. |
    | myenv.yml| Fornisce informazioni sulle dipendenze per il modello di Machine Learning distribuito.|

1. Nel portale di Azure verificare che nel gruppo di risorse sia presente l'area di lavoro di Machine Learning **turboFanDemo**.

### <a name="debugging"></a>Debug

È possibile inserire istruzioni Python nel notebook per il debug, principalmente il comando `print()`. Se vengono visualizzate variabili o oggetti che non sono definiti, eseguire le celle in cui vengono dichiarati o di cui viene creata un'istanza la prima volta.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati usati due notebook di Jupyter in esecuzione in Azure Notebooks per usare i dati dei dispositivi Turbofan per eseguire il training di un classificatore di vita utile rimanente, salvarlo come modello, creare un'immagine di contenitore, quindi distribuire e testare l'immagine come servizio Web.

Continuare con l'articolo successivo per creare un dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurare un dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
