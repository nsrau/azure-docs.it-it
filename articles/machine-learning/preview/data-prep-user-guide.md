---
title: Guida dettagliata sull'uso della preparazione dei dati di Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce i dettagli e una panoramica sulla risoluzione dei problemi dei dati con la preparazione dei dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 22389ba85edb119acdd21b63f2deae2d71f31373
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="data-preparation-user-guide"></a>Guida dell'utente per la preparazione dati 
L'esperienza di preparazione dei dati offre molte funzionalità avanzate. Si seguito sono riportate le parti fondamentali di tale esperienza.

### <a name="step-execution-history-and-caching"></a>Esecuzione dei passaggi, cronologia e memorizzazione nella cache 
La cronologia dei passaggi di preparazione dei dati gestisce una serie di cache per motivi di prestazioni. Se si fa clic su un passaggio e questo accede a una cache, tale passaggio non viene nuovamente eseguito. Se si dispone di un blocco di scrittura alla fine della cronologia dei passaggi e ci si sposta avanti e indietro nei passaggi senza apportare modifiche, la scrittura verrà attivata solo la prima volta. Se 
- si apportano modifiche al blocco di scrittura,
- si aggiunge un nuovo blocco di trasformazione e lo si sposta sopra il blocco di scrittura generando un invalidamento dei dati della cache,
- si modificano le proprietà di un blocco sopra il blocco di scrittura generando un invalidamento dei dati della cache oppure
- si seleziona l'aggiornamento di un campione invalidando tutta la cache,

Si verifica una nuova scrittura che sovrascrive quella precedente.

### <a name="error-values"></a>Valori di errore

Le trasformazioni dei dati potrebbero non riuscire per un valore di input perché tale valore non può essere gestito in modo appropriato. Nel caso di operazioni di coercizione, ad esempio, la coercizione non riesce se non è possibile eseguire il cast del valore di stringa di input al tipo di destinazione specificato. Un'operazione di coercizione potrebbe essere la conversione di una colonna di tipo stringa in un tipo numerico o booleano, così come il tentativo di duplicare una colonna che non esiste (il risultato dello spostamento dell'operazione di eliminazione della colonna X prima dell'operazione di duplicazione della colonna X).

In questi casi, la preparazione dei dati produce come output un **valore di errore**. I valori di errore indicano che un'operazione precedente non è riuscita per il valore specificato. Internamente vengono considerati come un tipo valore di prima classe ma la loro presenza non modifica il tipo sottostante di una colonna anche se la colonna è interamente costituita da valori di errore.

I valori di errore sono facilmente identificabili. Sono evidenziati in rosso e riportano la dicitura "Errore". Per determinare la causa dell'errore passare con il mouse su un valore di errore per ottenere una descrizione di testo dell'errore.

Propagazione dei valori di errore. Quando si verifica, nella maggior parte dei casi un valore di errore si propaga come errore in gran parte delle operazioni. Attualmente esistono tuttavia tre modi per rimuovere o sostituire tali valori:

1) Replace
    -  Fare clic su una colonna e selezionare *Replace Error Values* (Sostituisci i valori di errore). È quindi possibile scegliere un valore di sostituzione per ogni valore di errore rilevato nella colonna.

2) Rimuovere
    - La preparazione dei dati include filtri interattivi per mantenere o rimuovere i valori di errore.
    - Fare clic con il pulsante destro del mouse su una colonna e selezionare *Filter Column* (Filtra colonna). Per mantenere o rimuovere i valori di errore, creare un'istruzione condizionale con la condizione *"is error"* (errore) o *"is not error"* (non errore).

3) Usare un'espressione Python per operare in modo condizionale sui valori di errore. Per altre informazioni, visitare la [sezione relativa alle estensioni di Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>campionamento
Un file di origine dati accetta dati non elaborati da una o più origini, dal file system locale o da una posizione remota. Il blocco Campione consente di specificare se usare un subset di dati tramite la generazione di campioni. Operare su un campione di dati anziché su un set di dati di grandi dimensioni spesso assicura prestazioni migliori durante l'esecuzione di operazioni nei passaggi successivi.

Per ogni file di origine dati è possibile generare e archiviare più campioni. È possibile tuttavia impostare un solo campione come campione attivo. È possibile creare, modificare o eliminare i campioni nella Creazione guidata origine dati o modificando il blocco Campione. Tutti i file della preparazione dati che fanno riferimento a un'origine dati usano intrinsecamente il campione specificato nel file di origine dati.

Esistono varie strategie di campionamento disponibili, ognuna con diversi parametri configurabili.

#### <a name="top"></a>Top
Questa strategia può essere applicata ai file locali o remoti. Prende le prime N righe specificate da Conteggio nell'origine dati.

#### <a name="random-n"></a>N casuale 
Questa strategia è applicabile solo ai file locali. Prende le righe N casuali specificate da Conteggio nell'origine dati. È possibile fornire un valore di inizializzazione specifico per garantire che venga generato lo stesso campione purché anche Conteggio sia lo stesso.

#### <a name="random-"></a>% di casuali 
Questa strategia può essere applicata ai file locali o remoti. In entrambi i casi è necessario fornire una probabilità e un valore di inizializzazione analogalmente alla strategia di N casuale.

Per i campioni di file remoti è necessario specificare parametri aggiuntivi.

- Generatore di esempi 
  - Selezionare un cluster Spark o una destinazione di calcolo Docker remota da usare per la generazione del campione. La destinazione di calcolo per il progetto deve essere creata in anticipo affinché venga visualizzata in questo elenco. Seguire i passaggi in [How to use GPU in Azure Machine Learning](how-to-use-gpu.md) "Create a new Compute Target" (Come usare la GPU in Azure Machine Learning "Creare una nuova destinazione di calcolo") per creare destinazioni di calcolo.
- Archiviazione dei campioni 
  - Fornire una posizione di archiviazione intermedia per archiviare il campione remoto. Questo percorso deve essere una directory diversa dal percorso del file di input.

#### <a name="full-file"></a>File completo 
Questa strategia è applicabile solo ai file locali, portando il file completo nell'origine dati. Se il file è troppo grande, questa opzione potrebbe rallentare le operazioni future nell'app e potrebbe essere più opportuno usare una strategia di campionamento diversa.


### <a name="forking-merging-and-appending"></a>Creazione di una copia tramite fork, unione e accodamento

Quando si applica un filtro su un set di dati l'operazione suddivide i dati in due set di risultati in cui un set rappresenta i record corretti nel filtro e un altro set rappresenta i record non corretti. In entrambi i casi, l'utente può scegliere il set di risultati da visualizzare. L'utente può rimuovere l'altro set di dati o inserirlo in un nuovo flusso di dati. Quest'ultima opzione viene definita come *creazione di una copia tramite fork*.

Per creare una copia tramite fork selezionare una colonna, fare clic con il pulsante destro del mouse e selezionare (Filter Column) Filtra colonna.
- Nella sezione "I Want To" (Voglio), selezionare *Keep Rows* (Mantenere le righe) per visualizzare il set di risultati che supera il filtro o *Remove Rows* (Rimuovere le righe) per visualizzare il set non corretto.
- Dopo "Conditions" (Condizioni), selezionare *Create Dataflow Containing the Filtered Out Rows* (Crea un flusso di dati contenente le righe escluse dal filtro) per creare una copia tramite fork del set di risultati non visualizzato in un nuovo flusso di dati.


Questa procedura viene spesso eseguita per separare un set di dati che richiede ulteriore preparazione. Dopo il wrangling del set di dati di cui è stata creata una copia tramite fork i dati vengono spesso uniti con il set dei risultati nel flusso di dati originale. Per eseguire una "unione" (il contrario di un'operazione di "creazione di una copia tramite fork") eseguire una delle azioni seguenti:
- *Accodare le righe*. Unire due o più flussi di dati in verticale (per riga). 
- *Accodare le colonne*. Unire due o più flussi di dati in orizzontale (per colonna).


>[!NOTE]
>AppendColumns ha esito negativo se si verifica un conflitto di colonna.


Dopo un'operazione di unione a uno o più flussi di dati verrà fatto riferimento da un flusso di dati di origine. DataPrep invia le notifiche all'utente con una notifica nell'angolo inferiore destro dell'app sotto l'elenco dei passaggi.


Qualsiasi operazione sul flusso di dati di riferimento richiede che il flusso di dati padre aggiorni il campione usato dal flusso di dati di riferimento. In tal caso una finestra di dialogo di conferma sostituisce la notifica di riferimento del flusso di dati nell'angolo inferiore destro. La finestra di dialogo conferma che è necessario aggiornare il flusso di dati per la sincronizzazione con le modifiche ai flussi di dati di qualsiasi dipendenza.

### <a name="list-of-appendices"></a>Elenco delle appendici 
[Appendix 2 - Supported Data Sources](data-prep-appendix2-supported-data-sources.md) (Appendice 2 - Origini dati supportate)  
[Appendix 3 - Supported Transforms](data-prep-appendix3-supported-transforms.md) (Appendice 3 - Trasformazioni supportate)  
[Appendix 4 - Supported Inspectors](data-prep-appendix4-supported-inspectors.md) (Appendice 4 - Controlli supportati)  
[Appendix 5 - Supported Destinations](data-prep-appendix5-supported-destinations.md) (Appendice 5 - Destinazioni supportate)  
[Appendix 6 - Sample Filter Expressions in Python](data-prep-appendix6-sample-filter-expressions-python.md) (Appendice 6 - Esempio di espressioni filtro in Python)  
[Appendix 7 - Sample Transform Dataflow Expressions in Python](data-prep-appendix7-sample-transform-data-flow-python.md) (Appendice 7 - Esempio di trasformazione di espressioni del flusso di dati in Python)  
[Appendix 8 - Sample Data Sources in Python](data-prep-appendix8-sample-source-connections-python.md) (Appendice 8 - Esempio di origini dati in Python)  
[Appendix 9 - Sample Destination Connections in Python](data-prep-appendix9-sample-destination-connections-python.md) (Appendice 9 - Esempio di connessioni di destinazione in Python)  
[Appendix 10 - Sample Column Transforms in Python](data-prep-appendix10-sample-custom-column-transforms-python.md) (Appendice 10 - Esempio di trasformazioni di colonna in Python)  

