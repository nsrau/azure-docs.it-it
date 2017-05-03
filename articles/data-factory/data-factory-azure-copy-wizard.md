---
title: Copia guidata di Azure Data Factory | Microsoft Docs
description: Informazioni su come usare Copia guidata di Azure Data Factory per copiare i dati da origini dati supportate nei sink.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 445dd0dcd05aa25cc531e2d10cc32ad8f32a6e8c
ms.openlocfilehash: def038842dc3c1037d5dd11ffef6a9e2de3cf09f
ms.lasthandoff: 02/09/2017


---
# <a name="azure-data-factory-copy-wizard"></a>Copia guidata di Azure Data Factory
La copia guidata di Azure Data Factory semplifica l'inserimento di dati, che rappresenta in genere il primo passaggio in uno scenario di integrazione dati end-to-end. Quando si esegue la copia guidata di Azure Data Factory, non è necessario comprendere le definizioni JSON per i set di dati, le pipeline e i servizi collegati. La procedura guidata crea automaticamente una pipeline per copiare i dati dall'origine dati selezionata alla destinazione scelta. In più, la copia guidata consente di convalidare i dati inseriti al momento della creazione, risparmiando tempo soprattutto quando si inseriscono i dati per la prima volta dall'origine dati. Per avviare Copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia guidata](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Pensata per i Big Data
Questa procedura guidata consente di spostare facilmente i dati da un'ampia gamma di origini alle destinazioni in pochi minuti. Al termine della procedura guidata viene creata automaticamente una pipeline con un'attività di copia insieme a entità di Data Factory dipendenti (set di dati e servizi collegati). Per creare la pipeline non occorre eseguire altri passaggi.   

![Selezionare l'origine dati](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Leggere l'articolo sull'[esercitazione Copia guidata](data-factory-copy-data-wizard-tutorial.md) per istruzioni dettagliate sulla creazione di una pipeline di esempio per copiare i dati da un BLOB Azure a una tabella del database SQL di Azure.
>
>

La procedura guidata è stata concepita fin da subito per l'uso con i Big Data, offrendo il supporto a tipi differenti di oggetti e dati. È possibile creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle. La procedura guidata supporta l'anteprima automatica dei dati, il mapping e l'acquisizione dello schema, oltre al filtro dei dati.

## <a name="automatic-data-preview"></a>Anteprima automatica dei dati
È possibile visualizzare l'anteprima di una parte dei dati dall'origine dati selezionata per confermare che si tratti dei dati che si vuole copiare. in più, se l'origine dati è in un file di testo, la copia guidata analizza il file di testo per apprendere automaticamente lo schema e i delimitatori di riga e colonna.

![Impostazioni sul formato del file](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Acquisizione dello schema e mapping
In alcuni casi è possibile che lo schema di dati di input non corrisponda allo schema dei dati di output. Se succede, è necessario eseguire il mapping delle colonne che appartengono allo schema di origine con le colonne dello schema di destinazione.

> [!TIP]
> Quando si copiano dati da SQL Server o da Database SQL di Azure in SQL Data Warehouse, se la tabella non esiste nell'archivio di destinazione, Data Factory ne supporta la creazione automatica usando lo schema dell'origine. Altre informazioni su come [Spostare dati da e verso Azure SQL Data Warehouse con Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Usare un elenco a discesa per selezionare una colonna dallo schema di origine per eseguire il mapping a una colonna nello schema di destinazione. La copia guidata tenta di comprendere il modello dell'utente per il mapping di colonna, applicandone uno uguale alle colonne rimanenti, senza la necessità di selezionarle singolarmente per completare il mapping dello schema. Se si preferisce, è possibile eseguire l'override di questi mapping usando gli elenchi a discesa per eseguire il mapping singolo di ciascuna colonna. Il modello diventa più preciso quando si esegue il mapping di più colonne. La copia guidata aggiorna costantemente il modello e, alla fine, delinea il modello corretto per il mapping della colonna che si vuole ottenere.     

![Mapping dello schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtro dei dati
È possibile filtrare i dati di origine per selezionare soltanto quelli da copiare nell'archivio dati sink. L'uso del filtro consente di ridurre il volume di dati da copiare nell'archivio dati sink, aumentando l'efficacia dell'operazione di copia. Offre un modo flessibile di filtrare i dati in un database relazionale tramiteil linguaggio query SQL o i file in una cartella BLOB di Azure usando [le variabili e le funzioni di Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtro dei dati in un database
La schermata seguente mostra una query SQL usando la funzione `Text.Format` e la variabile `WindowStart`.

![Espressione di convalida](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtro dei dati in una cartella BLOB di Azure
È possibile usare le variabili nel percorso della cartella per copiare dati da una cartella determinata in fase di esecuzione in base alle [variabili di sistema](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{custom}**. Ad esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Fare clic sul pulsante **Esplora** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere visualizzato `2016/03/01/02`. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** con **{day}** e **02** con **{hour}**, quindi premere il tasto **Tab**. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il formato per queste quattro variabili:

![Uso di variabili di sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Come mostra lo screenshot seguente, è anche possibile usare una variabile **personalizzata** e qualsiasi [stringa di formato supportato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Per selezionare una cartella con tale struttura, usare prima di tutto il pulsante **Esplora** . Sostituire un valore con **{custom}**e quindi premere il tasto **Tab** per visualizzare la casella di testo in cui è possibile digitare la stringa di formato.     

![Uso di variabili personalizzate](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opzioni di pianificazione
È possibile eseguire l'operazione di copia una sola volta oppure in base a una pianificazione, con cadenza oraria, giornaliera e così via. Entrambe queste opzioni possono essere usate per tutti i vari connettori tra gli ambienti, incluse le copie in locale, nel cloud e nei computer desktop locali.

Un'operazione di copia eseguita una sola volta permette di spostare dati da un'origine a una destinazione una sola volta. Si applica ai dati di qualsiasi dimensione e in qualsiasi formato supportato. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Per configurare la copia pianificata sono disponibili impostazioni avanzate, come la ripetizione dei tentativi, il timeout, gli avvisi e così via.

![Proprietà di pianificazione](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passaggi successivi
Per una procedura dettagliata sull'uso di Copia guidata di Data Factory per creare una pipeline con l'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).

