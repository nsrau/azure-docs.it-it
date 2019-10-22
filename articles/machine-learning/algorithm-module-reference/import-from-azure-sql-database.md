---
title: Importare dal database SQL di Azure
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Import Data (Importa dati) in Azure Machine Learning interfaccia visiva per ottenere dati da un database SQL di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694610"
---
# <a name="import-from-azure-sql-database"></a>Importare dal database SQL di Azure

Questo articolo descrive come usare il modulo [Import Data (Importa dati](import-data.md) ) in Azure Machine Learning interfaccia visiva per ottenere dati da un database SQL di Azure.  

Per importare dati da un database di, è necessario specificare sia il nome del server che il nome del database e un'istruzione SQL che definisce la tabella, la vista o la query.  

In generale, l'archiviazione dei dati nei database di Azure è più costosa rispetto all'uso di tabelle o BLOB in Azure. Potrebbero inoltre essere presenti limiti sulla quantità di dati che è possibile archiviare in un database, a seconda del tipo di sottoscrizione. Tuttavia, non sono previsti costi delle transazioni per SQL Azure database, pertanto questa opzione è ideale per l'accesso rapido a quantità più ridotte di informazioni usate di frequente, ad esempio tabelle di ricerca dei dati o dizionari di dati.

È preferibile archiviare i dati in un database di Azure anche se è necessario essere in grado di filtrare i dati prima di leggerli oppure se si desidera salvare le stime o le metriche nel database per la creazione di report.

## <a name="how-to-import-data-from-azure-sql-database"></a>Come importare dati dal database SQL di Azure

1. Aggiungere il modulo [Import Data (Importa dati](import-data.md) ) alla pipeline. È possibile trovare questo modulo nell'interfaccia visiva, nella categoria input e output dei dati.

1. Per **origine dati**selezionare **database SQL di Azure**.

1. Impostare le opzioni seguenti specifiche per il database SQL di Azure.

    **Nome server di database**: digitare il nome del server generato da Azure. In genere ha il formato `<generated_identifier>.database.windows.net`.

    **Nome database**: digitare il nome di un database esistente nel server specificato.

    **Nome account utente del server**: digitare il nome utente di un account che dispone delle autorizzazioni di accesso per il database.

    **Password account utente server**: specificare la password per l'account utente specificato.

    **Query di database**: digitare o incollare un'istruzione SQL che descriva i dati da leggere. Convalidare sempre l'istruzione SQL e verificare i risultati della query in anticipo, usando uno strumento come Visual Studio Esplora server o SQL Server Data Tools.

1. Se il set di dati letto Azure Machine Learning non è previsto per la modifica tra le esecuzioni della pipeline, selezionare l'opzione **USA risultati memorizzati nella cache** .

    Quando viene selezionato, se non sono presenti altre modifiche ai parametri del modulo, la pipeline carica i dati la prima volta che il modulo viene eseguito e successivamente usa una versione memorizzata nella cache del set di dati.

    Deselezionare questa opzione se si vuole ricaricare il set di dati in ogni iterazione della pipeline. Il set di dati viene ricaricato dall'origine ogni volta che i parametri vengono modificati nei [dati di importazione](import-data.md).

1. Eseguire la pipeline.

    Poiché i dati di [importazione](import-data.md) caricano i dati nell'interfaccia visiva, è possibile che venga eseguita una conversione implicita del tipo, a seconda dei tipi di dati utilizzati nel database di origine.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.

Facoltativamente, è possibile modificare il set di dati e i relativi metadati usando gli strumenti disponibili in interfaccia visiva:

- Utilizzare [Modifica metadati](edit-metadata.md) per modificare i nomi delle colonne, convertire una colonna in un tipo di dati diverso o per indicare quali colonne sono etichette o funzionalità.

- Utilizzare [Seleziona colonne nel set di dati](select-columns-in-dataset.md) per selezionare un subset di colonne.

- Usare [Partition and Sample](partition-and-sample.md) per separare il set di dati in base ai criteri oppure ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
