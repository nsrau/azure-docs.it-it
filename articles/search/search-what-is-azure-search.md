<properties
	pageTitle="Che cos'è la Ricerca di Azure? | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Ricerca di Azure è un servizio di ricerca interamente ospitato sul cloud. In questa panoramica delle funzionalità sono fornite ulteriori informazioni sul servizio."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="01/12/2016"
	ms.author="ashmaka"/>

# Che cos'è la Ricerca di Azure?
Ricerca di Azure è una soluzione di ricerca distribuita come servizio cloud che delega la gestione di server e infrastruttura a Microsoft, offrendo un servizio pronto per l'uso che consente di inserire dati e di aggiungere ricerche sulle applicazioni Web o mobili. Ricerca di Azure consente di implementare facilmente un'esperienza di ricerca affidabile nelle applicazioni usando una semplice [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [.NET SDK](search-howto-dotnet-sdk.md) senza dover gestire l'infrastruttura di ricerca o diventare esperti di ricerca.

## Offrire agli utenti un'esperienza di ricerca avanzata

**Query efficaci** possono essere formulate mediante una [semplice sintassi di query](https://msdn.microsoft.com/library/azure/dn798920.aspx), in grado di offrire operatori logici, operatori di ricerca di una frase, operatori di suffisso, operatori di precedenza. Inoltre, la [sintassi di query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) (attualmente in anteprima) consente la ricerca fuzzy, la ricerca di errori di ortografia, la ricerca per prossimità, l'aumento della priorità dei termini e le espressioni regolari. Ricerca di Azure supporta anche gli analizzatori lessicali personalizzati per consentire all'applicazione di gestire query di ricerca complesse mediante la corrispondenza fonetica e le espressioni regolari.

È incluso il **supporto** per [56 lingue diverse](https://msdn.microsoft.com/library/azure/dn879793.aspx). Tramite gli analizzatori Lucene e Microsoft (perfezionati da anni di elaborazione del linguaggio naturale in Office e Bing), la Ricerca di Azure può analizzare il testo nella casella di ricerca dell'applicazione per gestire in modo intelligente le funzionalità linguistiche di una lingua specifica, tra cui i tempi verbali, il genere, i sostantivi plurali irregolari (ad esempio, 'uomo' vs. 'uomini'), la scomposizione delle parole, il ritorno a capo (per le lingue senza spazi) e altro.

I **suggerimenti per la ricerca** possono essere abilitati per le barre di ricerca con completamento automatico e le query di digitazione. Agli utenti che immettono un input di ricerca parziale vengono [suggeriti i documenti presenti nell'indice](https://msdn.microsoft.com/library/azure/dn798936.aspx).

L’**evidenziazione** [consente](https://msdn.microsoft.com/library/azure/dn798927.aspx) agli utenti di visualizzare il frammento di testo in ogni risultato che contiene corrispondenze per la query effettuata. È possibile scegliere i campi che devono restituire i frammenti evidenziati.

L’**esplorazione in base a facet** può essere aggiunta facilmente alle pagine dei risultati della ricerca con Ricerca di Azure. Usando [un singolo parametro di query](https://msdn.microsoft.com/library/azure/dn798927.aspx), Ricerca di Azure restituirà tutte le informazioni necessarie per creare un'esperienza di ricerca basata su facet nell'interfaccia utente dell'applicazione in modo da consentire agli utenti di eseguire il drill-down e filtrare i risultati di ricerca, ad esempio filtrando gli articoli del catalogo per fascia di prezzo o marca.

Con il **supporto** [geospaziale](search-create-geospatial.md) è possibile elaborare, filtrare e visualizzare le posizioni geografiche in modo intelligente. Ricerca di Azure consente agli utenti di esplorare i dati in base alla prossimità di un risultato della ricerca in una posizione specificata o in base a una specifica area geografica.

È possibile utilizzare i **filtri** per incorporare facilmente l'esplorazione in base a facet nell'interfaccia utente dell'applicazione, migliorare la formulazione di query e filtrare in base a criteri specificati dall'utente o dallo sviluppatore. Creare filtri efficaci mediante la [sintassi OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## Un servizio facile da usare a disposizione degli sviluppatori

La **disponibilità elevata** garantisce un'esperienza del servizio di ricerca estremamente affidabile. Se si applica una scala corretta, [Ricerca di Azure offre un contratto di servizio con disponibilità del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché prevede una **gestione completa** come soluzione end-to-end, Ricerca di Azure non richiede assolutamente alcun tipo di gestione dell'infrastruttura. Il servizio può essere facilmente adattato alle proprie esigenze scalando in due dimensioni per gestire più archivi di documenti, carichi di query maggiori o entrambi.

L'**integrazione dei dati** basata sugli [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) consente a Ricerca di Azure di eseguire automaticamente una ricerca per indicizzazione nel database SQL di Azure, in Azure DocumentDB o nell'[archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) per sincronizzare il contenuto dell'indice di ricerca con l'archivio dati primario.

È disponibile la **decifrazione del documento** [ (attualmente in anteprima) per leggere e indicizzare i principali formati di file](search-howto-indexing-azure-blob-storage.md) tra cui Microsoft Office, oltre a documenti PDF e HTML.

I dati sull’**analisi del traffico di ricerca** possono essere [facilmente raccolti e analizzati](search-traffic-analytics.md) per dedurre informazioni sulla base delle parole digitate dagli utenti nella casella di ricerca.

Uno dei vantaggi chiave di Ricerca di Azure è la **semplicità di assegnazione dei punteggi**. Vengono usati [profili di punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx) per consentire alle organizzazioni di modellare la rilevanza in funzione dei valori nei documenti stessi. Ad esempio, si potrebbe decidere che prodotti più recenti o prodotti scontati compaiano prima tra i risultati della ricerca. È inoltre possibile compilare profili di puntaggio utilizzando tag per il punteggio personalizzati in base alle preferenze di ricerca cliente che sono stati rilevati e archiviati separatamente.

L’**ordinamento** è disponibile per più campi tramite lo schema dell'indice e viene attivato o disattivato in fase di query con un solo parametro di ricerca.

Il **paging** e la limitazione dei risultati della ricerca [non comportano alcuna difficoltà grazie al controllo accurato](search-pagination-page-layout.md) che Ricerca di Azure offre sui risultati della ricerca.

**Esplora ricerche** consente di eseguire query su tutti gli indici direttamente dal portale di Azure dell'account in uso per poter facilmente testare le query e perfezionare i profili di assegnazione punteggi.

## Funzionamento

### 1\. Servizio di provisioning
È possibile attivare un servizio Ricerca di Azure usando il [portale Azure](https://portal.azure.com/) o l’[API Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

A seconda della configurazione del servizio, si userà il servizio gratuito condiviso con altri sottoscrittori di Ricerca di Azure o il [piano tariffario](https://azure.microsoft.com/pricing/details/search/) Standard che riserva risorse all’uso esclusivo da parte dal servizio. Durante il provisioning del servizio, è anche possibile scegliere l'area del data center che ospita il servizio.

Quando si usa Ricerca di Azure nel livello Standard, è possibile scalare il servizio in due dimensioni: 1) aggiungere repliche per aumentare la capacità di gestire ingenti carichi di query e 2) aggiungere partizioni per aumentare lo spazio di archiviazione per altri documenti. Grazie alla gestione separata dell’archiviazione dei documenti e della velocità effettiva delle query è possibile personalizzare il servizio di ricerca in base alle esigenze.

### 2\. Creare un indice
Per poter caricare il contenuto nel servizio Ricerca di Azure, è innanzitutto necessario definire un indice di Ricerca di Azure. Un indice è simile a una tabella di database che contiene i dati e può accettare query di ricerca. È necessario definire lo schema di indice per eseguire il mapping della struttura dei documenti in cui si desidera eseguire la ricerca, in modo analogo ai campi in un database.

Lo schema di questi indici può essere creato nel portale di Azure o a livello di codice [usando .NET SDK](search-howto-dotnet-sdk.md) o [l'API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una volta definito l’indice, è quindi possibile caricare i dati nel servizio Ricerca di Azure dove sono successivamente indicizzati.

### 3\. Dati dell'indice
Dopo aver definito i campi e gli attributi dell'indice, è possibile caricare il contenuto nell'indice, scegliendo il modello push o pull per caricare i dati.

Il modello pull è fornito tramite gli indicizzatori che possono essere configurati su richiesta o con aggiornamenti pianificati (vedere [operazioni indicizzatore (API REST del servizio Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), permettendo di acquisire facilmente dati e modifiche ai dati da Azure DocumentDB, database SQL di Azure, archivio BLOB di Azure o SQL Server ospitato in una macchina virtuale di Azure.

Il modello push viene fornito tramite il SDK o l'API REST per l'invio di documenti aggiornati a un indice. È possibile eseguire il push dei dati da qualsiasi set di dati usando il formato JSON. Per informazioni sul caricamento dei dati, vedere [Aggiungere, aggiornare o eliminare documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx) oppure [Come usare .NET SDK](search-howto-dotnet-sdk.md).

### 4\. Search
Dopo avere compilato l'indice di Ricerca di Azure, è ora possibile [eseguire query di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx) nell'endpoint del servizio tramite semplici richieste HTTP con l'API REST o .NET SDK.

## Prova gratuita subito
È possibile provare oggi stesso Ricerca di Azure. Se si dispone già di un account Azure, è possibile [eseguire il provisioning di un servizio al livello gratuito](search-create-service-portal.md).

Se non si dispone di un account Azure, è possibile provare una sessione gratuita di 60 minuti senza effettuare l'iscrizione obbligatoria. Andare al sito [Prova il Servizio app di Azure](http://go.microsoft.com/fwlink/p/?LinkId=618214) e selezionare "App Web". Quindi selezionare il modello di "ASP.NET + Ricerca di Azure" per iniziare.

<!---HONumber=AcomDC_0114_2016-->