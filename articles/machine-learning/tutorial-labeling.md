---
title: 'Esercitazione: Creare un progetto di etichettatura per la classificazione delle immagini'
titleSuffix: Azure Machine Learning
description: Informazioni su come gestire il processo di etichettatura delle immagini in modo che possano essere usate in modelli di classificazione delle immagini multi-classe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.openlocfilehash: 5f3a73ed6c7843c13d35a91a75189fe9dd8d4dbd
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84628371"
---
# <a name="tutorial-create-a-labeling-project-preview-for-multi-class-image-classification"></a>Esercitazione: Creare un progetto di etichettatura (anteprima) per la classificazione delle immagini multi-classe 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa esercitazione illustra come gestire il processo di etichettatura (detto anche assegnazione di tag) di immagini da usare come dati per la creazione di modelli di Machine Learning. La funzionalità di etichettatura dei dati in Azure Machine Learning è in anteprima pubblica.

Se si vuole eseguire il training di un modello di Machine Learning per classificare le immagini, sono necessarie centinaia o addirittura migliaia di immagini etichettate correttamente.  Azure Machine Learning consente di gestire lo stato di avanzamento del team privato di esperti di settore che etichettano i dati.
 
In questa esercitazione si useranno immagini di gatti e cani.  Dato che ogni immagine contiene un gatto o un cane, si tratta di un progetto di etichettatura *multi-classe*. Si apprenderà come:

> [!div class="checklist"]
>
> * Creare un account di archiviazione di Azure e caricare immagini nell'account.
> * Creare un'area di lavoro di Azure Machine Learning.
> * Creare un progetto di etichettatura delle immagini multi-classe.
> * Etichettare i dati.  Questa attività può essere eseguita dall'utente corrente o dagli etichettatori.
> * Completare il progetto esaminando ed esportando i dati.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nel servizio.

Per creare un'area di lavoro è necessario usare il portale di Azure, una console basata sul Web per la gestione delle risorse di Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Avviare un progetto di etichettatura

Il progetto di etichettatura dei dati verrà gestito in Azure Machine Learning Studio, un'interfaccia consolidata che include strumenti di Machine Learning che consentono di eseguire scenari di data science a professionisti della materia di qualsiasi livello di competenza. Studio non è supportato nei browser Internet Explorer.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare la sottoscrizione e l'area di lavoro create.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Creare un archivio dati

Gli archivi dati di Azure Machine Learning vengono usati per archiviare le informazioni di connessione, come l'ID sottoscrizione e l'autorizzazione del token. In questo caso si usa un archivio dati per connettersi all'account di archiviazione contenente le immagini per l'esercitazione.

1. Sul lato sinistro dell'area di lavoro selezionare **Archivi dati**.

1. Selezionare **+ Nuovo archivio dati**.

1. Compilare il modulo con le impostazioni seguenti:

    Campo|Descrizione 
    ---|---
    Nome dell'archivio dati | Assegnare un nome all'archivio dati.  Qui si userà **labeling_tutorial**.
    Tipi di archivio dati | Selezionare il tipo di archivio.  Qui si userà **Archivio BLOB di Azure**, la risorsa di archiviazione preferita per le immagini.
    Metodo di selezione dell'account | Selezionare **Immetti manualmente**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Tipo di autenticazione | Selezionare **Token di firma di accesso condiviso**.
    Chiave account | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selezionare **Crea** per creare l'archivio dati.

### <a name="create-a-labeling-project"></a>Creare un progetto di etichettatura

Ora che si ha l'accesso ai dati da etichettare, creare il progetto di etichettatura.

1. Nella parte superiore della pagina selezionare **Progetti**.

1. Selezionare **+ Aggiungi progetto**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Creare un progetto":::

### <a name="project-details"></a>Dettagli del progetto

1. Per il modulo **Dettagli del progetto** usare l'input seguente:

    Campo|Descrizione 
    ---|---
    Project name (Nome progetto) | Assegnare un nome al progetto.  Qui si userà **tutorial-cats-n-dogs**.
    Tipo di attività di etichettatura | Selezionare **Classificazione delle immagini multi-classe**.  
    
    Selezionare **Avanti** per continuare a creare il progetto.

### <a name="select-or-create-a-dataset"></a>Selezionare o creare un set di dati

1.   Nel modulo **Seleziona o crea un set di dati** selezionare la seconda opzione **Crea un set di dati** e quindi il collegamento **Da archivio dati**.

1. Per il modulo **Crea set di dati da archivio dati** usare l'input seguente:

    1. Nel modulo **Informazioni di base** aggiungere un nome. Qui si userà **images-for-tutorial**.  Aggiungere una descrizione, se si vuole.  Fare quindi clic su **Avanti**.
    1. Nel modulo **Selezione archivio dati** usare l'elenco a discesa per selezionare **Archivio dati creato in precedenza** e quindi, ad esempio, **tutorial_images (Azure Blob Storage)** (tutorial_images - archivio BLOB di Azure)
    1. Successivamente, sempre nel modulo **Selezione archivio dati** selezionare **Sfoglia** e quindi **MultiClass - DogsCats**.  Selezionare **Salva** per usare **/MultiClass - DogsCats** come percorso.
    1. Selezionare **Avanti** per confermare i dettagli e quindi **Crea** per creare il set di dati.
    1. Selezionare il cerchio accanto al nome del set di dati nell'elenco, ad esempio **images-for-tutorial**.

1. Selezionare **Avanti** per continuare a creare il progetto.

### <a name="incremental-refresh"></a>Aggiornamento incrementale

Se si prevede di aggiungere nuove immagini al set di dati, l'aggiornamento incrementale troverà queste nuove immagini e le aggiungerà al progetto.  Quando si abilita questa funzionalità, il progetto verificherà periodicamente la presenza di nuove immagini.  Poiché in questa esercitazione non verranno aggiunte nuove immagini all'archivio dati, lasciare deselezionata questa funzionalità.

Selezionare **Avanti** per continuare.

### <a name="label-classes"></a>Classi di etichette

1. Nel modulo **Classi di etichette** digitare un nome di etichetta e quindi selezionare **+ Aggiungi etichetta** per digitare l'etichetta successiva.  Per questo progetto, le etichette sono **Cat**, **Dog** e **Uncertain**.

1. Dopo aver aggiunto tutte le etichette, selezionare **Avanti**.

### <a name="labeling-instructions"></a>Istruzioni di etichettatura

1. Nel modulo **Istruzioni di etichettatura** è possibile inserire un collegamento a un sito Web contenente istruzioni dettagliate per gli etichettatori.  Per questa esercitazione verrà lasciato vuoto.

1. È anche possibile aggiungere una breve descrizione dell'attività direttamente nel modulo.  Digitare **Esercitazione sull'etichettatura - Gatti e cani**.

1. Selezionare **Avanti**.

1. Nel modulo **Etichettatura assistita da ML** lasciare la casella di controllo deselezionata. Per l'etichettatura assistita da ML sono necessari più dati rispetto a quelli usati in questa esercitazione.

1. Selezionare **Create project** (Crea progetto).

Questa pagina non viene aggiornata automaticamente. Dopo una pausa, aggiornare manualmente la pagina finché lo stato del progetto non passa a **Creato**.

## <a name="start-labeling"></a>Avviare l'etichettatura

Ora che sono stati configurati le risorse di Azure e un progetto di etichettatura dei dati, aggiungere le etichette ai dati.

### <a name="tag-the-images"></a>Assegnare tag alle immagini

In questa parte dell'esercitazione si passerà dal ruolo di *amministratore di progetto* a quello di *etichettatore*.  Chiunque abbia accesso come collaboratore all'area di lavoro può diventare un'etichettatore.

1. In [Machine Learning Studio](https://ml.azure.com) selezionare **Etichettatura dei dati** sul lato sinistro per trovare il progetto.  

1. Selezionare il nome del progetto nell'elenco.

1. Sotto il nome del progetto selezionare **Etichetta dati**.

1. Leggere le istruzioni, quindi selezionare **Attività**.

1. Selezionare un'immagine di anteprima sulla destra per visualizzare il numero di immagini che si vuole etichettare in un'unica operazione. Prima di procedere, è necessario etichettare tutte queste immagini. Cambiare il layout solo quando si apre una nuova pagina di dati non etichettati. Quando si passa da un layout all'altro, l'attività di aggiunta dei tag in corso della pagina viene cancellata.

1. Selezionare una o più immagini e quindi un tag da applicare alla selezione. Il tag verrà visualizzato sotto l'immagine.  Continuare a selezionare e assegnare tag a tutte le immagini nella pagina.  Per selezionare tutte le immagini visualizzate contemporaneamente, scegliere **Seleziona tutto**. Selezionare almeno un'immagine per applicare un tag.


    > [!TIP]
    > È possibile selezionare i primi nove tag usando i tasti numerici della tastiera.

1. Dopo aver assegnato tag a tutte le immagini della pagina, selezionare **Invia** per inviare le etichette.

    ![Assegnazione di tag alle immagini](media/tutorial-labeling/catsndogs.gif)

1. Dopo aver inviato i tag per i dati a disposizione, Azure aggiornerà la pagina con un nuovo set di immagini dalla coda di lavoro.

## <a name="complete-the-project"></a>Completare il progetto

Tornare ora al ruolo di *amministratore di progetto* per il progetto di etichettatura.

Per il responsabile può essere opportuno esaminare il lavoro dell'etichettatore.  

### <a name="review-labeled-data"></a>Esaminare i dati etichettati

1. In [Machine Learning Studio](https://ml.azure.com) selezionare **Etichettatura dei dati** sul lato sinistro per trovare il progetto.  

1. Selezionare il collegamento con il nome del progetto.

1. Nel dashboard viene visualizzato lo stato di avanzamento del progetto.

1. Nella parte superiore della pagina selezionare **Dati**.

1. Sul lato sinistro selezionare **Dati con etichetta** per visualizzare le immagini con tag.  

1. Quando non si è d'accordo in merito a un'etichetta, selezionare l'immagine e quindi **Rifiuta** nella parte inferiore della pagina.  I tag verranno rimossi e l'immagine verrà reinserita nella coda delle immagini senza etichetta.

### <a name="export-labeled-data"></a>Esportare i dati etichettati

In qualsiasi momento, è possibile esportare i dati delle etichette per la sperimentazione di Machine Learning. Gli utenti spesso ripetono più volte l'esportazione ed eseguono il training di diversi modelli, anziché attendere che tutte le immagini siano etichettate.

Le etichette delle immagini possono essere esportate in [formato COCO](http://cocodataset.org/#format-data) o come set di dati di Azure Machine Learning. Il formato del set di dati ne facilita l'uso per il training in Azure Machine Learning.  

1. In [Machine Learning Studio](https://ml.azure.com) selezionare **Etichettatura dei dati** sul lato sinistro per trovare il progetto.  

1. Selezionare il collegamento con il nome del progetto.

1. Selezionare **Esporta** e scegliere **Esporta come set di dati di Azure ML**. 

    Lo stato dell'esportazione verrà visualizzato immediatamente sotto il pulsante **Esporta**. 

1. Al termine dell'esportazione delle etichette, selezionare **Set di dati** sul lato sinistro per visualizzare i risultati.

## <a name="clean-up-resources"></a>Pulire le risorse


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si sono etichettate le immagini.  Usare ora i dati etichettati:

> [!div class="nextstepaction"]
> [Eseguire il training di un modello di Machine Learning di riconoscimento delle immagini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
