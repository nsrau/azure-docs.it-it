<properties 
	pageTitle="Introduzione a Ricerca di Azure | Microsoft Azure | Introduzione a Ricerca di Azure | DocumentDB | Servizio di ricerca cloud" 
	description="Creare la prima soluzione di Ricerca di Azure con questa procedura dettagliata. Informazioni su come creare un indice di Ricerca di Azure con dati di DocumentDB. Questo è un esercizio senza codice basato sul portale, che usa la procedura guidata Importa dati." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

# Introduzione a Ricerca di Azure nel portale
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-get-started-dotnet.md)

Questa introduzione senza codice permette di iniziare a usare Ricerca di Microsoft Azure con funzionalità incorporate direttamente nel portale.

L'esercitazione presuppone la disponibilità di un [database Azure DocumentDB di esempio](#apdx-sampledata) semplice da creare con i dati di esempio e le istruzioni fornite, ma questi passaggi possono essere applicati anche ai dati esistenti nel database SQL o DocumentDB.

> [AZURE.NOTE] Questa esercitazione introduttiva richiede una [sottoscrizione di Azure](../../includes/free-trial-note.md) e un [servizio di Ricerca di Azure](search-create-service-portal.md). Se non si è pronti per iscriversi a una sottoscrizione di valutazione, è possibile ignorare questa esercitazione e scegliere invece [provare Azure App Service](search-tryappservice.md). Questa opzione alternativa non consente di Ricerca di Azure con un'app Web ASP.NET gratuitamente - un'ora per ogni sessione - è necessaria la sottoscrizione.
 
## Trovare il servizio

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
	- Nell'indice fare clic su **Servizi di ricerca**. L'indice elenca ogni servizio di cui è stato effettuato il provisioning nella sottoscrizione. Se è stato definito un servizio di ricerca, verrà visualizzato nell'elenco **Servizi di ricerca**.
	- Nell'indice fare clic su **Sfoglia** e quindi digitare "ricerca" nella casella di ricerca per generare un elenco di tutti i servizi di ricerca creati nelle proprie sottoscrizioni.

## Verificare lo spazio

Molti clienti iniziano con il servizio gratuito. Questa versione è limitata a tre indici, tre origini dati e tre gli indicizzatori. Assicurarsi di avere spazio per gli elementi aggiuntivi prima di iniziare, perché ne verrà creato uno per ogni oggetto.

## Creare un indice e caricare i dati

Le query di ricerca scorrono un *indice* contenente dati ricercabili, metadati e costrutti usati per l'ottimizzazione di determinati comportamenti di ricerca. Come primo passaggio, viene definito e popolato un indice.

Per creare un indice si possono usare diversi modi. Se i dati si trovano in un archivio in cui Ricerca di Azure può effettuare una ricerca per indicizzazione, ad esempio un database SQL di Azure, SQL Server in una macchina virtuale di Azure o DocumentDB, si potrà creare e popolare molto facilmente un indice con un *indicizzatore*.

Per mantenere questa attività basata sul portale, si presuppone che sia possibile effettuare una ricerca per indicizzazione sui dati provenienti da DocumentDB usando un indicizzatore con l'**Importazione guidata dati**.

Prima di continuare, creare un [database DocumentDB di esempio](#apdx-sampledata) da usare con l'esercitazione e quindi tornare a questa sezione per completare la procedura seguente.

<a id="defineDS"></a>
#### Passaggio 1: Definire l'origine dati

1. Nel dashboard del servizio Ricerca di Azure fare clic su **Importa dati** nella barra dei comandi per avviare una procedura guidata che crea e popola un indice.

  ![][7]

2. Nella procedura guidata fare clic su **Origine dati** > **DocumentDB** > **Nome** e digitare un nome per l'origine dati. Un'origine dati è un oggetto connessione in Ricerca di Azure che può essere usato con altri indicizzatori. Una volta creata, diventa disponibile come "origine dati esistente" nel servizio.

3. Scegliere l'account DocumentDB esistente, nonché il database e la raccolta. Se si usano i dati di esempio forniti, la definizione dell'origine dati sarà simile alla seguente:

  ![][2]

Si noti che la query viene ignorata. Il motivo è che questa volta non viene implementato il rilevamento delle modifiche nel set di dati. Se il set di dati include un campo che tiene traccia del momento in cui un record viene aggiornato, è possibile configurare un indicizzatore di Ricerca di Azure per usare il rilevamento delle modifiche per aggiornamenti selettivi dell'indice.

Fare clic su **OK** per completare questo passaggio della procedura guidata.

#### Passaggio 2: Definire l'indice

Sempre nella procedura guidata fare clic su **Indice** e osservare l'area di progettazione usata per creare un indice di Ricerca di Azure. Un indice richiede come minimo un nome e una raccolta di campi, con un campo contrassegnato come chiave del documento. Poiché si usa un set di dati di DocumentDB, i campi vengono rilevati automaticamente dalla procedura guidata e l'indice viene precaricato con campi e assegnazioni dei tipi di dati.

  ![][3]

Anche se i campi e i tipi di dati sono configurati, è comunque necessario assegnare gli attributi. Le caselle di controllo nella parte superiore dell'elenco di campi sono *attributi dell'indice* che controllano come viene usato il campo.

- **Recuperabile** indica che viene visualizzato nell'elenco dei risultati della ricerca. Deselezionando questa casella di controllo è possibile contrassegnare i singoli campi perché siano esclusi dai risultati della ricerca, ad esempio quando vengono usati solo nelle espressioni di filtro. 
- **Filtrabile**, **Ordinabile** e **Con facet** determinano se un campo può essere usato in un filtro, un ordinamento o una struttura di esplorazione in base a facet. 
- **Ricercabile** indica che un campo è incluso nella ricerca full-text. Le stringhe sono in genere ricercabili. I campi numerici e i campi booleani sono spesso contrassegnati come non ricercabili. 

Prima di uscire da questa pagina, contrassegnare i campi nell'indice per l'uso delle opzioni seguenti, ovvero Recuperabile, Ricercabile e così via. La maggior parte dei campi è Recuperabile. Quasi tutti i campi di tipo stringa sono ricercabili. Non è necessario impostare il campo Key come ricercabile. Alcuni campi come genre, orderableOnline, rating e tags sono anche Filtrabile, Ordinabile e Con facet.
	
Campo | Tipo | Opzioni |
------|------|---------|
key | Edm.String | |
albumTitle | Edm.String | Recuperabile, Ricercabile |
albumUrl | Edm.String | Recuperabile, Ricercabile |
genre | Edm.String | Recuperabile, Ricercabile, Filtrabile, Ordinabile, Con facet |
genreDescription | Edm.String | Recuperabile, Ricercabile |
artistName | Edm.String | Recuperabile, Ricercabile |
orderableOnline | Edm.Boolean | Recuperabile, Filtrabile, Ordinabile, Con facet |
tags | Collection(Edm.String) | Recuperabile, Filtrabile, Con facet |
price | Edm.Double | Recuperabile, Filtrabile, Con facet |
margin | Edm.Int32 | |
rating | Edm.Int32 | Recuperabile, Filtrabile, Ordinabile, Con facet |
inventory | Edm.Int32 | Recuperabile |
lastUpdated | Edm.DateTimeOffset | |

Come punto di confronto, nella schermata seguente è riportata l'immagine di un indice creato in base alla specifica riportata nella tabella precedente.

 ![][4]

Fare clic su **OK** per completare questo passaggio della procedura guidata.

#### Passaggio 3: Definire l'indicizzatore

Sempre nella procedura guidata **Importa dati** fare clic su **Indicizzatore** > **Nome**, digitare un nome per l'indicizzatore e usare i valori predefiniti per tutti gli altri valori. Questo oggetto definisce un processo eseguibile. Dopo averlo creato sarà possibile inserirlo nella pianificazione ricorrente. Per il momento usare l'opzione predefinita per avviare l'esecuzione dell'indicizzatore una volta, immediatamente, facendo clic su **OK**.

Tutte le voci relative all'importazione dei dati risulteranno compilate e pronte all'uso.

  ![][5]

Per eseguire la procedura guidata, fare clic su **OK** per avviare l'importazione e chiudere la procedura guidata.

## Verificare l'avanzamento

Per controllare lo stato, tornare al dashboard del servizio, scorrere verso il basso e fare doppio clic sul riquadro **Indicizzatori** per aprire l'elenco corrispondente. L'indicizzatore appena creato verrà visualizzato nell'elenco e lo stato indicherà "in corso" oppure operazione riuscita, insieme al numero di documenti indicizzati in Ricerca di Azure.

  ![][6]

## Eseguire una query sull'indice

Ora è disponibile un indice di ricerca su cui è possibile eseguire query.

**Esplora ricerche** è uno strumento di query integrato nel portale. Fornisce una casella di ricerca che consente di verificare se l'input della ricerca restituisce i dati previsti.

1. Fare clic su **Esplora ricerche** nella barra dei comandi.
2. Verificare qual è l'indice attivo. Se non è quello appena creato, fare clic su **Cambia indice** nella barra dei comandi per selezionare l'indice richiesto.
2. Lasciare vuota la casella di ricerca e quindi fare clic sul pulsante **Cerca** per eseguire una ricerca con caratteri jolly che restituisce tutti i documenti.
3. Immettere alcune query di ricerca full-text. È possibile esaminare i risultati della ricerca con caratteri jolly per acquisire familiarità con gli artisti, gli album e i generi su cui eseguire query.
4. Provare un'altra sintassi di query usando gli [esempi forniti alla fine di questo articolo](https://msdn.microsoft.com/library/azure/dn798927.aspx) per avere qualche spunto. Modificare quindi la query in modo da usare stringhe di ricerca che è probabile trovare nell'indice.

## Passaggi successivi

Dopo aver eseguito una volta la procedura guidata, è possibile tornare indietro e visualizzare o modificare i singoli componenti, ovvero indice, indicizzatore oppure origine dati. Alcune modifiche, ad esempio il cambiamento del tipo di dati del campo, non sono consentite nell'indice, ma la maggior parte delle proprietà e delle impostazioni sono modificabili. Per visualizzare i singoli componenti, fare clic sul riquadro **Indice**, **Indicizzatore** oppure **Origini dati** nel dashboard per visualizzare un elenco di oggetti esistenti.

Per informazioni sulle altre funzionalità descritte in questo articolo, visitare i collegamenti seguenti:

- [Indicizzatori](search-indexer-overview.md)
- [Creare l'indice, include una spiegazione dettagliata degli attributi dell'indice.](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Esplora ricerche](search-explorer.md)
- [Eseguire ricerche nei documenti, include esempi di sintassi di query.](https://msdn.microsoft.com/library/azure/dn798927.aspx)

È possibile provare questo stesso flusso di lavoro con la procedura guidata Imposta dati per altre origini dati, ad esempio database SQL di Azure o SQL Server in macchine virtuali di Azure.

> [AZURE.NOTE] È stato annunciato di recente il supporto di un indicizzatore per la ricerca per indicizzazione nell'archivio BLOB di Azure, ma la funzionalità è in anteprima e non ancora un'opzione del portale. Per provare l'indicizzatore, è necessario scrivere codice. Per altre informazioni, vedere [Indicizzazione di documenti nell'archivio BLOB di Azure con Ricerca di Azure](search-howto-indexing-azure-blob-storage.md). <a id="apdx-sampledata"></a>


## Appendice: Creare dati di esempio in DocumentDB

Questa sezione crea un piccolo database in DocumentDB che può essere usato per completare le attività in questa esercitazione.

Le istruzioni seguenti forniscono indicazioni generali, ma non sono esaustive. Se è necessaria ulteriore assistenza per le attività o lo spostamento nel portale di DocumentDB, è possibile vedere la documentazione di DocumentDB. La maggior parte dei comandi necessari sono tuttavia sulla barra dei comandi del servizio nella parte superiore del dashboard o nel pannello del database.

  ![][1]

### Creare musicstoredb per questa esercitazione

1. [Fare clic qui](https://github.com/HeidiSteen/azure-search-get-started-sample-data) per scaricare un file ZIP contenente i file di dati JSON del Music Store. Per questo set di dati vengono forniti 246 documenti JSON.
2. Aggiungere DocumentDB alla sottoscrizione e quindi aprire il dashboard del servizio.
2. Fare clic su **Aggiungi database** per creare un nuovo database con l'ID `musicstoredb`. Una volta creato, verrà visualizzato in un elenco di database più in basso nella pagina.
2. Fare clic sul nome del database per aprire il pannello corrispondente.
3. Fare clic su **Aggiungi raccolta** per creare una raccolta con l'ID `musicstorecoll`.
3. Fare clic su **Esplora documenti**.
4. Fare clic su **Aggiungi documenti**.
5. In **Aggiungi documento** caricare i file JSON in batch non superiori a 100 file.
	- 386\.json
	- 387\.json
	- . . .
6. Fare clic su **Esplora query** per verificare che i dati vengano caricati in base ai requisiti di caricamento di Esplora documenti.

Un modo semplice per eseguire questa operazione consiste nel modificare la query predefinita in modo che selezioni i primi 300 elementi, se sono presenti meno di 300 elementi nel set di dati. In alternativa, è possibile scrivere `select * from musicstorecoll` e quindi fare clic su **Esegui query**.

Verrà restituito l'output JSON, a partire dal numero di documento 386 fino al documento 669. Dopo aver caricato i dati, è possibile [tornare ai passaggi di questa procedura dettagliata](#defineDS) per compilare un indice usando l'**Importazione guidata**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png

<!---HONumber=AcomDC_0224_2016-->