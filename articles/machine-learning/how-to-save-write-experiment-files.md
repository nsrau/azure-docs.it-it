---
title: Dove salvare & scrivere file di esperimento
titleSuffix: Azure Machine Learning
description: Scopri dove salvare i file di input dell'esperimento e dove scrivere i file di output per evitare errori di limitazione dello spazio di archiviazione e latenza dell'esperimento.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078428"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Dove salvare e scrivere file per gli esperimenti di Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato dove salvare i file di input e dove scrivere i file di output dagli esperimenti per evitare errori di limite di archiviazione e latenza dell'esperimento.

Quando il training viene avviato viene eseguito in una [destinazione di calcolo,](how-to-set-up-training-targets.md)sono isolati da ambienti esterni. Lo scopo di questo progetto è quello di garantire la riproducibilità e la portabilità dell'esperimento. Se si esegue lo stesso script due volte, nella stessa destinazione di calcolo o in un'altra destinazione di calcolo, si ricevono gli stessi risultati. Con questa progettazione, è possibile considerare le destinazioni di calcolo come risorse di calcolo senza stato, ognuna senza affinità con i processi in esecuzione al termine.

## <a name="where-to-save-input-files"></a>Dove salvare i file di input

Prima di poter avviare un esperimento su una destinazione di calcolo o nel computer locale, è necessario assicurarsi che i file necessari siano disponibili per tale destinazione di calcolo, ad esempio i file di dipendenza e i file di dati necessari per l'esecuzione del codice.

Azure Machine Learning esegue script di training copiando l'intera cartella di script nel contesto di calcolo di destinazione e quindi crea uno snapshot. Il limite di archiviazione di snapshot dell'esperimento è 300 MB e/o 2000 file.

Per questo motivo, si consiglia:

* **Archiviazione dei file in un [archivio dati](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)di Azure Machine Learning.** Ciò impedisce problemi di latenza dell'esperimento e presenta i vantaggi dell'accesso ai dati da una destinazione di calcolo remota, ovvero l'autenticazione e il montaggio sono gestiti da Azure Machine Learning.This prevents experiment latency issues, and has the advantages of accessing data from a remote compute target, which means authentication and mounting are managed by Azure Machine Learning. Altre informazioni su come specificare un archivio dati come directory di origine e sul caricamento di file nell'archivio dati nell'articolo Dati di [Access dagli archivi dati.](how-to-access-data.md)

* Se sono necessari solo un paio di file di **dati e script di dipendenza e non è possibile utilizzare un archivio dati,** inserire i file nella stessa directory della cartella dello script di training. Specificare questa `source_directory` cartella come direttamente nello script di training o nel codice che chiama lo script di training.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limiti di archiviazione delle istantanee degli esperimenti

Per gli esperimenti, Azure Machine Learning crea automaticamente uno snapshot dell'esperimento del codice in base alla directory suggerita quando si configura l'esecuzione. Ha un limite totale di 300 MB e/o 2000 file. Se superi questo limite, vedrai il seguente errore:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Per risolvere questo errore, archiviare i file dell'esperimento in un archivio dati. Se non è possibile utilizzare un archivio dati, la tabella seguente offre possibili soluzioni alternative.

Descrizione&nbsp;dell'esperimento|Soluzione di limite di archiviazione
---|---
Meno di 2000 file & non possono utilizzare un archivio dati| Ignora limite dimensioni snapshot con <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> L'operazione potrebbe richiedere alcuni minuti a seconda del numero e delle dimensioni dei file.
Deve utilizzare una directory di script specifica| Crea `.amlignore` un file per escludere i file dallo snapshot dell'esperimento che non fanno parte del codice sorgente. Aggiungere i nomi `.amlignore` dei file al file e inserirlo nella stessa directory dello script di training. Il `.amlignore` file utilizza la stessa `.gitignore` [sintassi e gli](https://git-scm.com/docs/gitignore) stessi modelli di un file.
Pipeline|Utilizzare una sottodirectory diversa per ogni passaggioUse a different subdirectory for each step
Jupyter Notebook| Creare `.amlignore` un file o spostare il blocco appunti in una nuova sottodirectory vuota ed eseguire di nuovo il codice.

## <a name="where-to-write-files"></a>Dove scrivere i file

A causa dell'isolamento degli esperimenti di training, le modifiche apportate ai file che si verificano durante le esecuzioni non vengono necessariamente rese persistenti all'esterno dell'ambiente. Se lo script modifica i file locali da calcolare, le modifiche non vengono rese persistenti per la successiva esecuzione dell'esperimento e non vengono propagate automaticamente al computer client. Pertanto, le modifiche apportate durante la prima esecuzione dell'esperimento non influiscono su quelle nella seconda.

Quando si scrivono le modifiche, è consigliabile scrivere file in un archivio dati di Azure Machine Learning.When writing changes, we recommend writing files to an Azure Machine Learning datastore. Vedere [Accedere ai dati dai datastore.](how-to-access-data.md)

Se non è necessario un archivio dati, scrivere i file nella `./outputs` cartella e/o. `./logs`

>[!Important]
> Due cartelle, *output* e *log*, ricevono un trattamento speciale da Azure Machine Learning. Durante l'allenamento, quando`./outputs` `./logs` si scrivono file e cartelle, i file verranno caricati automaticamente nella cronologia di esecuzione, in modo da avere accesso a essi una volta terminata l'esecuzione.

* **Per l'output, ad esempio i** messaggi `./outputs` di stato o i risultati del punteggio, scrivere i file nella cartella, in modo che vengano resi persistenti come elementi nella cronologia di esecuzione. Tenere presente il numero e le dimensioni dei file scritti in questa cartella, poiché la latenza può verificarsi quando il contenuto viene caricato per eseguire la cronologia. Se la latenza è un problema, è consigliabile scrivere file in un archivio dati.

* **Per salvare il file scritto come log nella cronologia di esecuzione,** scrivere i file nella `./logs` cartella. I log vengono caricati in tempo reale, pertanto questo metodo è adatto per lo streaming di aggiornamenti in tempo reale da un'esecuzione remota.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'accesso ai dati dai datastore](how-to-access-data.md).

* Ulteriori informazioni su [come impostare gli obiettivi](how-to-set-up-training-targets.md)di formazione .
