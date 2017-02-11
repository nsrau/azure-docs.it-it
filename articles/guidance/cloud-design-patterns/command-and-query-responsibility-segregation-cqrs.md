---
title: Modello CQRS | Azure | Documentazione Microsoft
description: Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l&quot;utilizzo di interfacce separate.
categories:
- data-management
- design-implementation
- performance-scalability
keywords: schema progettuale
services: 
documentationcenter: na
author: dragon119
manager: bennage
tags: 
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: ececb5775b0b2d6f463c28e06e69468d601a952c

---

# <a name="command-and-query-responsibility-segregation-cqrs"></a>Separazione di responsabilità per query e comandi (CQRS, Command and Query Responsibility Segregation)

Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate. In questo modo è possibile ottimizzare prestazioni, scalabilità e sicurezza, supportare l'evoluzione del sistema nel tempo grazie a una maggiore flessibilità e impedire che i comandi di aggiornamento provochino conflitti di unione a livello di dominio.

## <a name="context-and-problem"></a>Contesto e problema

Nei sistemi di gestione dati tradizionali sia i comandi (aggiornamenti dei dati) che le query (richieste di dati) vengono eseguiti nello stesso set di entità in un repository di dati singolo. Tali entità possono essere un subset di righe di una o più tabelle in un database relazionale, ad esempio SQL Server.

In questi sistemi tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) vengono in genere applicate alla stessa rappresentazione dell'entità. Un oggetto di trasferimento dati (DTO, Data Transfer Object) che rappresenta un cliente viene recuperato ad esempio dall'archivio dati a livello di accesso ai dati (DAL, Data Access Layer) e visualizzato sullo schermo. Un utente aggiorna alcuni campi dell'oggetto DTO (ad esempio tramite data binding) e quest'ultimo viene salvato nuovamente nell'archivio dati a livello DAL. Lo stesso oggetto DTO viene usato per entrambe le operazioni di lettura e scrittura. La figura illustra un'architettura CRUD tradizionale.

![Architettura CRUD tradizionale](images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png)

I progetti CRUD tradizionali funzionano bene solo quando alle operazioni sui dati viene applicata logica di business limitata. I meccanismi di scaffolding presenti negli strumenti di sviluppo possono creare molto rapidamente codice di accesso ai dati, che può quindi essere personalizzato in base alle esigenze.

L'approccio CRUD tradizionale presenta tuttavia alcuni svantaggi:

- Indica spesso una mancata corrispondenza tra le rappresentazioni in lettura e scrittura dei dati, ad esempio colonne aggiuntive o proprietà che devono essere aggiornate correttamente anche se non sono necessarie come parte di un'operazione.

- Si verificano conflitti di dati quando i record sono bloccati nell'archivio dati in un dominio di collaborazione, dove più attori operano in parallelo sullo stesso set di dati, oppure conflitti di aggiornamento provocati dagli aggiornamenti simultanei quando si usa il blocco ottimistico. Tali rischi aumentano in modo proporzionale all'incremento della complessità e della velocità effettiva del sistema. L'approccio tradizionale può avere anche un effetto negativo sulle prestazioni dovuto al carico sull'archivio dati e sul livello di accesso ai dati e alla complessità delle query necessarie per recuperare informazioni.

- La gestione della sicurezza e delle autorizzazioni può diventare più complessa perché ogni entità è sottoposta a operazioni di lettura e scrittura, con possibile esposizione dei dati in un contesto non corretto.

> Per informazioni più dettagliate sui limiti dell'approccio CRUD, vedere [CRUD, solo quando è accessibile](https://msdn.microsoft.com/library/ms978509.aspx). 

## <a name="solution"></a>Soluzione

Il modello di separazione di responsabilità per query e comandi divide le operazioni di lettura dei dati (query) da quelle di aggiornamento dei dati (comandi) grazie all'uso di interfacce separate. Ciò significa che i modelli di dati usati per l'esecuzione di query e aggiornamenti sono diversi. Sebbene non si tratti di un requisito assoluto, i modelli possono quindi essere isolati, come illustrato nella figura seguente.

![Architettura CQRS di base](images/command-and-query-responsibility-segregation-cqrs-basic.png)


Rispetto al modello di dati singolo usato nei sistemi basati su CRUD, l'uso di modelli di query e di aggiornamento separati per i dati nei sistemi CQRS semplifica progettazione e implementazione. A differenza dei progetti CRUD, tuttavia, il codice CQRS non può essere generato automaticamente con i meccanismi di scaffolding.

Il modello di query per la lettura dei dati e il modello di aggiornamento per la scrittura dei dati possono accedere allo stesso archivio fisico, usando ad esempio le visualizzazioni SQL o generando proiezioni in tempo reale. È comune tuttavia separare i dati in archivi fisici diversi per ottimizzare prestazioni, scalabilità e sicurezza, come illustrato nella figura seguente.

![Architettura CQRS con archivi di lettura e scrittura separati](images/command-and-query-responsibility-segregation-cqrs-separate-stores.png)


L'archivio di lettura può essere una replica di sola lettura di quello di scrittura oppure gli archivi di lettura e scrittura possono avere una struttura completamente diversa. L'uso di più repliche di sola lettura dell'archivio di lettura può aumentare notevolmente le prestazioni delle query e il tempo di risposta dell'interfaccia utente delle applicazioni, soprattutto in scenari distribuiti in cui le repliche di sola lettura si trovano vicino alle istanze delle applicazioni stesse. Per ottimizzare la disponibilità, alcuni sistemi di database (SQL Server) offrono funzionalità aggiuntive, quali le repliche di failover.

La separazione degli archivi di lettura e scrittura consente anche di dimensionare ogni archivio a seconda del carico. Il carico degli archivi di lettura, ad esempio, è in genere molto più elevato rispetto a quello degli archivi di scrittura.

Quando il modello di query/lettura contiene dati denormalizzati (vedere [Modello di vista materializzata](materialized-view.md)), le prestazioni sono ottimizzate durante la lettura dei dati per ogni visualizzazione in un'applicazione o quando si eseguono query sui dati nel sistema. 

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

- La suddivisione dei dati memorizzati in archivi fisici separati per le operazioni di lettura e scrittura può migliorare le prestazioni e la sicurezza di un sistema, ma può aumentare la complessità in termini di resilienza e coerenza finale. L'archivio di modelli di lettura deve essere aggiornato per riflettere le modifiche all'archivio di modelli di scrittura e può essere difficile rilevare quando un utente ha inviato una richiesta basata su dati letti non aggiornati, che implica che l'operazione non può essere completata.

    > Per una descrizione della coerenza finale, vedere [Nozioni di base sulla coerenza dei dati](https://msdn.microsoft.com/library/dn589800.aspx).

- È possibile applicare il modello CQRS a sezioni limitate del sistema in cui è più rilevante. 

- Un approccio tipico alla distribuzione di coerenza finale consiste nell'usare l'origine evento in combinazione con CQRS in modo che il modello di scrittura sia un flusso di sola aggiunta di eventi guidati dall'esecuzione dei comandi. Tali eventi vengono usati per aggiornare le viste materializzate che agiscono come modello di lettura. Per altre informazioni, vedere [Origine evento e modello CQRS](https://msdn.microsoft.com/library/dn568103.aspx#EventSourcingandCQRS).

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello nelle situazioni seguenti: 

- Domini di collaborazione in cui più operazioni vengono eseguite in parallelo sugli stessi dati. Il modello CQRS consente di definire i comandi con una granularità sufficiente per ridurre i conflitti di unione a livello di dominio (eventuali conflitti che si verificano possono essere uniti dal comando), anche durante l'aggiornamento di dati che sembrano essere dello stesso tipo.

- Interfacce utente basate su attività in cui gli utenti vengono guidati in un processo complesso, ad esempio con una serie di passaggi o con modelli di dominio complessi. È anche utile per i team che hanno già familiarità con tecniche di progettazione basate su domini. Il modello di scrittura dispone di uno stack di elaborazione di comandi completo con logica di business e convalida aziendale e di input per garantire una coerenza costante per ogni funzione aggregata (in cui ogni cluster degli oggetti associati viene considerato come un'unità per la modifica dei dati) nel modello di scrittura. Il modello di lettura non dispone di alcuna logica di business né di alcuno stack di convalida e restituisce semplicemente un oggetto DTO da usare in un modello di visualizzazione. Il modello di lettura è coerente con il modello di scrittura. 

- Scenari in cui le prestazioni di lettura dei dati devono essere ottimizzate separatamente rispetto alle prestazioni di scrittura, soprattutto quando il rapporto di letture/scritture è molto elevato e quando è necessaria la scalabilità orizzontale. In molti sistemi, ad esempio, il numero di operazioni di lettura è notevolmente maggiore del numero di operazioni di scrittura. Per risolvere questo problema, si prenda in considerazione la scalabilità orizzontale del modello di lettura, eseguendo tuttavia il modello di scrittura solo su una o su poche istanze. Un numero ridotto di istanze di un modello di scrittura consente anche di ridurre l'occorrenza di conflitti di unione.

- Scenari in cui un team di sviluppatori possa concentrarsi su un modello di dominio complesso che fa parte del modello di scrittura e in cui un altro team possa concentrarsi sul modello di lettura e sulle interfacce utente. 

- Scenari in cui si prevede che il sistema si evolva nel tempo e che possa contenere più versioni del modello oppure in cui le regole di business cambiano regolarmente.

- Integrazione con altri sistemi, soprattutto in combinazione con un'origine eventi, in cui l'errore temporaneo di un sottosistema non deve influire sulla disponibilità degli altri.

Questo modello non è consigliabile nelle situazioni seguenti:

- Semplicità del dominio o delle regole di business.

- Casi in cui una semplice interfaccia utente di tipo CRUD e le operazioni di accesso ai dati correlate sono sufficienti.

- Implementazione in tutto il sistema. Sono disponibili componenti specifici di uno scenario di gestione dei dati complessivo in cui il modello CQRS può essere utile, ma può aggiungere complessità significativa e inutile qualora non sia necessario.

## <a name="event-sourcing-and-cqrs"></a>Origine evento e modello CQRS

Il modello CQRS viene spesso usato con il modello di origine evento. I sistemi basati su CQRS usano modelli dati di lettura e scrittura separati, ognuno personalizzato in base ad attività rilevanti e spesso situato in archivi separati fisicamente. Quando si usa il modello di [origine evento](event-sourcing.md), l'archivio di eventi rappresenta il modello di scrittura ed è l'origine ufficiale delle informazioni. Il modello di lettura di un sistema basato su CQRS offre viste materializzate dei dati, in genere come viste fortemente denormalizzate. Tali viste sono specifiche per le interfacce e i requisiti di visualizzazione dell'applicazione, in modo da ottimizzare sia le prestazioni delle query che quelle di visualizzazione.

L'uso del flusso di eventi come archivio di scrittura, anziché dei dati effettivi in un punto nel tempo, consente di evitare conflitti di aggiornamento su una singola funzione di aggregazione e di ottimizzare prestazioni e scalabilità. Gli eventi possono essere usati per generare in modo asincrono le viste materializzate dei dati usati per popolare l'archivio di lettura.

Poiché l'archivio di eventi è l'origine ufficiale delle informazioni, è possibile eliminare le viste materializzate e riprodurre tutti gli eventi precedenti per creare una nuova rappresentazione dello stato corrente quando il sistema si evolve o quando il modello di lettura deve cambiare. Le viste materializzate sono in effetti una cache di sola lettura permanente dei dati.

Quando si usa il modello CQRS in combinazione con il modello di origine evento, è necessario considerare quanto segue:

- In modo analogo a qualsiasi sistema in cui gli archivi di lettura e scrittura sono separati, i sistemi basati su questo modello sono caratterizzati solo da coerenza finale. Si verifica pertanto un ritardo tra l'evento generato e l'archivio dati aggiornato.

- Il modello aggiunge complessità perché è necessario creare codice per avviare e gestire gli eventi e assemblare o aggiornare le viste o gli oggetti appropriati richiesti dalle query o da un modello di lettura. La complessità del modello CQRS quando si usa il modello di origine evento rende più difficile una corretta implementazione e richiede un approccio diverso alla progettazione di sistemi. Il modello di origine evento, tuttavia, può semplificare la modellazione del dominio e la rigenerazione di viste o la creazione di nuove perché lo scopo delle modifiche dei dati viene mantenuto.

- La generazione di viste materializzare per l'uso nel modello di lettura o nelle proiezioni dei dati per la riproduzione e la gestione degli eventi per entità specifiche o raccolte di entità può richiedere tempo di elaborazione e uso di risorse significativi. Questo aspetto è particolarmente vero se è richiesta la somma o l'analisi dei valori per lunghi periodi, poiché potrebbe essere necessario esaminare tutti gli eventi associati. Per risolvere questo problema, è necessario implementare snapshot di dati a intervalli pianificati, ad esempio un conteggio totale del numero di azioni specifiche che si sono verificate o lo stato corrente di un'entità.

## <a name="example"></a>Esempio

Il codice seguente mostra alcuni estratti da un esempio di un'implementazione del modello CQRS che usa definizioni diverse per modelli di lettura e di scrittura. Le interfacce di modello non impongono alcuna funzionalità degli archivi dati sottostanti e possono evolversi ed essere ottimizzate in modo indipendente, perché queste interfacce sono separate.

Il codice seguente illustra la definizione di modello di lettura.

```csharp
// Query interface
namespace ReadModel
{
  public interface ProductsDao
  {
    ProductDisplay FindById(int productId);
    IEnumerable<ProductDisplay> FindByName(string name);
    IEnumerable<ProductInventory> FindOutOfStockProducts();
    IEnumerable<ProductDisplay> FindRelatedProducts(int productId);
  }

  public class ProductDisplay
  {
    public int ID { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal UnitPrice { get; set; }
    public bool IsOutOfStock { get; set; }
    public double UserRating { get; set; }
  }

  public class ProductInventory
  {
    public int ID { get; set; }
    public string Name { get; set; }
    public int CurrentStock { get; set; }
  }
}
```

Il sistema consente agli utenti di valutare i prodotti. Il codice dell'applicazione può usare il comando `RateProduct` illustrato nel codice seguente.

```csharp
public interface Icommand
{
  Guid Id { get; }
}

public class RateProduct : Icommand
{
  public RateProduct()
  {
    this.Id = Guid.NewGuid();
  }
  public Guid Id { get; set; }
  public int ProductId { get; set; }
  public int rating { get; set; }
  public int UserId {get; set; }
}
```
Il sistema usa la classe `ProductsCommandHandler` per gestire i comandi inviati dall'applicazione. I client inviano in genere comandi al dominio usando un sistema di messaggistica, ad esempio una coda. Il gestore del comando accetta tali comandi e richiama i metodi dell'interfaccia di dominio. La granularità di ogni comando è progettata per ridurre il rischio di richieste in conflitto. Il codice seguente illustra la classe `ProductsCommandHandler`.

```csharp
public class ProductsCommandHandler : 
    ICommandHandler<AddNewProduct>,
    ICommandHandler<RateProduct>,
    ICommandHandler<AddToInventory>,
    ICommandHandler<ConfirmItemShipped>,
    ICommandHandler<UpdateStockFromInventoryRecount>    
{
  private readonly IRepository<Product> repository;

  public ProductsCommandHandler (IRepository<Product> repository)
  {
    this.repository = repository;
  }

  void Handle (AddNewProduct command)
  {
    ...
  }

  void Handle (RateProduct command)
  {
    var product = repository.Find(command.ProductId);
    if (product != null)
    {
      product.RateProuct(command.UserId, command.rating);
      repository.Save(product);
    }
  }

  void Handle (AddToInventory command)
  {
    ...
  }

  void Handle (ConfirmItemsShipped command)
  {
    ...
  }

  void Handle (UpdateStockFromInventoryRecount command)
  {
    ...
  }
}
```

Il codice seguente illustra l'interfaccia `IProductsDomain` del modello di scrittura.

```csharp
public interface IProductsDomain
{
  void AddNewProduct(int id, string name, string description, decimal price);
  void RateProduct(int userId int rating);
  void AddToInventory(int productId, int quantity);
  void ConfirmItemsShipped(int productId, int quantity);
  void UpdateStockFromInventoryRecount(int productId, int updatedQuantity);
}
```

Si noti anche come l'interfaccia `IProductsDomain` includa metodi che hanno un significato nel dominio. In un ambiente CRUD questi metodi avrebbero in genere nomi comuni, ad esempio `Save` o `Update`, e hanno un oggetto DTO come unico argomento. L'approccio CQRS può essere progettato per soddisfare le esigenze aziendali e i sistemi di gestione dell'inventario dell'organizzazione.

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Quando si implementa questo modello, possono essere utili i modelli e le linee guida seguenti:

- [Nozioni di base sulla coerenza dei dati](https://msdn.microsoft.com/library/dn589800.aspx). Descrive i problemi che si verificano in genere a causa della coerenza finale tra gli archivi dati di lettura e scrittura quando si usa il modello CQRS e il modo in cui possono essere risolti.

- [Linee guida di partizionamento di dati](https://msdn.microsoft.com/library/dn589795.aspx). Descrive il modo in cui gli archivi dati in lettura e scrittura usati nel modello CQRS possono essere suddivisi in partizioni che è possibile gestire e a cui è possibile accedere separatamente per migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni.

- [Modello di origine evento](event-sourcing.md). Descrive in dettaglio il modo in cui l'origine evento può essere usata con il modello CQRS per semplificare le attività in domini complessi e migliorare nel contempo prestazioni, scalabilità e velocità di risposta. Descrive anche come offrire coerenza per i dati transazionali e mantenere controlli completi e cronologia che possono abilitare le azioni di compensazione.

- [Modello di viste materializzate](materialized-view.md). Il modello di lettura di un'implementazione CQRS può contenere viste materializzate dei dati del modello di scrittura oppure può essere usato per generare viste materializzate.

- Guida ai modelli e alle procedure consigliate [Percorso CQRS](http://aka.ms/cqrs). In particolare, [Introduzione al modello di separazione di responsabilità per query e comandi](https://msdn.microsoft.com/library/jj591573.aspx) esamina il modello e i momenti in cui risulta più utile, mentre [Epilogo - Lezioni apprese](https://msdn.microsoft.com/library/jj591568.aspx) consente di comprendere alcuni problemi da affrontare quando si usa questo modello.

- Il post [CQRS di Martin Fowler](http://martinfowler.com/bliki/CQRS.html), che illustra le nozioni di base del modello e contiene collegamenti ad altre risorse utili.

- [Post di Greg Young](http://codebetter.com/gregyoung/), che esaminano molti aspetti del modello CQRS.



<!--HONumber=Nov16_HO3-->


