---
title: 'Importare da archiviazione Blob di Azure: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su che questo argomento viene descritto come utilizzare l'importazione del modulo di archiviazione Blob di Azure nel servizio Azure Machine Learning per leggere i dati dall'archiviazione blob di Azure, in modo che è possibile usare i dati in un esperimento di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029686"
---
# <a name="import-from-azure-blob-storage-module"></a>Importazione del modulo di archiviazione Blob di Azure

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per leggere i dati da archiviazione blob di Azure in modo che è possibile usare i dati in un esperimento di machine learning.  

Il servizio Blob di Azure è per archiviare grandi quantità di dati, inclusi dati binari. I BLOB di Azure sono accessibili da qualsiasi posizione, tramite HTTP o HTTPS. L'autenticazione potrebbe essere necessaria a seconda del tipo di archiviazione blob. 

- BLOB pubblici sono accessibili da chiunque, o dagli utenti che dispongono di un URL di firma di accesso condiviso.
- BLOB privati richiedono un account di accesso e le credenziali.

L'importazione dall'archivio blob è necessario archiviare i dati nei blob che usano il **blob in blocchi** formato. I file archiviati in blob devono usare delimitato da virgole (CSV) o delimitati da tabulazioni (TSV) formati. Quando si legge il file, i record e le intestazioni di attributi applicabili vengono caricate come righe in memoria come un set di dati.


Si consiglia di profilare i dati prima dell'importazione, per assicurarsi che lo schema è come previsto. Il processo di importazione esegue l'analisi di un certo numero di righe head per determinare lo schema, ma le righe successive possono contenere colonne aggiuntive o dati che causano errori.



## <a name="manually-set-properties-in-the-import-data-module"></a>Impostare manualmente le proprietà nel modulo Import Data

I passaggi seguenti descrivono come configurare l'origine di importazione.

1. Aggiungere il **Import Data** modulo nell'esperimento. È possibile trovare questo modulo nell'interfaccia nel **Data Input and Output**

2. Per la **zdroj dat**, selezionare **archivio Blob di Azure**.

3. Per la **tipo di autenticazione**, scegliere **Public (URL di firma di accesso condiviso)** se si sa che sono state fornite le informazioni come origine dati pubblici. Un URL di firma di accesso condiviso è un URL con associazione temporale per l'accesso pubblico che è possibile generare tramite un'utilità di archiviazione di Azure.

    In caso contrario, scegliere **Account**.

4. Se i dati si trovano in una **pubblica** blob accessibili tramite un URL di firma di accesso condiviso, non sono necessarie credenziali aggiuntive poiché la stringa dell'URL contiene tutte le informazioni necessarie per il download e l'autenticazione.

    Nel **URI** campo digitare o incollare l'URI completo che definisce l'account e i blob pubblico.



5. Se i dati si trovano in una **privato** account, è necessario fornire le credenziali, inclusi il nome dell'account e la chiave.

    - Per la **nome dell'Account**digitare o incollare il nome dell'account che contiene il blob che si desidera accedere.

        Ad esempio, se è l'URL completo dell'account di archiviazione `http://myshared.blob.core.windows.net`, si digiterà `myshared`.

    - Per la **chiave dell'Account**, incollare la chiave di accesso di archiviazione che è associata all'account.

        Se non si conosce la chiave di accesso, vedere la sezione, "Gestire gli account di archiviazione di Azure" in questo articolo: [Informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Per la **percorso di directory, contenitore o blob**, digitare il nome del blob specifico a cui si desidera recuperare.

    Ad esempio, se è stato caricato un file denominato **data01.csv** nel contenitore **trainingdata** in un account denominato **mymldata**, l'URL completo per il file è: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Pertanto, nel campo **percorso di directory, contenitore o blob**, digitare: `trainingdata/data01.csv`

    Per importare più file, è possibile usare i caratteri jolly `*` (asterisco) o `?` (punto interrogativo).

    Ad esempio, supponendo che il contenitore `trainingdata` contiene più file di un formato compatibile, è possibile usare la specifica seguente per leggere tutti i file a partire da `data`e di concatenarle in un singolo set di dati:

    `trainingdata/data*.csv`

    È possibile usare caratteri jolly nei nomi dei contenitori. Se è necessario importare i file da più contenitori, usare un'istanza separata del **Import Data** modulo per ogni contenitore e quindi unire i set di dati usando la [Add Rows](./add-rows.md) modulo.

    > [!NOTE]
    > Se è stata selezionata l'opzione **Usa nella cache i risultati**, eventuali modifiche apportate ai file nel contenitore non attivano un aggiornamento dei dati nell'esperimento.

7. Per la **formato di file Blob**, selezionare un'opzione che indica il formato dei dati archiviati in blob, in modo che Azure Machine Learning può elaborare i dati in modo appropriato. Sono supportati i formati seguenti:

    - **CSV**: Valori delimitati da virgole (CSV) rappresentano il formato di archiviazione predefinito per l'esportazione e importazione di file in Azure Machine Learning. Se i dati contengono già una riga di intestazione, assicurarsi di selezionare l'opzione **File con riga di intestazione**, o l'intestazione verrà considerata come una riga di dati.

       

    - **TSV**: Valori delimitati da tabulazioni (TSV) sono un formato usato da strumenti di apprendimento molti. Se i dati contengono già una riga di intestazione, assicurarsi di selezionare l'opzione **File con riga di intestazione**, o l'intestazione verrà considerata come una riga di dati.

       

    - **ARFF**: Questo formato supporta l'importazione di file nel formato utilizzato dal set di strumenti Weka. 

   

8. Eseguire l'esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 