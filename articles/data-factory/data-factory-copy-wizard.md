---
title: Copiare i dati facilmente con la Copia guidata - Azure | Documentazione Microsoft
description: Informazioni su come usare Copia guidata di Data Factory per copiare i dati da origini dati supportate nei sink.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: 49545648a069f25820f6a6e483072be8410b091e
ms.openlocfilehash: 0fc9eb7869ebe7316e4aed0d561638a05bc9a91c
ms.contentlocale: it-it
ms.lasthandoff: 01/24/2017

---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Copiare o spostare facilmente i dati con Copia guidata di Azure Data Factory
La copia guidata di Azure Data Factory semplifica l'inserimento di dati, che rappresenta in genere il primo passaggio in uno scenario di integrazione dati end-to-end. Quando si esegue la copia guidata di Azure Data Factory, non è necessario comprendere le definizioni JSON per i set di dati, le pipeline e i servizi collegati. Tuttavia, dopo aver completato tutti i passaggi della procedura guidata, verrà automaticamente creata una pipeline per copiare i dati dall'origine dati selezionata alla destinazione scelta. In più, questa procedura guidata consente di convalidare i dati aggiunti al momento della creazione, così da risparmiare tempo, soprattutto al primo inserimento dei dati dall'origine dati. Per avviare Copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia guidata](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Una procedura guidata intuitiva per la copia dei dati
Questa procedura guidata consente di spostare facilmente i dati da un'ampia gamma di origini alle destinazioni in pochi minuti. Al termine della procedura guidata viene creata automaticamente una pipeline con un'attività di copia insieme a entità di Data Factory dipendenti (set di dati e servizi collegati). Per creare la pipeline non occorre eseguire altri passaggi.   

![Selezionare l'origine dati](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Leggere l'articolo sull'[esercitazione Copia guidata](data-factory-copy-data-wizard-tutorial.md) per istruzioni dettagliate sulla creazione di una pipeline di esempio per copiare i dati da un BLOB Azure a una tabella del database SQL di Azure. 
> 
> 

La procedura guidata è totalmente pensata per i Big Data. È semplice ed efficiente creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle con la procedura guidata per copiare i dati. La procedura guidata supporta le tre funzionalità seguenti: anteprima automatica dei dati, acquisizione dello schema e mapping e filtro dei dati. 

## <a name="automatic-data-preview"></a>Anteprima automatica dei dati
La copia guidata consente di esaminare parte dei dati dall'origine dati selezionata per confermare che si tratti esattamente dei dati che si desidera copiare. in più, se l'origine dati è in un file di testo, la copia guidata analizza il file di testo per apprendere automaticamente lo schema e i delimitatori di riga e colonna. 

![Impostazioni sul formato del file](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Acquisizione dello schema e mapping
In alcuni casi è possibile che lo schema di dati di input non corrisponda allo schema dei dati di output. Se succede, è necessario eseguire il mapping delle colonne che appartengono allo schema di origine con le colonne dello schema di destinazione. 

La copia guidata esegue automaticamente il mapping delle colonne nello schema di origine con le colonne nello schema di destinazione. È possibile ignorare i mapping usando gli elenchi a discesa oppure specificando se una colonna deve essere saltata durante la copia dei dati.   

![Mapping dello schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtro dei dati
La procedura guidata consente di filtrare i dati di origine per selezionare soltanto quelli da copiare nell'archivio dati di destinazione/sink. L'uso del filtro consente di ridurre il volume di dati da copiare nell'archivio dati sink, aumentando l'efficacia dell'operazione di copia. Offre un modo flessibile di filtrare i dati in un database relazionale tramiteil linguaggio query SQL o i file in una cartella BLOB di Azure usando [le variabili e le funzioni di Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtro dei dati in un database
Nell'esempio, la query SQL usa la funzione `Text.Format` e la variabile `WindowStart`. 

![Espressione di convalida](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtro dei dati in una cartella BLOB di Azure
È possibile usare le variabili nel percorso della cartella per copiare dati da una cartella determinata in fase di esecuzione in base alle [variabili di sistema](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{custom}**. Esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Fare clic sul pulsante **Esplora** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere visualizzato `2016/03/01/02`. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** con **{day}** e **02** con **{hour}**, poi premere Tab. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il formato per queste quattro variabili:

![Uso di variabili di sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Come mostra lo screenshot seguente, è anche possibile usare una variabile **personalizzata** e qualsiasi [stringa di formato supportato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Per selezionare una cartella con tale struttura, usare prima di tutto il pulsante **Esplora** . Sostituire un valore con **{custom}**e quindi premere TAB per visualizzare la casella di testo in cui è possibile digitare la stringa di formato.     

![Uso di variabili personalizzate](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Supporto per tipi di dati e di oggetti diversi
La procedura guidata di copia permette di spostare centinaia di cartelle, file o tabelle in modo efficiente.

![Selezionare le tabelle da cui copiare i dati](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Opzioni di pianificazione
È possibile eseguire l'operazione di copia una sola volta oppure in base a una pianificazione, con cadenza oraria, giornaliera e così via. Entrambe queste opzioni possono essere usate per tutti i vari connettori tra copie in locale, nel cloud e nei computer desktop locali.

Un'operazione di copia eseguita una sola volta permette di spostare dati da un'origine a una destinazione una sola volta. Si applica ai dati di qualsiasi dimensione e in qualsiasi formato supportato. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Per configurare la copia pianificata sono disponibili impostazioni avanzate, come la ripetizione dei tentativi, il timeout, gli avvisi e così via.

![Proprietà di pianificazione](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Passaggi successivi
Per una procedura dettagliata sull'uso di Copia guidata di Data Factory per creare una pipeline con l'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).


