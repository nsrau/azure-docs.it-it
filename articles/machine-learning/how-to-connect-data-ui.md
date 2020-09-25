---
title: Connettersi ai dati nei servizi di archiviazione in Azure
titleSuffix: Azure Machine Learning
description: Creare archivi dati e set di dati per connettersi in modo sicuro ai dati nei servizi di archiviazione in Azure con Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to
ms.openlocfilehash: feb79a2a077f819cce22925f23f5ed640d05e8d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296672"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Connettersi ai dati con Azure Machine Learning Studio

Questo articolo illustra come accedere ai dati con [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). Connettersi ai dati nei servizi di archiviazione in Azure con [Azure Machine Learning archivi](how-to-access-data.md)dati, quindi creare un pacchetto di tali dati per le attività nei flussi di lavoro di ML con [Azure Machine Learning set](how-to-create-register-datasets.md)di dati.

La tabella seguente definisce e riepiloga i vantaggi degli archivi dati e dei set di dati. 

|Oggetto|Descrizione| Vantaggi|   
|---|---|---|
|Archivi dati| Connettersi in modo sicuro al servizio di archiviazione in Azure archiviando le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token nell' [Key Vault](https://azure.microsoft.com/services/key-vault/) associata all'area di lavoro | Poiché le informazioni sono archiviate in modo sicuro, <br><br> <li> Non &nbsp; inserire &nbsp; le &nbsp; credenziali &nbsp; di autenticazione o le &nbsp; &nbsp; origini dati originali a rischio. <li> Non è più necessario codificarli in modo rigido negli script.
|Set di dati| Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Con i set di impostazioni è possibile, <br><br><li> Accedere ai dati durante il training del modello.<li> Condividere i dati e collaborare con altri utenti.<li> Sfrutta le librerie open source, come Pandas, per l'esplorazione dei dati. | Poiché i set di dati vengono valutati in modo differito e i dati rimangono nella posizione esistente, <br><br><li>Conservare una sola copia dei dati nella risorsa di archiviazione.<li> Non sostenere costi di archiviazione aggiuntivi <li> Non è rischioso modificare involontariamente le origini dati originali.<li>Migliorare le velocità di prestazioni del flusso di lavoro ML. 

Per informazioni sul modo in cui gli archivi dati e i set di dati si adattano al flusso di lavoro di accesso ai dati complessivo di Azure Machine Learning, vedere l'articolo [accesso sicuro ai dati](concept-data.md#data-workflow) .

Per un'esperienza di Code First, vedere gli articoli seguenti per usare [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) per:
* [Connettersi ai servizi di archiviazione di Azure con gli archivi dati](how-to-access-data.md). 
* [Creare Azure Machine Learning set di impostazioni](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning. [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

    -  Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati automaticamente come archivi dati nell'area di lavoro. A questi due elementi vengono assegnati, rispettivamente, i nomi `workspaceblobstore` e `workspacefilestore`. Se l'archiviazione BLOB è sufficiente per le proprie esigenze, `workspaceblobstore` viene impostato come archivio dati predefinito e già configurato per l'utilizzo. In caso contrario, è necessario un account di archiviazione in Azure con un [tipo di archiviazione supportato](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Creare archivi dati

È possibile creare archivi dati da [queste soluzioni di archiviazione di Azure](how-to-access-data.md#matrix). **Per le soluzioni di archiviazione non supportate**e per risparmiare i costi di uscita durante gli esperimenti ml, è necessario [spostare i dati](how-to-access-data.md#move) in una soluzione di archiviazione di Azure supportata. [Altre informazioni sugli archivi dati](how-to-access-data.md). 



Creare un nuovo archivio dati in pochi passaggi con il Azure Machine Learning Studio.

> [!IMPORTANT]
> Se l'account di archiviazione dati si trova in una rete virtuale, sono necessari passaggi di configurazione aggiuntivi per garantire che lo studio abbia accesso ai dati. Vedere [isolamento rete & privacy](how-to-enable-virtual-network.md#machine-learning-studio) per assicurarsi che vengano applicati i passaggi di configurazione appropriati.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **Archivi dati** nel riquadro sinistro in **Gestisci**.
1. Selezionare **+ Nuovo archivio dati**.
1. Completare il modulo per un nuovo archivio dati. Il modulo si aggiorna in modo intelligente in base alle selezioni effettuate per il tipo di archiviazione di Azure e il tipo di autenticazione. Vedere la [sezione accesso e autorizzazioni di archiviazione](#access-validation) per informazioni su dove trovare le credenziali di autenticazione necessarie per popolare il modulo.

Nell'esempio seguente viene illustrato l'aspetto del form quando si crea un **archivio dati BLOB di Azure**:

![Modulo per un nuovo archivio dati](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Creare set di dati

Dopo aver creato un archivio dati, creare un set di dati per interagire con i dati. I set di dati impacchettano i dati in un oggetto utilizzabile differito per le attività di Machine Learning, ad esempio il training. [Altre informazioni sui set di dati](how-to-create-register-datasets.md).

Sono disponibili due tipi di set di dati, filedataset e TabularDataset. 
I [filedataset](how-to-create-register-datasets.md#filedataset) creano riferimenti a uno o più file o URL pubblici. Mentre [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) rappresentano i dati in formato tabulare. 

I passaggi e l'animazione seguenti illustrano come creare un set di dati in [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> I set di impostazioni creati tramite Azure Machine Learning Studio vengono registrati automaticamente nell'area di lavoro.

![Creare un set di dati con l'interfaccia utente](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Per creare un set di dati in studio:
1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **set di impostazioni** nella sezione **Asset** del riquadro sinistro.
1. Selezionare **Crea set di dati** per scegliere l'origine del set di dati. Questa origine può essere costituita da file locali, da un archivio dati, da URL pubblici o da set di dati [aperti di Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Selezionare **tabulare** o **file** per tipo di set di dati.
1. Selezionare **Avanti** per aprire il modulo **archivio dati e selezione file** . In questo modulo è possibile selezionare la posizione in cui salvare il set di dati dopo la creazione, nonché selezionare i file di dati da usare per il set di dati.
    1. Abilitare Ignora convalida se i dati si trova in una rete virtuale. Scopri di più sull' [isolamento e sulla privacy della rete virtuale](how-to-enable-virtual-network.md#machine-learning-studio).
1. Selezionare **Avanti** per popolare i moduli **Impostazioni e anteprima** e **schema** ; vengono popolate in modo intelligente in base al tipo di file ed è possibile configurare ulteriormente il set di dati prima della creazione in questi moduli. 
1. Selezionare **Avanti** per esaminare il modulo **Conferma dettagli** . Controllare le selezioni e creare un profilo dati facoltativo per il set di dati. Altre informazioni sulla [profilatura dei dati](#profile).
1. Selezionare **Crea** per completare la creazione del set di dati.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Profilo dati e anteprima

Dopo aver creato il set di dati, verificare che sia possibile visualizzare il profilo e l'anteprima in studio con i passaggi seguenti. 

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/)
1. Selezionare **set di impostazioni** nella sezione **Asset** del riquadro sinistro.
1. Selezionare il nome del set di dati che si desidera visualizzare. 
1. Selezionare la scheda **Explore** (Esplora). 
1. Selezionare la scheda **Anteprima** o **profilo** . 

![Visualizza profilo set di dati e anteprima](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è idoneo per il Machine Learning. Per le colonne non numeriche, sono incluse solo statistiche di base come min, max e conteggio errori. Per le colonne numeriche, è anche possibile esaminare i momenti statistici e i quantili stimati. 

In particolare, Azure Machine Learning profilo dati del set di dati include:

>[!NOTE]
> Per le funzionalità con tipi irrilevanti vengono visualizzate voci vuote.

|Statistiche|Descrizione
|------|------
|Funzionalità| Nome della colonna sottoposta a riepilogo.
|Profilo| Visualizzazione in linea basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi di valori, mentre i decimali (numerici) avranno istogrammi approssimati. In questo modo è possibile ottenere una visione immediata della distribuzione dei dati.
|Distribuzione dei tipi| Conteggio dei valori in linea dei tipi all'interno di una colonna. I valori Null hanno un proprio tipo, quindi questa visualizzazione è utile per rilevare valori strani o mancanti.
|Type|Tipo dedotto della colonna. I valori possibili includono: stringhe, valori booleani, date e decimali.
|Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.
|Max| Valore massimo della colonna. 
|Conteggio| Numero totale di voci mancanti e non mancanti nella colonna.
|Totale non mancanti| Numero di voci nella colonna che non sono mancanti. Le stringhe vuote e gli errori vengono considerati come valori, quindi non contribuiscono al "totale non mancanti".
|Quantili| Valori approssimati a ogni quantile per una visione generale della distribuzione dei dati.
|Media| Media aritmetica o media della colonna.
|Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
|Variance| Misura della distribuzione dei dati di questa colonna rispetto al relativo valore medio. 
|Asimmetria| Misura della differenza dei dati di questa colonna rispetto a una distribuzione normale.
|Curtosi| Misura della pesantezza delle code dei dati di questa colonna in relazione a una distribuzione normale.

## <a name="storage-access-and-permissions"></a>Accesso e autorizzazioni di archiviazione

Per garantire la connessione sicura al servizio di archiviazione di Azure, Azure Machine Learning necessario avere l'autorizzazione per accedere all'archivio dati corrispondente. Questo accesso dipende dalle credenziali di autenticazione usate per registrare l'archivio dati.

### <a name="virtual-network"></a>Rete virtuale

Se l'account di archiviazione dati si trova in una **rete virtuale**, sono necessari passaggi di configurazione aggiuntivi per garantire che Azure Machine Learning abbia accesso ai dati. Vedere [isolamento rete & privacy](how-to-enable-virtual-network.md#machine-learning-studio) per assicurarsi che vengano applicati i passaggi di configurazione appropriati quando si crea e si registra l'archivio dati.  

### <a name="access-validation"></a>Convalida dell'accesso

**Come parte del processo di creazione e registrazione dell'archivio dati iniziale**, Azure Machine Learning convalida automaticamente il servizio di archiviazione sottostante e l'entità fornita dall'utente (nome utente, entità servizio o token SAS) ha accesso alla risorsa di archiviazione specificata.

**Dopo la creazione dell'archivio dati**, questa convalida viene eseguita solo per i metodi che richiedono l'accesso al contenitore di archiviazione sottostante, **non** ogni volta che vengono recuperati gli oggetti dell'archivio dati. La convalida viene ad esempio eseguita se è necessario scaricare i file dall'archivio dati, mentre se si vuole solo modificare l'archivio dati predefinito, la convalida non viene eseguita.

Per autenticare l'accesso al servizio di archiviazione sottostante, è possibile fornire la chiave dell'account, le firme di accesso condiviso (SAS) o l'entità servizio in base al tipo di archivio dati che si vuole creare. Nella [matrice del tipo di archiviazione](how-to-access-data.md#matrix) sono elencati i tipi di autenticazione supportati che corrispondono a ogni tipo di archivio dati.

È possibile trovare la chiave dell'account, il token SAS e le informazioni sull'entità servizio nel [portale di Azure](https://portal.azure.com).

* Se si prevede di usare una chiave dell'account o un token di firma di accesso condiviso per l'autenticazione, selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare.
  * La pagina **Panoramica** fornisce informazioni come il nome dell'account, il contenitore e il nome della condivisione file.
      1. Per le chiavi dell'account, passare a **Chiavi di accesso** nel riquadro **Impostazioni**.
      1. Per i token di firma di accesso condiviso, passare a **Shared access signatures** (Firme di accesso condiviso) nel riquadro **Impostazioni**.

* Se si prevede di usare un' [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per l'autenticazione, passare alla **registrazioni app** e selezionare l'app che si vuole usare.
    * La pagina di **Panoramica** corrispondente conterrà informazioni obbligatorie come ID tenant e ID client.

> [!IMPORTANT]
> Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure (chiave dell'account o token SAS). Quando si esegue questa operazione, assicurarsi di sincronizzare le nuove credenziali con l'area di lavoro e gli archivi dati connessi. Informazioni su come [sincronizzare le credenziali aggiornate](how-to-change-storage-access-key.md).

### <a name="permissions"></a>Autorizzazioni

Per il contenitore BLOB di Azure e l'archiviazione Azure Data Lake generazione 2, assicurarsi che le credenziali di autenticazione dispongano dell'accesso in **lettura dati BLOB di archiviazione** . Altre informazioni sul [lettore di dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

## <a name="train-with-datasets"></a>Eseguire il training con set di dati

Usare i set di impostazioni negli esperimenti di machine learning per il training dei modelli ML. [Altre informazioni su come eseguire il training con DataSet](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esempio dettagliato di formazione con TabularDatasets e Machine Learning automatizzato](tutorial-first-experiment-automated-ml.md).

* Eseguire [il training di un modello](how-to-train-ml-models.md).

* Per altri esempi di training del set di dati, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
