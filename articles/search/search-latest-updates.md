<properties 
	pageTitle="Novità dell'ultimo aggiornamento di Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Note sulla versione per Ricerca di Azure che descrivono gli aggiornamenti più recenti al servizio" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/23/2016" 
	ms.author="heidist"/>

#Novità dell’ultimo aggiornamento di Ricerca di Azure#

Ricerca di Azure è un servizio di ricerca ospitato sul cloud in Microsoft Azure. Ricerca di Azure è disponibile a livello generale e offre un contratto di servizio con disponibilità del 99,9% per le configurazioni supportate della [versione del 28/02/2015 dell'API REST del servizio di ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx).

##Controllo delle versioni e distribuzione delle funzionalità

Le funzionalità vengono rilasciate separatamente o congiuntamente tramite l'[API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) o il dashboard del servizio nel [portale di Azure](https://portal.azure.com). La libreria .NET e le API REST hanno di più versioni. Le API precedenti restano operative mentre vengono distribuite nuove funzionalità. Per ulteriori informazioni sui criteri di controllo delle versioni, visitare [Controllo delle versioni di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx).

Le funzionalità di anteprima e disponibili a livello generale sono associate a una versione dell'API della stessa categoria.

- Le funzionalità di anteprima sono sperimentali e possono subire modifiche o persino essere abbandonate prima di diventare disponibili a livello generale. Le funzionalità di anteprima sono sempre disponibili nella [versione di anteprima dell'API REST](search-api-2015-02-28-preview.md) e in alcuni casi in [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216). La documentazione relativa alle funzionalità illustra come accedere alla funzionalità in questione.
- Le funzionalità disponibili a livello generale sono stabili e non è probabile che subiscano modifiche. Qualsiasi modifica apportata a una funzionalità disponibile a livello generale viene annunciata come una modifica di rilievo.

Le funzionalità che si basano esclusivamente su strumenti o sul portale in genere cambiano nel tempo e non sono classificate come di anteprima o disponibili a livello generale.

##Novità del 2016

Funzionalità|Rilasciata|Stato|Dettagli
-------|--------|------|-------
[Analizzatori personalizzati](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|Gennaio 2016|[Anteprima](search-api-2015-02-28-preview.md)|Configurazioni di tokenizer e filtri di token definiti dall'utente. Vedere la pagina relativa all'[analisi in Ricerca di Azure](https://msdn.microsoft.com/library/azure/mt605304.aspx) su MSDN.
[Indicizzatore di archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)|Gennaio 2016|[Anteprima](search-api-2015-02-28-preview.md)|È possibile unire o inserire documenti di Office, file PDF, XML, HTML o persino file audio e video in un indice di Ricerca di Azure.
[Esplora ricerche](search-explorer.md)|Gennaio 2016|[Portale](https://portal.azure.com)|Strumento di query incorporato per le query ad hoc in un indice.
[Pacchetto di contenuti di Power BI per Ricerca di Azure](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|Gennaio 2016|Strumento|Visualizzazione e analisi dei dati del servizio tramite un nuovo pacchetto di contenuti di Power BI per Ricerca di Azure. Disponibile tramite l'analisi di ricerca.
[Analisi di ricerca](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|Gennaio 2016|Portale|Abilitare la raccolta di dati per informazioni dettagliate sulle attività di ricerca dell'utente.
[.NET SDK 1.0.1 di anteprima](https://msdn.microsoft.com/library/azure/dn951165.aspx)|Gennaio 2016|Preview|Questa è la seconda iterazione della libreria client .NET Microsoft.Azure.Search.dll. Questa versione introduce modifiche di rilievo. Per informazioni aggiuntive sulla migrazione, vedere [Aggiornamento ad Azure Search .NET SDK versione 1.0.1 - Anteprima](search-dotnet-sdk-migration.md).

##Novità del 2015

Funzionalità|Rilasciata|Stato|Dettagli
-------|--------|------|-------
Analizzatori di linguaggi Lucene|Ottobre 2015|GA|Questa funzionalità è ora disponibile a livello generale nell'API REST del servizio e in .NET SDK.
[Processori del linguaggio naturale Microsoft](search-api-2015-02-28-Preview.md)|Ottobre 2015|GA|Questa funzionalità è ora disponibile a livello generale nell'API REST del servizio e in .NET SDK. 
[Supporto per la sintassi di query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Settembre 2015|[Anteprima](search-api-2015-02-28-preview.md)|Aggiunge l'analizzatore di linguaggi Lucene. Per usare la nuova sintassi, è necessario specificare `queryType` in un'operazione di ricerca dei documenti.
[Processori del linguaggio naturale](search-language-support.md)|Settembre 2015|[Anteprima](search-api-2015-02-28-preview.md)|Sono stati aggiunti i processori del linguaggio Microsoft, aumentando il numero di linguaggi complessivi e offrendo un'implementazione alternativa per altri linguaggi.
POST nella ricerca, nei suggerimenti e nelle query di ricerca|Settembre 2015|[Anteprima](search-api-2015-02-28-preview.md)|Si applica all'API REST del servizio
[API REST di gestione ](https://msdn.microsoft.com/library/azure/dn832684.aspx)|Settembre 2015|GA|Seconda versione dell'API REST di gestione. Include checkNameAvailability che verifica se un determinato nome di servizio è già in uso, l'intervallo di replica in precedenza era 1-6 e ora è 1-12, la proprietà SKU è stata spostata dal contenitore delle proprietà al livello superiore del payload del servizio, il corpo della risposta dell'operazione di creazione del servizio di ricerca è stato aggiornato per supportare la rilocazione dell'impostazione SKU.
.NET SDK 0.10.0-anteprima|Agosto 2015|Preview|Questa è la seconda iterazione della libreria client .NET, Microsoft.Azure.Search.dll. Questa versione aggiunge il supporto per la creazione, la gestione e l'uso della [classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) e della [classe Indexers](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) tramite le classi .NET. Inoltre, per gli indicizzatori di SQL Azure, esiste un nuovo supporto per l'indicizzazione dei punti geografici.
Costrutti fieldMapping|Aprile 2015|Preview|Gli indicizzatori ora supportano i costrutti fieldMapping che forniscono le assegnazioni di campo quando i nomi di campo effettivi differiscono tra il database esterno e l'indice di Ricerca di Azure. Vedere [Indicizzatori](search-api-indexers-2015-02-28-Preview.md) per la versione `2015-02-28-preview` della documentazione degli indicizzatori.
Trasformazioni di tipo di campo|Aprile 2015|Preview|Gli indicizzatori supportano ora le trasformazioni di tipo di campo per cui è possibile eseguire il mapping di un campo stringa in una tabella SQL a un campo di insieme di stringhe in un indice di ricerca, presumendo che il campo di origine rappresenti una matrice JSON.
[API REST del servizio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|Marzo 2015|GA|Prima versione disponibile a livello generale dell'API REST del servizio di Ricerca di Azure. Questa versione include funzionalità precedenti. Include inoltre gli [indicizzatori](http://go.microsoft.com/fwlink/p/?LinkID=528210). [Suggesters](https://msdn.microsoft.com/library/azure/dn798936.aspx) sostituisce il supporto più limitato della query di suggerimento per la digitazione fornito nell'implementazione precedente (in cui venivano trovate solo le corrispondenze dei prefissi) aggiungendo il supporto per la corrispondenza degli infissi. Con questa implementazione vengono trovate corrispondenze con qualsiasi punto di un termine e sono supportate anche le corrispondenze fuzzy. [Tag boosting](http://go.microsoft.com/fwlink/p/?LinkId=528212) offre a un nuovo scenario per i profili di punteggio. In particolare, sfrutta i dati persistenti (ad esempio le preferenze di acquisto) in modo da migliorare i risultati della ricerca per i singoli utenti in base ad informazioni personalizzate. 
.NET SDK 0.9.6-anteprima|Marzo 2015|Preview|La prima versione pubblica di .NET SDK per Ricerca di Azure. Include una libreria client, Microsoft.Azure.Search.dll, con due spazi dei nomi: [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) e [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx).
API REST di gestione|Marzo 2015|GA|Prima versione dell'API REST di gestione appartenente alla versione disponibile a livello generale di Ricerca di Azure. Non esistono differenze di funzionalità tra l'anteprima precedente e questa.
[Processori del linguaggio naturale Microsoft](search-api-2015-02-28-Preview.md)|Marzo 2015|Preview|Aggiunge altre lingue e lo stemming esteso per tutte le lingue supportate da Office e Bing.
[moreLikeThis=](search-api-2015-02-28-Preview.md)|Marzo 2015|Preview|Parametro di ricerca reciprocamente esclusivo di `search=`, che attiva un percorso di esecuzione di query alternativo. Invece della ricerca con testo completo di `search=` in base all'input del termine di ricerca, `moreLikeThis=` trova i documenti simili a un determinato documento confrontandone i campi in cui è possibile eseguire ricerche.

##Novità del 2014

Funzionalità|Rilasciata|Stato|Dettagli
-------|--------|------|-------
Analizzatori di linguaggi Lucene|Novembre 2014|Preview|Aggiunti per fornire il supporto multilingua per gli analizzatori di lingua personalizzati distribuiti con Lucene. 
Il supporto per il portale è stato introdotto per la creazione di indici|Novembre 2014|[Portale](https://portal.azure.com)|Nel portale è possibile creare indici, analizzatori e profili di assegnazione dei punteggi.
Versione api di gestione 31/07/2014-Anteprima|Ottobre 2014|Preview|Prima versione di anteprima pubblica dell'API REST di gestione. La documentazione per questa versione dell'API è disponibile su richiesta.
API REST per il servizio di ricerca|Agosto 2014|Preview|Prima versione di anteprima pubblica del servizio API REST (versione API 31/07/2014 di anteprima). Questa è l'API REST per le operazioni relative a indici e documenti, per i profili di assegnazione dei punteggi per l'ottimizzazione dei risultati di ricerca e per il supporto geospaziale. Questa versione supporta il provisioning del servizio nel portale di Azure. La documentazione per questa versione dell'API è disponibile su richiesta. Il controllo delle versioni avviene indipendentemente dall'API REST di servizio.














 

<!---HONumber=AcomDC_0128_2016-->