<properties
	pageTitle="Modellazione multi-tenancy in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni sui modelli di progettazione comuni per le applicazioni SaaS multi-tenant quando si usa Ricerca di Azure."
	services="search"
	authors="ashmaka"
	documentationCenter=""/> 

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="09/20/2016"
	ms.author="ashmaka"/> 

# Modelli di progettazione per le applicazioni SaaS multi-tenant e Ricerca di Azure

Un'applicazione multi-tenant è un'applicazione che fornisce gli stessi servizi e funzionalità a un numero qualsiasi di tenant che non possono vedere o condividere i dati di nessun altro tenant. Questo documento illustra le strategie di isolamento dei tenant per le applicazioni multi-tenant compilate con Ricerca di Azure.

## Concetti di Ricerca di Azure
Come soluzione di ricerca come servizio, Ricerca di Azure consente agli sviluppatori di aggiungere esperienze di ricerca avanzate alle applicazioni, senza dover gestire un'infrastruttura o diventare esperti di ricerca. I dati vengano caricati nel servizio e quindi archiviati nel cloud. Tramite semplici richieste all'API di Ricerca di Azure, i dati possono essere modificati e ricercati. Una panoramica del servizio è riportata in [questo articolo](http://aka.ms/whatisazsearch). Prima di esaminare i modelli di progettazione, è importante comprendere alcuni concetti di Ricerca di Azure.

### Servizi di ricerca, indici, campi e documenti
Quando si usa Ricerca di Azure, si sottoscrive un _servizio di ricerca_. Quando i dati vengono caricati in Ricerca di Azure, vengono archiviati in un _indice_ all'interno del servizio di ricerca. In un solo servizio possono essere presenti molti indici. Facendo riferimento ai familiari concetti relativi ai database, il servizio di ricerca può essere paragonato a un database, mentre gli indici all'interno di un servizio possono essere paragonati alle tabelle di un database.

Ogni indice all'interno di un servizio di ricerca ha un proprio schema, definito da un certo numero di _campi_ personalizzabili. I dati vengono aggiunti a un indice di Ricerca di Azure sotto forma di singoli _documenti_. Ogni documento deve essere caricato in un indice specifico e deve adattarsi allo schema di tale indice. Quando si eseguono ricerche di dati tramite Ricerca di Azure, vengono eseguite query di ricerca full-text su un particolare indice. Facendo riferimento ai database, i campi possono essere paragonati alle colonne e i documenti alle righe di una tabella del database.

### Scalabilità
Qualsiasi servizio di Ricerca di Azure nel [piano tariffario](https://azure.microsoft.com/pricing/details/search/) Standard può essere ridimensionato in due sensi: archiviazione e disponibilità.
* È possibile aggiungere _partizioni_ per aumentare lo spazio di archiviazione di un servizio di ricerca.
* Possono essere aggiunte _repliche_ a un servizio per aumentare la velocità effettiva delle richieste che un servizio di ricerca può gestire.

Aggiungendo e rimuovendo partizioni e repliche si consentirà alla capacità del servizio di ricerca di crescere in base alla quantità di dati e al traffico richiesti dall'applicazione. Affinché un servizio di ricerca rispetti un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/) di lettura, sono necessarie due repliche. Affinché un servizio rispetti un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/) di lettura-scrittura, sono necessarie tre repliche.


### Limiti dei servizi e degli indici in Ricerca di Azure
Esistono vari [piani tariffari](https://azure.microsoft.com/pricing/details/search/) per Ricerca di Azure e ciascuno presenta [limiti e quote](search-limits-quotas-capacity.md) diverse. Alcuni di questi limiti sono a livello di servizio, altri sono a livello di indice e altri ancora a livello di partizione.


| | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Massimo numero di repliche per servizio | 3 | 12 | 12 | 12 | 12 |
| Massimo numero di partizioni per servizio | 1 | 12 | 12 | 12 | 1 |
| Massimo numero di unità di ricerca (repliche * partizioni) per servizio | 3 | 36 | 36 | 36 | 12 |
| Massimo numero di documenti per servizio | 1 milione | 180 milioni | 720 milioni | 1,4 miliardi | 200 milioni |
| Massimo spazio di archiviazione per servizio | 2 GB | 300 GB | 1,2 TB | 2,4 TB | 200 GB |
| Massimo numero di documenti per partizione | 1 milione | 15 milioni | 60 milioni | 120 milioni | 200 milioni |
| Massimo spazio di archiviazione per partizione | 2 GB | 25 GB | 100 GB | 200 GB | 200 GB |
| Massimo numero di indici per servizio | 5 | 50 | 200 | 200 | 1000 |


#### S3 ad alta densità
Nel piano tariffario S3 di Ricerca di Azure è disponibile un'opzione per la modalità a densità elevata (HD) progettata specificamente per gli scenari multi-tenant. Nella modalità ad alta densità, lo SKU di S3 presenta limiti diversi rispetto alla configurazione S3 standard:
* Sono ammessi fino a 1000 indici per ogni servizio, invece di 200
* Sono ammessi fino a 200 GB di dati per ogni servizio, invece di 2,4 TB
* È ammessa 1 sola partizione per ogni servizio, anziché 12

Il piano S3 HD è ideale per applicazioni con abilitazione SaaS che implementano il modello "indice per tenant" descritto di seguito.


## Considerazioni per le applicazioni multi-tenant
Le applicazioni multi-tenant devono distribuire in modo efficace le risorse tra i tenant mantenendo al tempo stesso un certo livello di privacy tra i vari tenant. Quando si progetta l'architettura di un'applicazione di questo tipo, ci sono alcuni aspetti da considerare:

* _Isolamento del tenant:_ gli sviluppatori di applicazioni devono adottare misure appropriate per assicurarsi che nessun tenant non autorizzato o indesiderato acceda ai dati di altri tenant. Oltre alla questione della privacy dei dati, le strategie di isolamento tenant richiedono una gestione efficace delle risorse condivise e la protezione da vicini fastidiosi.
* _Costi delle risorse del cloud:_ come con qualsiasi altra applicazione, le soluzioni software devono rimanere competitive quando sono componenti di un'applicazione multi-tenant.
* _Semplicità delle operazioni:_ quando si sviluppa un'architettura multi-tenant, l'impatto sulle operazioni e la complessità dell'applicazione è un fattore importante. Ricerca di Azure ha un [contratto di servizio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Presenza globale:_ le applicazioni multi-tenant potrebbero dover gestire in modo efficace tenant distribuiti in tutto il mondo.
* _Scalabilità:_ gli sviluppatori di applicazioni devono mantenere le applicazioni a un livello di complessità sufficientemente ridotto e allo stesso tempo progettarle in maniera che siano scalabili in base al numero di tenant e alla dimensione dei dati e del carico di lavoro dei tenant.

Ricerca di Azure offre alcuni confini che consentono di isolare i dati e il carico di lavoro dei tenant.

## Modellazione del multi-tenancy con Ricerca di Azure
Nel caso di uno scenario multi-tenant, lo sviluppatore dell'applicazione usa uno o più servizi di ricerca e divide i tenant tra i servizi, gli indici o entrambi. Ricerca di Azure dispone di alcuni modelli comuni per la modellazione di uno scenario multi-tenant:

1. _Indice per tenant:_ ogni tenant dispone di un proprio indice all'interno di un servizio di ricerca che è condiviso con altri tenant.
1. _Servizio per tenant:_ ogni tenant dispone di un proprio servizio Ricerca di Azure dedicato, il che offre un livello più elevato di separazione dei dati e del carico di lavoro.
1. _Combinazione di entrambi:_ ai tenant più grandi e attivi vengono assegnati servizi dedicati mentre ai tenant più piccoli vengono assegnati singoli indici all'interno di servizi condivisi.

## 1\. Indice per tenant
![Un'immagine del modello "indice per tenant"](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png) 

In un modello "indice per tenant", più tenant occupano un singolo servizio di Ricerca di Azure in cui ogni tenant dispone di un proprio indice.

I tenant ottengono l'isolamento dei dati perché tutte le richieste di ricerca e le operazioni sui documenti sono emesse a livello di indice in Ricerca di Azure. Nel livello dell'applicazione c'è la necessità di indirizzare il traffico dei diversi tenant agli indici corretti gestendo al tempo stesso le risorse a livello di servizio tra tutti i tenant.

Una caratteristica importante del modello "indice per tenant" è la possibilità per lo sviluppatore di applicazioni di eseguire l'oversubscription della capacità di un servizio di ricerca tra i tenant dell'applicazione. Se la distribuzione del carico di lavoro fra i tenant non è uniforme, è possibile distribuire una combinazione ottimale di tenant tra gli indici di un servizio di ricerca in modo da sistemare un certo numero di tenant molto attivi e che richiedono molte risorse, servendo al tempo stesso una lunga coda di tenant meno attivi. Lo svantaggio del modello è la sua incapacità di gestire le situazioni in cui tutti i tenant sono simultaneamente molto attivi.

Il modello "indice per tenant" costituisce la base per un modello di costo variabile, in cui un intero servizio di Ricerca di Azure viene prima acquistato e poi completato con i tenant. In questo modo la capacità inutilizzata può essere destinata ad account di prova e gratuiti.

Per le applicazioni con una presenza globale, il modello "indice per tenant" potrebbe non essere il più efficiente. Se i tenant di un'applicazione vengono distribuiti in tutto il mondo, potrebbe essere necessario un servizio diverso per ogni area, il che potrebbe duplicare i costi per ognuno di essi.

Ricerca di Azure consente il ridimensionamento verso l'alto dei singoli indici e anche l'incremento del numero totale di indici. Se viene scelto un piano tariffario appropriato, possono essere aggiunte partizioni e repliche all'intero servizio di ricerca quando un singolo indice all'interno del servizio diventa troppo grande in termini di archiviazione o traffico.

Se il numero totale di indici diventa troppo grande per un singolo servizio, è necessario eseguire il provisioning di un altro servizio per accogliere i nuovi tenant. Se è necessario spostare determinati indici tra i servizi di ricerca quando vengono aggiunti nuovi servizi, i dati dell'indice devono essere copiati manualmente da un indice all'altro in quanto Ricerca di Azure non consente di spostare un indice.


## 2\. Servizio per tenant
![Un'immagine del modello "servizio per tenant"](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png) 

In un'architettura "servizio per tenant" ogni tenant dispone di un proprio servizio di ricerca.

In questo modello l'applicazione ottiene il massimo livello di isolamento per i suoi tenant. Ogni servizio ha risorse di archiviazione e velocità effettiva dedicate per la gestione delle richiesta di ricerca nonché chiavi API diverse.

Per le applicazioni in cui ogni tenant ha grandi dimensioni o il carico di lavoro è poco variabile da un tenant all'altro, il modello "servizio per tenant" è una scelta efficace in quanto le risorse non sono condivise tra i carichi di lavoro dei diversi tenant.

Un modello "servizio per tenant" offre inoltre il vantaggio della stimabilità dei costi fissi. Non è necessario alcun investimento iniziale in un intero servizio di ricerca fino a quando non c'è un tenant che lo occupa, tuttavia il costo per tenant è superiore rispetto al modello "indice per tenant".

Il modello "servizio per tenant" è una scelta efficiente per le applicazioni con una presenza globale. Con i tenant distribuiti geograficamente è facile avere il servizio di ciascun tenant nell'area appropriata.

Problematiche di scalabilità di questo modello si verificano quando i singoli tenant diventano troppo grandi per il servizio. Ricerca di Azure attualmente non supporta l'aggiornamento del piano tariffario di un servizio di ricerca, pertanto tutti i dati dovranno essere copiati manualmente in un nuovo servizio.

## 3\. Valutazione di entrambi i modelli
Un'altra possibilità per la modellazione multi-tenancy consiste nell'unione delle due strategie "indice per tenant" e "servizio per tenant".

Combinando i due modelli, i tenant più grandi di un'applicazione possono occupare servizi dedicati mentre la lunga coda di tenant più piccoli e meno attivi può occupare gli indici in un servizio condiviso. Questo modello garantisce ai tenant più grandi di ottenere prestazioni elevate e coerenti dal servizio, contribuendo al tempo stesso a proteggere i tenant più piccoli da eventuali vicini fastidiosi.

L'implementazione di questa strategia si basa tuttavia sulla previsione di quali tenant richiederanno un servizio dedicato e non un indice in un servizio condiviso. La complessità dell'applicazione aumenta con la necessità di gestire entrambi questi modelli multi-tenancy.

## Raggiungimento di una granularità ancora maggiore
I modelli di progettazione descritti sopra per la modellazione di scenari multi-tenant in Ricerca di Azure si basano sul presupposto di un ambito uniforme in cui ogni tenant è un'istanza completa di un'applicazione. Le applicazioni tuttavia possono gestire a volte numerosi ambiti più piccoli.

Se i modelli "servizio per tenant" e "indice per tenant" non hanno un ambito sufficientemente piccolo, è possibile modellare un indice per ottenere un livello di granularità ancora più preciso.

Per ottenere che un singolo indice si comporti in modo diverso per endpoint del client diversi, è possibile aggiungere un campo a un indice che stabilisce un determinato valore per ogni possibile client. Ogni volta che un client chiama Ricerca di Azure per eseguire una query o modificare un indice, il codice dall'applicazione client specifica il valore appropriato per quel campo mediante la funzionalità di [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) di Ricerca di Azure in fase di query.

Questo metodo può essere usato per ottenere funzionalità di account utente diversi, livelli di autorizzazione diversi e persino applicazioni completamente diverse.

## Passaggi successivi
Ricerca di Azure è una scelta interessante per molte applicazioni: [altre informazioni sulle affidabili funzionalità del servizio sono disponibili qui](http://aka.ms/whatisazsearch). Quando si valutano i vari modelli di progettazione per le applicazioni multi-tenant, è opportuno considerare i [vari piani tariffari](https://azure.microsoft.com/pricing/details/search/) e i rispettivi [limiti del servizio](search-limits-quotas-capacity.md) per adattare al meglio Ricerca di Azure a carichi di lavoro e architetture applicative di tutte le dimensioni.

Le domande su Ricerca di Azure e gli scenari multi-tenant possono essere indirizzate a azuresearch_contact@microsoft.com.

<!---HONumber=AcomDC_0921_2016-->