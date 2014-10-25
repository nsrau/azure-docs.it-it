<properties linkid="mobile-services-dotnet-backend-use-existing-sql-database" urlDisplayName="Build a service using an existing SQL database with the Mobile Services .NET backend" pageTitle="Build a service using an existing SQL database with the Mobile Services .NET backend - Azure Mobile Services" metaKeywords="" description="Learn how to use an existing cloud or on-premises SQL database with your .NET based mobile service" metaCanonical="" services="mobile-services,biztalk-services" documentationCenter="Mobile" title="Build a service using an existing SQL database with the Mobile Services .NET backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Compilare un servizio che usa un database SQL esistente con il back-end .NET per Servizi mobili

Il back-end .NET per Servizi mobili consente di sfruttare le risorse esistenti nella compilazione di un servizio mobile. Uno scenario particolarmente interessante è l'uso di un database SQL esistente (locale o nel cloud), che potrebbe essere già usato da altre applicazioni, per rendere i dati esistenti disponibili per i client mobili. In questo caso è indispensabile che il modello di database (o *schema*) rimanga invariato, per permettere alle soluzioni esistenti di continuare a funzionare.

Questa esercitazione include le sezioni seguenti:

1.  [Esplorazione del modello di database esistente][Esplorazione del modello di database esistente]
2.  [Creazione di oggetti DTO (Data Transfer Object, oggetto di trasferimento dei dati) per il servizio mobile][Creazione di oggetti DTO (Data Transfer Object, oggetto di trasferimento dei dati) per il servizio mobile]
3.  [Definizione di un mapping tra gli oggetti DTO e il modello][Definizione di un mapping tra gli oggetti DTO e il modello]
4.  [Implementazione della logica specifica del dominio][Implementazione della logica specifica del dominio]
5.  [Implementazione di un elemento TableController con gli oggetti DTO][Implementazione di un elemento TableController con gli oggetti DTO]

<a name="ExistingModel"></a>

## Esplorazione del modello di database esistente

Per questa esercitazione verrà usato il database creato con il servizio mobile, ma non il modello predefinito creato. Verrà invece creato manualmente un modello arbitrario che rappresenterà una possibile applicazione esistente. Per informazioni complete su come connettersi invece a un database locale, vedere [Connettersi a un'istanza locale di SQL Server da un servizio mobile Azure usando Connessioni ibride][Connettersi a un'istanza locale di SQL Server da un servizio mobile Azure usando Connessioni ibride].

1.  Per iniziare, creare un progetto server di Servizi mobili in **Visual Studio 2013 Update 2** o usare il progetto di guida introduttiva che è possibile scaricare nella scheda Servizi mobili del servizio nel [portale di gestione di Azure][portale di gestione di Azure]. Ai fini di questa esercitazione, si suppone che il nome del progetto server sia **ShoppingService**.

2.  Creare un file **Customer.cs** nella cartella **Models** e usare l'implementazione seguente. Sarà necessario aggiungere al progetto un riferimento all'assembly **System.ComponentModel.DataAnnotations**.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }

                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3.  Creare un file **Order.cs** nella cartella **Models** e usare l'implementazione seguente:

        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }

                public virtual Customer Customer { get; set; }

            }
        }

    Si noterà che queste due classi hanno una *relazione*: ogni **Order** è associato a un solo **Customer** e un **Customer** può essere associato a più **Orders**. Le relazioni sono comuni nei modelli di dati esistenti.

4.  Creare un file **ExistingContext.cs** nella cartella **Models** e implementarlo nel modo seguente:

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

La struttura precedente è simile a un modello di Entity Framework esistente che potrebbe essere già in uso per un'applicazione esistente. Tenere presente che in questa fase il modello non riconosce in alcun modo Servizi mobili.

<a name="DTOs"></a>

## Creazione di oggetti DTO (Data Transfer Object, oggetto di trasferimento dei dati) per il servizio mobile

Il modello di dati da usare con il servizio mobile potrebbe essere complesso. Potrebbe contenere centinaia di entità collegate da svariate relazioni. Quando si compila un'app mobile, in genere è preferibile semplificare il modello di dati ed eliminare le relazioni (o gestirle manualmente) per ridurre al minimo il payload spostato tra l'app e il servizio. In questa sezione, verrà creato un set di oggetti semplificati (chiamati "oggetti di trasferimento dei dati" o "DTO"), mappati ai dati presenti nel database, ma contenenti solo il set minimo di proprietà necessarie per l'app mobile.

1.  Creare il file **MobileCustomer.cs** nella cartella **DataObjects** del progetto del servizio e usare l'implementazione seguente:

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Tenere presente che questa classe è simile alla classe **Customer** del modello, fatta eccezione per la proprietà relazione per **Order** che è stata rimossa. Poiché un oggetto, per usare correttamente la sincronizzazione offline di Servizi mobili, richiede un set di *proprietà di sistema* per la concorrenza ottimistica, tenere presente che l'oggetto DTO eredita da [**EntityData**][**EntityData**], che contiene tali proprietà. La proprietà **CustomerId** basata su Internet del modello originale viene sostituita dalla proprietà **Id** basata su stringa di **EntityData**, che sarà l'**Id** usato da Servizi mobili.

2.  Creare il file **MobileOrder.cs** nella cartella **DataObjects** del progetto del servizio.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    La proprietà relazione **Customer** è stata sostituita con il nome **Customer** e una proprietà **MobileCustomerId** che può essere usata per modellare manualmente la relazione nel client. Per ora è possibile ignorare la proprietà **CustomerId** che verrà usata solo più avanti.

3.  È possibile notare che con l'aggiunta delle proprietà di sistema nella classe base **EntityData**, gli oggetti DTO hanno ora più proprietà dei tipi di modello. Ovviamente è necessario archiviare queste proprietà e quindi verranno aggiunte alcune nuove colonne al database originale. Questa operazione, anche se modifica il database, non interrompe le applicazioni esistenti perché le modifiche sono esclusivamente additive (aggiunta di nuove colonne allo schema). A tal fine, aggiungere le istruzioni seguenti all'inizio di **Customer.cs** e **Order.cs**:

        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

    Aggiungere quindi queste altre proprietà a ogni classe:

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index(IsClustered = true)]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4.  Le proprietà di sistema appena aggiunte hanno alcuni comportamenti predefiniti (ad esempio, l'aggiornamento automatico dell'ora di creazione/aggiornamento) che si verificano in modo trasparente con le operazioni sul database. Per abilitare questi comportamenti, è necessario apportare una modifica a **ExistingContext.cs**. All'inizio del file aggiungere quanto segue:

        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

    Quindi nel corpo di **ExistingContext** eseguire l'override di [**OnModelCreating**][**OnModelCreating**]:

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5.  Il database verrà ora popolato con alcuni dati di esempio. Aprire il file **WebApiConfig.cs**. Creare un nuovo [**IDatabaseInitializer**][**IDatabaseInitializer**] e configurarlo nel metodo **Register** come mostrato di seguito.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> { 
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> { 
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> { 
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>

## Definizione di un mapping tra gli oggetti DTO e il modello

Ora sono disponibili i tipi di modello **Customer** e **Order** e gli oggetti DTO **MobileCustomer** e **MobileOrder**, ma è necessario indicare al back-end di eseguire automaticamente la trasformazione da uno all'altro. Qui Servizi mobili si basa su [**AutoMapper**][**AutoMapper**], un mapper relazionale di oggetti, già referenziato nel progetto.

1.  Aggiungere quanto segue all'inizio di **WebApiConfig.cs**:

        using AutoMapper;
        using ShoppingService.DataObjects;

2.  Per definire il mapping, aggiungere quanto segue al metodo **Register** della classe **WebApiConfig**.

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper mapperà ora un oggetto all'altro. Tutte le proprietà con nomi corrispondenti verranno associate, ad esempio **MobileOrder.CustomerId** verrà automaticamente mappata a **Order.CustomerId**. I mapping personalizzati possono essere configurati come mostrato sopra, dove la proprietà **MobileCustomerName** viene mappata alla proprietà **Name** della proprietà relazione **Customer**.

<a name="DomainManager"></a>

## Implementazione della logica specifica del dominio

Il passaggio successivo consiste nell'implementare un elemento [**MappedEntityDomainManager**][**MappedEntityDomainManager**], che viene usato come livello di astrazione tra l'archivio dati mappato e il controller che gestirà il traffico HTTP dai client. Sarà possibile scrivere il controller nella sezione successiva solo in base agli oggetti DTO e l'elemento **MappedEntityDomainManager** aggiunto qui gestirà la comunicazione con l'archivio dati originale, permettendo anche di implementare la logica specifica.

1.  Aggiungere un file **MobileCustomerDomainManager.cs** alla cartella **Models** del progetto. Incollare l'implementazione seguente:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }

                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    Una parte importante di questa classe è il metodo **GetKey** dove si indica come trovare la proprietà ID dell'oggetto nel modello di dati originale.

2.  Aggiungere un file **MobileOrderDomainManager.cs** alla cartella **Models** del progetto:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    In questo caso i metodi **InsertAsync** e **UpdateAsync** sono interessanti perché è a questi metodi che viene applicata la relazione in base alla quale a ogni **Order** deve essere associato un elemento **Customer** valido. In **InsertAsync** si noterà che viene popolata la proprietà **MobileOrder.CustomerId**, che esegue il mapping alla proprietà **Order.CustomerId**. Tale valore viene ottenuto cercando l'elemento **Customer** con il corrispondente **MobileOrder.MobileCustomerId**. Questo avviene perché, per impostazione predefinita, il client riconosce solo l'ID Servizi mobili (**MobileOrder.MobileCustomerId**) dell'elemento **Customer**, che è diverso dalla chiave primaria effettiva necessaria per impostare la chiave esterna (**MobileOrder.CustomerId**) da **Order** su **Customer**. Questa viene usata solo internamente al servizio per facilitare l'operazione di inserimento.

A questo punto è possibile creare i controller per esporre gli oggetti DTO ai client.

<a name="Controller"></a>

## Implementazione di un elemento TableController con gli oggetti DTO

1.  Nella cartella **Controllers** aggiungere il file **MobileCustomerController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    Si noterà l'uso dell'attributo AuthorizeLevel per limitare l'accesso pubblico alle operazioni di inserimento/aggiornamento/eliminazione sul controller. Ai fini di questo scenario, l'elenco di clienti sarà di sola lettura, ma sarà possibile creare nuovi ordini e associarli ai clienti esistenti.

2.  Nella cartella **Controllers** aggiungere il file **MobileOrderController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3.  È ora possibile eseguire il servizio. Premere **F5** e usare il client di test incorporato nella pagina della Guida per modificare i dati.

Tenere presente che entrambe le implementazioni del controller usano esclusivamente gli oggetti DTO **MobileCustomer** e **MobileOrder** e non considerano il modello sottostante. Questi oggetti DTO vengono serializzati direttamente in JSON e possono essere usati per scambiare dati con l'SDK del client di Servizi mobili su tutte le piattaforme. Ad esempio, se si compila un'app di Windows Store, il corrispondente tipo lato client sarà simile al seguente. Il tipo sarà analogo su altre piattaforme client.

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }
            
            [Version]
            public string Version { get; set; }

        }

    }

Come passaggio successivo, sarà possibile compilare l'app client per accedere al servizio.

  [Esplorazione del modello di database esistente]: #ExistingModel
  [Creazione di oggetti DTO (Data Transfer Object, oggetto di trasferimento dei dati) per il servizio mobile]: #DTOs
  [Definizione di un mapping tra gli oggetti DTO e il modello]: #Mapping
  [Implementazione della logica specifica del dominio]: #DomainManager
  [Implementazione di un elemento TableController con gli oggetti DTO]: #Controller
  [Connettersi a un'istanza locale di SQL Server da un servizio mobile Azure usando Connessioni ibride]: /it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [**EntityData**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx
  [**OnModelCreating**]: http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx
  [**IDatabaseInitializer**]: http://msdn.microsoft.com/library/gg696323.aspx
  [**AutoMapper**]: http://automapper.org/
  [**MappedEntityDomainManager**]: http://msdn.microsoft.com/library/dn643300.aspx
