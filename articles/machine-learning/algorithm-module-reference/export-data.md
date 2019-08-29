---
title: 'Esporta dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Export data nel servizio Azure Machine Learning per salvare i risultati, i dati intermedi e i dati di lavoro dagli esperimenti nelle destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128769"
---
# <a name="export-data-module"></a>Modulo Export Data

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per salvare i risultati, i dati intermedi e i dati di lavoro dagli esperimenti nelle destinazioni di archiviazione cloud all'esterno Azure Machine Learning.

Questo modulo supporta l'esportazione o il salvataggio dei dati nei servizi cloud data seguenti:


- **Esportare nell'archivio BLOB di Azure**: Salva i dati nel servizio BLOB in Azure. I dati nel servizio BLOB possono essere condivisi pubblicamente o salvati in archivi dati dell'applicazione protetti.

  
## <a name="how-to-configure-export-data"></a>Come configurare i dati di esportazione

1. Aggiungere il modulo **Export Data (Esporta dati** ) all'esperimento nell'interfaccia. È possibile trovare questo modulo nella categoria **input e output** .

2. Connettere **Esporta dati** al modulo che contiene i dati che si desidera esportare.

3. Fare doppio clic su **Esporta dati** per aprire il riquadro **Proprietà** .

4. Per **destinazione dati**selezionare il tipo di archiviazione cloud in cui verranno salvati i dati. Se si apportano modifiche a questa opzione, tutte le altre proprietà vengono reimpostate. Prima di tutto, assicurarsi di scegliere questa opzione.

5. Fornire un nome account e un metodo di autenticazione necessari per accedere all'account di archiviazione specificato.

    L' **esportazione nell'archiviazione BLOB di Azure** è l'unica opzione dell'anteprima privata. Di seguito viene illustrato come impostare il modulo.
    1. Il servizio BLOB di Azure è per l'archiviazione di grandi quantità di dati, inclusi i dati binari. Sono disponibili due tipi di archiviazione BLOB: BLOB pubblici e BLOB che richiedono credenziali di accesso.

    2. Per **tipo di autenticazione**scegliere **pubblico (SAS)** se si è certi che l'archiviazione supporta l'accesso tramite un URL di firma di accesso condiviso.

          Un URL di firma di accesso condiviso è un tipo speciale di URL che può essere generato usando un'utilità di archiviazione di Azure ed è disponibile solo per un periodo di tempo limitato.  Contiene tutte le informazioni necessarie per l'autenticazione e il download.

        Per **URI**, digitare o incollare l'URI completo che definisce l'account e il BLOB pubblico.

        Per il formato di file CSV e TSV sono supportati.

    3. Per gli account privati, scegliere **account**e specificare il nome dell'account e la chiave dell'account, in modo che l'esperimento possa scrivere nell'account di archiviazione.

         - **Account name** (Nome dell'account): Digitare o incollare il nome dell'account in cui si desidera salvare i dati. Ad esempio, se l'URL completo dell'account di archiviazione è `http://myshared.blob.core.windows.net`, digitare. `myshared`

        - **Account key** (Chiave dell'account): Incollare la chiave di accesso alle archiviazione associata all'account.

        -  **Percorso del contenitore, della directory o del BLOB**: Digitare il nome del BLOB in cui verranno archiviati i dati esportati. Ad esempio, per salvare i risultati dell'esperimento in un nuovo BLOB denominato **results01. csv** nelle **stime** del contenitore in un account denominato **mymldata**, l'URL completo per il BLOB sarà `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Quindi, nel campo **percorso del contenitore, della directory o del BLOB**, è necessario specificare il nome del contenitore e del BLOB come indicato di seguito:`predictions/results01.csv`

        - Se si specifica il nome di un BLOB che non esiste già, Azure crea automaticamente il BLOB.

       -  Quando si scrive in un BLOB esistente, è possibile specificare che il contenuto corrente del BLOB venga sovrascritto impostando la proprietà, **modalità di scrittura dell'archiviazione BLOB di Azure**. Per impostazione predefinita, questa proprietà è impostata su **Error**, a indicare che viene generato un errore ogni volta che viene trovato un file BLOB esistente con lo stesso nome.


    4. Per **formato file per file BLOB**selezionare il formato in cui archiviare i dati.

        - **CSV**: I valori delimitati da virgole (CSV) sono il formato di archiviazione predefinito. Per esportare le intestazioni di colonna insieme ai dati, selezionare l'opzione **Scrivi riga intestazione BLOB**.  Per altre informazioni sul formato delimitato da virgole usato in Azure Machine Learning, vedere [Convert to CSV (Converti in CSV](./convert-to-csv.md)).

        - **TSV**: Il formato con valori delimitati da tabulazioni (TSV) è compatibile con molti strumenti di machine learning. Per esportare le intestazioni di colonna insieme ai dati, selezionare l'opzione **Scrivi riga intestazione BLOB**.  

 
    5. **USA risultati memorizzati nella cache**: Selezionare questa opzione se si vuole evitare di riscrivere i risultati nel file BLOB ogni volta che si esegue l'esperimento. Se non sono presenti altre modifiche ai parametri del modulo, l'esperimento scrive i risultati solo la prima volta che il modulo viene eseguito o quando sono presenti modifiche ai dati.

    6. Eseguire l'esperimento.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 