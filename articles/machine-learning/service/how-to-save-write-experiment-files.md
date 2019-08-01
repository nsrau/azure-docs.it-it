---
title: Impedisci limitazioni di archiviazione e latenza dell'esperimento con le directory di input e di output
description: In questo articolo viene illustrato dove salvare i file di input dell'esperimento e dove scrivere i file di output per evitare errori di limitazione dell'archiviazione e la latenza degli esperimenti.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: b0e0ef93b2782cd44eca3dc6023a7eb556cd3245
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618382"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Dove salvare e scrivere file per esperimenti di Azure Machine Learning

In questo articolo viene illustrato dove salvare i file di input e dove scrivere i file di output dagli esperimenti per evitare errori di limite di archiviazione e latenza dell'esperimento.

Quando si avvia l'esecuzione del training in una [destinazione di calcolo](how-to-set-up-training-targets.md), questi vengono isolati dagli ambienti esterni. Lo scopo di questa progettazione è garantire la riproducibilità e la portabilità dell'esperimento. Se si esegue lo stesso script due volte, nello stesso o in un'altra destinazione di calcolo, si ottengono gli stessi risultati. Con questa progettazione è possibile considerare le destinazioni di calcolo come risorse di calcolo senza stato, ognuna delle quali non presenta affinità ai processi in esecuzione dopo il completamento.

## <a name="where-to-save-input-files"></a>Posizione in cui salvare i file di input

Prima di poter avviare un esperimento in una destinazione di calcolo o nel computer locale, è necessario assicurarsi che i file necessari siano disponibili per la destinazione di calcolo, ad esempio file di dipendenza e file di dati di cui è necessario eseguire il codice.

Azure Machine Learning esegue gli script di training copiando l'intera cartella di script nel contesto di calcolo di destinazione e quindi crea uno snapshot. Il limite di archiviazione per gli snapshot dell'esperimento è di 300 MB e/o 2000 file.

Per questo motivo, è consigliabile:

* **Archiviazione dei file in un [archivio dati](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)Azure Machine Learning.** In questo modo si evitano problemi di latenza dell'esperimento e si hanno i vantaggi di accedere ai dati da una destinazione di calcolo remota, ovvero l'autenticazione e il montaggio sono gestiti dal servizio Azure Machine Learning. Altre informazioni su come specificare un archivio dati come directory di origine e caricare i file nell'archivio dati nell'articolo accedere ai [dati dall'archivio dati](how-to-access-data.md) .

* **Se sono necessari solo un paio di file di dati e script di dipendenza e non è possibile usare un archivio dati,** inserire i file nella stessa directory della cartella dello script di training. Specificare questa cartella come `source_directory` direttamente nello script di training o nel codice che chiama lo script di training.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limiti di archiviazione degli snapshot dell'esperimento

Per gli esperimenti, Azure Machine Learning crea automaticamente uno snapshot dell'esperimento del codice in base alla directory che si suggerisce quando si configura l'esecuzione. Questa operazione ha un limite totale di 300 MB e/o 2000 file. Se si supera questo limite, verrà visualizzato l'errore seguente:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Per correggere l'errore, archiviare i file dell'esperimento in un archivio dati. Se non è possibile usare un archivio dati, la tabella seguente offre soluzioni alternative possibili.

Descrizione&nbsp;esperimento|Soluzione limite di archiviazione
---|---
Meno di 2000 file & non possono usare un archivio dati| Sostituisci limite dimensioni snapshot con <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Questa operazione può richiedere alcuni minuti a seconda del numero e delle dimensioni dei file.
Deve usare una directory di script specifica| Creare un `.amlignore` file per escludere i file dallo snapshot dell'esperimento che non fanno parte del codice sorgente. Aggiungere i nomi `.amlignore` file al file e posizionarlo nella stessa directory dello script di training. Il `.amlignore` file utilizza la stessa [sintassi e](https://git-scm.com/docs/gitignore) gli stessi modelli `.gitignore` di un file.
Pipeline|Usare una sottodirectory diversa per ogni passaggio
Jupyter Notebook| Creare un `.amlignore` file o spostare il notebook in una nuova sottodirectory vuota, quindi eseguire di nuovo il codice.

## <a name="where-to-write-files"></a>Posizione in cui scrivere i file

A causa dell'isolamento degli esperimenti di training, le modifiche apportate ai file che si verificano durante le esecuzioni non sono necessariamente mantenute all'esterno dell'ambiente. Se lo script modifica i file locali in calcolo, le modifiche non vengono rese permanente per l'esecuzione dell'esperimento successiva e non vengono propagate automaticamente al computer client. Di conseguenza, le modifiche apportate durante la prima esecuzione dell'esperimento non possono influire su quelle nel secondo.

Quando si scrivono le modifiche, è consigliabile scrivere file in un archivio dati Azure Machine Learning. Vedere [accedere ai dati dagli archivi dati](how-to-access-data.md).

Se non è necessario un archivio dati, scrivere i file `./outputs` nella cartella e/o. `./logs`

>[!Important]
> Due cartelle, *output* e *log*, ricevono un trattamento speciale per Azure Machine Learning. Durante il training, quando si scrivono file`./outputs` in`./logs` cartelle e, i file vengono caricati automaticamente nella cronologia di esecuzione, in modo da potervi accedere al termine dell'esecuzione.

* `./outputs` **Per l'output, ad esempio i messaggi di stato o i risultati** di assegnazione dei punteggi, scrivere i file nella cartella, in modo che siano salvati in modo permanente come artefatti nella cronologia di esecuzione. Tenere presente il numero e le dimensioni dei file scritti in questa cartella, in quanto la latenza può verificarsi quando il contenuto viene caricato nella cronologia di esecuzione. Se la latenza rappresenta un problema, è consigliabile scrivere file in un archivio dati.

* **Per salvare il file scritto come log nella cronologia di esecuzione,** scrivere `./logs` i file nella cartella. Poiché i log vengono caricati in tempo reale, questo metodo è adatto per lo streaming di aggiornamenti dinamici da un'esecuzione remota.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull' [accesso ai dati dagli archivi dati](how-to-access-data.md).

* Altre informazioni su [come configurare gli obiettivi di training](how-to-set-up-training-targets.md).
