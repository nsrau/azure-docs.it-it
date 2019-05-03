---
title: 'Esportazione dei dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di esportare i dati nel servizio Azure Machine Learning per salvare i risultati, i dati intermedi e dati in uso dei propri esperimenti in destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028321"
---
# <a name="export-data-module"></a>Modulo di esportazione dati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per salvare i risultati, i dati intermedi e inoltre i dati derivanti dagli esperimenti in destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.

Questo modulo supporta l'esportazione o salvataggio dei dati per i servizi dati cloud seguenti:


- **Esportare in archiviazione Blob di Azure**: Salva i dati nel servizio Blob di Azure. I dati nel servizio Blob possono essere condivise pubblicamente o salvati in archivi dati protetti dell'applicazione.

  
## <a name="how-to-configure-export-data"></a>Come configurare l'esportazione di dati

1. Aggiungere il **esportazione di dati** modulo all'esperimento nell'interfaccia. È possibile trovare questo modulo nel **di Input e Output** categoria.

2. Connettere **esportazione di dati** al modulo che contiene i dati da esportare.

3. Fare doppio clic su **Export Data** per aprire il **proprietà** riquadro.

4. Per la **destinazione dati**, selezionare il tipo di archiviazione cloud in cui si salveranno i dati. Se si apportano modifiche a questa opzione, vengono reimpostate tutte le altre proprietà. Pertanto, assicurarsi di scegliere questa opzione prima di tutto!

5. Fornire un metodo di autenticazione e nome account necessario per accedere all'account di archiviazione specificato.

    **Esportare in archiviazione Blob di Azure** è l'unica opzione disponibile in anteprima privata. Di seguito viene illustrato come impostare il modulo.
    1. Il servizio blob di Azure è per archiviare grandi quantità di dati, inclusi dati binari. Esistono due tipi di archiviazione blob: BLOB pubblici e i blob che richiedono credenziali di accesso.

    2. Per la **tipo di autenticazione**, scegliere **pubblico (SAS)** se si sa che lo spazio di archiviazione supporta l'accesso tramite un URL di firma di accesso condiviso.

          Un URL di firma di accesso condiviso è un tipo speciale di URL che può essere generato utilizzando un'utilità di archiviazione di Azure, è disponibile per un periodo di tempo limitato.  Contiene tutte le informazioni necessarie per l'autenticazione e il download.

        Per la **URI**digitare o incollare l'URI completo che definisce l'account e i blob pubblico.

        Per il formato di file, CSV e TSV sono supportati.

    3. Per gli account privati, scegliere **Account**e fornire il nome dell'account e la chiave dell'account, in modo che l'esperimento è possibile scrivere nell'account di archiviazione.

         - **Account name** (Nome dell'account): Digitare o incollare il nome dell'account in cui si desidera salvare i dati. Ad esempio, se è l'URL completo dell'account di archiviazione `http://myshared.blob.core.windows.net`, si digiterà `myshared`.

        - **Account key** (Chiave dell'account): Incollare la chiave di accesso di archiviazione che è associata all'account.

        -  **Percorso di directory, contenitore o blob**: Digitare il nome del blob in cui verranno archiviati i dati esportati. Ad esempio, per salvare i risultati dell'esperimento in un nuovo blob denominato **results01.csv** nel contenitore **stime** in un account denominato **mymldata**, l'URL completo per il BLOB sarebbe `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Pertanto, nel campo **percorso di directory, contenitore o blob**, si specificherà il contenitore e il nome di blob come segue: `predictions/results01.csv`

        - Se si specifica il nome di un blob che non esiste, Azure crea il blob per l'utente.

       -  Quando si scrive in un blob esistente, è possibile specificare che il contenuto corrente del blob sovrascritto impostando la proprietà **archiviazione blob di Azure alla modalità di scrittura**. Per impostazione predefinita, questa proprietà è impostata su **errore**, vale a dire che viene generato un errore ogni volta che viene trovato un file di blob esistente con lo stesso nome.


    4. Per la **formato di file blob**, selezionare il formato in cui devono essere archiviati i dati.

        - **CSV**: Valori delimitati da virgole (CSV) rappresentano il formato di archiviazione predefinito. Per esportare le intestazioni di colonna insieme ai dati, selezionare l'opzione **riga di intestazione di scrittura blob**.  Per altre informazioni sulle virgole - formato delimitato usato in Azure Machine Learning, vedere [Convert to CSV](./convert-to-csv.md).

        - **TSV**: Formato con valori delimitati da tabulazioni (TSV) è compatibile con molti strumenti di machine learning. Per esportare le intestazioni di colonna insieme ai dati, selezionare l'opzione **riga di intestazione di scrittura blob**.  

 
    5. **Usare i risultati memorizzati nella cache**: Selezionare questa opzione se si desidera evitare di dover riscrivere i risultati del file blob ogni volta che si esegue l'esperimento. Se vi sono altri cambiamenti per i parametri del modulo, l'esperimento scrive i risultati solo la prima volta il modulo viene eseguito, o quando sono presenti modifiche ai dati.

    6. Eseguire l'esperimento.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 