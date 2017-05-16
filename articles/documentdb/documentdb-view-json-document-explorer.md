---
title: 'Strumento del portale di Azure DocumentDB: Esplora documenti | Documentazione Microsoft'
description: Informazioni su Esplora documenti di DocumentDB, uno strumento del portale di Azure per visualizzare codice JSON, modificare, creare e caricare documenti JSON con il database di documenti NoSQL DocumentDB.
keywords: visualizzare json
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 029d81b3-6382-4799-a1bd-0dcbccd9968d
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 4338fc03664175b7c27a48f1ab682179c1035313
ms.lasthandoff: 04/21/2017


---
# <a name="view-edit-create-and-upload-json-documents"></a>Visualizzare, modificare, creare e caricare documenti JSON 

In questo articolo viene fornita una panoramica dei due modi per creare, modificare ed eseguire query su documenti nel portale: [Esplora documenti](#launch-document-explorer) e [Esplora dati (anteprima)](#data-explorer).

> [!NOTE]
> Esplora documenti non è abilitato per gli account DocumentDB con supporto del protocollo per MongoDB. Questa pagina verrà aggiornata quando verrà abilitata questa funzionalità.

<a id="launch-document-explorer"></a>

## <a name="launch-document-explorer-in-the-azure-portal"></a>Avviare Esplora documenti nel portale di Azure
1. Nel [portale di Azure](https://portal.azure.com) nel riquadro di navigazione a sinistra, fare clic sull'![icona di Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**. 

    Se **NoSQL (DocumentDB)** non è visibile, fare clic su **Altri servizi** nella parte inferiore, quindi fare clic sull'![icona di Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**.
2. Selezionare il nome dell'account. 
3. Nel menu delle risorse fare clic su **Esplora documenti**. 
   
    ![Schermata del comando Esplora documenti](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)
   
    Nel pannello **Esplora documenti** gli elenchi a discesa **Database** e **Raccolta** vengono precompilati a seconda del contesto in cui viene avviato Esplora documenti. 

## <a name="create-a-json-document"></a>Creare un documento JSON
1. [Avviare Esplora documenti](#launch-document-explorer).
2. Nel pannello **Esplora documenti** fare clic su **Crea documento**. 
   
    Nel pannello **Documento** è disponibile un frammento di codice JSON minimo.
   
    ![Screenshot dell'esperienza di creazione documenti di Esplora documenti, in cui è possibile visualizzare e modificare codice JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)
3. Nel pannello **Documento** digitare o incollare il contenuto del documento JSON da creare e quindi fare clic su **Salva** per eseguire il commit del documento nel database e nella raccolta specificati nel pannello **Esplora documenti**.
   
    ![Schermata del comando di salvataggio di Esplora documenti](./media/documentdb-view-JSON-document-explorer/savedocument1.png)
   
   > [!NOTE]
   > Se non si specifica una proprietà "ID", Esplora documenti aggiunge automaticamente una proprietà ID e genera un GUID come valore ID.
   > 
   > 
   
    Se si dispone già di dati di file JSON, MongoDB, SQL Server, file CSV, archiviazione tabelle di Azure, Amazon DynamoDB, HBase o di altre raccolte di DocumentDB, è possibile usare lo [strumento di migrazione dati](documentdb-import-data.md) di DocumentDB per importare rapidamente i dati.

## <a name="edit-a-json-document"></a>Modificare un documento JSON
1. [Avviare Esplora documenti](#launch-document-explorer).
2. Per modificare un documento esistente, selezionarlo nel pannello **Esplora documenti**, modificarlo nel pannello **Documento** e quindi fare clic su **Salva**.
   
    ![Screenshot della funzionalità di modifica documenti di Esplora documenti usata per visualizzare codice JSON](./media/documentdb-view-JSON-document-explorer/editdocument.png)
   
    Se si modifica un documento e si decide di eliminare il set di modifiche corrente, è sufficiente fare clic su **Annulla** nel pannello **Documento** e confermare l'azione. Verrà ricaricato lo stato precedente del documento.
   
    ![Schermata del comando per ignorare di Esplora documenti](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## <a name="delete-a-document-from-documentdb"></a>Eliminare un documento da DocumentDB
1. [Avviare Esplora documenti](#launch-document-explorer).
2. Selezionare il documento in **Esplora documenti**, fare clic su **Elimina** e quindi confermare l'eliminazione. Dopo la conferma, il documento viene immediatamente rimosso dall'elenco di Esplora documenti.
   
    ![Schermata del comando di eliminazione di Esplora documenti](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## <a name="work-with-json-documents"></a>Usare documenti JSON
Esplora documenti verifica che qualsiasi documento nuovo o modificato includa contenuto JSON valido.  È possibile visualizzare gli errori JSON anche passando il puntatore sulla sezione non corretta per ottenere informazioni dettagliate sull'errore di convalida.

![Screenshot di Esplora documenti in cui è evidenziato un codice JSON che non è valido](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

Esplora documenti impedisce anche di salvare un documento con contenuto JSON non valido.

![Screenshot di Esplora documenti con errore di salvataggio JSON non valido](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

Infine, Esplora documenti consente di visualizzare facilmente le proprietà di sistema del documento attualmente caricato facendo clic sul comando **Proprietà** .

![Schermata di visualizzazione delle proprietà del documento di Esplora documenti](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [!NOTE]
> La proprietà timestamp (_ts) viene rappresentata internamente come periodo, ma Esplora documenti consente di visualizzare il valore in un formato GMT leggibile.
> 
> 

## <a name="filter-documents"></a>Filtrare documenti
Esplora documenti supporta numerose opzioni di navigazione e impostazioni avanzate.

Per impostazione predefinita, Esplora documenti carica i primi 100 documenti della raccolta selezionata, dal meno recente al più recente, in base alla data di creazione.  È possibile caricare altri documenti (in batch di 100) selezionando l'opzione **Carica altro** nella parte inferiore del pannello di Esplora documenti. È possibile scegliere i documenti da caricare usando il comando **Filtra** .

1. [Avviare Esplora documenti](#launch-document-explorer).
2. Nella parte superiore del pannello **Esplora documenti** fare clic su **Filtra**.  
   
    ![Screenshot delle impostazioni filtro di Esplora documenti](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
3. Le impostazioni del filtro vengono visualizzate sotto la barra dei comandi. Nelle impostazioni del filtro specificare una clausola WHERE e/o ORDER BY e quindi fare clic su **Filtro**.
   
   ![Screenshot del pannello Impostazioni di Esplora documenti](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)
   
   Esplora documenti aggiorna automaticamente i risultati con i documenti corrispondenti alla query del filtro. Per altre informazioni sulla grammatica SQL di DocumentDB, vedere l'articolo [Query e sintassi SQL in DocumentDB](documentdb-sql-query.md) o stampare una copia del [Foglio informativo PDF di SQL DocumentDB](documentdb-sql-query-cheat-sheet.md).
   
   Gli elenchi a discesa **Database** e **Raccolta** possono essere usati per modificare facilmente la raccolta da cui sono attualmente visualizzati i documenti senza dover chiudere e riavviare Esplora documenti.  
   
   Esplora documenti supporta anche l'applicazione di filtri al set di documenti attualmente caricato in base alla relativa proprietà ID.  È sufficiente digitare Filtra per ID nella casella Documenti.
   
   ![Schermata di Esplora documenti con filtro evidenziato](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)
   
   I risultati nell'elenco di Esplora documenti vengono filtrati in base ai criteri forniti.
   
   ![Schermata di Esplora documenti con risultati filtrati](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)
   
   > [!IMPORTANT]
   > La funzionalità di filtro di Esplora documenti consente di filtrare solo dal set di documenti caricati ***attualmente*** e non esegue una query sulla raccolta attualmente selezionata.
   > 
   > 
4. Per aggiornare l'elenco dei documenti caricati da Esplora documenti, fare clic su **Aggiorna** nella parte superiore del pannello.
   
    ![Schermata del comando di aggiornamento di Esplora documenti](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## <a name="bulk-add-documents"></a>Aggiungere documenti in blocco
Esplora documenti supporta l'inserimento in blocco di uno o più documenti JSON esistenti, fino a 100 file JSON per operazione di caricamento.  

1. [Avviare Esplora documenti](#launch-document-explorer).
2. Per avviare il processo di caricamento, fare clic su **Carica documento**.
   
    ![Schermata della funzionalità di inserimento in blocco di Esplora documenti](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)
   
    Verrà visualizzato il pannello **Carica documento** . 
3. Fare clic sul pulsante Sfoglia per aprire una finestra di Esplora file, selezionare uno o più documenti JSON da caricare e quindi fare clic su **Apri**.
   
    ![Schermata del processo di inserimento in blocco di Esplora documenti](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)
   
   > [!NOTE]
   > Esplora documenti supporta attualmente fino a 100 documenti JSON per singola operazione di caricamento.
   > 
   > 
4. Dopo aver completato la selezione, fare clic sul pulsante **Carica** .  I documenti vengono aggiunti automaticamente alla griglia di Esplora documenti e i risultati del caricamento vengono visualizzati con l'avanzare dell'operazione. Gli errori di importazione vengono segnalati per i singoli file.
   
    ![Schermata dei risultati dell’inserimento in blocco di Esplora documenti](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)
5. Al termine dell'operazione, è possibile selezionare un massimo di altri 100 documenti da caricare.

<a id="data-explorer"></a>
## <a name="create-a-document-by-using-data-explorer-preview"></a>Creare un documento usando Esplora dati (anteprima)

L'altro metodo per creare, modificare ed eseguire query sui documenti nel portale consiste nell'usare Esplora dati. Per aprire Esplora dati, fare clic su **Esplora dati (anteprima)** nella barra di spostamento del portale, quindi espandere il nome del database e il nome della raccolta, fare clic su **Documenti**, quindi su **Nuovo documento**, come illustrato nella schermata seguente.

![Schermata che mostra il pulsante Nuova raccolta nel portale](./media/documentdb-view-JSON-document-explorer/azure-documentdb-data-explorer.png)

## <a name="work-with-json-documents-outside-the-portal"></a>Usare documenti JSON all'esterno del portale
Esplora documenti nel portale di Azure è soltanto uno dei modi in cui è possibile usare i documenti in DocumentDB. Per lavorare con i documenti è anche possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/mt489082.aspx) o gli [SDK client](documentdb-sdk-dotnet.md). Per un codice di esempio, vedere gli [esempi di documento per .NET SDK](documentdb-dotnet-samples.md#document-examples) e gli [esempi di documento per Node.js SDK](documentdb-nodejs-samples.md#document-examples).

Per eseguire la migrazione o importare file da un'altra origine, ad esempio file JSON, MongoDB, SQL Server, file CSV, archiviazione tabelle di Azure, Amazon DynamoDB o HBase, è possibile usare lo [strumento di migrazione dati](documentdb-import-data.md) di DocumentDB per importare rapidamente i dati in DocumentDB.

## <a name="troubleshoot"></a>Risoluzione dei problemi
**Sintomo**: Esplora documenti restituisce **Non sono stati trovati documenti**.

**Soluzione**: assicurarsi di avere selezionato la sottoscrizione, il database e la raccolta corretti in cui sono stati inseriti i documenti. Verificare anche che si stia usando una quota di velocità effettiva all'interno dei limiti consentiti. Se si usa il livello massimo di velocità effettiva e si riceve una limitazione, ridurre l'uso delle applicazioni a funzionare con la quota massima di velocità effettiva per la raccolta.

**Spiegazione**: il portale è un'applicazione come qualsiasi altra, che esegue chiamate alla raccolta e al database di DocumentDB. Se le richieste sono attualmente limitate a causa di chiamate effettuate da un'applicazione separata, anche il portale può essere limitato, causando la mancata visualizzazione delle risorse nel portale. Per risolvere il problema, correggere la causa dell'uso di velocità effettiva elevata e quindi aggiornare il pannello del portale. Informazioni su come misurare e ridurre l'uso della velocità effettiva sono reperibili nella sezione [Velocità effettiva](documentdb-performance-tips.md#throughput) dell'articolo [Suggerimenti per incrementare le prestazioni](documentdb-performance-tips.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla grammatica SQL di DocumentDB supportata in Esplora documenti, vedere l'articolo [Query e sintassi SQL in DocumentDB](documentdb-sql-query.md) o stampare una copia del [Foglio informativo PDF di SQL DocumentDB](documentdb-sql-query-cheat-sheet.md).


