---
title: Creare e gestire risorse VS Code estensione (anteprima)
titleSuffix: Azure Machine Learning
description: Creare e gestire le risorse con l'estensione VS Code
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: 2deb939745647636cc04ae124d1e843db0bef7f6
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206500"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Gestire le risorse di Azure Machine Learning con l'estensione VS Code (anteprima)

Informazioni su come gestire Azure Machine Learning risorse con l'estensione VS Code.

![Estensione Azure Machine Learning VS Code](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure. Se non se ne possiede una, iscriversi per provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se non è disponibile, [installarlo](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code Azure Machine Learning estensione. Per installare l'estensione, seguire la [Guida all'installazione di Azure Machine Learning vs code Extension](tutorial-setup-vscode-extension.md#install-the-extension) .

Tutti i processi riportati di seguito presuppongono che l'utente si trovi nella visualizzazione Azure Machine Learning Visual Studio Code. Per avviare l'estensione, selezionare l'icona di **Azure** nella barra delle attività del vs code.

## <a name="workspaces"></a>Aree di lavoro

Per ulteriori informazioni, vedere [aree di lavoro](concept-workspace.md).

### <a name="create-a-workspace"></a>Creare un'area di lavoro

1. Nella visualizzazione Azure Machine Learning fare clic con il pulsante destro del mouse sul nodo della sottoscrizione e selezionare **Crea area di lavoro**.
1. Nel prompt dei comandi:
    1. Specificare un nome per l'area di lavoro
    1. Scegliere la sottoscrizione di Azure
    1. Scegliere o creare un nuovo gruppo di risorse in cui eseguire il provisioning dell'area di lavoro
    1. Selezionare la località in cui eseguire il provisioning dell'area di lavoro.
    1. Scegliere tra *Basic* ed *Enterprise* Edition. Altre informazioni sulle diverse [edizioni di Azure Machine Learning](concept-editions.md).

I metodi alternativi per creare un'area di lavoro includono:

- Fare clic sull' `+` icona nella parte superiore della visualizzazione Azure Machine Learning.
- Creare una nuova area di lavoro quando viene richiesto di selezionare un'area di lavoro durante il provisioning di altre risorse.

### <a name="remove-a-workspace"></a>Rimuovere un'area di lavoro

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Fare clic con il pulsante destro del mouse sull'area di lavoro che si desidera rimuovere.
1. Selezionare se si desidera rimuovere:
    - *Solo l'area di lavoro*: questa opzione consente di eliminare **solo** la risorsa di Azure dell'area di lavoro. Il gruppo di risorse, gli account di archiviazione e tutte le altre risorse a cui l'area di lavoro è stata collegata sono ancora in Azure.
    - *Con le risorse associate*: questa opzione consente di eliminare l'area di lavoro **e** tutte le risorse ad esso associate.

## <a name="datastores"></a>Archivi dati

L'estensione VS Code supporta attualmente gli archivi dati dei tipi seguenti:

- Condivisione file di Azure
- Archiviazione BLOB di Azure

Quando si crea un'area di lavoro, viene creato un archivio dati per ognuno di questi tipi.

Per altre informazioni, vedere [datastores](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Creare un archivio dati

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro in cui si desidera creare l'archivio dati.
1. Fare clic con il pulsante destro del mouse sul nodo **archivi dati** e selezionare **Registra archivio dati**.
1. Nel prompt dei comandi:
    1. Consente di specificare un nome per l'archivio dati.
    1. Scegliere il tipo di archivio dati.
    1. Selezionare la risorsa di archiviazione. È possibile scegliere una risorsa di archiviazione associata all'area di lavoro oppure selezionare una risorsa di archiviazione valida nelle sottoscrizioni di Azure.
    1. Scegliere il contenitore in cui si trovino i dati all'interno della risorsa di archiviazione selezionata in precedenza.
1. Un file di configurazione viene visualizzato in VS Code. Se si è soddisfatti del file di configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**Visualizza > riquadro comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="manage-a-datastore"></a>Gestire un archivio dati

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo **archivi dati** all'interno dell'area di lavoro.
1. Selezionare l'archivio dati che si desidera:
    - *Imposta come predefinito*. Ogni volta che si eseguono gli esperimenti, si tratta dell'archivio dati che verrà usato.
    - *Controllare le impostazioni di sola lettura*.
    - *Modificare*. Modificare il tipo di autenticazione e le credenziali. I tipi di autenticazione supportati includono la chiave dell'account e il token SAS.

## <a name="datasets"></a>Set di dati

L'estensione supporta attualmente i tipi di set di dati seguenti:

- *Tabular*: consente di materializzare i dati in un frame di dati (Pandas o PySpark).
- *File*: un file o una raccolta di file. Consente di scaricare o montare i file nel calcolo.

Per altre informazioni, vedere [DataSets](concept-data.md#datasets)

### <a name="create-dataset"></a>Creare set di dati

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro in cui si desidera creare l'archivio dati.
1. Fare clic con il pulsante destro del mouse sul nodo **DataSets** e scegliere **Crea set di dati**.
1. Nel prompt dei comandi:
    1. Scegliere il tipo di set di dati
    1. Definire se i dati si trovano nel PC o sul Web
    1. Consente di specificare la posizione dei dati. Può trattarsi di un singolo file o di una directory contenente i file di dati.
    1. Scegliere l'archivio dati in cui si desidera caricare i dati.
    1. Specificare un prefisso che consenta di identificare il set di dati nell'archivio dati.

### <a name="version-datasets"></a>Set di impostazioni di versione

Quando si compilano modelli di apprendimento automatico, in caso di modifica dei dati, è possibile eseguire la versione del set di dati. A tale scopo, nell'estensione VS Code:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo **set di impostazioni** .
1. Fare clic con il pulsante destro del mouse sul set di dati di cui si desidera eseguire la versione e selezionare **Crea nuova**
1. Nel prompt dei comandi:
    1. Selezionare il tipo di set di dati
    1. Definire se i dati si trovano nel PC o sul Web.
    1. Consente di specificare la posizione dei dati. Può trattarsi di un singolo file o di una directory contenente i file di dati.
    1. Scegliere l'archivio dati in cui si desidera caricare i dati.
    1. Specificare un prefisso che consenta di identificare il set di dati nell'archivio dati.

### <a name="view-dataset-properties"></a>Visualizzare le proprietà del set di dati

Questa opzione consente di visualizzare i metadati associati a un set di dati specifico. A tale scopo, nell'estensione VS Code:

1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo **set di impostazioni** .
1. Fare clic con il pulsante destro del mouse sul set di dati da controllare e selezionare **Visualizza proprietà set di dati**. Verrà visualizzato un file di configurazione con le proprietà della versione più recente del set di dati.

> [!NOTE]
> Se si dispone di più versioni del set di dati, è possibile scegliere di visualizzare solo le proprietà del set di dati di una versione specifica espandendo il nodo del set di dati ed eseguendo gli stessi passaggi descritti in questa sezione sulla versione di interesse.

### <a name="unregister-datasets"></a>Annulla registrazione set di impostazioni

Per rimuovere un set di dati e tutte le versioni di esso, annullarne la registrazione. A tale scopo, nell'estensione VS Code:

1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo **set di impostazioni** .
1. Fare clic con il pulsante destro del mouse sul set di dati di cui si desidera annullare la registrazione e selezionare **Annulla registrazione DataSet**

## <a name="environments"></a>Ambienti

Per ulteriori informazioni, vedere [ambienti](concept-environments.md).

### <a name="create-environment"></a>Crea ambiente

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro in cui si desidera creare l'archivio dati.
1. Fare clic con il pulsante destro del mouse sul nodo **ambienti** e selezionare **Crea ambiente**.
1. Nel prompt dei comandi:
    1. Specificare un nome per l'ambiente
    1. Definire la configurazione dell'ambiente:
        - *Ambienti curati*: ambienti preconfigurati in Azure Machine Learning. È possibile personalizzare ulteriormente l'ambiente modificando la `dependencies` proprietà nel file JSON. Altre informazioni sugli [ambienti curati](resource-curated-environments.md).
        - *File delle dipendenze conda*: per gli ambienti Anaconda è possibile specificare il file che contiene la definizione dell'ambiente.
        - *File dei requisiti PIP*: per gli ambienti PIP, è possibile specificare il file che contiene la definizione dell'ambiente.
        - *Ambiente conda esistente*: questa opzione Cerca gli ambienti conda nel PC locale e tenta di creare un ambiente dall'ambiente selezionato.
        - *Personalizzata*: definire i propri canali e dipendenze
    1. Un file di configurazione verrà aperto nell'editor. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="view-environment-configurations"></a>Visualizzare le configurazioni dell'ambiente

Per visualizzare le dipendenze e le configurazioni per un ambiente specifico nell'estensione:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo **ambienti** .
1. Fare clic con il pulsante destro del mouse sull'ambiente che si desidera visualizzare e selezionare **Visualizza ambiente**.

### <a name="edit-environment-configurations"></a>Modificare le configurazioni dell'ambiente

Per modificare le dipendenze e le configurazioni per un ambiente specifico nell'estensione:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **ambienti** nell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sull'ambiente che si desidera visualizzare e scegliere **modifica ambiente**.
1. Dopo aver apportato le modifiche, se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**Visualizza > riquadro comandi**) e digitare **Azure ml: Salva e continua**.

## <a name="experiments"></a>Sperimentazioni

Per altre informazioni, vedere [esperimenti](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Creare un esperimento

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul nodo **esperimenti** nell'area di lavoro e selezionare **Crea esperimento**.
1. Nella richiesta specificare un nome per l'esperimento.

### <a name="run-experiment"></a>Eseguire esperimento

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **esperimenti** nell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sull'esperimento che si desidera eseguire.
1. Selezionare l'icona **Esegui esperimento** sulla barra delle attività.
1. Scegliere la propria sottoscrizione.
1. Scegliere il area di lavoro di ML di Azure in cui eseguire l'esperimento.
1. Scegliere l'esperimento.
1. Scegliere o creare un calcolo per l'esecuzione dell'esperimento.
1. Scegliere o creare una configurazione di esecuzione per l'esperimento.

In alternativa, è possibile selezionare il pulsante **Esegui esperimento** nella parte superiore dell'estensione e configurare l'esecuzione dell'esperimento nel prompt.

### <a name="view-experiment"></a>Visualizza esperimento

Per visualizzare l'esperimento in Azure Machine Learning Studio:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **esperimenti** nell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sull'esperimento che si desidera visualizzare e selezionare **Visualizza esperimento**. 
1. Viene visualizzato un messaggio in cui viene chiesto di aprire l'URL dell'esperimento in Azure Machine Learning Studio. Selezionare **Open** (Apri).

### <a name="track-run-progress"></a>Traccia stato esecuzione

Quando si esegue l'esperimento, è possibile che si desideri visualizzarne lo stato di avanzamento. Per tenere traccia dello stato di avanzamento di un'esecuzione in Azure Machine Learning Studio dall'estensione:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **esperimenti** nell'area di lavoro.
1. Espandere il nodo dell'esperimento per cui si vuole tenere traccia dello stato di avanzamento.
1. Fare clic con il pulsante destro del mouse su Esegui e selezionare **Visualizza esecuzione in portale di Azure**.
1. Viene visualizzato un messaggio in cui viene chiesto di aprire l'URL di esecuzione in Azure Machine Learning Studio. Selezionare **Open** (Apri).

### <a name="download-run-logs--outputs"></a>Scaricare i log di esecuzione & output

Una volta completata l'esecuzione, è possibile scaricare i log e gli asset, ad esempio il modello generato come parte di un'esecuzione dell'esperimento.

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **esperimenti** nell'area di lavoro.
1. Espandere il nodo dell'esperimento per cui si vuole tenere traccia dello stato di avanzamento.
1. Fare clic con il pulsante destro del mouse sull'esecuzione:
    - Per scaricare gli output, selezionare **Scarica output**.
    - Per scaricare i log, selezionare **Scarica log**.

### <a name="view-run-metadata"></a>Visualizza metadati esecuzione

Nell'estensione è possibile esaminare i metadati, ad esempio la configurazione di esecuzione utilizzata per l'esecuzione, nonché i dettagli dell'esecuzione.

## <a name="compute-clusters"></a>Cluster di elaborazione

L'estensione supporta i tipi di calcolo seguenti:

- Cluster di calcolo di Azure Machine Learning
- Servizio Azure Kubernetes

Per altre informazioni, vedere [destinazioni di calcolo](concept-compute-target.md#train).

### <a name="create-compute"></a>Crea calcolo

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro in cui si vuole creare il cluster di elaborazione.
1. Fare clic con il pulsante destro del mouse sul nodo **cluster di calcolo** e scegliere **Crea calcolo**.
1. Nel prompt dei comandi:
    1. Scegliere un tipo di calcolo
    1. Scegliere le dimensioni per la macchina virtuale. Altre informazioni sulle [dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
    1. Specificare un nome per il calcolo.

### <a name="view-compute-configuration"></a>Visualizzazione del calcolo Edge

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **cluster di calcolo** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul calcolo che si desidera visualizzare e selezionare **Visualizza proprietà di calcolo**.

### <a name="edit-compute-scale-settings"></a>Modificare le impostazioni di scalabilità di calcolo

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **cluster di calcolo** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul calcolo che si desidera modificare e scegliere **Modifica calcolo**.
1. Un file di configurazione per il calcolo verrà aperto nell'editor. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="delete-compute"></a>Elimina calcolo

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **cluster di calcolo** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul calcolo che si desidera eliminare e scegliere **Elimina calcolo**.

### <a name="create-run-configuration"></a>Creare la configurazione di esecuzione

Per creare una configurazione di esecuzione nell'estensione:

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **cluster di calcolo** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sulla destinazione di calcolo in cui si vuole creare la configurazione di esecuzione e selezionare **Crea configurazione di esecuzione**.
1. Nel prompt dei comandi:
    1. Specificare un nome per la destinazione di calcolo
    1. Scegliere o creare un nuovo ambiente.
    1. Digitare il nome dello script che si desidera eseguire oppure premere **invio** al browser per lo script nel computer locale.
    1. Opzionale Scegliere se si desidera creare un riferimento ai dati per l'esecuzione del training. In questo modo verrà richiesto di definire un set di dati nella configurazione di esecuzione.
        1. Selezionare uno dei set di dati registrati da collegare alla configurazione di esecuzione un file di configurazione per il set di dati verrà aperto nell'editor. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.
    1. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="edit-run-configuration"></a>Modifica configurazione di esecuzione

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo del cluster di calcolo nel nodo **cluster di calcolo** dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sulla configurazione di esecuzione che si desidera modificare e scegliere **modifica configurazione di esecuzione**.
1. Un file di configurazione per la configurazione di esecuzione viene aperto nell'editor. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="delete-run-configuration"></a>Elimina configurazione di esecuzione

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Espandere il nodo del cluster di calcolo di interesse nel nodo **cluster di calcolo** .
1. Fare clic con il pulsante destro del mouse sulla configurazione di esecuzione che si desidera modificare e scegliere **Elimina configurazione di esecuzione**.

## <a name="models"></a>Modelli

Per ulteriori informazioni, vedere [modelli](concept-azure-machine-learning-architecture.md#models) .

### <a name="register-model"></a>Registrare il modello

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul nodo **modelli** e selezionare **Registra modello**.
1. Nel prompt dei comandi:
    1. Specificare un nome per il modello
    1. Scegliere se il modello è un file o una cartella.
    1. Trovare il modello nel PC locale.
    1. Un file di configurazione per il modello nell'editor. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

### <a name="view-model-properties"></a>Visualizzare le proprietà del modello

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **modelli** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul modello di cui si desidera visualizzare le proprietà e selezionare **Visualizza proprietà modello**. Un file viene aperto nell'Editor contenente le proprietà del modello.

### <a name="download-model"></a>Scarica modello

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **modelli** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul modello che si desidera scaricare e scegliere **Scarica file modello**.

### <a name="delete-a-model"></a>Eliminare un modello

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **modelli** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul modello che si desidera eliminare e scegliere **Rimuovi modello**.

## <a name="endpoints"></a>Endpoint

L'estensione VS Code supporta le seguenti destinazioni di distribuzione:

- Istanze di Azure Container
- Servizio Azure Kubernetes

Per ulteriori informazioni, vedere [endpoint del servizio Web](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Creare distribuzioni

> [!NOTE]
> La creazione della distribuzione attualmente funziona solo con gli ambienti conda.

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sul nodo **endpoint** e scegliere **Distribuisci servizio**.
1. Nel prompt dei comandi:
    1. Scegliere se si desidera utilizzare un modello già registrato o un file di modello locale.
    1. Selezionare il modello
    1. Scegliere la destinazione di distribuzione in cui si vuole distribuire il modello.
    1. Consente di specificare un nome per il modello.
    1. Consente di specificare lo script da eseguire quando si assegna un punteggio al modello.
    1. Fornire un file di dipendenze conda.
    1. Nell'editor verrà visualizzato un file di configurazione per la distribuzione. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.

> [!NOTE]
> In alternativa, è possibile fare clic con il pulsante destro del mouse su un modello registrato nel nodo *modelli* e selezionare **Distribuisci servizio dal modello registrato**.

### <a name="delete-deployments"></a>Elimina distribuzioni

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **endpoint** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sulla distribuzione che si desidera rimuovere e selezionare **Rimuovi servizio**.
1. Verrà visualizzato un messaggio di conferma che si desidera rimuovere il servizio. Selezionare **OK**.

### <a name="manage-deployments"></a>Gestisci distribuzioni

Oltre a creare ed eliminare le distribuzioni, è possibile visualizzare e modificare le impostazioni associate alla distribuzione.

1. Espandere il nodo sottoscrizione che contiene l'area di lavoro.
1. Espandere il nodo **endpoint** all'interno dell'area di lavoro.
1. Fare clic con il pulsante destro del mouse sulla distribuzione che si desidera gestire:
    - Per modificare le impostazioni, selezionare **modifica servizio**.
        - Nell'editor verrà visualizzato un file di configurazione per la distribuzione. Se si è soddisfatti della configurazione, selezionare **Salva e continua** oppure aprire il riquadro comandi vs code (**visualizzazione > tavolozza comandi**) e digitare **Azure ml: Salva e continua**.
    - Per visualizzare le impostazioni di configurazione della distribuzione, selezionare **Visualizza proprietà del servizio**.

## <a name="next-steps"></a>Passaggi successivi

Eseguire il [training di un modello di classificazione delle immagini](tutorial-train-deploy-image-classification-model-vscode.md) con l'estensione vs code.