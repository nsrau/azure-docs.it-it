---
title: 'Importare da archiviazione BLOB di Azure: informazioni di riferimento sui moduli'
titleSuffix: Azure Machine Learning service
description: Questo argomento descrive come usare il modulo Importa da archiviazione BLOB di Azure nel servizio Azure Machine Learning per leggere i dati dall'archivio BLOB di Azure, in modo che sia possibile usare i dati in una pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f8d23bfbee6d3665d770d8cbbcb9440827a88e8e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693733"
---
# <a name="import-from-azure-blob-storage-module"></a>Importare dal modulo di archiviazione BLOB di Azure

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per leggere i dati dall'archivio BLOB di Azure, in modo che sia possibile usare i dati in una pipeline di machine learning.  

Il servizio BLOB di Azure è per l'archiviazione di grandi quantità di dati, inclusi i dati binari. È possibile accedere ai BLOB di Azure da qualsiasi luogo, usando HTTP o HTTPS. Potrebbe essere necessaria l'autenticazione a seconda del tipo di archiviazione BLOB. 

- È possibile accedere ai BLOB pubblici da chiunque o da utenti che dispongono di un URL SAS.
- Per i BLOB privati sono necessari un account di accesso e le credenziali.

Per l'importazione dall'archiviazione BLOB è necessario che i dati vengano archiviati in BLOB che usano il formato **BLOB in blocchi** . I file archiviati nel BLOB devono usare formati separati da virgola (CSV) o con valori delimitati da tabulazioni (TSV). Quando si legge il file, i record ed eventuali intestazioni di attributo applicabili vengono caricati come righe in memoria come set di dati.


È consigliabile profilare i dati prima dell'importazione, per assicurarsi che lo schema sia quello previsto. Il processo di importazione esegue l'analisi di un numero di righe di intestazione per determinare lo schema, ma le righe successive potrebbero contenere colonne aggiuntive o dati che provocano errori.



## <a name="manually-set-properties-in-the-import-data-module"></a>Impostare manualmente le proprietà nel modulo Import Data

Nei passaggi seguenti viene descritto come configurare manualmente l'origine di importazione.

1. Aggiungere il modulo **Import Data (Importa dati** ) alla pipeline. È possibile trovare questo modulo nell'interfaccia, nell' **input e nell'output dei dati**

2. Per **origine dati**selezionare **archiviazione BLOB di Azure**.

3. Per **tipo di autenticazione**scegliere **pubblico (URL SAS)** se si è certi che le informazioni sono state fornite come origine dati pubblica. Un URL di firma di accesso condiviso è un URL con scadenza per l'accesso pubblico che è possibile generare usando un'utilità di archiviazione di Azure.

    In caso contrario, scegliere **account**.

4. Se i dati si trovano in un BLOB **pubblico** accessibile tramite un URL di firma di accesso condiviso, non sono necessarie credenziali aggiuntive perché la stringa URL contiene tutte le informazioni necessarie per il download e l'autenticazione.

    Nel campo **URI** Digitare o incollare l'URI completo che definisce l'account e il BLOB pubblico.



5. Se i dati sono in un account **privato** , è necessario fornire le credenziali, inclusi il nome dell'account e la chiave.

    - Per **nome account**Digitare o incollare il nome dell'account che contiene il BLOB a cui si vuole accedere.

        Ad esempio, se l'URL completo dell'account di archiviazione è `http://myshared.blob.core.windows.net`, digitare `myshared`.

    - Per la **chiave dell'account**, incollare la chiave di accesso alle archiviazione associata all'account.

        Se non si conosce la chiave di accesso, vedere la sezione relativa alla gestione degli account di archiviazione di Azure in questo articolo: [informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Per **percorso del contenitore, della directory o del BLOB**, digitare il nome del blob specifico che si desidera recuperare.

    Ad esempio, se è stato caricato un file denominato **data01. csv** nel contenitore **trainingdata** in un account denominato **mymldata**, l'URL completo per il file sarà: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    Quindi, nel campo **percorso del contenitore, della directory o del BLOB**, digitare: `trainingdata/data01.csv`

    Per importare più file, è possibile usare i caratteri jolly `*` (asterisco) o `?` (punto interrogativo).

    Ad esempio, supponendo che il contenitore `trainingdata` contenga più file di un formato compatibile, è possibile usare la specifica seguente per leggere tutti i file che iniziano con `data` e concatenarli in un singolo set di dati:

    `trainingdata/data*.csv`

    Non è possibile usare caratteri jolly nei nomi dei contenitori. Se è necessario importare file da più contenitori, usare un'istanza separata del modulo **Import Data** per ogni contenitore, quindi unire i set di dati usando il modulo [Add Rows](./add-rows.md) .

    > [!NOTE]
    > Se è stata selezionata l'opzione **USA risultati memorizzati nella cache**, le eventuali modifiche apportate ai file nel contenitore non attivano un aggiornamento dei dati nella pipeline.

7. Per **formato file BLOB**selezionare un'opzione che indichi il formato dei dati archiviati nel BLOB, in modo che Azure Machine Learning possibile elaborare i dati in modo appropriato. Sono supportati i formati seguenti:

    - **CSV**: i valori delimitati da virgole (CSV) rappresentano il formato di archiviazione predefinito per l'esportazione e l'importazione di file in Azure Machine Learning. Se i dati contengono già una riga di intestazione, assicurarsi di selezionare l'opzione **file con riga di intestazione**oppure l'intestazione verrà considerata come una riga di dati.

       

    - **TSV**: i valori delimitati da tabulazioni (TSV) sono un formato usato da molti strumenti di machine learning. Se i dati contengono già una riga di intestazione, assicurarsi di selezionare l'opzione **file con riga di intestazione**oppure l'intestazione verrà considerata come una riga di dati.

       

    - **ARFF**: questo formato supporta l'importazione di file nel formato usato dal set di strumenti weka. 

   

8. Eseguire la pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 