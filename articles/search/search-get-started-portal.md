---
title: Introduzione a Ricerca di Azure | Documentazione Microsoft
description: Informazioni sulla creazione del primo indice di Ricerca di Azure con questa procedura dettagliata e i dati di esempio di DocumentDB. Questo esercizio senza codice basato sul portale usa l&quot;importazione guidata dati.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/03/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dddbcbcd82900d7537c2d60631cc1753554d9486


---
# <a name="get-started-with-azure-search-in-the-portal"></a>Introduzione a Ricerca di Azure nel portale
Questa introduzione senza codice permette di iniziare a usare Ricerca di Microsoft Azure con funzionalità incorporate direttamente nel portale. 

L'esercitazione presuppone un [database di Azure DocumentDB di esempio](#apdx-sampledata) semplice da creare usando i dati e le istruzioni, ma è anche possibile adattare questi passaggi ai dati esistenti in DocumentDB o nel database SQL.

> [!NOTE]
> Questa esercitazione introduttiva richiede una [sottoscrizione di Azure](/pricing/free-trial/?WT.mc_id=A261C142F) e un [servizio Ricerca di Azure](search-create-service-portal.md). 
> 
> 

## <a name="find-your-service"></a>Trovare il servizio
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
   
   * Nell'indice fare clic su **Servizi di ricerca**. L'indice elenca tutti i servizi di cui è stato effettuato il provisioning nella sottoscrizione. Se è stato definito un servizio di ricerca, nell'elenco verrà visualizzato **Servizi di ricerca**.
   * Nell'indice fare clic su **Esplora** e quindi digitare "ricerca" nella casella di ricerca per generare un elenco di tutti i servizi di ricerca creati nelle proprie sottoscrizioni.

## <a name="check-for-space"></a>Verificare lo spazio
Molti clienti iniziano con il servizio gratuito. Questa versione è limitata a tre indici, tre origini dati e tre gli indicizzatori. Assicurarsi di avere spazio per gli elementi aggiuntivi prima di iniziare, perché ne verrà creato uno per ogni oggetto.

## <a name="create-an-index-and-load-data"></a>Creare un indice e caricare i dati
Le query di ricerca scorrono un *indice* contenente dati ricercabili, metadati e costrutti usati per l'ottimizzazione di determinati comportamenti di ricerca. Come primo passaggio viene definito e popolato un indice.

Per creare un indice si possono usare diversi modi. Se i dati si trovano in un archivio in cui Ricerca di Azure può eseguire una ricerca per indicizzazione, ad esempio un database SQL di Azure, SQL Server in una macchina virtuale di Azure o DocumentDB, è possibile creare e popolare un indice in tutta semplicità con un *indicizzatore*.

Per mantenere questa attività basata sul portale, si usano dati di DocumentDB sui quali è possibile eseguire una ricerca per indicizzazione con un indicizzatore mediante l'**importazione guidata dati**. 

Prima di continuare, creare un [database di DocumentDB di esempio](#apdx-sampledata) da usare con l'esercitazione e quindi tornare a questa sezione per completare la procedura seguente.

<a id="defineDS"></a>

#### <a name="step-1-define-the-data-source"></a>Passaggio 1: Definire l'origine dati
1. Nel dashboard del servizio Ricerca di Azure fare clic su **Importa dati** nella barra dei comandi per avviare una procedura guidata che crea e popola un indice.
   
    ![][7]
2. Nella procedura guidata fare clic su **Origine dati** > **DocumentDB** > **Nome** e digitare un nome per l'origine dati. Un'origine dati è un oggetto connessione in Ricerca di Azure che può essere usato con altri indicizzatori. Una volta creata, diventa disponibile come "origine dati esistente" nel servizio.
3. Scegliere l'account DocumentDB esistente, nonché il database e la raccolta. Se si usano i dati di esempio messi a disposizione, la definizione dell'origine dati sarà simile alla seguente:
   
    ![][2]

Si noti che la query viene ignorata. Il motivo è che questa volta non viene implementato il rilevamento delle modifiche nel set di dati. Se il set di dati include un campo che tiene traccia del momento in cui un record viene aggiornato, è possibile configurare un indicizzatore di Ricerca di Azure per usare il rilevamento delle modifiche per aggiornamenti selettivi dell'indice.

Fare clic su **OK** per completare questo passaggio della procedura guidata.

#### <a name="step-2-define-the-index"></a>Passaggio 2: Definire l'indice
Sempre nella procedura guidata fare clic su **Indice** e osservare l'area di progettazione usata per creare un indice di Ricerca di Azure. Un indice richiede come minimo un nome e una raccolta di campi, con un campo contrassegnato come chiave del documento. Poiché si usa un set di dati di DocumentDB, i campi vengono rilevati automaticamente dalla procedura guidata e l'indice viene precaricato con campi e assegnazioni dei tipi di dati. 

  ![][3]

Anche se i campi e i tipi di dati sono configurati, è comunque necessario assegnare gli attributi. Le caselle di controllo nella parte superiore dell'elenco di campi sono *attributi dell'indice* che controllano il modo in cui viene usato il campo. 

* **Recuperabile** indica che viene visualizzato nell'elenco dei risultati della ricerca. Deselezionando questa casella di controllo è possibile contrassegnare i singoli campi perché siano esclusi dai risultati della ricerca, ad esempio quando vengono usati solo nelle espressioni di filtro. 
* **Filtrabile**, **Ordinabile** e **Con facet** determinano se un campo può essere usato in un filtro, un ordinamento o una struttura di esplorazione in base a facet. 
* **Ricercabile** indica che un campo è incluso nella ricerca full-text. Le stringhe sono in genere ricercabili. I campi numerici e i campi booleani sono spesso contrassegnati come non ricercabili. 

Prima di uscire da questa pagina, contrassegnare i campi nell'indice per l'uso delle opzioni seguenti, ovvero Recuperabile, Ricercabile e così via. La maggior parte dei campi è Recuperabile. Quasi tutti i campi di tipo stringa sono ricercabili. Non è necessario impostare il campo Key come ricercabile. Alcuni campi come genre, orderableOnline, rating e tags sono anche Filtrabile, Ordinabile e Con facet. 

| Campo | Tipo | Opzioni |
| --- | --- | --- |
| id |Edm.String | |
| albumTitle |Edm.String |Recuperabile, Ricercabile |
| albumUrl |Edm.String |Recuperabile, Ricercabile |
| genre |Edm.String |Recuperabile, Ricercabile, Filtrabile, Ordinabile, Con facet |
| genreDescription |Edm.String |Recuperabile, Ricercabile |
| artistName |Edm.String |Recuperabile, Ricercabile |
| orderableOnline |Edm.Boolean |Recuperabile, Filtrabile, Ordinabile, Con facet |
| tags |Collection(Edm.String) |Recuperabile, Filtrabile, Con facet |
| price |Edm.Double |Recuperabile, Filtrabile, Con facet |
| margin |Edm.Int32 | |
| rating |Edm.Int32 |Recuperabile, Filtrabile, Ordinabile, Con facet |
| inventory |Edm.Int32 |Recuperabile |
| lastUpdated |Edm.DateTimeOffset | |

Come punto di confronto, nella schermata seguente è riportata l'immagine di un indice creato in base alla specifica riportata nella tabella precedente.

 ![][4]

Fare clic su **OK** per completare questo passaggio della procedura guidata.

#### <a name="step-3-define-the-indexer"></a>Passaggio 3: Definire l'indicizzatore
Sempre nell'**Importazione guidata dati** fare clic su **Indicizzatore** > **Nome**, digitare un nome per l'indicizzatore e usare i valori predefiniti per tutti gli altri valori. Questo oggetto definisce un processo eseguibile. Dopo averlo creato, sarà possibile inserirlo nella pianificazione ricorrente. Per il momento usare l'opzione predefinita per avviare l'esecuzione dell'indicizzatore una volta, immediatamente, facendo clic su **OK**. 

Tutte le voci relative all'importazione dei dati risulteranno compilate e pronte all'uso.

  ![][5]

Per eseguire la procedura guidata, fare clic su **OK** per avviare l'importazione e chiudere la procedura guidata.

## <a name="check-progress"></a>Verificare l'avanzamento
Per controllare lo stato, tornare al dashboard del servizio, scorrere verso il basso e fare doppio clic sul riquadro **Indicizzatori** per aprire l'elenco corrispondente. L'indicizzatore appena creato verrà visualizzato nell'elenco e lo stato indicherà "in corso" oppure operazione riuscita, insieme al numero di documenti indicizzati in Ricerca di Azure.

  ![][6]

## <a name="query-the-index"></a>Eseguire una query sull'indice
Ora è disponibile un indice di ricerca su cui è possibile eseguire query. 

**Esplora ricerche** è uno strumento di query integrato nel portale. Fornisce una casella di ricerca che consente di verificare se l'input della ricerca restituisce i dati previsti. 

1. Fare clic su **Esplora ricerche** nella barra dei comandi.
2. Verificare qual è l'indice attivo. Se non è quello appena creato, fare clic su **Cambia indice** nella barra dei comandi per selezionare l'indice richiesto.
3. Lasciare vuota la casella di ricerca e quindi fare clic sul pulsante **Cerca** per eseguire una ricerca con caratteri jolly che restituisce tutti i documenti.
4. Immettere alcune query di ricerca full-text. È possibile esaminare i risultati della ricerca con caratteri jolly per acquisire familiarità con gli artisti, gli album e i generi su cui eseguire query.
5. Provare un'altra sintassi di query usando gli [esempi forniti alla fine di questo articolo](https://msdn.microsoft.com/library/azure/dn798927.aspx) per avere qualche spunto. Modificare quindi la query per poter usare stringhe di ricerca che è probabile trovare nell'indice.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver eseguito una volta la procedura guidata, è possibile tornare indietro e visualizzare o modificare i singoli componenti, ovvero indice, indicizzatore oppure origine dati. Alcune modifiche, ad esempio il cambiamento del tipo di dati del campo, non sono consentite nell'indice, ma la maggior parte delle proprietà e delle impostazioni sono modificabili. Per visualizzare i singoli componenti, fare clic sul riquadro **Indice**, **Indicizzatore** oppure **Origini dati** nel dashboard per visualizzare un elenco di oggetti esistenti.

Per informazioni sulle altre funzionalità descritte in questo articolo, visitare i collegamenti seguenti:

* [Indicizzatori](search-indexer-overview.md)
* [Creare l'indice, include una spiegazione dettagliata degli attributi dell'indice.](https://msdn.microsoft.com/library/azure/dn798941.aspx)
* [Esplora ricerche](search-explorer.md)
* [Eseguire ricerche nei documenti, include esempi di sintassi di query.](https://msdn.microsoft.com/library/azure/dn798927.aspx)

È possibile provare questo stesso flusso di lavoro con la procedura guidata Imposta dati per altre origini dati, ad esempio database SQL di Azure o SQL Server in macchine virtuali di Azure.

> [!NOTE]
> È stato annunciato di recente il supporto di un indicizzatore per la ricerca per indicizzazione nell'archiviazione BLOB di Azure, ma la funzionalità è in anteprima e non ancora un'opzione del portale. Per provare l'indicizzatore è necessario scrivere codice. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) .
> <a id="apdx-sampledata"></a>
> 
> 

## <a name="appendix-create-sample-data-in-documentdb"></a>Appendice: Creare dati di esempio in DocumentDB
Questa sezione crea un piccolo database in DocumentDB che può essere usato per completare le attività in questa esercitazione.

Le istruzioni seguenti forniscono indicazioni generali, ma non sono esaustive. Se è necessaria altra assistenza per le attività o lo spostamento nel portale di DocumentDB, è possibile vedere la documentazione di DocumentDB. La maggior parte dei comandi necessari sono tuttavia sulla barra dei comandi del servizio nella parte superiore del dashboard o nel pannello del database. 

  ![][1]

### <a name="create-musicstoredb-for-this-tutorial"></a>Creare musicstoredb per questa esercitazione
1. [Fare clic qui](https://github.com/HeidiSteen/azure-search-get-started-sample-data) per scaricare un file ZIP contenente i file di dati JSON del Music Store. Per questo set di dati vengono forniti 246 documenti JSON.
2. Aggiungere DocumentDB alla sottoscrizione e quindi aprire il dashboard del servizio.
3. Fare clic su **Aggiungi database** per creare un nuovo database con l'ID `musicstoredb`. Una volta creato, il database verrà visualizzato nel riquadro di database più in basso nella pagina.
4. Fare clic sul nome del database per aprire il pannello corrispondente.
5. Fare clic su **Aggiungi raccolta** per creare una raccolta con l'ID `musicstorecoll`.
6. Fare clic su **Esplora documenti**.
7. Fare clic su **Carica**.
8. In **Carica documento**passare alla cartella locale che contiene i file JSON scaricati in precedenza. Selezionare i file JSON in batch non superiori a 100.
   * 386.json
   * 387.json
   * . . .
   * 486.json
9. Ripetere questo passaggio per ottenere il successivo batch di file fino a caricare l'ultimo, ovvero 669.json.
10. Fare clic su **Esplora query** per verificare che i dati vengano caricati in base ai requisiti di caricamento di Esplora documenti.

Un modo semplice per eseguire questa operazione consiste nell'usare la query predefinita, ma questa può anche essere modificata in modo che selezioni i primi 300 elementi, se il set di dati contiene meno di 300 elementi.

Verrà restituito l'output JSON, a partire dal numero di documento 386 fino al documento 669. Dopo aver caricato i dati, è possibile [tornare ai passaggi di questa procedura dettagliata](#defineDS) per creare un indice tramite l'**Importazione guidata dati**.

<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=Nov16_HO2-->


