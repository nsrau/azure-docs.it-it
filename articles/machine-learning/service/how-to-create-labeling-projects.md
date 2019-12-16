---
title: Creare un progetto di etichettatura dei dati
titleSuffix: Azure Machine Learning
description: Informazioni su come creare ed eseguire i progetti di etichettatura per aggiungere tag ai dati per Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: bdedc3ee0c76eafc4b4bc95116fb5be846f4bee5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978237"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Creare un progetto di etichettatura dei dati ed esportare le etichette 

L'etichettatura di un volume elevato di dati in progetti di Machine Learning è spesso un'operazione complicata. I progetti che includono un componente visione artificiale, ad esempio la classificazione di immagini o il rilevamento di oggetti, richiedono in genere le etichette per migliaia di immagini.
 
[Azure Machine Learning](https://ml.azure.com/) offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. È possibile coordinare i dati, le etichette e i membri del team per gestire in modo efficiente le attività di etichettatura. Machine Learning supporta la classificazione di immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Machine Learning tiene traccia dello stato di avanzamento e mantiene la coda delle attività di etichettatura incomplete. Gli etichettatori non necessitano di un account Azure per partecipare. Una volta eseguita l'autenticazione con il proprio account Microsoft personale o con [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), possono eseguire il numero di attività di etichettatura desiderato, a seconda del tempo a disposizione.

In Machine Learning è possibile avviare e arrestare il progetto, aggiungere e rimuovere utenti e team, nonché monitorare lo stato di avanzamento. È possibile esportare i dati etichettati in formato COCO o come set di dati di Azure Machine Learning.

> [!Important]
> Attualmente sono supportati solo i progetti etichettatura per la classificazione di immagini e l'identificazione degli oggetti. Le immagini dei dati devono inoltre essere disponibili in un archivio dati BLOB di Azure. Se non si dispone di un archivio dati esistente, è possibile caricare le immagini durante la creazione del progetto. 

L'articolo spiega come:

> [!div class="checklist"]
> * Creare un progetto
> * Specificare i dati e la struttura del progetto
> * Gestire i team e le persone che collaborano al progetto
> * Eseguire e monitorare il progetto
> * Esportare le etichette


## <a name="prerequisites"></a>Prerequisiti

* I dati da etichettare, inclusi in file locali o in una risorsa di archiviazione di Azure.
* I set di etichette da applicare.
* Le istruzioni per l'etichettatura.
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Creare un progetto di etichettatura

I progetti di etichettatura vengono amministrati da Azure Machine Learning. La pagina **Progetti di etichettatura** consente di gestire i progetti, i team e le persone. A un progetto sono assegnati uno o più team e a un team sono assegnati uno o più utenti.

Se i dati si trovano già nell'archiviazione BLOB di Azure, è necessario renderli disponibili come archivio dati prima di creare il progetto di etichettatura. Per altre informazioni, vedere [Creare e registrare gli archivi dati](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores).

Per creare un progetto, scegliere **Aggiungi il progetto**. Assegnare al progetto un nome appropriato e selezionare **Tipo di attività di etichettatura**.

![Creazione guidata progetto di assegnazione di etichette](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Scegliere **Classificazione delle immagini multi-etichetta** per i progetti in cui applicare a un'immagine *una o più* etichette da un set di classi. Ad esempio, la foto di un cane potrebbe essere etichettata sia con *cane* che con *giorno*.
* Scegliere **Classificazione delle immagini multi-classe** per i progetti in cui applicare a un'immagine solo una *classe singola* da un set di classi.
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

È importante spiegare chiaramente l'attività di etichettatura. Nella pagina **Istruzioni di etichettatura** è possibile aggiungere un collegamento a un sito esterno per tali istruzioni. Le istruzioni devono essere basate sull'attività e appropriate per i destinatari. Considerare queste domande:

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

>[!NOTE]
> Tenere presente che gli etichettatori potranno selezionare le prime 9 etichette usando i tasti numerici 1-9.

## <a name="initialize-the-labeling-project"></a>Inizializzare il progetto di etichettatura

Dopo l'inizializzazione del progetto di etichettatura, alcuni aspetti non saranno modificabili. Non è possibile cambiare il tipo di attività o il set di dati. È invece *possibile* modificare le etichette e l'URL per la descrizione dell'attività. Esaminare attentamente le impostazioni prima di creare il progetto. Dopo aver inviato il progetto, si tornerà nella pagina iniziale **Etichettatura** in cui verrà mostrato il progetto con lo stato **Inizializzazione**. Questa pagina non viene aggiornata automaticamente. Quindi, dopo una pausa, aggiornare manualmente la pagina per visualizzare lo stato del progetto come **Creato**.

## <a name="manage-teams-and-people"></a>Gestire team e persone

Per impostazione predefinita, per ogni progetto di etichettatura che si crea si viene inseriti come membri di un nuovo team. Tuttavia, è anche possibile condividere i team tra progetti. Inoltre, ai progetti possono essere assegnati più team. Per creare un team, selezionare **Aggiungi team** nella pagina **Team**.

Le persone vengono invece gestite nella pagina **Persone**. Aggiungere e rimuovere persone tramite l'indirizzo di posta elettronica. Ogni etichettatore dovrà eseguire l'autenticazione usando l'account Microsoft o Azure Active Directory, se disponibile.  

Dopo aver aggiunto una persona, è possibile assegnarla a uno o più team: Passare alla pagina **Team**, selezionare il team e quindi selezionare **Assegna persone** o **Rimuovi persone**.

Per inviare un messaggio di posta elettronica al team, selezionare il team per visualizzare la pagina **Dettagli del team**. In questa pagina selezionare **Invia messaggio di posta elettronica al team** per aprire una bozza di messaggio con gli indirizzi di tutti i membri del team.

## <a name="run-and-monitor-the-project"></a>Eseguire e monitorare il progetto

Una volta inizializzato il progetto, Azure inizierà a eseguirlo. Selezionare il progetto nella pagina principale **Etichettatura** per passare a **Dettagli del progetto**. La scheda **Dashboard** indicherà lo stato di avanzamento dell'attività di etichettatura.

Nella scheda **Dati** è possibile esaminare il set di dati e rivedere i dati etichettati. Se vengono individuati dati etichettati in modo errato, selezionarli e fare clic su **Rifiuta**. Le etichette verranno rimosse e i dati verranno inseriti nuovamente nella coda dei dati non etichettati.

Usare la scheda **Team** per assegnare o annullare l'assegnazione di team a questo progetto.

Per sospendere o riavviare il progetto, selezionare il pulsante **Sospendi**/**Avvia**. È possibile etichettare i dati solo quando il progetto è in esecuzione.

È possibile etichettare i dati direttamente dalla pagina **Dettagli del progetto** selezionando **Etichetta dati**.

## <a name="export-the-labels"></a>Esportare le etichette

In qualsiasi momento, è possibile esportare i dati delle etichette per la sperimentazione di Machine Learning. Le etichette delle immagini possono essere esportate in [formato COCO](http://cocodataset.org/#format-data) o come set di dati di Azure Machine Learning. Usare il pulsante **Esporta** nella pagina **Dettagli del progetto** del progetto di etichettatura.

Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure Machine Learning in una cartella all'interno di *export/coco*. È possibile accedere al set di dati di Azure Machine Learning esportato nella sezione **Set di dati** di Machine Learning. La pagina Dettagli del set di dati fornisce anche il codice di esempio per accedere alle etichette da Python.

![Set di dati esportato](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passaggi successivi

* Etichettare le immagini per la [classificazione di immagini o il rilevamento di oggetti](how-to-label-images.md)
* Altre informazioni su [Azure Machine Learning e Machine Learning Studio (versione classica)](../compare-azure-ml-to-studio-classic.md)
