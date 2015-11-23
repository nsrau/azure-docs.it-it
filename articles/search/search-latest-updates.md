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
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Novità dell’ultimo aggiornamento di Ricerca di Azure#

Ricerca di Azure è un servizio di ricerca ospitato sul cloud in Microsoft Azure. È generalmente disponibile, offrendo un contratto a livello del servizio (SLA) con disponibilità del 99,9% per le configurazioni supportate della [versione del 28/02/2015 dell'API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

##Controllo delle versioni e distribuzione delle funzionalità

Le funzionalità vengono rilasciate separatamente o congiuntamente tramite l’[API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) o il dashboard del servizio nel [portale di Azure](https://portal.azure.com).

La libreria .NET e le API REST hanno di più versioni. Le API precedenti restano operative mentre vengono distribuite nuove funzionalità. Per ulteriori informazioni sui criteri di controllo delle versioni, visitare [Controllo delle versioni di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx).


##Versione API 28/02/2015-Anteprima
**Data di rilascio: settembre 2015**

Questa versione aggiunge un nuovo [supporto per la sintassi della query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) che può essere utilizzato con la [versione di anteprima dell'API REST del servizio di ricerca di Azure](search-api-2015-02-28-preview.md). Per utilizzare la nuova sintassi, è necessario specificare il `queryType` in un'operazione di ricerca documenti.

Inoltre, entrambe le seguenti funzionalità sono passate fuori dall’anteprima, e fanno ora parte dell'API ufficiale su MSDN: - [Processori naturali del linguaggio](search-language-support.md) - POST nella ricerca, suggerimenti e query di ricerca

##.NET SDK 0.10.0-anteprima
**Data di rilascio: agosto 2015**

Questa è la seconda iterazione della libreria client .NET, Microsoft.Azure.Search.dll. Questa versione aggiunge il supporto per la creazione, la gestione e l’utilizzo degli indicizzatori tramite le classi .NET. Inoltre, per gli indicizzatori di SQL Azure, esiste un nuovo supporto per l'indicizzazione dei punti geografici.

- [Classe Indexers](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [Classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6-anteprima
**Data di rilascio: 5 marzo 2015**

La prima versione pubblica di .NET SDK per Ricerca di Azure. Include una libreria client Microsoft.Azure.Search.dll, con due spazi dei nomi:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Non comprende:

- [Indicizzatori](http://go.microsoft.com/fwlink/p/?LinkId=528173) (questa funzionalità non è più esclusa nella versione 0.10.0-anteprima)
- [API REST di gestione ](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- Funzionalità della versione di [anteprima del 28/02/2015](search-api-2015-02-28-Preview.md) (attualmente le funzionalità di sola anteprima sono costituite dai processori del linguaggio naturale di Microsoft e `moreLikeThis`).

Visitare [Come utilizzare Ricerca di Azure in .NET](http://go.microsoft.com/fwlink/p/?LinkId=528088) per istruzioni sull'installazione e l'utilizzo del SDK.

##Versione API 28/02/2015-Anteprima
**Data di rilascio: 22 aprile 2015**

- Gli indicizzatori ora supportano i costrutti fieldMapping che forniscono le assegnazioni di campo quando i nomi di campo effettivi differiscono tra il database esterno e l'indice di Ricerca di Azure. Vedere [Indicizzatori](search-api-indexers-2015-02-28-Preview.md) per la versione `2015-02-28-preview` della documentazione degli indicizzatori.

- In questo aggiornamento di anteprima, gli indicizzatori supportano le trasformazioni di tipo di campo per cui è possibile eseguire il mapping di un campo Stringa in una tabella SQL a un campo di insieme String in un indice di ricerca, presumendo che il campo di origine rappresenti una matrice JSON.

**Data di rilascio: 5 marzo 2015**

- I [processori del linguaggio naturale di Microsoft](search-api-2015-02-28-Preview.md) forniscono il supporto per più lingue e lo stemming esteso per tutte le lingue supportate da Office e Bing.

- [moreLikeThis =](search-api-2015-02-28-Preview.md) è un parametro di ricerca reciprocamente esclusivo di `search=`, che attiva un percorso di esecuzione di query alternativo. Invece della ricerca con testo completo di `search=` in base all'input del termine di ricerca, `moreLikeThis=` trova i documenti simili a un determinato documento confrontandone i campi in cui è possibile eseguire ricerche.

##Versione api 28/02/2015
**Data di rilascio: 5 marzo 2015**

- [Indexers](http://go.microsoft.com/fwlink/p/?LinkID=528210) è una nuova funzionalità che semplifica enormemente l'indicizzazione da origini dati nel database SQL di Azure, Azure DocumentDB e SQL Server in macchine virtuali di Azure.

- [Suggesters](https://msdn.microsoft.com/library/azure/dn798936.aspx) sostituisce il supporto più limitato della query di suggerimento per la digitazione fornito nell'implementazione precedente (in cui venivano trovate solo le corrispondenze dei prefissi) aggiungendo il supporto per la corrispondenza degli infissi. Con questa implementazione vengono trovate corrispondenze con qualsiasi punto di un termine e sono supportate anche le corrispondenze fuzzy.

- [Tag boosting](http://go.microsoft.com/fwlink/p/?LinkId=528212) offre a un nuovo scenario per i profili di punteggio. In particolare, sfrutta i dati persistenti (ad esempio le preferenze di acquisto) in modo da migliorare i risultati della ricerca per i singoli utenti in base ad informazioni personalizzate.

Visitare [Azure Search is now Generally Available](http://go.microsoft.com/fwlink/p/?LinkId=528211) per l'annuncio del servizio nel blog di Azure che illustra tutte queste funzionalità.

##Versione api 20/10/2014-Anteprima
**Data di rilascio: novembre 2014, gennaio 2015**

- [Analizzatori di lingua Lucene](search-api-2014-10-20-preview.md) sono stati aggiunti per fornire il supporto multilingua per gli analizzatori di lingua personalizzati distribuiti con Lucene. 

- È stato introdotto il supporto di strumenti per la compilazione di indici, inclusi i profili di punteggio, nel [portale di gestione di Azure](https://portal.azure.com).

##Versione api 31/07/2014-Anteprima
**Data di rilascio: 21 agosto 2014**

Questa versione è stata la versione di anteprima pubblica per Ricerca di Azure e fornisce le funzionalità di base seguenti:

- API REST per le operazioni di indice e documenti. La maggior parte di questa versione dell'API è rimasta inalterata nella versione del 28 febbraio 2015. La documentazione relativa alla versione `2014-07-31-Preview` è reperibile in [Azure Search Service REST API Version 2014-07-31](search-api-2014-07-31-preview.md).

- Profili di punteggio per l'ottimizzazione dei risultati della ricerca. Un profilo di punteggio consente di aggiungere i criteri utilizzati per calcolare i punteggi di ricerca. La documentazione relativa a questa funzionalità è reperibile in [Azure Search Service Scoring Profiles REST API Version 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md).

- Il supporto geospaziale è stato reso disponibile dall'inizio, fornito tramite il tipo di dati `Edm.GeographyPoint` che è stato parte di Ricerca di Azure fin dal principio.

- Provisioning nella versione di anteprima del [portale di gestione di Azure](https://portal.azure.com). Ricerca di Azure era uno dei pochi servizi disponibili solo nel nuovo portale.

##Versione api di gestione 2015-08-19
**Data di rilascio: 11 settembre 2015**

[API REST di gestione](https://msdn.microsoft.com/library/azure/dn832684.aspx) include i seguenti aggiornamenti.

- checkNameAvailability controlla se un determinato nome di servizio è già in uso.
- L’intervallo di replica in precedenza era 1-6 ed è ora 1-12.
- La proprietà SKU è stata spostata dall'elenco delle proprietà al livello superiore del carico del servizio.
- Il corpo della risposta dell'operazione di creazione del servizio di ricerca è stato aggiornato per supportare il trasferimento dell'impostazione SKU.

##Versione api di gestione 28/02/2015
**Data di rilascio: 5 marzo 2015**

[API REST di gestione](search-management-api-2014-02-28.md) contrassegna la prima versione dell'API di gestione appartenente alla versione disponibile a livello generale di Ricerca di Azure. Non esistono differenze di funzionalità tra l'anteprima precedente e questa.

##Versione api di gestione 31/07/2014-Anteprima
**Data di rilascio: ottobre 2014**

La versione di anteprima dell'[API REST di gestione](search-management-api-2014-07-31-preview.md) è stata aggiunta per supportare l'amministrazione di servizi a livello di codice. Il controllo delle versioni avviene indipendentemente dall'API REST di servizio.


 

<!---HONumber=Nov15_HO3-->