---
title: Evitare le limitazioni di archiviazione e sperimentare latenza con le directory di input e outpue
description: In questo articolo descrive la posizione in cui salvare i file di input esperimento e in cui scrivere i file di output per evitare errori di limitazione di archiviazione e provare la latenza.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595272"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>In cui per salvare e scrivere i file per esperimenti di Azure Machine Learning

In questo articolo descrive la posizione in cui salvare i file di input e in cui scrivere i file di output dei propri esperimenti verrà impedita l'archiviazione limitano gli errori e provare la latenza.

Se l'avvio di training viene eseguito in un [destinazione di calcolo](how-to-set-up-training-targets.md), sono isolate da ambienti esterni. Lo scopo di questa progettazione è garantire la riproducibilità e la portabilità dell'esperimento. Se si esegue lo stesso script due volte, nello stesso o in un'altra destinazione di calcolo, si ottengono gli stessi risultati. Con questa struttura, è possibile trattare le destinazioni di calcolo come risorse di calcolo senza stato, ognuno dei quali senza affinità per i processi in esecuzione al loro termine.

## <a name="where-to-save-input-files"></a>Percorso in cui salvare i file di input

Prima di poter avviare un esperimento in una destinazione di calcolo o il computer locale, è necessario assicurarsi che i file necessari sono disponibili alla destinazione di calcolo, ad esempio file di dipendenza e i file di dati che deve essere eseguito il codice.

Azure Machine Learning esegue gli script di training copiando la cartella intero script per il contesto di calcolo di destinazione e quindi viene creato uno snapshot. Il limite di archiviazione di snapshot dell'esperimento è 300 MB e/o file 2000.

Per questo motivo, è consigliabile:

* **L'archiviazione dei file in un Azure Machine Learning [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** In questo modo si evitano problemi di latenza esperimento e offre i vantaggi dell'accesso ai dati da una destinazione di calcolo remoto, ovvero l'autenticazione e il montaggio vengono gestite dal servizio di Azure Machine Learning. Altre informazioni sulla specifica di un archivio dati come directory di origine e sul caricamento di file all'archivio dati nel [accedere ai dati da archivi di dati](how-to-access-data.md) articolo.

* **Se è necessario solo due file di dati e delle dipendenze degli script e non è possibile usare un archivio dati,** inserire i file nella directory della cartella stessa, mentre lo script di training. Specificare questa cartella come il `source_directory` direttamente nello script di training o nel codice che chiama lo script di training.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limiti di archiviazione di snapshot dell'esperimento

Per gli esperimenti, Azure Machine Learning rende automaticamente uno snapshot esperimento del codice basato sulla directory che consigliabile quando si configura l'esecuzione. Ciò ha un limite totale di 300 MB e/o file 2000. Se si supera questo limite, si noterà l'errore seguente:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Per risolvere questo errore, archiviare i file esperimento in un archivio dati. Se non è possibile usare un archivio dati, la tabella seguente sono disponibili soluzioni alternative possibili.

Esperimento&nbsp;descrizione|Soluzione di limite di archiviazione
---|---
Meno di 2000 i file e non è possibile usare un archivio dati| Ignorare il limite di dimensioni dello snapshot con <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> L'operazione potrebbe richiedere alcuni minuti a seconda del numero e dimensioni dei file.
Deve usare lo script specifica directory| Rendere un `.amlignore` file da escludere file dallo snapshot dell'esperimento che non fanno parte del codice sorgente. Aggiungere i nomi di file per il `.amlignore` file e posizionarlo nella stessa directory dello script di training. Il `.amlignore` file Usa lo stesso [sintassi e i modelli](https://git-scm.com/docs/gitignore) come un `.gitignore` file.
Pipeline|Utilizzare una sottodirectory diversa per ogni passaggio
Jupyter Notebook| Creare un `.amlignore` file o spostare il blocco appunti in una sottodirectory di nuova e vuota, ed eseguire nuovamente il codice.

## <a name="where-to-write-files"></a>Posizione in cui scrivere i file

A causa dell'isolamento degli esperimenti di training, le modifiche ai file che si verificano durante l'esecuzione non vengono necessariamente mantenute all'esterno dell'ambiente. Se lo script modifica i file locali per il calcolo, le modifiche non vengono mantenute per la successiva esecuzione dell'esperimento e non vengono propagate al computer client automaticamente. Di conseguenza, le modifiche apportate durante il primo esperimento di esecuzione non e non hanno impatto su quelle nella seconda.

Quando si scrive le modifiche, è consigliabile la scrittura di file in un archivio dati di Azure Machine Learning. Visualizzare [accedere ai dati da archivi di dati](how-to-access-data.md).

Se si non richiedono un archivio dati, scrivere i file per il `./outputs` e/o `./logs` cartella.

>[!Important]
> Due cartelle *emette* e *log*, ricevono un trattamento speciale da Azure Machine Learning. Durante il training, quando si scrivono i file`./outputs` e`./logs` cartelle, i file vengono caricati automaticamente per la cronologia di esecuzione, in modo da poter accedervi al termine dell'esecuzione.

* **Per l'output, ad esempio i messaggi di stato o risultati di punteggio** scrivere i file di `./outputs` cartella, quindi vengono rese persistenti come gli elementi della cronologia di esecuzione. Tenere presenti il numero e dimensioni dei file scritti in questa cartella, come latenza può verificarsi quando viene caricato il contenuto della cronologia di esecuzione. Se la latenza rappresenta un problema, è consigliabile la scrittura di file in un archivio dati.

* **Per salvare i file scritti sotto forma di log nella cronologia di esecuzione** scrivere i file `./logs` cartella. I log vengono caricati in tempo reale, in modo che questo metodo è adatto per lo streaming live degli aggiornamenti da un'esecuzione remota.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [l'accesso ai dati da archivi di dati](how-to-access-data.md).

* Altre informazioni sulle [come Set di destinazioni di Training](how-to-set-up-training-targets.md).
