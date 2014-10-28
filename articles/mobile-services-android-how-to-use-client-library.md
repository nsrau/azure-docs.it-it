<properties linkid="develop-mobile-how-to-guides/work-with-android-client-library" urlDisplayName="Android Client Library" pageTitle="Working with the Mobile Services Android Client Library" metaKeywords="" description="Learn how to use an Android client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the Android client library for Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Come utilizzare la libreria client Android per Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/it-it/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/it-it/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/it-it/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/it-it/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

In questa guida viene illustrato come eseguire scenari comuni utilizzando un client Android per Servizi mobili di Azure. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti, la gestione di errori e la personalizzazione del client. Se non si ha familiarità con Servizi mobili, si consiglia di completare prima il progetto di [guida introduttiva per Servizi mobili][]. L'esercitazione relativa alla guida introduttiva è utile per configurare il proprio account e creare il primo servizio mobile.

Gli esempi sono scritti in Java ed è necessario avere installato [Mobile Services SDK][]. Per completare questa esercitazione, è inoltre necessario disporre di [Android SDK][], che include l'IDE (Integrated Development Environment) di Eclipse e il plug-in Android Developer Tools (ADT). Con Mobile Services SDK è supportato Android 2.2 o versione successiva, tuttavia è consigliabile compilare su Android 4.2 o versione successiva.

## Sommario

-   [Informazioni su Servizi mobili][]
-   [Concetti][]
-   [Installazione e prerequisiti][]
-   [Procedura: Creare il client di Servizi mobili][]
-   [Procedura: Creare un riferimento alla tabella][]

    -   [Struttura dell'API][]
-   [Procedura: Eseguire query sui dati da un servizio mobile][]

    -   [Filtrare i dati restituiti][]
    -   [Ordinare i dati restituiti][]
    -   [Restituire i dati in pagine][]
    -   [Selezionare colonne specifiche][]
    -   [Procedura: Concatenare metodi di query][]
-   [Procedura: Inserire dati in un servizio mobile][]
-   [Procedura: Aggiornare dati in un servizio mobile][]
-   [Procedura: Eliminare dati in un servizio mobile][]
-   [Procedura: Cercare un elemento specifico][]
-   [Procedura: Utilizzare dati non tipizzati][]
-   [Procedura: Associare dati all'interfaccia utente][]

    -   [Procedura: Definire il layout][]
    -   [Procedura: Definire l'adattatore][]
    -   [Procedura: Utilizzare l'adattatore][]
-   [Procedura: Autenticare gli utenti][]

    -   [Memorizzare nella cache i token di autenticazione][]
-   [Procedura: Gestire gli errori][]
-   [Procedura: Personalizzare il client][]

    -   [Personalizzare le intestazioni di richieste][]
    -   [Personalizzare la serializzazione][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [mobile-services-concepts][]]

## <a name="setup"></a><span class="short-header">Installazione</span>Installazione e prerequisiti

Si presuppone che siano stati creati un servizio mobile e una tabella. Per ulteriori informazioni, vedere [Creare una tabella][]. Nel codice utilizzato in questo argomento si presuppone che la tabella sia denominata *ToDoItem* e che contenga le colonne seguenti:

-   id
-   text
-   complete
-   due
-   duration

L'oggetto tipizzato corrispondente sul lato client è il seguente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
        private Date due
        private Integer duration;
    }

Quando è abilitato lo schema dinamico, in Servizi mobili di Azure vengono generate automaticamente nuove colonne basate sull'oggetto nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico][].

## <a name="create-client"></a><span class="short-header">Creare il client di Servizi mobili</span>Procedura: Creare il client di Servizi mobili

Il codice seguente consente di creare l'oggetto [MobileServiceClient][] usato per accedere al servizio mobile.

            MobileServiceClient mClient = new MobileServiceClient(
                    "MobileServiceUrl", // Replace with the above Site URL
                    "AppKey",           // replace with the Application Key 
                    this)

Nel codice riportato sopra sostituire `MobileServiceUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, nell'ordine specificato. Entrambi gli elementi sono disponibili nel portale di gestione di Azure selezionando il servizio mobile e quindi facendo clic su *Dashboard*.

## <a name="instantiating"></a><span class="short-header">Creare un riferimento alla tabella</span>Procedura: Creare un riferimento alla tabella

Il modo più semplice per eseguire query sui dati o modificarli nel servizio mobile consiste nell'utilizzare il *modello di programmazione tipizzato*, dal momento che Java è un linguaggio fortemente tipizzato. Più avanti verrà illustrato il modello *non tipizzato*. Questo modello garantisce la serializzazione e la deserializzazione automatica con JSON tramite la libreria [gson][] durante l'invio di dati tra il client e il servizio mobile, di conseguenza la gestione è affidata al framework e lo sviluppatore non deve eseguire altre operazioni.

Per eseguire una query sui dati o modificarli, è innanzitutto necessario creare un oggetto [MobileServiceTable][] chiamando il metodo **getTable** su [**MobileServiceClient**][MobileServiceClient]. Verranno ora esaminati i due overload di questo metodo:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Nel codice seguente *mClient* è un riferimento al client del servizio mobile.

Il [primo overload][] viene utilizzato nel caso in cui il nome della classe e il nome della tabella sono identici:

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

Il [secondo overload][] viene invece utilizzato quando il nome della tabella è diverso da quello del tipo.

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="api"></a>Struttura dell'API

Per le operazioni su tabelle di Servizi mobili viene utilizzato il metodo di callback asincrono. I metodi che interessano query e operazioni quali inserimenti, aggiornamenti ed eliminazioni includono tutti un parametro che corrisponde a un oggetto callback. Tale oggetto contiene sempre un metodo **OnCompleted**. Il metodo **onCompleted** contiene un parametro che corrisponde a un oggetto **Exception**, di cui è possibile eseguire il test per determinare la riuscita della chiamata al metodo. Un oggetto **Exception** Null indica un esito positivo; in caso contrario, l'oggetto **Exception** descrive la causa dell'errore.

Sono disponibili diversi oggetti callback. Quello utilizzato varia a seconda che si intenda eseguire query, modificare o eliminare i dati. I parametri per il metodo *onCompleted* sono diversi a seconda dell'oggetto callback di cui fa parte.

## <a name="querying"></a><span class="short-header">Eseguire query sui dati</span>Procedura: Eseguire query sui dati da un servizio mobile

In questa sezione viene descritto come eseguire query nel servizio mobile. Nelle sottosezioni vengono descritti aspetti diversi come ordinamento, filtri e paging. Nella parte finale viene spiegato come concatenare queste operazioni.

Il codice seguente consente di restituire tutti gli elementi della tabella *ToDoItem*.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
                public void onCompleted(List<ToDoItem> result, int count,
                    Exception exception, ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    }
                }
            });

In query come questa viene utilizzato l'oggetto callback [**TableQueryCallback\<E\>**][].

Con il parametro *result* viene restituito il set di risultati della query, mentre il codice all'interno della diramazione relativa all'esito positivo del test *exception* illustra come analizzare le singole righe.

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente consente di restituire tutti gli elementi della tabella *ToDoItem* il cui campo *complete* è uguale a *false*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

        mToDoTable.where().field("complete").eq(false)
                  .execute(new TableQueryCallback<ToDoItem>() {
                        public void onCompleted(List<ToDoItem> result, 
                                                int count, 
                                                Exception exception,
                                                ServiceFilterResponse response) {
                if (exception == null) {
                    for (ToDoItem item : result) {
                        Log.i(TAG, "Read object with ID " + item.id);  
                    }
                } 
            }
        });

Per iniziare un filtro, è necessario inserire una chiamata al metodo [**where**][] sul riferimento alla tabella. Tale chiamata è seguita da un metodo [**field**][], a sua volta seguito da una chiamata al metodo che specifica il predicato logico. I possibili metodi del predicato includono tra gli altri [**eq**][], [**ne**][], [**gt**][], [**ge**][], [**lt**][] e [**le**][].

Tali informazioni sono sufficienti per confrontare campi numerici e di stringhe con valori specifici. Tuttavia è anche possibile effettuare altre operazioni,

ad esempio filtrare in base alle date. È possibile confrontare l'intero campo data, nonché parti della data, come [**year**][], [**month**][], [**day**][], [**hour**][], [**minute**][] e [**second**][]. Il codice parziale seguente consente di aggiungere un filtro per elementi il valore per la *scadenza* è uguale a 2013.

        mToDoTable.where().year("due").eq(2013)

È possibile eseguire una vasta gamma di filtri complessi su campi di tipo stringa con metodi quali [**startsWith**][], [**endsWith**][], [**concat**][], [**subString**][], [**indexOf**][], [**replace**][], [**toLower**][], [**toUpper**][], [**trim**][] e [**length**][]. Il codice parziale seguente consente di filtrare le righe della tabella in cui la colonna *text* inizia con "PRI0".

        mToDoTable.where().startsWith("text", "PRI0")

Anche con i campi di tipo numerico è possibile utilizzare una vasta gamma di filtri più complessi con metodi quali [**add**][], [**sub**][], [**mul**][], [**div**][], [**mod**][], [**floor**][], [**ceiling**][] e [**round**][]. Il codice parziale seguente consente di filtrare le righe della tabella in cui il valore di *duration* è un numero pari.

        mToDoTable.where().field("duration").mod(2).eq(0)

È possibile combinare i predicati con metodi quali [**and**][], [**or**][] e [**not**][]. Nel codice parziale seguente vengono combinati due degli esempi precedenti.

        mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

È inoltre possibile raggruppare e annidare operatori logici, come illustrato in questo codice parziale:

        mToDoTable.where()
                    .year("due").eq(2013)
                        .and
                    (startsWith("text", "PRI0").or().field("duration").gt(10))

Per una trattazione più dettagliata e per esempi di filtri, vedere il post relativo alle [funzionalità complesse disponibili nel modello di query client Android di Servizi mobili][].

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice di esempio seguente consente di restituire tutti gli elementi di una tabella di *ToDoItems* elencati in ordine crescente in base al campo *text*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

        mToDoTable.orderBy("text", QueryOrder.Ascending)
            .execute(new TableQueryCallback<ToDoItem>() { 
                /* same implementation as above */ 
            }); 

Il primo parametro del metodo [**orderBy**][] è una stringa uguale al nome del campo in base al quale eseguire l'ordinamento.

Nel secondo parametro viene utilizzata l'enumerazione [**QueryOrder**][] per specificare l'ordinamento crescente o decrescente.

Si noti che se per il filtro si utilizza il metodo ***where***, è necessario chiamare tale metodo ****prima del metodo ***orderBy***.

### <a name="paging"></a>Procedura: Restituire i dati in pagine

Nel primo esempio viene illustrato come selezionare i primi cinque elementi di una tabella. La query restituisce gli elementi di una tabella di *ToDoItems*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

        mToDoTable.top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                public void onCompleted(List<ToDoItem> result, 
                                        int count,
                                        Exception exception, 
                                        ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    } 
                }
            });

Verrà ora definita una query che ignora i primi cinque elementi e restituisce i cinque successivi.

        mToDoTable.skip(5).top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                // implement onCompleted logic here
            });

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

Nel codice seguente viene illustrato come restituire tutti gli elementi di una tabella di *ToDoItems*, visualizzando però solo i campi *complete* e *text*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

        mToDoTable.select("complete", "text")
                .execute(new TableQueryCallback<ToDoItem>() { 
                    /* same implementation as above */ 
            }); 

In questo caso, i parametri della funzione select sono i nomi in formato stringa delle colonne della tabella che si desidera restituire.

Il metodo [**select**][] deve essere inserito dopo metodi quali [**where**][1] e [**orderBy**][2], se presenti. Può essere seguito da metodi quali [**top**][].

### <a name="chaining"></a>Procedura: Concatenare metodi di query

È possibile concatenare i metodi utilizzati per eseguire query su tabelle di servizi mobili. In questo modo è possibile eseguire operazioni quali selezionare colonne specifiche di righe filtrate ordinate e sottoposte a paging. È possibile creare filtri logici abbastanza complessi.

Il funzionamento è garantito dal fatto che i metodi di query utilizzati restituiscono oggetti [**MobileServiceQuery\<T\>**][], su cui è possibile chiamare ulteriori metodi. Per terminare la serie di metodi ed eseguire effettivamente la query, è necessario chiamare il metodo [**execute**][].

Nel codice di esempio seguente *mToDoTable* è un riferimento alla tabella *ToDoItem* dei servizi mobili.

        mToDoTable.where().year("due").eq(2013)
                        .and().startsWith("text", "PRI0")
                        .or().field("duration").gt(10)
                    .select("id", "complete", "text", "duration")
                    .orderBy(duration, QueryOrder.Ascending).top(20)                
                    .execute(new TableQueryCallback<ToDoItem>() { 
                        /* code to execute */ 
                });

Per un corretto concatenamento dei metodi è essenziale inserire per primi il metodo *where* e i predicati. In seguito è possibile chiamare metodi successivi nell'ordine più appropriato alle esigenze dell'applicazione.

## <a name="inserting"></a><span class="short-header">Inserire dati</span>Procedura: Inserire dati in un servizio mobile

Nel codice seguente viene illustrato come inserire nuove righe in una tabella.

In primo luogo viene creata un'istanza della classe *ToDoItem* e vengono impostate le relative proprietà.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 
        

Viene quindi chiamato il metodo [**insert**][].

        mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                Exception exception, 
                                ServiceFilterResponse response) {   
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Per le operazioni di **insert** l'oggetto callback è [**TableOperationCallback\<ToDoItem\>**][].

Il parametro entity del metodo **onCompleted** contiene il nuovo oggetto inserito. Nel codice relativo all'operazione riuscita viene illustrato come accedere all'*id* della riga inserita.

Servizi mobili supporta valori di stringa univoci personalizzati per l'ID della tabella. In tal modo alle applicazioni è consentito utilizzare valori personalizzati come indirizzi di posta elettronica o nomi utente per la colonna ID di una tabella di Servizi mobili. Se ad esempio si desidera identificare ogni record con un indirizzo di posta elettronica, è possibile utilizzare l'oggetto JSON seguente.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.id = "myemail@mydomain.com";
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 

Se al momento dell'inserimento di nuovi record in una tabella non viene specificato un valore ID di stringa, Servizi mobili genera un valore univoco per l'ID.

Il supporto di ID di stringa comporta i seguenti vantaggi per gli sviluppatori

-   È possibile generare ID senza generare un round trip del database.
-   L'unione di record da tabelle o database diversi risulta semplificata.
-   L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Per impostare i valori di ID, è inoltre possibile utilizzare script del server. L'esempio di script seguente genera un GUID personalizzato e lo assegna all'ID di un nuovo record. Si tratta di un valore di ID simile a quello che verrebbe generato da Servizi mobili se non viene passato un valore per l'ID di un record.

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Il valore di ID specificato da un'applicazione verrà archiviato da Servizi mobili così com'è. Sono inclusi spazi vuoti iniziali o finali. Lo spazio vuoto non verrà rimosso dal valore.

Il valore per `id` deve essere univoco e non deve includere caratteri appartenenti ai set seguenti:

-   Caratteri di controllo: [0x0000-0x001F] e [0x007F-0x009F]. Per altre informazioni, vedere la pagina relativa ai [codici di controllo ASCII C0 e C1][].
-   Caratteri stampabili: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Gli ID "." e ".."

In alternativa è possibile utilizzare ID di tipo integer per le tabelle. Per utilizzare un ID integer, è necessario creare la tabella con il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene usato con l'interfaccia della riga di comando (CLI) per Azure. Per altre informazioni sull'utilizzo dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili][].

## <a name="updating"></a><span class="short-header">Aggiornare dati</span>Procedura: Aggiornare dati in un servizio mobile

Nel codice seguente viene illustrato come aggiornare i dati in una tabella. Nell'esempio *mToDoItem* è un riferimento a un elemento della tabella *ToDoItem* del quale verrà aggiornata la proprietà *duration*.

        mToDoItem.duration = 5;
        mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                    Exception exception, 
                                    ServiceFilterResponse response) {
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Si noti che l'oggetto callback e i parametri del metodo *onCompleted* sono identici quando si esegue un'operazione di insert.

## <a name="deleting"></a><span class="short-header">Eliminare dati</span>Procedura: Eliminare dati in un servizio mobile

Nel codice seguente viene illustrato come eliminare i dati da una tabella. In particolare viene eliminato un elemento esistente dalla tabella ToDoItem utilizzando un riferimento all'elemento stesso, in questo caso *mToDoItem*.

        mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
            public void onCompleted(Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Si noti che nel caso di *delete* l'oggetto callback è [**TableDeleteCallback**][] e che il metodo **onCompleted** è leggermente diverso in quando non viene restituita nessuna riga di tabella.

Nel codice seguente viene illustrato un altro modo per eseguire questa operazione. In questo caso per eliminare un elemento esistente dalla tabella ToDoItem viene specificato il valore del campo id della riga da eliminare, presupponendo che sia uguale "37BBF396-11F0-4B39-85C8-B319C729AF6D".

        mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

## <a name="lookup"></a><span class="short-header">Cercare dati</span>Procedura: Cercare un elemento specifico

In alcuni casi si desidera cercare un elemento specifico in base al relativo *id*, anziché eseguire una query che invece consente in genere di ottenere una raccolta di elementi che soddisfano alcuni criteri. Nel codice seguente viene illustrato come eseguire questa operazione per *id* = "37BBF396-11F0-4B39-85C8-B319C729AF6D".

        mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
            public void onCompleted(item entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Read object with ID " + entity.id);    
                }
            }
        });

## <a name="untyped"></a><span class="short-header">Utilizzare dati non tipizzati</span>Procedura: Utilizzare dati non tipizzati

Il modello di programmazione non tipizzato consente di disporre di controllo accurato sulla serializzazione JSO, pertanto è possibile che si desideri utilizzarlo in alcuni scenari, ad esempio se la tabella del servizio mobile contiene un numero elevato di colonne e si desidera fare riferimento solo ad alcune di esse. Per utilizzare il modello tipizzato è necessario definire tutte le colonne della tabella del servizio mobile nella classe dati. Tuttavia con il modello non tipizzato si possono definire solo le colonne che è necessario utilizzare.

Analogamente al modello tipizzato, si inizia recuperando un riferimento alla tabella, ma in questo caso si tratta di un oggetto [MobileServicesJsonTable][]. Per ottenere il riferimento, viene chiamato il metodo [getTable()][] su un'istanza del client di Servizi mobili.

Utilizzare quindi il seguente overload di questo metodo, utilizzato per i modelli di programmazione basati su JSON non tipizzati:

        public class MobileServiceClient {
            public MobileServiceJsonTable getTable(String name);
        }

La maggior parte delle chiamate API per l'accesso ai dati è simile alle chiamate della programmazione tipizzata. La principale differenza consiste nel fatto che nel modello non tipizzato i metodi vengono chiamati sull'oggetto **MobileServiceJsonTable**, anziché su **MobileServiceTable**. La sintassi dell'oggetto callback e il metodo **onCompleted** sono molto simili a quelli del modello tipizzato.

### <a name="json_instance"></a>Procedura: Creare un'istanza di una tabella non tipizzata

Dopo avere creato un'istanza del client di Servizi mobili (in questo caso la variabile *mClient*), viene creata un'istanza di un oggetto **MobileServiceJsonTable**, con il codice seguente.

        MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

Dopo avere creato un'istanza dell'oggetto **MobileServiceJsonTable**, sarà possibile chiamarvi quasi tutti i metodi utilizzabili con il modello di programmazione tipizzato. In alcuni casi, tuttavia, i metodi accettano un parametro non tipizzato, come è possibile notare negli esempi che seguono.

### <a name="json_insert"></a>Procedura: Eseguire insert in una tabella non tipizzata

Nel codice seguente viene illustrato come eseguire un'operazione di insert. Il primo passaggio consiste nel creare un oggetto [**JsonObject**][], incluso nella libreria [gson][].

        JsonObject task = new JsonObject();
        task.addProperty("text", "Wake up");
        task.addProperty("complete", false);
        task.addProperty("duration", 5);

Il passaggio successivo consiste nell'inserire l'oggetto. La funzione di callback passata al metodo [**insert**][3] è un'istanza della classe [**TableJsonOperationCallback**][]. Si noti che il primo parametro del metodo *onCompleted* è un oggetto JsonObject.

        mTable.insert(task, new TableJsonOperationCallback() {
            public void onCompleted(JsonObject jsonObject, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object inserted with ID " + 
                jsonObject.getAsJsonPrimitive("id").getAsString());
                }
            }
        });

Si noti come ottenere l'ID dell'oggetto inserito con questa chiamata al metodo:

                jsonObject.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Procedura: Eliminare un'istanza da una tabella non tipizzata

Nel codice seguente viene illustrato come eliminare un'istanza, in questo caso la stessa istanza di un oggetto **JsonObject** creato nell'esempio *insert* precedente. Si noti che l'oggetto callback **TableDeleteCallback** è uguale a quello utilizzato nel modello di programmazione tipizzato e che il relativo metodo **onCompleted** presenta una firma diversa da quella utilizzata nell'esempio relativo a **insert**.

        mTable.delete(task, new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

È inoltre possibile eliminare un'istanza direttamente utilizzando il relativo ID:

        mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)

### <a name="json_get"></a>Procedura: Restituire tutte le righe di una tabella non tipizzata

Nel codice seguente viene illustrato come recuperare un'intera tabella. Si noti che nel modello di programmazione non tipizzato viene utilizzato un oggetto callback diverso: [**TableJsonQueryCallback**][].

        mTable.execute(new TableJsonQueryCallback() {
            public void onCompleted(JsonElement result, 
                                    int count, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    JsonArray results = result.getAsJsonArray();
                    for(JsonElement item : results){
                        Log.i(TAG, "Read object with ID " + 
                    item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
                    }
                }
            }
        });

È possibile applicare filtri, ordinare ed eseguire il paging concatenando i metodi con gli stessi nomi di quelli utilizzati nel modello di programmazione tipizzato.

## <a name="binding"></a><span class="short-header">Associare dati</span>Procedura: Associare dati all'interfaccia utente

L'associazione dati riguarda tre componenti:

-   Origine dati
-   Layout della schermata
-   Adattatore che collega i due componenti precedenti

Nel codice di esempio i dati vengono restituiti in una matrice dalla tabella del servizio mobile *ToDoItem*. Si tratta di uno dei modelli più comuni per le applicazioni dati: le query su database restituiscono in genere una raccolta di righe che il client ottiene in un elenco o una matrice. In questo esempio la matrice è l'origine dati.

Nel codice viene specificato un layout di schermata che definisce la visualizzazione dei dati che appariranno sul dispositivo.

I due elementi vengono associati tra loro tramite un adattatore, che in questo codice è un'estensione della classe *ArrayAdapter\<ToDoItem\>*.

### <a name="layout"></a>Procedura: Definire il layout

Il layout è definito da diversi frammenti di codice XML. Dato un layout esistente, si presupponga che il codice seguente rappresenti l'oggetto **ListView** che si desidera popolare con i dati del server.

        <ListView
            android:id="@+id/listViewToDo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            tools:listitem="@layout/row_list_to_do" >
        </ListView>

Nel codice precedente l'attributo *listitem* consente di specificare l'ID del layout per una singola riga dell'elenco. Di seguito è riportato il codice che consente di specificare una casella di controllo e il testo ad essa associato. Per tale codice viene creata una sola istanza per ogni elemento dell'elenco. In un layout più complesso vengono specificati altri campi nella visualizzazione. Il codice è incluso nel file *row\_list\_to\_do.xml*.

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="horizontal">           
            <CheckBox
                android:id="@+id/checkToDoItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/checkbox_text" />
        </LinearLayout>
        

### <a name="adapter"></a>Procedura: Definire l'adattatore

Poiché l'origine dati della visualizzazione è una matrice di oggetti *ToDoItem*, verrà creata una sottoclasse per l'adattatore da una classe *ArrayAdapter\<ToDoItem\>*. Tale sottoclasse consentirà di ottenere una visualizzazione per ogni oggetto *ToDoItem* che utilizza il layout *row\_list\_to\_do*.

Nel codice viene definita la classe seguente che costituisce un'estensione della classe *ArrayAdapter\<E\>*:

        public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

È necessario eseguire l'override del metodo *getView* dell'adattatore. Nel codice di esempio è illustrato uno dei modi per eseguire questa operazione. I dettagli dipenderanno dall'applicazione.

    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        return row;
    }

Creare un'istanza della classe nell'attività nel modo seguente:

        ToDoItemAdapter mAdapter;
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Si noti che il secondo parametro del costruttore ToDoItemAdapter è un riferimento al layout. La chiamata al costruttore è seguita dal codice seguente in cui viene prima ottenuto un riferimento a **ListView** e quindi viene chiamato *setAdapter* per definire la configurazione per l'uso dell'adattatore appena creato:

        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

### <a name="use-adapter"></a>Procedura: Utilizzare l'adattatore

È ora possibile utilizzare l'associazione dati. Nel codice seguente viene illustrato come recuperare gli elementi nella tabella di Servizi mobili, cancellare l'adattatore e quindi chiamare il metodo *add* dell'adattatore per inserirvi gli elementi restituiti.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
            public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
                if (exception == null) {
                    mAdapter.clear();
                    for (ToDoItem item : result) {
                        mAdapter.add(item);
                    }
                } 
            }
        });

Se si desidera visualizzare i risultati di tale operazione, è inoltre necessario chiamare l'adattatore ogni volta che si modifica la tabella *ToDoItem*. Dal momento che le modifiche vengono fatte record per record, le operazioni riguardano una singola riga anziché una raccolta. Quando si inserisce un elemento, si chiama il metodo *add* sull'adattatore, mentre quando lo si elimina, si chiama il metodo *remove*.

## <a name="authentication"></a><span class="short-header">Autenticare</span>Procedura: Autenticare gli utenti

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti delle app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili][].

Sono supportati due flussi di autenticazione: un *flusso server* e un *flusso client*. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

Per abilitare l'autenticazione nell'app, è necessario eseguire tre passaggi:

1.  Registrazione dell'app per l'autenticazione con un provider e configurazione di Servizi mobili
2.  Limitazione delle autorizzazioni per la tabella ai soli utenti autenticati
3.  Aggiunta del codice di autenticazione all'app

In Servizi mobili sono supportati i seguenti provider di identità esistenti che è possibile utilizzare per l'autenticazione degli utenti:

-   Account Microsoft
-   Facebook
-   Twitter
-   Google
-   Azure Active Directory

È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. Per modificare le richieste, è inoltre possibile utilizzare l'ID di un utente autenticato.

Per le prime due attività viene utilizzato il [portale di gestione di Azure][]. Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili][].

### <a name="caching"></a>Procedura: Aggiunta del codice di autenticazione all'app

1.  Aggiungere le istruzioni import seguenti al file delle attività dell'app.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Nel metodo **onCreate** della classe activity aggiungere la riga di codice seguente dopo il codice che crea l'oggetto `MobileServiceClient`. Si presuppone che il riferimento all'oggetto `MobileServiceClient` sia *mClient*.

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {  
                            if (exception == null) {
                                /* User now logged in, you can get their identity via user.getUserId() */ 
                            } else {
                                /* Login error */
                            }
                        }
                    });

    L'utente viene autenticato nel codice tramite un account di accesso di Google. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    <div class="dev-callout"><b>Nota</b>
<p>Se si utilizza un provider di identit&agrave; diverso da Google, sostituire il valore passato al metodo di <strong>accesso</strong> riportato in precedenza con uno dei seguenti: <i>MicrosoftAccount</i>, <i>Facebook</i>, <i>Twitter</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Quando si esegue l'app, effettuare l'accesso con il provider di identità scelto.

### <a name="caching"></a>Procedura: Memorizzare nella cache i token di autenticazione

In questa sezione viene illustrato come memorizzare nella cache un token di autenticazione. Effettuare questa operazione per evitare che gli utenti debbano eseguire di nuovo l'autenticazione se l'app diventa inattiva mentre il token è ancora valido.

Per memorizzare nella cache i token di autenticazione, è necessario archiviare l'ID utente e il token di autenticazione in locale nel dispositivo. Al successivo avvio dell'app, la cache verrà verificata e, se sono presenti tali valori, sarà possibile ignorare la procedura di accesso e riattivare il client con questi dati. Questi dati sono tuttavia sensibili e devono essere crittografati per garantire la sicurezza in caso di furto del telefono.

Il frammento di codice seguente mostra come si ottiene un token per un accesso con account Microsoft. Il token viene memorizzato nella cache e ricaricato se viene rilevata la cache.

    private void authenticate() {
        if (LoadCache())
        {
            createTable();
        }
        else
        {
            // Login using the provider.
            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                createTable();
                                cacheUser(mClient.getCurrentUser());
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }
    }   


    private boolean LoadCache()
    {
        SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
        String tmp1 = prefs.getString("tmp1", "undefined"); 
        if (tmp1 == "undefined")
            return false;
        String tmp2 = prefs.getString("tmp2", "undefined"); 
        if (tmp2 == "undefined")
            return false;
        MobileServiceUser user = new MobileServiceUser(tmp1);
        user.setAuthenticationToken(tmp2);
        mClient.setCurrentUser(user);       
        return true;
    }


    private void cacheUser(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString("tmp1", user.getUserId());
        editor.putString("tmp2", user.getAuthenticationToken());
        editor.commit();
    }

Se il token scade, quando si prova a utilizzarlo per la connessione, verrà visualizzata una risposta di tipo *401 - Non autorizzato*. L'utente deve quindi effettuare di nuovo l'accesso per ottenere nuovi token. Per evitare di scrivere codice per gestire questa situazione in ogni punto dell'app che chiama Servizi mobili, è possibile utilizzare i filtri, che consentono di intercettare le chiamate inviate a e le risposte ricevute da Servizi mobili. Il codice di filtro verificherà quindi la risposta per un errore di tipo 401, attiverà, se necessario, il processo di accesso e quindi riprenderà la richiesta che ha generato l'errore.

## <a name="errors"></a><span class="short-header">Gestire gli errori</span>Procedura: Gestire gli errori

Per un esempio di convalida e gestione degli errori, vedere [qui][], in cui vengono implementati la convalida tramite script del server che restituiscono eccezioni in caso di errore server e il codice del client per la gestione delle eccezioni.

Un altro approccio consiste nel fornire un gestire di eventi *globale*. Il codice esaminato in cui viene effettuato l'accesso alla tabella del servizio mobile implica l'utilizzo di tre diversi oggetti callback:

-   **TableQueryCallback** / **TableQueryJsonCallback**
-   **TableOperationCallback** / **TableJsonOperationCallback**
-   **TableDeleteCallback**

Ognuno di essi dispone di un metodo **OnCompleted** in cui il secondo parametro è un oggetto **java.lang.Exception**. È possibile creare una sottoclasse di questi oggetti callback e implementare il metodo **onCompleted** personalizzato che verifica se il parametro exception è Null. In tal caso, non si verificano errori ed è sufficiente chiamare **super.OnCompleted()**.

Se l'oggetto **Exception** non è Null, vengono eseguite alcune operazioni generiche di gestione degli errori in cui vengono visualizzate informazioni più dettagliate sull'errore. Nel frammento di codice seguente viene illustrato uno dei modi per visualizzare ulteriori dettagli.

        String msg = exception.getCause().getMessage();

A questo punto gli sviluppatori possono utilizzare callback con sottoclassi senza preoccuparsi di verificare l'eccezione, poiché questa viene gestita in un'unica posizione (n. 2) per tutte le istanze del callback.

## <a name="customizing"></a><span class="short-header">Personalizzare il client</span>Procedura: Personalizzare il client

### <a name="headers"></a>Procedura: Personalizzare le intestazioni di richieste

È possibile allegare un'intestazione personalizzata a tutte le richieste in uscita. A tale scopo, configurare un elemento ServiceFilter simile a questo:

        client = client.withFilter(new ServiceFilter() {
        
            @Override
            public void handleRequest(ServiceFilterRequest request,
                    NextServiceFilterCallback nextServiceFilterCallback,
                    ServiceFilterResponseCallback responseCallback) {
                request.addHeader("My-Header", "Value");      
                nextServiceFilterCallback.onNext(request, responseCallback);
            }
        });

### <a name="serialization"></a>Procedura: Personalizzare la serializzazione

In Servizi mobili si presuppone per impostazione predefinita che i nomi di tabella, i nomi di colonna e i tipi di dati nel server corrispondano esattamente a quelli nel client. Esistono però diversi motivi per i quali i nomi nel client e nel server potrebbero non corrispondere. È ad esempio possibile che si disponga di un client esistente che si desidera modificare in modo che utilizzi Servizi mobili di Azure anziché il prodotto di un concorrente.

È possibile effettuare i seguenti tipi di personalizzazioni:

-   
    I nomi di colonna usati nella tabella del servizio mobile non corrispondono ai nomi usati nel client
-   Utilizzare una tabella del servizio mobile il cui nome è diverso rispetto alla classe cui è mappata nel client
-   Attivare l'utilizzo automatico dell'iniziale maiuscola per le proprietà
-   Aggiungere proprietà complesse a un oggetto

### <a name="columns"></a>Procedura: Mappare nomi di client e di server diversi

Si supponga che nel codice del client Java vengano usati nomi Java standard per le proprietà dell'oggetto *ToDoItem*, come quelli riportati di seguito.

-   mId
-   mText
-   mComplete
-   mDuration

È necessario serializzare i nomi di client in nomi JSON che corrispondono ai nomi di colonna della tabella *ToDoItem* nel server. Questa operazione viene effettuata nel codice seguente, in cui viene utilizzata la libreria [gson][].

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Procedura: Eseguire il mapping di nomi di tabella diversi tra client e servizi mobili

Il mapping del nome della tabella del client a un nome di tabella di Servizi mobili è un'operazione semplice, in cui viene utilizzato uno solo degli override della funzione
[getTable()][secondo overload], come illustrato nel codice seguente.

        mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Procedura: Automatizzare i mapping dei nomi di colonna

Il mapping dei nomi di colonna per una tabella con poche colonne non è un'operazione complessa, come è stato illustrato nella sezione precedente. Si supponga tuttavia che la tabella contenga numerose colonne, ad esempio 20 o 30. In questo caso, è possibile chiamare l'API [gson][] e specificare una strategia di conversione che verrà applicata a tutte le colonne, senza che sia necessario annotare il nome di ogni singola colonna.

A tale scopo viene utilizzata la libreria [gson][] che la libreria client Android utilizza in modo invisibile per serializzare oggetti Java in dati JSON, inviati a Servizi mobili di Azure.

Nel codice seguente viene utilizzato il metodo *setFieldNamingStrategy()*, nel quale viene definito un metodo *FieldNamingStrategy()*. In tale metodo viene indicato di eliminare il carattere iniziale (una "m"), e quindi di applicare il minuscolo al carattere successivo per ogni nome di campo. Nel codice viene inoltre abilitata la pretty print dell'output di JSON.

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            })
            .setPrettyPrinting());

Tale codice deve essere eseguito prima di qualsiasi altra chiamata a metodi sull'oggetto client di Servizi mobili.

### <a name="complex"></a>Procedura: Archiviare una proprietà di oggetto o di matrice in una tabella

Finora in tutti gli esempi di serializzazione offerti hanno riguardato tipi primitivi, ad esempio interi e stringhe, che vengono serializzati facilmente in JSON e nella tabella di Servizi mobili. Si supponga di aggiungere al tipo di client un oggetto complesso che non viene serializzato automaticamente in JSON e nella tabella, ad esempio una matrice di stringhe all'oggetto client. In questo caso è necessario specificare la modalità di esecuzione della serializzazione e di archiviazione della matrice nella tabella di Servizi mobili.

Per un esempio relativo a questa operazione, vedere il post del blog relativo alla [personalizzazione della serializzazione tramite la libreria][][gson][] nel client Android di Servizi mobili</a>.

È possibile utilizzare questo metodo generale ogni volta in cui si dispone di un oggetto complesso che non è automaticamente serializzabile in JSON e nella tabella di Servizi mobili.

## <a name="next-steps"></a>Passaggi successivi

I riferimenti Javadocs per l'API del client Android sono disponibili [][]<http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html></a>

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [.NET Framework]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework"
  [HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript"
  [iOS]: /it-it/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS"
  [Android]: /it-it/develop/mobile/how-to-guides/work-with-android-client-library/ "Android"
  [Xamarin]: /it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin"
  [guida introduttiva per Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-android/
  [Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?linkid=280126
  [Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409
  [Informazioni su Servizi mobili]: #what-is
  [Concetti]: #concepts
  [Installazione e prerequisiti]: #setup
  [Procedura: Creare il client di Servizi mobili]: #create-client
  [Procedura: Creare un riferimento alla tabella]: #instantiating
  [Struttura dell'API]: #api
  [Procedura: Eseguire query sui dati da un servizio mobile]: #querying
  [Filtrare i dati restituiti]: #filtering
  [Ordinare i dati restituiti]: #sorting
  [Restituire i dati in pagine]: #paging
  [Selezionare colonne specifiche]: #selecting
  [Procedura: Concatenare metodi di query]: #chaining
  [Procedura: Inserire dati in un servizio mobile]: #inserting
  [Procedura: Aggiornare dati in un servizio mobile]: #updating
  [Procedura: Eliminare dati in un servizio mobile]: #deleting
  [Procedura: Cercare un elemento specifico]: #lookup
  [Procedura: Utilizzare dati non tipizzati]: #untyped
  [Procedura: Associare dati all'interfaccia utente]: #binding
  [Procedura: Definire il layout]: #layout
  [Procedura: Definire l'adattatore]: #adapter
  [Procedura: Utilizzare l'adattatore]: #use-adapter
  [Procedura: Autenticare gli utenti]: #authentication
  [Memorizzare nella cache i token di autenticazione]: #caching
  [Procedura: Gestire gli errori]: #errors
  [Procedura: Personalizzare il client]: #customizing
  [Personalizzare le intestazioni di richieste]: #headers
  [Personalizzare la serializzazione]: #serialization
  [Passaggi successivi]: #next-steps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Creare una tabella]: http://go.microsoft.com/fwlink/p/?LinkId=298592
  [Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [MobileServiceClient]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
  [gson]: %20http://go.microsoft.com/fwlink/p/?LinkId=290801
  [MobileServiceTable]: http://go.microsoft.com/fwlink/p/?LinkId=296835
  [primo overload]: http://go.microsoft.com/fwlink/p/?LinkId=296839
  [secondo overload]: http://go.microsoft.com/fwlink/p/?LinkId=296840
  [**TableQueryCallback\<E\>**]: http://go.microsoft.com/fwlink/p/?LinkId=296849
  [**where**]: http://go.microsoft.com/fwlink/p/?LinkId=296867
  [**field**]: http://go.microsoft.com/fwlink/p/?LinkId=296869
  [**eq**]: http://go.microsoft.com/fwlink/p/?LinkId=298461
  [**ne**]: http://go.microsoft.com/fwlink/p/?LinkId=298462
  [**gt**]: http://go.microsoft.com/fwlink/p/?LinkId=298463
  [**ge**]: http://go.microsoft.com/fwlink/p/?LinkId=298464
  [**lt**]: http://go.microsoft.com/fwlink/p/?LinkId=298465
  [**le**]: http://go.microsoft.com/fwlink/p/?LinkId=298466
  [**year**]: http://go.microsoft.com/fwlink/p/?LinkId=298467
  [**month**]: http://go.microsoft.com/fwlink/p/?LinkId=298468
  [**day**]: http://go.microsoft.com/fwlink/p/?LinkId=298469
  [**hour**]: http://go.microsoft.com/fwlink/p/?LinkId=298470
  [**minute**]: http://go.microsoft.com/fwlink/p/?LinkId=298471
  [**second**]: http://go.microsoft.com/fwlink/p/?LinkId=298472
  [**startsWith**]: http://go.microsoft.com/fwlink/p/?LinkId=298473
  [**endsWith**]: http://go.microsoft.com/fwlink/p/?LinkId=298474
  [**concat**]: http://go.microsoft.com/fwlink/p/?LinkId=298475
  [**subString**]: http://go.microsoft.com/fwlink/p/?LinkId=298477
  [**indexOf**]: http://go.microsoft.com/fwlink/p/?LinkId=298488
  [**replace**]: http://go.microsoft.com/fwlink/p/?LinkId=298491
  [**toLower**]: http://go.microsoft.com/fwlink/p/?LinkId=298492
  [**toUpper**]: http://go.microsoft.com/fwlink/p/?LinkId=298493
  [**trim**]: http://go.microsoft.com/fwlink/p/?LinkId=298495
  [**length**]: http://go.microsoft.com/fwlink/p/?LinkId=298496
  [**add**]: http://go.microsoft.com/fwlink/p/?LinkId=298497
  [**sub**]: http://go.microsoft.com/fwlink/p/?LinkId=298499
  [**mul**]: http://go.microsoft.com/fwlink/p/?LinkId=298500
  [**div**]: http://go.microsoft.com/fwlink/p/?LinkId=298502
  [**mod**]: http://go.microsoft.com/fwlink/p/?LinkId=298503
  [**floor**]: http://go.microsoft.com/fwlink/p/?LinkId=298505
  [**ceiling**]: http://go.microsoft.com/fwlink/p/?LinkId=298506
  [**round**]: http://go.microsoft.com/fwlink/p/?LinkId=298507
  [**and**]: http://go.microsoft.com/fwlink/p/?LinkId=298512
  [**or**]: http://go.microsoft.com/fwlink/p/?LinkId=298514
  [**not**]: http://go.microsoft.com/fwlink/p/?LinkId=298515
  [funzionalità complesse disponibili nel modello di query client Android di Servizi mobili]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
  [**orderBy**]: http://go.microsoft.com/fwlink/p/?LinkId=298519
  [**QueryOrder**]: http://go.microsoft.com/fwlink/p/?LinkId=298521
  [**select**]: http://go.microsoft.com/fwlink/p/?LinkId=290689
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=296296
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=296313
  [**top**]: http://go.microsoft.com/fwlink/p/?LinkId=298731
  [**MobileServiceQuery\<T\>**]: http://go.microsoft.com/fwlink/p/?LinkId=298551
  [**execute**]: http://go.microsoft.com/fwlink/p/?LinkId=298554
  [**insert**]: http://go.microsoft.com/fwlink/p/?LinkId=296862
  [**TableOperationCallback\<ToDoItem\>**]: http://go.microsoft.com/fwlink/p/?LinkId=296865
  [codici di controllo ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [Comandi per la gestione delle tabelle di Servizi mobili]: http://www.windowsazure.com/it-it/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [**TableDeleteCallback**]: http://go.microsoft.com/fwlink/p/?LinkId=296858
  [MobileServicesJsonTable]: http://go.microsoft.com/fwlink/p/?LinkId=298733
  [getTable()]: http://go.microsoft.com/fwlink/p/?LinkId=298734
  [**JsonObject**]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
  [3]: http://go.microsoft.com/fwlink/p/?LinkId=298535
  [**TableJsonOperationCallback**]: http://go.microsoft.com/fwlink/p/?LinkId=298532
  [**TableJsonQueryCallback**]: http://go.microsoft.com/fwlink/p/?LinkId=298543
  [Introduzione all'autenticazione in Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=296316
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [qui]: https://www.windowsazure.com/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [personalizzazione della serializzazione tramite la libreria]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
  []: http://go.microsoft.com/fwlink/p/?LinkId=298735 "qui"
