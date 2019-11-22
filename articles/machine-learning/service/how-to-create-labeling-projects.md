---
title: Ottenere etichette per i dati
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come creare ed eseguire i progetti di etichettatura per aggiungere tag ai dati per Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837490"
---
# <a name="get-labels-for-data"></a>Ottenere etichette per i dati

L'etichettatura di grandi quantità di dati nei progetti di Machine Learning è spesso stata considerata un'attività molto complessa. I progetti di Machine Learning con un componente visione artificiale, ad esempio la classificazione di immagini o il rilevamento di oggetti, richiedono in genere migliaia di immagini ed etichette corrispondenti. 
 
Azure Machine Learning offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. I progetti di etichettatura consentono di coordinare i dati, le etichette e i membri del team, per una gestione più efficiente delle attività di etichettatura. Le attività attualmente supportate sono la classificazione delle immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Azure tiene traccia dello stato di avanzamento e mantiene la coda delle attività di etichettatura incomplete. Agli etichettatori non è richiesto un account Azure per partecipare. Una volta eseguita l'autenticazione con il proprio account Microsoft (account del servizio gestito) o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), è possibile eseguire il numero di attività di etichettatura desiderato, a seconda del tempo a disposizione. Gli etichettatori possono assegnare e modificare le etichette tramite le scelte rapide da tastiera, 

avviare e arrestare il progetto, aggiungere e rimuovere utenti e team e monitorare lo stato di avanzamento. È possibile esportare i dati con etichette in formato COCO o come set di dati di Azure Machine Learning. 

L'articolo spiega come:

> [!div class="checklist"]
> * Creare un progetto
> * Specificare i dati e la struttura del progetto
> * Gestire i team e gli utenti che collaborano al progetto
> * Eseguire e monitorare il progetto
> * Esportare le etichette 

## <a name="prerequisites"></a>Prerequisiti

* Dati da etichettare, in file locali o già in una risorsa di archiviazione di Azure
* Set di etichette da applicare
* Istruzioni per l'etichettatura
* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree) oggi stesso
* Un'area di lavoro di Azure Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Creare un progetto di etichettatura

I progetti di etichettatura vengono amministrati da [Azure Machine Learning](https://ml.azure.com/). La pagina **Progetti di etichettatura** consente di gestire i progetti, i team e gli utenti. A un progetto sono assegnati uno o più team e a un team sono assegnati uno o più utenti. 

Se i dati sono già archiviati nell'archivio BLOB di Azure, è necessario renderli disponibili come archivio dati prima di creare il progetto di etichettatura. Per altre informazioni, vedere [Creare e registrare gli archivi dati](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Per creare un progetto, scegliere **Aggiungi il progetto**. Assegnare al progetto un nome appropriato e selezionare **Tipo di attività di etichettatura**. 

![Creazione guidata progetto di assegnazione di etichette](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Scegliere **Classificazione delle immagini multi-etichetta** per i progetti in cui è possibile applicare a un'immagine **una _o più_** etichette da un set di classi. Ad esempio, la foto di un cane potrebbe essere etichettata sia con *cane* che con *giorno*
* Scegliere **Classificazione delle immagini multi-classe** per i progetti in cui è possibile applicare a un'immagine solo una **classe singola** da un set di classi
* Scegliere **Identificazione oggetto (rettangolo di selezione)** per i progetti in cui l'attività consiste sia nell'assegnazione di una classe a un oggetto all'interno di un'immagine sia nella definizione un rettangolo di selezione intorno all'oggetto

Scegliere **Avanti** quando si è pronti per procedere.

## <a name="specify-data-to-be-labeled"></a>Specificare i dati a cui assegnare le etichette

Se è già stato creato un set di dati contenente i dati, è possibile selezionarlo dall'elenco a discesa **Seleziona un set di dati esistente**. In alternativa, scegliere **Crea un set di dati** per selezionare un archivio dati di Azure esistente o caricare i file locali. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Creare un set di dati da un archivio dati di Azure

Sebbene la scelta di caricare direttamente i file locali sia valida per molti casi d'uso, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è più potente e più veloce per il trasferimento di quantità significative di dati. È consigliabile usare Azure Storage Explorer come modalità predefinita per lo spostamento dei file.

Per creare un set di dati da dati già archiviati nell'archivio BLOB di Azure:

1. Scegliere **Crea set di dati** e **Da archivio dati**
1. Assegnare un **Nome** al set di dati
1. Scegliere "File" come **Tipo di set di dati**  
1. Selezione archivio dati 
1. Se i dati si trovano in una sottocartella all'interno dell'archivio BLOB, scegliere **Sfoglia** per selezionare il percorso. 
    * È inoltre possibile aggiungere `/**` dopo il percorso per includere tutti i file nelle sottocartelle del percorso selezionato
    * Usare `**/*.*` per includere tutti i dati nel contenitore e nelle sottocartelle correnti
1. Fornire una descrizione del set di dati
1. Scegliere **Avanti** 
1. Confermare i dettagli. È possibile scegliere **Indietro** per modificare le impostazioni o scegliere **Crea** per creare il set di dati

### <a name="create-a-dataset-by-uploading-data"></a>Creare un set di dati caricando i dati

Per caricare direttamente i dati:

1. Scegliere **Crea set di dati** e **Da file locali**
1. Assegnare un **Nome** al set di dati
1. Scegliere "File" come **Tipo di set di dati**
1. Se si sceglie **Impostazioni avanzate**, è possibile personalizzare l'archivio dati, il contenitore e il percorso dei dati
1. Scegliere **Sfoglia** per selezionare i file locali da caricare
1. Fornire una descrizione del set di dati
1. Scegliere **Avanti** 
1. Confermare i dettagli. È possibile scegliere **Indietro** per modificare le impostazioni o scegliere **Crea** per creare il set di dati

I dati vengono caricati nell'archivio BLOB predefinito (`workspaceblobstore`) dell'area di lavoro di Azure ML.

## <a name="specify-label-classes"></a>Specificare le classi di etichetta

Nella pagina **Classi di etichetta** specificare il set di classi usato per classificare i dati. Prestare attenzione a queste classi, in quanto l'accuratezza e la velocità degli etichettatori saranno influenzate dalla facilità con cui è possibile scegliere tra di esse. Ad esempio, anziché includere genere e specie completi per le piante o gli animali, potrebbe essere preferibile usare i codici di campo o abbreviare il genere. 

Immettere un'etichetta per riga, usando il pulsante **+** per aggiungere una nuova riga. Se sono presenti più di 3 o 4 etichette, ma meno di 10, è consigliabile aggiungere un prefisso, ad esempio "1:", "2:" e così via, per fornire indicazioni agli etichettatori che usano i tasti numerici per accelerare il lavoro. 

## <a name="describe-the-labeling-task"></a>Descrivere l'attività di etichettatura

È importante spiegare chiaramente l'attività di etichettatura. La pagina **Istruzioni di etichettatura** consente di collegarsi a un sito esterno per ottenere istruzioni da presentare agli etichettatori. Le istruzioni devono essere basate sull'attività e appropriate per i destinatari. 

* Che cosa sono le etichette visualizzate e come scegliere tra di esse? Esiste un testo di riferimento?
* Cosa è necessario fare se nessuna etichetta risulta appropriata? 
* Cosa è necessario fare se più etichette risultano appropriate?
* Qual è la soglia di attendibilità da applicare a un'etichetta? In caso di incertezza, occorre fornire l'"ipotesi migliore"?
* Cosa è necessario fare con gli oggetti di interesse parzialmente bloccati o sovrapposti?
* Cosa è necessario fare se un oggetto di interesse è tagliato dal bordo dell'immagine?
* Cosa accade se si ritiene di aver commesso un errore con un'immagine dopo averla inviata? 

Altre domande importanti relative ai rettangoli di selezione sono:

* Come viene definito il rettangolo di selezione per questa attività? Deve essere completamente interno all'oggetto, ritagliato il più vicino possibile al bordo oppure è accettabile una certa quantità di spazio libero? 
* Qual è il livello di attenzione e coerenza che l'etichettatore dovrà applicare per la definizione dei rettangoli di selezione?

>[!Note]
> Assicurarsi di indicare che l'etichettatore potrà scegliere tra le prime 9 etichette con i tasti numerici da 1 a 9. 

## <a name="initialize-the-labeling-project"></a>Inizializzare il progetto di etichettatura

Una volta inizializzato, alcuni aspetti del progetto di etichettatura non sono modificabili, ad esempio non è possibile modificare il tipo di attività o il set di dati. È possibile modificare le etichette e l'URL per la descrizione dell'attività. Esaminare attentamente le impostazioni prima di creare il progetto. Dopo aver inviato il progetto, si verrà reindirizzati alla pagina iniziale **Etichettatura** in cui verrà mostrato il progetto con lo stato **Inizializzazione**. Questa pagina non viene aggiornata automaticamente, quindi dopo un certo periodo di tempo, se la si aggiorna manualmente il progetto mostrerà lo stato **Creato**. 

## <a name="manage-teams-and-people"></a>Gestire team e persone

A un progetto di etichettatura viene assegnato un team predefinito e l'utente viene aggiunto come membro predefinito. Ogni progetto di etichettatura ottiene un nuovo team predefinito, ma i team possono essere condivisi tra i progetti. Ai progetti possono essere assegnati più team. Per creare un team, scegliere **Aggiungi team** nella pagina **Team**. 

Le persone vengono invece gestite nella pagina **Persone**. È possibile aggiungere e rimuovere gli utenti in base all'indirizzo di posta elettronica. Ogni etichettatore dovrà eseguire l'autenticazione usando l'account Microsoft o Azure Active Directory se disponibile.  

Dopo aver aggiunto una persona, è possibile assegnarla a uno o più team. Passare alla pagina **Team**, selezionare il team specifico a cui si è interessati e quindi usare **Assegna persone** o **Rimuovi persone**, a seconda delle esigenze.

Per inviare un messaggio di posta elettronica a tutti gli utenti del team, è possibile scegliere il team per visualizzare la pagina **Dettagli del team**. In questa pagina il pulsante **Invia messaggio di posta elettronica al team** aprirà l'editor di posta elettronica con gli indirizzi di tutti gli utenti del team.

## <a name="run-and-monitor-the-project"></a>Eseguire e monitorare il progetto

Una volta inizializzato il progetto, Azure inizierà a eseguirlo. Nella pagina principale **Etichettatura** se si fa clic sul progetto, si verrà reindirizzati a **Dettagli del progetto**. La scheda **Dashboard** indicherà lo stato dell'attività di etichettatura. 

Nella scheda **Dati** è possibile esaminare il set di dati e rivedere i dati etichettati. Se vengono individuati dati etichettati in modo errato, è possibile fare clic su **Seleziona** e scegliere **Rifiuta**. Le etichette verranno rimosse e i dati verranno inseriti nuovamente nella coda dei dati non etichettati. 

Nella scheda **Team** è possibile assegnare o annullare l'assegnazione di team a questo progetto. 

Per portare il progetto offline o online, scegliere il pulsante **Sospendi**/**Avvia**, che consente di attivare o disattivare lo stato di esecuzione del progetto.

È possibile etichettare i dati direttamente dalla pagina **Dettagli del progetto** selezionando **Etichetta dati**. È possibile etichettare i dati solo quando il progetto è in esecuzione. 

## <a name="export-the-labels"></a>Esportare le etichette

In qualsiasi momento, è possibile esportare i dati delle etichette per la sperimentazione di Machine Learning. È possibile esportare le etichette delle immagini nel [formato COCO](http://cocodataset.org/#format-data) o come set di dati di Azure ML. Il pulsante **Esporta** è disponibile nella pagina **Dettagli del progetto** del progetto di etichettatura.

Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure ML in una cartella all'interno di **export/coco**. È possibile accedere al set di dati di Azure ML esportato nella sezione **Set di dati** di Azure Machine Learning. La pagina Dettagli del set di dati fornisce anche il codice di esempio per accedere alle etichette da Python.

![Set di dati esportato](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passaggi successivi

* Etichettare le immagini per la [classificazione di immagini o il rilevamento di oggetti](how-to-label-images.md)
* Altre informazioni su [Azure Machine Learning e sullo studio](../compare-azure-ml-to-studio-classic.md)