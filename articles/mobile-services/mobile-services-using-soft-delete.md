<properties
	pageTitle="Uso dell'eliminazione temporanea in Servizi mobili (Windows Store) | Microsoft Azure"
	description="Informazioni sull'uso della funzionalità di eliminazione temporanea di Servizi mobili di Azure nell'applicazione"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="wesmc"/>

# Uso dell'eliminazione temporanea in Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Panoramica

Le tabelle create con il back-end JavaScript o .NET possono facoltativamente avere l'eliminazione temporanea abilitata. Quando si usa l'eliminazione temporanea, viene aggiunta al database una nuova colonna chiamata *\_\_deleted* di [tipo bit SQL]. Con l'eliminazione temporanea abilitata, un'operazione di eliminazione non elimina fisicamente le righe dal database, ma imposta il valore della colonna eliminata su TRUE.

Quando si esegue una query dei record in una tabella con l'eliminazione temporanea abilitata, le righe eliminate non vengono restituite nella query per impostazione predefinita. Per richiedere queste righe, è necessario passare un parametro di query *\_\_includeDeleted=true* nell'[operazione di query REST](http://msdn.microsoft.com/library/azure/jj677199.aspx). Nell'SDK del client .NET, è anche possibile usare il metodo helper `IMobileServiceTable.IncludeDeleted()`.

Il supporto dell'eliminazione temporanea per il back-end .NET è stato rilasciato per la prima volta con la versione 1.0.402 del back-end .NET per Servizi mobili di Microsoft Azure. Gli ultimi pacchetti NuGet sono disponibili in [Back-end .NET per Servizi mobili di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).


Alcuni dei potenziali vantaggi dell'uso dell'eliminazione temporanea:

* Quando si usa la funzionalità di [sincronizzazione dati offline per Servizi mobili], l'SDK del client esegue automaticamente la query dei record eliminati e li rimuove dal database locale. Senza l'eliminazione temporanea abilitata, è necessario scrivere codice aggiuntivo nel back-end in modo che l'SDK del client sappia quali record rimuovere dall'archivio locale; in caso contrario, l'archivio e il back-end del client locali non saranno coerenti in relazione a questi record eliminati e dovrà essere chiamato il metodo client `PurgeAsync()` per cancellare l'archivio locale.
* Per alcune applicazioni esiste un requisito aziendale in base al quale i dati non vengono mai eliminati fisicamente oppure vengono eliminati solo dopo essere stati controllati. La funzionalità di eliminazione temporanea può essere utile in questo scenario.
* L'eliminazione temporanea può essere usata per implementare una funzionalità di "ripristino", per poter recuperare i dati eliminati per errore. Tuttavia, i record eliminati temporaneamente occupano spazio nel database, quindi è consigliabile creare un processo pianificato per eliminare definitivamente a intervalli regolari i record eliminati temporaneamente. Per un esempio, vedere [Uso dell'eliminazione temporanea con il back-end .NET](#using-with-dotnet) e [Uso dell'eliminazione temporanea con il back-end JavaScript](#using-with-javascript). Il codice client deve anche chiamare periodicamente `PurgeAsync()` in modo che questi record eliminati definitivamente non rimangano nell'archivio dati locale del dispositivo.





##Abilitazione dell'eliminazione temporanea per il back-end .NET

Il supporto dell'eliminazione temporanea per il back-end .NET è stato rilasciato per la prima volta con la versione 1.0.402 del back-end .NET per Servizi mobili di Microsoft Azure. Gli ultimi pacchetti NuGet sono disponibili in [Back-end .NET per Servizi mobili di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).

Nei passaggi successivi viene illustrata l'abilitazione dell'eliminazione temporanea per un servizio mobile back-end .NET.

1. Aprire il servizio mobile back-end .NET in Visual Studio.
2. Fare clic con il pulsante destro del mouse sul progetto di back-end .NET e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra di dialogo Gestione pacchetti fare clic su **Nuget.org** sotto gli aggiornamenti e installare la versione 1.0.402 o successiva dei pacchetti NuGet del [back-end .NET per Servizi mobili di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).
3. In Esplora soluzioni per Visual Studio espandere il nodo **Controller** sotto il progetto del back-end .NET e aprire l'origine del controller. Ad esempio, *TodoItemController.cs*.
4. Nel metodo `Initialize()` del controller passare il parametro `enableSoftDelete: true` al costruttore EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##Abilitazione dell'eliminazione temporanea per il back-end JavaScript

Se si sta creando una nuova tabella per il servizio mobile, è possibile abilitare l'eliminazione temporanea nella pagina di creazione della tabella.

![][2]

Per abilitare l'eliminazione temporanea in una tabella esistente nel back-end JavaScript:

1. Nel [portale di Azure classico] fare clic sul servizio mobile. quindi fare clic sulla scheda Dati.
2. Nella pagina dei dati fare clic per selezionare la tabella desiderata, quindi fare clic sul pulsante **Abilita eliminazione temporanea** nella barra dei comandi. Se l'eliminazione temporanea è già abilitata per la tabella, questo pulsante non verrà visualizzato, ma sarà possibile vedere la colonna *\_\_deleted* facendo clic sulle schede **Sfoglia** o **Colonne**.

    ![][0]

    Per disabilitare l'eliminazione temporanea per la tabella, fare clic sulla scheda **Colonne** e quindi fare clic sulla colonna *\_\_deleted* e sul pulsante **Elimina**.

    ![][1]

## <a name="using-with-dotnet"></a>Uso dell'eliminazione temporanea con il back-end .NET


Il seguente processo pianificato ripulisce i record eliminati temporaneamente più vecchi di un mese:

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;

        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }

        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);

            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();

            return Task.FromResult(true);
        }
    }

Per altre informazioni sui processi pianificati con il back-end .NET di Servizi mobili, vedere [Pianificare processi ricorrenti con Servizi mobili per il back-end JavaScript](mobile-services-dotnet-backend-schedule-recurring-tasks.md).




## <a name="using-with-javascript"></a> Uso dell'eliminazione temporanea con il back-end JavaScript

Gli script delle tabelle vengono usati per aggiungere la logica alla funzionalità di eliminazione temporanea con Servizi mobili per il back-end JavaScript.

Per rilevare una richiesta di ripristino, usare la proprietà "undelete" nello script della tabella di aggiornamento:

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
Per includere in uno script i record eliminati nel risultato di una query, impostare il parametro "includeDeleted" su true:

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Per recuperare i record eliminati con una richiesta HTTP, aggiungere il parametro della query "\_\_includedeleted=true":

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Processo pianificato di esempio per ripulire i record eliminati temporaneamente.

Il seguente processo pianificato di esempio elimina i record aggiornati prima di una determinata data:

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Per altre informazioni sui processi pianificati con il back-end JavaScript di Servizi mobili, vedere [Pianificare processi ricorrenti con Servizi mobili per il back-end JavaScript](mobile-services-schedule-recurring-tasks.md).





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[tipo bit SQL]: http://msdn.microsoft.com/library/ms177603.aspx
[sincronizzazione dati offline per Servizi mobili]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[portale di Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->