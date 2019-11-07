---
title: Eseguire il training e la distribuzione di un modello - Machine Learning in Azure IoT Edge | Microsoft Docs
description: Eseguire il training di un modello di Machine Learning con Azure Machine Learning e quindi inserirlo in un pacchetto come immagine di contenitore che è possibile distribuire in un modulo Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6e1ee1fda658ef0884975e4055891f705c4f5058
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493977"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Esercitazione: Eseguire il training e distribuire un modello di Azure Machine Learning

> [!NOTE]
> Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Se l'articolo è stato aperto direttamente, è consigliabile iniziare con il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie per risultati ottimali.

In questo articolo viene prima usato il servizio Azure Notebooks per eseguire il training di un modello di Machine Learning con Azure Machine Learning, che quindi viene inserito in un pacchetto come immagine di contenitore da distribuire in un modulo Azure IoT Edge. Azure Notebooks sfrutta un'area di lavoro di Azure Machine Learning, un elemento di base usato per sperimentare, eseguire il training e distribuire modelli di Machine Learning.

Le attività di questa parte dell'esercitazione sono suddivise tra due notebook.

* **01-turbofan\_regression.ipynb:** questo notebook illustra la procedura per eseguire il training di un modello e pubblicarlo con Azure Machine Learning. In generale, i passaggi necessari sono:

  1. Scaricare, preparare ed esplorare i dati di training
  2. Usare l'area di lavoro del servizio per creare ed eseguire un esperimento di Machine Learning
  3. Valutare i risultati dell'esperimento per i modelli
  4. Pubblicare il modello migliore nell'area di lavoro del servizio

* **02-turbofan\_deploy\_model.ipynb:** questo notebook usa il modello creato nel notebook precedente per creare un'immagine di contenitore da distribuire in un dispositivo Azure IoT Edge.

  1. Creare uno script di assegnazione di punteggi per il modello
  2. Creare e pubblicare l'immagine
  3. Distribuire l'immagine come servizio Web in Istanze di Azure Container
  4. Usare il servizio Web per verificare se il modello e l'immagine funzionano come previsto

I passaggi di questo articolo vengono in genere eseguiti da data scientist.

## <a name="set-up-azure-notebooks"></a>Configurare Azure Notebooks

Azure Notebooks viene usato per ospitare due notebook di Jupyter e i file di supporto. Ora verrà creato e configurato un progetto di Azure Notebooks. Se Jupyter Notebook e/o Azure Notebooks non sono mai stati usati, ecco un paio di documenti introduttivi:

* **Avvio rapido:** [Creare e condividere un notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Esercitazione:** [Creare ed eseguire un notebook di Jupyter con Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Così come la macchina virtuale di sviluppo descritta in precedenza, l'uso di Azure Notebooks assicura un ambiente coerente per l'esercitazione.

> [!NOTE]
> Una volta configurato, il servizio Azure Notebooks diventa accessibile da qualsiasi computer. Durante la configurazione, è consigliabile usare la macchina virtuale di sviluppo, che include tutti i file necessari.

### <a name="create-an-azure-notebooks-account"></a>Creare un account di Azure Notebooks

Gli account di Azure Notebooks sono indipendenti dalle sottoscrizioni di Azure. Per usare Azure Notebooks, è necessario creare un account.

1. Passare ad [Azure Notebooks](https://notebooks.azure.com).

2. Fare clic su **Accedi** nell'angolo in alto a destra della pagina.

3. Accedere con l'account aziendale o dell'istituto di istruzione (Azure Active Directory) oppure con l'account personale (account Microsoft).

4. Se Azure Notebooks non è mai stato usato prima, verrà chiesto di concedere l'accesso alla relativa app.

5. Creare un ID utente per Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Caricare i file dei notebook di Jupyter

In questo passaggio verrà creato un nuovo progetto di Azure Notebooks in cui verranno caricati i file. In particolare, i file da caricare sono:

* **01-turbofan\_regression.ipynb:** file di notebook di Jupyter che illustra la procedura di download dall'account di archiviazione dei dati generati tramite DeviceHarness; l'esplorazione e la preparazione dei dati per il training del classificatore; il training del modello; i test dei dati con il set di dati di test disponibile nel file\_FD003.txt; e infine il salvataggio del modello di classificatore nell'area di lavoro del servizio Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** notebook di Jupyter che illustra come usare il modello di classificatore salvato nell'area di lavoro del servizio Machine Learning per produrre un'immagine di contenitore. Una volta creata l'immagine, il notebook illustra la procedura di distribuzione dell'immagine come servizio Web in modo che sia possibile verificare se funziona come previsto. L'immagine convalidata verrà distribuita nel dispositivo IoT Edge nella sezione [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md) di questa esercitazione.

* **Test\_FD003.txt:** questo file contiene i dati che verranno usati come set di test durante la convalida del classificatore sottoposto a training. Per semplicità, nell'esempio vengono usati i dati di test come set di test così come vengono forniti per il contesto originale.

* **RUL\_FD003.txt:** questo file contiene la vita utile rimanente relativa all'ultimo ciclo di ogni dispositivo incluso nel file Test\_FD003.txt. Per una spiegazione dettagliata dei dati, vedere il file **readme.txt** e il file **Damage Propagation Modeling.pdf** disponibili in C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py:** contiene un set di funzioni di utilità di Python per l'uso dei dati. Il primo notebook contiene una spiegazione dettagliata delle funzioni.

* **README.md:** file che descrive l'uso dei notebook.

Creare un nuovo progetto e caricare i file nel notebook.

1. Selezionare **My Projects** (Progetti personali) sulla barra dei menu superiore.

1. Selezionare **+New project** (Nuovo progetto). Specificare un nome e un ID. Non è necessario rendere pubblico il progetto o includere un file Readme.

1. Selezionare **Upload** (Carica) e scegliere **From Computer** (Da computer).

1. Selezionare **Choose files** (Scegli file).

1. Passare a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selezionare tutti i file e fare clic su **Open** (Apri).

1. Selezionare **Upload** (Carica) per avviare il caricamento e quindi **Done** (Fatto) al termine del processo.

## <a name="run-azure-notebooks"></a>Eseguire Azure Notebooks

Dopo aver creato il progetto, eseguire il notebook **01-turbofan\_regression.ipynb**.

1. Nella pagina del progetto Turbofan selezionare **01-turbofan\_regression.ipynb**.

    ![Selezionare il primo notebook da eseguire](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Se richiesto, scegliere il kernel Python 3.6 nella finestra di dialogo e selezionare **Set Kernel** (Imposta kernel).

3. Se il notebook è indicato come **Not Trusted** (Non attendibile), fare clic sul widget **Not Trusted** nell'angolo in alto a destra. Quando viene visualizzata la finestra di dialogo, selezionare **Trust** (Considera attendibile).

4. Seguire le istruzioni contenute nel notebook.

    * `Ctrl + Enter` esegue una cella.
    * `Shift + Enter` esegue una cella e passa alla cella successiva.
    * Durante l'esecuzione, le celle sono contrassegnate da un asterisco tra parentesi quadre, **[\*]** . Al termine, l'asterisco verrà sostituito da un numero e al di sotto potrebbe essere visualizzato l'output appropriato. Poiché le celle sono spesso basate sulle operazioni di quelle precedenti, può essere eseguita una sola cella alla volta.

5. Dopo aver completato l'esecuzione del notebook **01-turbofan\_regression.ipynb**, tornare nella pagina del progetto.

6. Aprire **02-turbofan\_deploy\_model.ipynb** e ripetere i passaggi di questa sezione per eseguire il secondo notebook.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati usati due notebook di Jupyter in esecuzione in Azure Notebooks per usare i dati dei dispositivi Turbofan per eseguire il training di un classificatore di vita utile rimanente, salvarlo come modello, creare un'immagine di contenitore, quindi distribuire e testare l'immagine come servizio Web.

Continuare con l'articolo successivo per creare un dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurare un dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
