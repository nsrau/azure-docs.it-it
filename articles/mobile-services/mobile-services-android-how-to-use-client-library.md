<properties
	pageTitle="Uso delle libreria client Android per Servizi mobili"
	description="Informazioni su come usare un client Android per Servizi mobili di Azure."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/11/2015" 
	ms.author="ricksal"/>


# Come usare la libreria client Android per Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Questa guida illustra come eseguire scenari comuni usando un client Android per Servizi mobili di Azure. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti, la gestione di errori e la personalizzazione del client.

Se non si ha familiarità con Servizi mobili, si consiglia di completare prima l’esercitazione di guida introduttiva [Introduzione a Servizi mobili]. Il completamento corretto dell'esercitazione garantisce che Android Studio sia installato e consente di configurare l'account, creare il primo servizio mobile e installare l'SDK di Servizi mobili, che supporta Android 2.2 o versione successiva, anche se è consigliabile usare Android 4.2 o versione successiva.

È possibile trovare il riferimento API Javadocs per la libreria client Android [qui](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Installazione e prerequisiti

Si presuppone che siano stati creati un servizio mobile e una tabella. Per ulteriori informazioni, vedere [Creare una tabella](http://go.microsoft.com/fwlink/p/?LinkId=298592). Nel codice utilizzato in questo argomento si presuppone che la tabella sia denominata *ToDoItem* e che contenga le colonne seguenti:

- id
- text
- complete

L'oggetto tipizzato corrispondente sul lato client è il seguente:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Quando è abilitato lo schema dinamico, in Servizi mobili di Azure vengono generate automaticamente nuove colonne basate sull'oggetto nella richiesta di inserimento o di aggiornamento. Per ulteriori informazioni, vedere [Schema dinamico](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Procedura: Creare il client di Servizi mobili
Il codice seguente consente di creare l'oggetto [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) utilizzato per accedere al servizio mobile. Il codice viene inserito nel metodo `onCreate` della classe di attività specificata in *AndroidManifest.xml* come azione **MAIN** e categoria **LAUNCHER**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

Nel codice riportato sopra sostituire `MobileServiceUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, nell'ordine specificato. Entrambi gli elementi sono disponibili nel portale di gestione di Azure selezionando il servizio mobile e quindi facendo clic su *Dashboard*.

##<a name="instantiating"></a>Procedura: Creare un riferimento alla tabella

Il modo più semplice per eseguire query sui dati o modificarli nel servizio mobile consiste nell'utilizzare il *modello di programmazione tipizzato*, dal momento che Java è un linguaggio fortemente tipizzato. Più avanti verrà illustrato il modello *non tipizzato*. Questo modello garantisce la serializzazione e la deserializzazione automatica con JSON tramite la libreria [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) durante l'invio di dati tra il client e il servizio mobile: di conseguenza la gestione è affidata al framework e lo sviluppatore non deve eseguire altre operazioni.

Per eseguire una query sui dati o modificarli, è innanzitutto necessario creare un oggetto [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) chiamando il metodo **getTable** su [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Verranno ora esaminati i due overload di questo metodo:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Nel codice seguente *mClient* è un riferimento al client del servizio mobile.

Il [primo overload](http://go.microsoft.com/fwlink/p/?LinkId=296839) viene utilizzato nel caso in cui il nome della classe e il nome della tabella sono identici:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


Il [secondo overload](http://go.microsoft.com/fwlink/p/?LinkId=296840) viene invece utilizzato quando il nome della tabella è diverso da quello del tipo.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>Struttura dell'API

A partire dalla versione 2.0 della libreria client, le operazioni su tabella dei servizi mobili usano gli oggetti [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) e [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) in tutte le operazioni asincrone, ad esempio i metodi che interessano query e operazioni come inserimenti, aggiornamenti ed eliminazioni. In questo modo viene semplificata l'esecuzione di più operazioni in un thread in background, senza dover gestire più callback annidati.


##<a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile

In questa sezione viene descritto come eseguire query nel servizio mobile. Nelle sottosezioni vengono descritti aspetti diversi come ordinamento, filtri e paging. Nella parte finale viene spiegato come concatenare queste operazioni.

### <a name="showAll"></a>Procedura: Restituire tutti gli elementi di una tabella

Il codice seguente consente di restituire tutti gli elementi della tabella *ToDoItem*. e li visualizza nell'interfaccia utente aggiungendoli a un adattatore. Questo codice è simile a quello disponibile nell’esercitazione di guida introduttiva [Introduzione a servizi mobili].

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


In query come questa viene usato l'oggetto [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

La variabile *result* restituisce il set di risultati della query e il codice che segue l'istruzione `mToDoTable.execute().get()` mostra come visualizzare le singole righe.


### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente consente di restituire tutti gli elementi della tabella *ToDoItem* il cui campo *complete* è uguale a *false*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Per iniziare un filtro, è necessario inserire una chiamata al metodo [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) sul riferimento alla tabella. Tale chiamata è seguita da un metodo [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869), a sua volta seguito da una chiamata al metodo che specifica il predicato logico. I possibili metodi del predicato includono tra gli altri [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466).

Tali informazioni sono sufficienti per confrontare campi numerici e di stringhe con valori specifici. Tuttavia è anche possibile effettuare altre operazioni,

ad esempio filtrare in base alle date. È possibile confrontare l'intero campo data, nonché parti della data, come [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) e [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). Il codice parziale seguente consente di aggiungere un filtro per elementi il valore per la *scadenza* è uguale a 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

È possibile eseguire una vasta gamma di filtri complessi su campi di tipo stringa con metodi quali [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) e [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Il codice parziale seguente consente di filtrare le righe della tabella in cui la colonna *text* inizia con "PRI0".

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Anche con i campi di tipo numerico è possibile utilizzare una vasta gamma di filtri più complessi con metodi quali [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) e [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Il codice parziale seguente consente di filtrare le righe della tabella in cui il valore di *duration* è un numero pari.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


È possibile combinare i predicati con metodi quali [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) e [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Nel codice parziale seguente vengono combinati due degli esempi precedenti.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

È inoltre possibile raggruppare e annidare operatori logici, come illustrato in questo codice parziale:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Per una trattazione più dettagliata e per esempi di filtri, vedere il post relativo alle [funzionalità complesse disponibili nel modello di query client Android di Servizi mobili](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice di esempio seguente consente di restituire tutti gli elementi di una tabella di *ToDoItems* elencati in ordine crescente in base al campo *text*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Il primo parametro del metodo [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) è una stringa uguale al nome del campo in base al quale eseguire l'ordinamento.

Nel secondo parametro viene utilizzata l'enumerazione [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) per specificare l'ordinamento crescente o decrescente.

Si noti che se per il filtro si utilizza il metodo ***where***, è necessario chiamare tale metodo prima del metodo ***orderBy***.

### <a name="paging"></a>Procedura: Restituire i dati in pagine

Nel primo esempio viene illustrato come selezionare i primi cinque elementi di una tabella. La query restituisce gli elementi di una tabella di *ToDoItems*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


Verrà ora definita una query che ignora i primi cinque elementi e restituisce i cinque successivi.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

Nel codice seguente viene illustrato come restituire tutti gli elementi di una tabella di *ToDoItems*, visualizzando però solo i campi *complete* e *text*. *mToDoTable* è il riferimento alla tabella del servizio mobile creata in precedenza.

		mToDoTable.select("complete", "text").execute().get();


In questo caso, i parametri della funzione select sono i nomi in formato stringa delle colonne della tabella che si desidera restituire.

Il metodo [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) deve essere inserito dopo metodi quali [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) e [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), se presenti. Può essere seguito da metodi quali [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Procedura: Concatenare metodi di query

È possibile concatenare i metodi utilizzati per eseguire query su tabelle di servizi mobili. In questo modo è possibile eseguire operazioni quali selezionare colonne specifiche di righe filtrate ordinate e sottoposte a paging. È possibile creare filtri logici abbastanza complessi.

Il funzionamento è garantito dal fatto che i metodi di query usati restituiscono oggetti [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), su cui è possibile chiamare altri metodi. Per terminare la serie di metodi ed eseguire effettivamente la query, è necessario chiamare il metodo [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Nel codice di esempio seguente *mToDoTable* è un riferimento alla tabella *ToDoItem* dei servizi mobili.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

Per un corretto concatenamento dei metodi è essenziale inserire per primi il metodo *where* e i predicati. In seguito è possibile chiamare metodi successivi nell'ordine più appropriato alle esigenze dell'applicazione.


##<a name="inserting"></a>Procedura: Inserire dati in un servizio mobile

Il seguente codice illustra come inserire una nuova riga in una tabella.

In primo luogo viene creata un'istanza della classe *ToDoItem* e vengono impostate le relative proprietà.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 In seguito si esegue il seguente codice:

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Questo codice consente di inserire il nuovo elemento e di aggiungerlo all'adattatore in modo che venga visualizzato nell'interfaccia utente.

Servizi mobili supporta valori di stringa univoci personalizzati per l'ID della tabella. In tal modo alle applicazioni è consentito usare valori personalizzati come indirizzi e-mail o nomi utente per la colonna ID di una tabella di Servizi mobili. Se ad esempio si desidera identificare ogni record con un indirizzo di posta elettronica, è possibile utilizzare l'oggetto JSON seguente.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

Se al momento dell'inserimento di nuovi record in una tabella non viene specificato un valore ID di stringa, in Servizi mobili verrà generato un valore univoco per l'ID.

Il supporto di ID di stringa comporta i seguenti vantaggi per gli sviluppatori

+ È possibile generare ID senza generare un round trip del database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Per impostare i valori di ID, è inoltre possibile utilizzare script del server. L'esempio di script seguente genera un GUID personalizzato e lo assegna all'ID di un nuovo record. Tale ID sarà simile al valore ID generato da Servizi mobili se non si è passato un valore per l'ID di un record.

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

+ Caratteri di controllo: [0x0000-0x001F] e [0x007F-0x009F]. Per ulteriori informazioni, vedere la pagina relativa ai [codici di controllo ASCII C0 e C1].
+  Caratteri stampabili: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Gli ID "." e ".."

In alternativa è possibile utilizzare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario creare la tabella con il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene utilizzato con l'interfaccia della riga di comando (CLI) per Azure. Per ulteriori informazioni sull'utilizzo dell'interfaccia della riga di comando, vedere [CLI per la gestione delle tabelle di Servizi mobili].


##<a name="updating"></a>Procedura: aggiornare dati in un servizio mobile

Il seguente codice illustra come aggiornare i dati in una tabella. In questo esempio *item* è un riferimento a una riga nella tabella *ToDoItem* alla quale sono state apportate alcune modifiche. Il seguente metodo aggiorna la tabella e l'adattatore dell'interfaccia utente.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Procedura: Eliminare dati in un servizio mobile

Il seguente codice illustra come eliminare i dati da una tabella. Elimina un elemento esistente dalla tabella ToDoItem in cui è stata selezionata la casella di controllo **Completato** nell'interfaccia utente.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


Nel codice seguente viene illustrato un altro modo per eseguire questa operazione. In questo caso per eliminare un elemento esistente dalla tabella ToDoItem viene specificato il valore del campo id della riga da eliminare, presupponendo che sia uguale a "2FA404AB-E458-44CD-BC1B-3BC847EF0902". In un'applicazione reale sarebbe necessario rilevare l'ID e passarlo come variabile. In questo caso, per semplificare il test, è possibile accedere al portale di Servizi mobili di Azure per il servizio, fare clic su **Dati** e copiare un ID con il quale si desidera eseguire il test.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Procedura: Cercare un elemento specifico
In alcuni casi si desidera cercare un elemento specifico in base al relativo *id*, anziché eseguire una query che invece consente in genere di ottenere una raccolta di elementi che soddisfano alcuni criteri. Il codice seguente illustra come effettuare questa operazione per un valore *id* di `0380BAFB-BCFF-443C-B7D5-30199F730335`. In un'applicazione reale sarebbe necessario rilevare l'ID e passarlo come variabile. In questo caso, per semplificare il test, è possibile accedere al portale di Servizi mobili di Azure per il servizio, fare clic su **Dati** e copiare un ID con il quale si desidera eseguire il test.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Procedura: Usare dati non tipizzati

Il modello di programmazione non tipizzato offre un controllo accurato sulla serializzazione JSO, pertanto è consigliabile usarlo in alcuni scenari, ad esempio se la tabella del servizio mobile contiene un numero elevato di colonne e si desidera fare riferimento solo ad alcune di esse. Per usare il modello tipizzato è necessario definire tutte le colonne della tabella del servizio mobile nella classe dati. Tuttavia con il modello non tipizzato si possono definire solo le colonne che è necessario utilizzare.

La maggior parte delle chiamate API per l'accesso ai dati è simile alle chiamate della programmazione tipizzata. La principale differenza consiste nel fatto che nel modello non tipizzato i metodi vengono chiamati sull'oggetto **MobileServiceJsonTable**, anziché su **MobileServiceTable**.


### <a name="json_instance"></a>Procedura: Creare un'istanza di una tabella non tipizzata

Analogamente al modello tipizzato, si inizia recuperando un riferimento alla tabella, ma in questo caso si tratta di un oggetto [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Per ottenere il riferimento, viene chiamato il metodo [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) su un'istanza del client di Servizi mobili.

È necessario innanzitutto definire la variabile:

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



Dopo avere creato un'istanza del client di Servizi mobili nel metodo **onCreate** (in questo caso la variabile *mClient*), si crea un'istanza di un oggetto **MobileServiceJsonTable** con il codice seguente.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

Dopo avere creato un'istanza dell'oggetto **MobileServiceJsonTable**, sarà possibile chiamarvi quasi tutti i metodi utilizzabili con il modello di programmazione tipizzato. In alcuni casi, tuttavia, i metodi accettano un parametro non tipizzato, come è possibile notare negli esempi che seguono.

### <a name="json_insert"></a>Procedura: Eseguire insert in una tabella non tipizzata

Nel codice seguente viene illustrato come eseguire un'operazione di insert. Il primo passaggio consiste nel creare un oggetto [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), incluso nella libreria <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

Il passaggio successivo consiste nell'inserire l'oggetto. La funzione di callback passata al metodo [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) è un'istanza della classe [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Si noti che il parametro del metodo *insert* è un oggetto JsonObject.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Se è necessario ottenere l'ID dell'oggetto inserito, usare questa chiamata al metodo:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Procedura: Eliminare un'istanza da una tabella non tipizzata

Nel codice seguente viene illustrato come eliminare un'istanza, in questo caso la stessa istanza di un oggetto **JsonObject** creato nell'esempio *insert* precedente. Si noti che il codice è analogo a quello del caso tipizzato, ma il metodo ha una firma diversa poiché fa riferimento a un oggetto **JsonObject**.


         mToDoTable.delete(item);


È inoltre possibile eliminare un'istanza direttamente utilizzando il relativo ID:

		 mToDoTable.delete(ID);



### <a name="json_get"></a>Procedura: Restituire tutte le righe di una tabella non tipizzata

Il seguente codice illustra come recuperare un'intera tabella. Poiché si sta usando una tabella JSONO, è possibile recuperare in modo selettivo solo alcune colonne della tabella.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

È possibile applicare filtri, ordinare ed eseguire il paging concatenando i metodi che hanno gli stessi dei metodi usati nel modello di programmazione tipizzato.


##<a name="binding"></a>Procedura: Associare dati all'interfaccia utente

Il data binding riguarda tre componenti:

- Origine dati
- Layout della schermata
- Adattatore che collega i due componenti precedenti

Nel codice di esempio i dati vengono restituiti in una matrice dalla tabella del servizio mobile *ToDoItem*. Si tratta di uno dei modelli più comuni per le applicazioni dati: le query su database restituiscono in genere una raccolta di righe che il client ottiene in un elenco o una matrice. In questo esempio la matrice è l'origine dati.

Nel codice viene specificato un layout di schermata che definisce la visualizzazione dei dati che appariranno sul dispositivo.

I due elementi vengono associati tra loro tramite un adattatore, che in questo codice è un'estensione della classe *ArrayAdapter&lt;ToDoItem&gt;*.

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

Poiché l'origine dati della visualizzazione è una matrice di oggetti *ToDoItem*, verrà creata una sottoclasse per l'adattatore da una classe *ArrayAdapter&lt;ToDoItem&gt;*. Tale sottoclasse consentirà di ottenere una visualizzazione per ogni oggetto *ToDoItem* che utilizza il layout *row\_list\_to\_do*.

Nel codice viene definita la classe seguente che costituisce un'estensione della classe *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


È necessario eseguire l'override del metodo *getView* dell'adattatore. Questo codice di esempio illustra come effettuare questa operazione: i dettagli variano in base all'applicazione.

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


### <a name="use-adapter"></a>Procedura: Usare l'adattatore

È ora possibile utilizzare l'associazione dati. Nel codice seguente viene illustrato come recuperare gli elementi nella tabella di Servizi mobili, cancellare l'adattatore e quindi chiamare il metodo *add* dell'adattatore per inserirvi gli elementi restituiti.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Se si desidera visualizzare i risultati di tale operazione, è inoltre necessario chiamare l'adattatore ogni volta che si modifica la tabella *ToDoItem*. Dal momento che le modifiche vengono fatte record per record, le operazioni riguardano una singola riga anziché una raccolta. Quando si inserisce un elemento, si chiama il metodo *add* sull'adattatore, mentre quando lo si elimina, si chiama il metodo *remove*.

##<a name="#custom-api"></a>Procedura: Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON. Per un esempio di come creare un'API personalizzata nel servizio mobile, vedere [Procedura: definire un endpoint API personalizzato](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>Procedura: Autenticare gli utenti

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti di app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione nel backend. Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Sono supportati due flussi di autenticazione, un flusso *server* e un flusso *client*. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK specifici del provider e del dispositivo.

Per abilitare l'autenticazione nell'app, è necessario eseguire tre passaggi:

- Registrazione dell'app per l'autenticazione con un provider e configurazione di Servizi mobili
- Limitazione delle autorizzazioni per la tabella ai soli utenti autenticati
- Aggiunta del codice di autenticazione all'app


In Servizi mobili sono supportati i seguenti provider di identità esistenti che è possibile utilizzare per l'autenticazione degli utenti:

- Account Microsoft
- Facebook
- Twitter
- Google
- Azure Active Directory

È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. Per modificare le richieste, è inoltre possibile utilizzare l'ID di un utente autenticato.

Per le prime due attività viene utilizzato il [portale di gestione di Azure](https://manage.windowsazure.com/). Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Procedura: Aggiungere codice di autenticazione all'app

1.  Aggiungere le istruzioni import seguenti al file delle attività dell'app.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Nel metodo **onCreate** della classe activity aggiungere la riga di codice seguente dopo il codice che crea l'oggetto `MobileServiceClient`. Si presuppone che il riferimento all'oggetto `MobileServiceClient` sia *mClient*.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    L'utente viene autenticato nel codice tramite un account di accesso di Google. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    > [AZURE.NOTE]Se si usa un provider di identità diverso da Google, sostituire il valore passato al metodo **login** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Facebook_, _Twitter_ o _WindowsAzureActiveDirectory_.


3. Quando si esegue l'app, effettuare l'accesso con il provider di identità scelto.


### <a name="caching"></a>Procedura: Memorizzare nella cache i token di autenticazione

In questa sezione viene illustrato come memorizzare nella cache un token di autenticazione. Effettuare questa operazione per evitare che gli utenti debbano eseguire di nuovo l'autenticazione se l'app diventa inattiva mentre il token è ancora valido.

Per memorizzare nella cache i token di autenticazione, è necessario archiviare l'ID utente e il token di autenticazione in locale nel dispositivo. Al successivo avvio dell'app, la cache verrà verificata e, se sono presenti tali valori, sarà possibile ignorare la procedura di accesso e riattivare il client con questi dati. Questi dati sono tuttavia sensibili e devono essere crittografati per garantire la sicurezza in caso di furto del telefono.

Il frammento di codice seguente mostra come ottenere un token per un accesso con account Microsoft. Il token viene memorizzato nella cache e ricaricato se viene rilevata la cache.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
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


Se il token scade, quando si prova a utilizzarlo per la connessione, verrà visualizzata una risposta di tipo *401 - Non autorizzato*. L'utente deve quindi effettuare di nuovo l'accesso per ottenere nuovi token. Per evitare di scrivere codice per gestire questa situazione in ogni punto dell'app che chiama Servizi mobili, è possibile usare i filtri, che consentono di intercettare le chiamate inviate a e le risposte ricevute da Servizi mobili. Il codice di filtro verificherà quindi la risposta per un errore di tipo 401, attiverà, se necessario, il processo di accesso e quindi riprenderà la richiesta che ha generato l'errore.


##<a name="customizing"></a>Procedura: Personalizzare il client

Esistono diversi modi per personalizzare il comportamento predefinito del client di Servizi mobili.

### <a name="headers"></a>Procedura: Personalizzare le intestazioni delle richieste

È possibile allegare un'intestazione personalizzata a tutte le richieste in uscita. A tale scopo, configurare un elemento **ServiceFilter** simile a questo:

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Procedura: Personalizzare la serializzazione

In Servizi mobili si presuppone per impostazione predefinita che i nomi di tabella, i nomi di colonna e i tipi di dati nel server corrispondano esattamente a quelli nel client. Esistono però diversi motivi per i quali i nomi nel client e nel server potrebbero non corrispondere. Ad esempio, è possibile che si voglia modificare un client esistente in modo che usi Servizi mobili anziché il prodotto di un concorrente.

È possibile che si desideri applicare i seguenti tipi di personalizzazioni:

- I nomi di colonna usati nella tabella del servizio mobile non corrispondono a quelli usati nel client
- Usare una tabella del servizio mobile il cui nome è diverso rispetto alla classe cui è mappata nel client
- Attivare l'utilizzo automatico dell'iniziale maiuscola per le proprietà
- Aggiungere proprietà complesse a un oggetto

### <a name="columns"></a>Procedura: Eseguire il mapping di nomi di client e di server diversi

Si supponga che nel codice del client Java vengano utilizzati nomi standard Java per le proprietà dell'oggetto *ToDoItem*, come riportato di seguito.

- mId
- mText
- mComplete
- mDuration


È necessario serializzare i nomi di client in nomi JSON che corrispondono ai nomi di colonna della tabella *ToDoItem* nel server. Questa operazione viene eseguita nel codice seguente, in cui viene usata la libreria <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Procedura: Eseguire il mapping di nomi di tabella diversi tra client e servizi mobili

Il mapping del nome della tabella del client a un nome di tabella dei servizi mobili è un'operazione semplice, in cui viene usato uno solo degli override della funzione <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, come illustrato nel codice seguente.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Procedura: Automatizzare i mapping dei nomi di colonna

Il mapping dei nomi di colonna per una tabella con poche colonne non è un'operazione complessa, come è stato illustrato nella sezione precedente. Si supponga però che la tabella contenga numerose colonne, ad esempio 20 o 30. In questo caso è possibile chiamare l'API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> e specificare una strategia di conversione che verrà applicata a tutte le colonne, senza che sia necessario annotare il nome di ogni singola colonna.

A tale scopo viene usata la libreria <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> che la libreria client Android usa in modo invisibile per serializzare oggetti Java in dati JSON, inviati a Servizi mobili di Azure.

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

Per un esempio relativo a questa operazione, vedere il post del blog relativo alla <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">personalizzazione della serializzazione tramite la libreria <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> nel client Android di Servizi mobili</a>.

È possibile usare questo metodo generale ogni volta in cui si dispone di un oggetto complesso che non è automaticamente serializzabile in JSON e nella tabella di Servizi mobili.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-android-get-started.md
[codici di controllo ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!---HONumber=Sept15_HO3-->