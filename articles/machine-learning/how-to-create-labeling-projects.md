---
title: Creare un progetto di etichettatura dei dati
titleSuffix: Azure Machine Learning
description: Informazioni su come creare ed eseguire i progetti di etichettatura per aggiungere tag ai dati per Machine Learning.  Gli strumenti includono l'etichettatura assistita da ML o l'etichettatura Human-in-the-loop per facilitare l'attività.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/09/2020
ms.openlocfilehash: 40c31d4dd4a6c675691f75d3717f7865d6b847f7
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171557"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Creare un progetto di etichettatura dei dati ed esportare le etichette 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

L'etichettatura di un volume elevato di dati in progetti di Machine Learning è spesso un'operazione complicata. I progetti che includono un componente visione artificiale, ad esempio la classificazione di immagini o il rilevamento di oggetti, richiedono in genere le etichette per migliaia di immagini.
 
[Azure Machine Learning](https://ml.azure.com/) offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura (anteprima pubblica). È possibile coordinare i dati, le etichette e i membri del team per gestire in modo efficiente le attività di etichettatura. Machine Learning supporta la classificazione di immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Azure Machine Learning tiene traccia dello stato di avanzamento e mantiene la coda delle attività di etichettatura non completate.

È possibile avviare e arrestare il progetto e monitorare lo stato di avanzamento dell'etichettatura. È possibile esportare i dati etichettati in formato COCO o come set di dati di Azure Machine Learning.

> [!Important]
> Attualmente sono supportati solo i progetti etichettatura per la classificazione di immagini e l'identificazione degli oggetti. Le immagini dei dati devono inoltre essere disponibili in un archivio dati BLOB di Azure. Se non si dispone di un archivio dati esistente, è possibile caricare le immagini durante la creazione del progetto.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto
> * Specificare i dati e la struttura del progetto
> * Eseguire e monitorare il progetto
> * Esportare le etichette


## <a name="prerequisites"></a>Prerequisiti


* I dati da etichettare, inclusi in file locali o nell'archiviazione BLOB di Azure.
* I set di etichette da applicare.
* Le istruzioni per l'etichettatura.
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Creare un progetto di etichettatura

I progetti di etichettatura vengono amministrati da Azure Machine Learning. La pagina **Progetti di etichettatura** consente di gestire i progetti.

Se i dati si trovano già nell'archiviazione BLOB di Azure, è necessario renderli disponibili come archivio dati prima di creare il progetto di etichettatura. Per un esempio dell'uso di un archivio dati, vedere [Esercitazione: Creare il primo progetto di etichettatura per la classificazione delle immagini](tutorial-labeling.md).

Per creare un progetto, scegliere **Aggiungi il progetto**. Assegnare al progetto un nome appropriato e selezionare **Tipo di attività di etichettatura**.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Creazione guidata progetto di etichettatura":::

* Scegliere **Classificazione delle immagini multi-classe** per i progetti in cui applicare a un'immagine solo una *classe singola* da un set di classi.
* Scegliere **Classificazione delle immagini multi-etichetta** per i progetti in cui applicare a un'immagine *una o più* etichette da un set di classi. Ad esempio, la foto di un cane potrebbe essere etichettata sia con *cane* che con *giorno*.
* Scegliere **Identificazione oggetto (rettangolo di selezione)** per i progetti in cui assegnare una classe e un rettangolo di selezione a ogni oggetto all'interno di un'immagine.

Selezionare **Avanti** quando si è pronti per procedere.

## <a name="specify-the-data-to-label"></a>Specificare i dati da etichettare

Se è già stato creato un set di dati contenente i dati da usare, è possibile selezionarlo nell'elenco a discesa **Seleziona un set di dati esistente**. In alternativa, selezionare **Crea un set di dati** per usare un archivio dati di Azure esistente o caricare i file locali.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Creare un set di dati da un archivio dati di Azure

In molti casi, è sufficiente caricare i file locali. Tuttavia [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) offre un modo più rapido e affidabile per trasferire una grande quantità di dati. È consigliabile usare Storage Explorer come modalità predefinita per lo spostamento dei file.

Per creare un set di dati da dati già archiviati nell'archiviazione BLOB di Azure:

1. Selezionare **Crea un set di dati** > **Da archivio dati**.
1. Assegnare un **Nome** al set di dati.
1. Scegliere **File** come **Tipo di set di dati**.  
1. Selezionare l'archivio dati.
1. Se i dati si trovano in una sottocartella all'interno dell'archiviazione BLOB, scegliere **Sfoglia** per selezionare il percorso.
    * Aggiungere "/**" al percorso per includere tutti i file nelle sottocartelle del percorso selezionato.
    * Aggiungere "* */* .*" per includere tutti i dati inclusi nel contenitore corrente e nelle relative sottocartelle.
1. Fornire una descrizione del set di dati.
1. Selezionare **Avanti**.
1. Confermare i dettagli. Selezionare **Indietro** per modificare le impostazioni oppure **Crea** per creare il set di dati.

> [!NOTE]
> I dati scelti vengono caricati nel progetto.  L'aggiunta di altri dati all'archivio dati non verrà visualizzata in questo progetto dopo la creazione del progetto.  

### <a name="create-a-dataset-from-uploaded-data"></a>Creare un set di dati dai dati caricati

Per caricare direttamente i dati:

1. Selezionare **Crea un set di dati** > **Da file locali**.
1. Assegnare un **Nome** al set di dati.
1. Scegliere "File" come **Tipo di set di dati**.
1. *Facoltativo:* Selezionare **Impostazioni avanzate** per personalizzare l'archivio dati, il contenitore e il percorso dei dati.
1. Selezionare **Sfoglia** per selezionare i file locali da caricare.
1. Fornire una descrizione del set di dati.
1. Selezionare **Avanti**.
1. Confermare i dettagli. Selezionare **Indietro** per modificare le impostazioni oppure **Crea** per creare il set di dati.

I dati vengono caricati nell'archivio BLOB predefinito ("workspaceblobstore") dell'area di lavoro di Machine Learning.

## <a name="specify-label-classes"></a>Specificare le classi di etichetta

Nella pagina **Classi di etichetta** specificare il set di classi usato per classificare i dati. Eseguire questa operazione con attenzione, perché la capacità di scegliere tra le classi influisce sull'accuratezza e sulla velocità degli etichettatori. Ad esempio, invece di includere genere e specie completi per le piante o gli animali, usare i codici di campo o abbreviare il genere.

Immettere un'etichetta per riga. Usare il pulsante **+** per aggiungere una nuova riga. Se le etichette sono più di 3 o 4 etichette, ma meno di 10, è possibile anteporre ai nomi i numeri ("1:", "2:") in modo che gli etichettatori possano usare i tasti numerici per velocizzare il lavoro.

## <a name="describe-the-labeling-task"></a>Descrivere l'attività di etichettatura

È importante spiegare chiaramente l'attività di etichettatura. Nella pagina **Istruzioni di etichettatura** è possibile aggiungere un collegamento a un sito esterno per tali istruzioni o fornire le istruzioni nella casella di modifica della pagina. Le istruzioni devono essere basate sull'attività e appropriate per i destinatari. Considerare queste domande:

* Che cosa sono le etichette visualizzate e come verranno scelte? Esiste un testo di riferimento da consultare?
* Cosa è necessario fare se nessuna etichetta risulta appropriata?
* Cosa è necessario fare se più etichette risultano appropriate?
* Qual è la soglia di attendibilità da applicare a un'etichetta? In caso di incertezza, occorre fornire l'"ipotesi migliore"?
* Cosa è necessario fare con gli oggetti di interesse parzialmente bloccati o sovrapposti?
* Cosa è necessario fare se un oggetto di interesse è tagliato dal bordo dell'immagine?
* Cosa si deve fare dopo aver inviato un'etichetta se si ritiene di aver commesso un errore?

Per i rettangoli di selezione, le domande importanti sono:

* Come viene definito il rettangolo di selezione per questa attività? Deve trovarsi completamente all'interno dell'oggetto o all'esterno? Deve essere ritagliato il più possibile o è accettabile lasciare uno spazio vuoto?
* Qual è il livello di attenzione e coerenza che l'etichettatore dovrà applicare per la definizione dei rettangoli di selezione?
* Come etichettare l'oggetto visualizzato parzialmente nell'immagine? 
* Come etichettare l'oggetto parzialmente coperto da un altro oggetto?

>[!NOTE]
> Tenere presente che gli etichettatori potranno selezionare le prime 9 etichette usando i tasti numerici 1-9.

## <a name="use-ml-assisted-labeling"></a>Usare l'etichettatura assistita da ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

La pagina **Etichettatura assistita da ML** consente di attivare modelli di Machine Learning automatici per accelerare l'attività di etichettatura. All'inizio del progetto di etichettatura, le immagini vengono mescolate in modo casuale per ridurre le potenziali distorsioni. Tuttavia, eventuali distorsioni presenti nel set di dati si rifletteranno nel modello con training. Se, ad esempio, l'80% delle immagini appartiene a una singola classe, circa l'80% dei dati usati per il training del modello sarà di tale classe. Questo training non include l'apprendimento attivo.

Selezionare *Abilita etichettatura assistita da ML* e specificare una GPU per abilitare l'etichettatura assistita, costituita da due fasi:
* Clustering
* Pre-etichettatura

Il numero esatto di immagini etichettate necessarie per avviare l'etichettatura assistita non è un numero fisso.  Può variare in modo significativo da un progetto di etichettatura a un altro. Per alcuni progetti, talvolta è possibile visualizzare le attività di pre-etichettatura o di clustering dopo che sono state etichettate manualmente 300 immagini. L'etichettatura assistita da ML usa una tecnica chiamata *Apprendimento induttivo*, che usa un modello con pre-training per avviare rapidamente il processo di training. Se le classi del set di dati sono simili a quelle del modello con pre-training, è possibile che le pre-etichette siano disponibili solo dopo alcune centinaia di immagini etichettate manualmente. Se il set di dati è significativamente diverso dai dati usati per il pre-training del modello, potrebbe essere necessario molto più tempo.

Poiché le etichette finali si basano ancora sull'input dell'etichettatore, questa tecnologia viene a volte chiamata etichettatura *human in the loop*.

### <a name="clustering"></a>Clustering

Dopo l'invio di un determinato numero di etichette, il modello di Machine Learning per la classificazione delle immagini inizia a raggruppare le immagini simili.  Queste immagini simili vengono presentate all'etichettatore nella stessa schermata per velocizzare l'assegnazione manuale di tag. Il clustering è particolarmente utile quando l'etichettatore visualizza una griglia di 4, 6 o 9 immagini. 

Dopo aver eseguito il training di un modello di Machine Learning sui dati etichettati manualmente, il modello viene troncato all'ultimo livello completamente connesso. Le immagini non etichettate vengono quindi passate attraverso il modello troncato in un processo comunemente noto come "incorporamento" o "definizione delle funzionalità". Questa operazione incorpora ogni immagine in uno spazio altamente dimensionale definito da questo livello del modello. Le immagini vicine più prossime nello spazio vengono usate per le attività di clustering. 

La fase di clustering non viene visualizzata per i modelli di rilevamento degli oggetti.

### <a name="prelabeling"></a>Pre-etichettatura

Dopo l'invio di una quantità sufficiente di etichette di immagini, viene usato un modello di classificazione per prevedere i tag di immagine oppure un modello di rilevamento degli oggetti per prevedere i rettangoli di selezione. L'etichettatore ora visualizza le pagine che contengono etichette stimate già presenti in ogni immagine. Per il rilevamento degli oggetti, vengono visualizzati anche i rettangoli di selezione previsti. L'attività consiste quindi nell'esaminare le previsioni e correggere le eventuali immagini etichettate in modo non corretto prima di inviare la pagina.  

Dopo avere eseguito il training di un modello di Machine Learning sui dati etichettati manualmente, il modello viene valutato su un set di test di immagini etichettate manualmente per determinarne l'accuratezza in base a una varietà di soglie di attendibilità diverse. Questo processo di valutazione viene usato per determinare una soglia di attendibilità oltre la quale il modello è sufficientemente accurato per mostrare le pre-etichette. Il modello viene quindi valutato in base ai dati non etichettati. Le immagini con stime più attendibili di questa soglia vengono usate per la pre-etichettatura.

> [!NOTE]
> L'etichettatura assistita da ML è disponibile **solo** nelle aree di lavoro Enterprise Edition.

## <a name="initialize-the-labeling-project"></a>Inizializzare il progetto di etichettatura

Dopo l'inizializzazione del progetto di etichettatura, alcuni aspetti non saranno modificabili. Non è possibile cambiare il tipo di attività o il set di dati. È invece *possibile* modificare le etichette e l'URL per la descrizione dell'attività. Esaminare attentamente le impostazioni prima di creare il progetto. Dopo aver inviato il progetto, si tornerà nella pagina iniziale **Etichettatura dei dati** in cui verrà mostrato il progetto con lo stato **Inizializzazione**. Questa pagina non viene aggiornata automaticamente. Quindi, dopo una pausa, aggiornare manualmente la pagina per visualizzare lo stato del progetto come **Creato**.

## <a name="run-and-monitor-the-project"></a>Eseguire e monitorare il progetto

Una volta inizializzato il progetto, Azure inizierà a eseguirlo. Selezionare il progetto nella pagina principale **Etichettatura dei dati** per passare a **Dettagli del progetto**. La scheda **Dashboard** indicherà lo stato di avanzamento dell'attività di etichettatura.

Nella scheda **Dati** è possibile esaminare il set di dati e rivedere i dati etichettati. Se vengono individuati dati etichettati in modo errato, selezionarli e fare clic su **Rifiuta**. Le etichette verranno rimosse e i dati verranno inseriti nuovamente nella coda dei dati non etichettati.

Per sospendere o riavviare il progetto, selezionare il pulsante **Sospendi**/**Avvia**. È possibile etichettare i dati solo quando il progetto è in esecuzione.

È possibile etichettare i dati direttamente dalla pagina **Dettagli del progetto** selezionando **Etichetta dati**.

## <a name="add-new-label-class-to-a-project"></a>Aggiungere una nuova classe di etichetta a un progetto

Durante il processo di etichettatura, si potrebbe rilevare che sono necessarie etichette aggiuntive per classificare le immagini.  Ad esempio, si potrebbe scegliere di aggiungere un'etichetta "Sconosciuto" o "Altro" per indicare immagini confuse.

Usare questi passaggi per aggiungere una o più etichette a un progetto:

1. Selezionare il progetto nella pagina principale **Etichettatura dei dati**.
1. Nella parte superiore della pagina selezionare **Sospendi** per arrestare l'attività degli etichettatori.
1. Selezionare la scheda **Dettagli**.
1. Nell'elenco a sinistra selezionare **Classi di etichetta**.
1. All'inizio dell'elenco selezionare **+ Aggiungi etichette** ![Aggiungere un'etichetta](media/how-to-create-labeling-projects/add-label.png)
1. Nel modulo aggiungere la nuova etichetta e scegliere come procedere.  Poiché le etichette disponibili per un'immagine sono state cambiate, scegliere come trattare i dati già etichettati:
    * Ricominciare rimuovendo tutte le etichette esistenti.  Scegliere questa opzione se si vuole iniziare a etichettare dall'inizio con il nuovo set completo di etichette. 
    * Ricominciare mantenendo tutte le etichette esistenti.  Scegliere questa opzione per contrassegnare tutti i dati come non etichettati, ma mantenere le etichette esistenti come tag predefinito per le immagini etichettate in precedenza.
    * Continuare mantenendo tutte le etichette esistenti. Scegliere questa opzione per mantenere tutti i dati etichettati così come sono e iniziare a usare la nuova etichetta per i dati non ancora etichettati.
1. Modificare la pagina di istruzioni secondo necessità per le nuove etichette.
1. Dopo aver aggiunto tutte le nuove etichette, nella parte superiore della pagina selezionare **Avvia** per riavviare il progetto.  

## <a name="export-the-labels"></a>Esportare le etichette

In qualsiasi momento, è possibile esportare i dati delle etichette per la sperimentazione di Machine Learning. Le etichette delle immagini possono essere esportate in [formato COCO](http://cocodataset.org/#format-data) o come set di dati di Azure Machine Learning. Usare il pulsante **Esporta** nella pagina **Dettagli del progetto** del progetto di etichettatura.

Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure Machine Learning in una cartella all'interno di *export/coco*. È possibile accedere al set di dati di Azure Machine Learning esportato nella sezione **Set di dati** di Machine Learning. La pagina Dettagli del set di dati fornisce anche il codice di esempio per accedere alle etichette da Python.

![Set di dati esportato](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Creare il primo progetto di etichettatura per la classificazione delle immagini](tutorial-labeling.md).
* Etichettare le immagini per la [classificazione di immagini o il rilevamento di oggetti](how-to-label-images.md)
* Altre informazioni su [Azure Machine Learning e Machine Learning Studio (versione classica)](compare-azure-ml-to-studio-classic.md)
