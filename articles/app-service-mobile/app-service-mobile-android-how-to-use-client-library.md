<properties
	pageTitle="Come usare la libreria client per le app per dispositivi mobili Android"
	description="Come usare Android SDK lato client per le app per dispositivi mobili di Azure"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>


# Come usare la libreria client Android per le app per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida illustra come usare Android SDK lato client per le app per dispositivi mobili per implementare scenari comuni, come l'esecuzione di query sui dati (inserimento, aggiornamento ed eliminazione), l'autenticazione degli utenti, la gestione degli errori e la personalizzazione del client. Fornisce anche un'analisi approfondita del codice client comune usato nella maggior parte della app per dispositivi mobili.

Questa guida descrive Android SDK lato client. Per altre informazioni sugli SDK lato server per le app per dispositivi mobili, vedere [Usare l'SDK del server back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) o [Come usare Node.js SDK back-end](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentazione di riferimento

È possibile trovare il riferimento API Javadocs per la libreria client Android [in GitHub](http://azure.github.io/azure-mobile-apps-android-client/).

## Installazione e prerequisiti

Mobile Services SDK per Android supporta Android versione 2.2 o successiva, tuttavia è consigliabile usare la versione 4.2 o successiva.

Completare l'[esercitazione introduttiva alle app per dispositivi mobili](app-service-mobile-android-get-started.md) per essere sicuri di avere installato Android Studio. Sarà anche più facile configurare l'account e creare il primo back-end delle app per dispositivi mobili. In questo caso, si potrà ignorare il resto di questa sezione.

Se si decide di non completare l'esercitazione introduttiva e si vuole connettere un'app Android a un back-end di app per dispositivi mobili, è necessario seguire questa procedura:

- [Creare un back-end dell'app per dispositivi mobili](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) da usare con l'app Android, a meno che ne sia già disponibile uno per la propria app
- In Android Studio [aggiornare i file di compilazione Gradle](#gradle-build) e
- [Abilitare l'autorizzazione per Internet.](#enable-internet)

Si dovrà quindi completare la procedura descritta nella sezione di approfondimento.

###<a name="gradle-build"></a>Aggiornare il file di compilazione Gradle

Modificare entrambi i file **build.gradle**:

1. Aggiungere il codice seguente al livello *Project* del file **build.gradle** all'interno del tag *buildscript*:

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. Aggiungere il codice seguente al livello *Module app* del file **build.gradle** all'interno del tag *dependencies*:

		compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

	La versione più recente è la 3.1.0. Le versioni supportate sono elencate [qui](http://go.microsoft.com/fwlink/p/?LinkID=717034).

###<a name="enable-internet"></a>Abilitare l'autorizzazione per Internet
Per accedere ad Azure, è necessario abilitare l'autorizzazione INTERNET per l'app. Se non è già abilitata, aggiungere la riga di codice seguente al file **AndroidManifest.xml**:

	<uses-permission android:name="android.permission.INTERNET" />

## Approfondimento delle nozioni di base

Questa sezione illustra parte del codice nell'app dell'esercitazione introduttiva. Se non si è completata questa esercitazione, sarà necessario aggiungere il codice all'app.

> [AZURE.NOTE] La stringa "MobileServices" ricorre più volte nel codice. In effetti, il codice fa riferimento a Mobile App SDK, ma è solo una voce riportata temporaneamente da versioni precedenti.


###<a name="data-object"></a>Definire le classi di dati client

Per accedere ai dati delle tabelle di SQL Azure, definire le classi di dati client che corrispondono alle tabelle nel back-end dell'app per dispositivi mobili. Negli esempi di questo argomento si presuppone l'uso di una tabella denominata *ToDoItem* con le colonne seguenti:

- id
- text
- complete

Il corrispondente oggetto tipizzato lato client è il seguente:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Il codice risiederà in un file denominato **ToDoItem.java**.

Se la propria tabella di SQL Azure include più colonne, aggiungere i campi corrispondenti a questa classe.

Se ad esempio include una colonna Priority di tipo Integer, è consigliabile aggiungere questo campo insieme ai relativi metodi getter e setter:

	private Integer priority;

	/**
	* Returns the item priority
	*/
	public Integer getPriority() {
	return mPriority;
	}
	
	/**
	* Sets the item priority
	*
	* @param priority
	*            priority to set
	*/
	public final void setPriority(Integer priority) {
	mPriority = priority;
	}

Per informazioni su come creare altre tabelle nel back-end delle app per dispositivi mobili, vedere [Procedura: Definire un controller tabelle](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (back-end .NET) o [Procedura: Definire le tabelle con uno schema dinamico](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (back-end Node.js). Per un back-end Node.js è anche possibile usare l'impostazione **Easy tables** nel [portale di Azure].

###<a name="create-client"></a>Procedura: Creare il contesto client

Il codice seguente crea l'oggetto **MobileServiceClient** usato per accedere al back-end dell'app per dispositivi mobili. Il codice viene inserito nel metodo `onCreate` della classe **Activity** specificata nel file *AndroidManifest.xml* come azione **MAIN** e categoria **LAUNCHER**. Nel codice dell'esercitazione introduttiva viene inserito nel file **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
			"MobileAppUrl", // Replace with the above Site URL
			this)

In questo codice sostituire `MobileAppUrl` con l'URL del back-end dell'app per dispositivi mobili, che si trova nel pannello per il back-end dell'app per dispositivi mobili del [portale di Azure](https://portal.azure.com/). Per consentire la compilazione di questa riga di codice, è necessario aggiungere anche l'istruzione **import** seguente:

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Procedura: Creare un riferimento alla tabella

Il modo più semplice per eseguire query sui dati o modificarli nel back-end consiste nell'usare il *modello di programmazione tipizzato*, dal momento che Java è un linguaggio fortemente tipizzato. Più avanti verrà illustrato il modello *non tipizzato*. Questo modello garantisce la serializzazione e la deserializzazione automatica di JSON tramite la libreria [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) durante l'invio di dati tra oggetti e tabelle client nel back-end di SQL Azure. La gestione è affidata al framework, quindi lo sviluppatore non deve eseguire altre operazioni.

Per accedere a una tabella, creare prima di tutto un oggetto [MobileServiceTable](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html) chiamando il metodo **getTable** su [MobileServiceClient](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Questo metodo presenta due overload:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Nel codice seguente *mClient* è un riferimento all'oggetto MobileServiceClient.

Il [primo overload](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-) viene usato quando il nome della classe e il nome della tabella sono identici ed è quello usato nella guida introduttiva:

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


Il [secondo overload](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-java.lang.Class-) viene usato quando il nome della tabella è diverso da quello della classe: il primo parametro è il nome della tabella.

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Procedura: Associare dati all'interfaccia utente

L'associazione dati riguarda tre componenti:

- Origine dati
- Layout della schermata
- Adattatore che collega i due componenti.

Nel codice di esempio i dati vengono restituiti dalla tabella di app per dispositivi mobili di SQL Azure *ToDoItem* in una matrice. Si tratta di un modello molto comune per le applicazioni dati: le query su database restituiscono spesso una raccolta di righe che il client riceve in un elenco o una matrice. In questo esempio la matrice è l'origine dati.

Nel codice viene specificato un layout di schermata che definisce la visualizzazione dei dati che appariranno sul dispositivo.

I due elementi vengono associati tra loro tramite un adattatore, che in questo codice è un'estensione della classe *ArrayAdapter&lt;ToDoItem&gt;*.

#### <a name="layout"></a>Procedura: Definire il layout

Il layout è definito da diversi frammenti di codice XML. Dato un layout esistente, si presupponga che il codice seguente rappresenti l'oggetto **ListView** che si desidera popolare con i dati del server.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Nel codice precedente l'attributo *listitem* consente di specificare l'ID del layout per una singola riga dell'elenco. Di seguito è riportato il codice che consente di specificare una casella di controllo e il testo ad essa associato. Per tale codice viene creata una sola istanza per ogni elemento dell'elenco. Questo layout non visualizza il campo **id**, ma se il layout è più complesso nella visualizzazione vengono specificati campi aggiuntivi. Il codice è incluso nel file **row\_list\_to\_do.xml**.

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


#### <a name="adapter"></a>Procedura: Definire l'adattatore

Poiché l'origine dati della visualizzazione è una matrice di oggetti *ToDoItem*, verrà creata una sottoclasse per l'adattatore da una classe *ArrayAdapter&lt;ToDoItem&gt;*. Tale sottoclasse consentirà di ottenere una visualizzazione per ogni oggetto *ToDoItem* che utilizza il layout *row\_list\_to\_do*.

Nel codice viene definita la classe seguente che costituisce un'estensione della classe *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


È necessario eseguire l'override del metodo *getView* dell'adattatore. Questo codice di esempio illustra come effettuare questa operazione: i dettagli variano in base all'applicazione.

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Creare un'istanza della classe nell'attività nel modo seguente:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Si noti che il secondo parametro del costruttore ToDoItemAdapter è un riferimento al layout. La chiamata al costruttore è seguita dal codice seguente in cui viene prima ottenuto un riferimento a **ListView** e quindi viene chiamato *setAdapter* per definire la configurazione per l'uso dell'adattatore appena creato:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>Struttura dell'API

Le operazioni sulla tabella di app per dispositivi mobili e le chiamate API personalizzate sono asincrone, quindi si useranno gli oggetti [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) e [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) in tutti i metodi asincroni che interessano query e inserimenti, aggiornamenti ed eliminazioni. In questo modo è più facile eseguire più operazioni in un thread in background, senza dover gestire più callback annidati.

Per verificare come vengono usate queste API asincrone nell'app Android e come vengono visualizzati i dati nell'interfaccia utente, esaminare il file **ToDoActivity.java** nel progetto di guida introduttiva per Android del [portale di Azure].


#### <a name="use-adapter"></a>Procedura: Usare l'adattatore

È ora possibile utilizzare l'associazione dati. Il codice seguente illustra come recuperare gli elementi nella tabella servizio mobile, cancellare l'adattatore e quindi chiamare il metodo *add* dell'adattatore per inserirvi gli elementi restituiti.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

Se si desidera visualizzare i risultati di tale operazione, è inoltre necessario chiamare l'adattatore ogni volta che si modifica la tabella *ToDoItem*. Dal momento che le modifiche vengono fatte record per record, le operazioni riguardano una singola riga anziché una raccolta. Quando si inserisce un elemento, si chiama il metodo *add* sull'adattatore, mentre quando lo si elimina, si chiama il metodo *remove*.

##<a name="querying"></a>Procedura: Eseguire query sui dati dal back-end di app per dispositivi mobili

Questa sezione descrive come eseguire query sul back-end di app per dispositivi mobili, incluse le attività seguenti:

- [Restituire tutti gli elementi]
- [Filtrare i dati restituiti]
- [Ordinare i dati restituiti]
- [Restituire i dati in pagine]
- [Selezionare colonne specifiche]
- [Concatenare metodi di query](#chaining)

### <a name="showAll"></a>Procedura: Restituire tutti gli elementi di una tabella

La query seguente restituisce tutti gli elementi nella tabella *ToDoItem*.

	List<ToDoItem> results = mToDoTable.execute().get();

La variabile *results* restituisce il set di risultati della query sotto forma di elenco.

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

L'esecuzione della query seguente restituisce tutti gli elementi della tabella *ToDoItem* in cui *complete* è uguale a *false*. Questo è il codice già presente nell'esercitazione introduttiva.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* è il riferimento alla tabella di Servizi mobili creata in precedenza.

Definire un filtro con la chiamata al metodo **where** sul riferimento alla tabella. Questa chiamata è seguita da una chiamata al metodo **field**, seguita a sua volta da una chiamata al metodo che specifica il predicato logico. I possibili metodi di predicato includono **eq** (uguale a), **ne** (non uguale a), **gt** (maggiore di), **ge** (maggiore o uguale a), **lt** (minore di), **le** (minore o uguale a) e così via. Questi metodi consentono di confrontare campi numerici e campi stringa con valori specifici,

ad esempio filtrare in base alle date. I metodi seguenti consentono di confrontare l'intero campo data o parti della data: **year**, **month**, **day**, **hour**, **minute** e **second**. L'esempio seguente aggiunge un filtro per gli elementi la cui *data di scadenza* (due) è uguale a 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

I metodi seguenti supportano filtri complessi sui campi di tipo stringa: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** e **length**. L'esempio seguente filtra le righe della tabella in cui la colonna *text* inizia con "PRI0".

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

I metodi operatore seguenti sono supportati sui campi numerici: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** e **round**. L'esempio seguente filtra le righe della tabella in cui il valore di *duration* è un numero pari.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

È possibile combinare i predicati con questi metodi logici: **and**, **or** e **not**. L'esempio seguente combina due degli esempi precedenti.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

È anche possibile raggruppare e annidare operatori logici come segue:

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Per una descrizione più dettagliata ed esempi di filtro, vedere il post relativo all'[analisi delle funzionalità complesse disponibili nel modello di query client per Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice di esempio seguente restituisce tutti gli elementi di una tabella di oggetti *ToDoItems* elencati in ordine crescente in base al campo *text*. *mToDoTable* è il riferimento alla tabella del back-end creata in precedenza.

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Il primo parametro del metodo **orderBy** è una stringa uguale al nome del campo in base al quale eseguire l'ordinamento.

Il secondo parametro usa l'enumerazione **QueryOrder** per specificare l'ordinamento crescente o decrescente.

Si noti che se per il filtro si utilizza il metodo ***where***, è necessario chiamare tale metodo prima del metodo ***orderBy***.

### <a name="paging"></a>Procedura: Restituire i dati in pagine

Nel primo esempio viene illustrato come selezionare i primi cinque elementi di una tabella. La query restituisce gli elementi di una tabella di oggetti *ToDoItems*. *mToDoTable* è il riferimento alla tabella del back-end creata in precedenza.

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Ecco una query che ignora i primi 5 elementi e quindi restituisce i 5 successivi:

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

Il codice seguente illustra come restituire tutti gli elementi di una tabella di oggetti *ToDoItems*, visualizzando però solo i campi *complete* e *text*. *mToDoTable* è il riferimento alla tabella del back-end creata in precedenza.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


In questo caso, i parametri della funzione select sono i nomi in formato stringa delle colonne della tabella che si desidera restituire.

Il metodo **select** deve seguire metodi come **where** e **orderBy**, se presenti. Può essere seguito da metodi di paging come **top**.

### <a name="chaining"></a>Procedura: Concatenare metodi di query

Come si è visto, i metodi usati per eseguire query su tabelle di back-end possono essere concatenati. In questo modo è possibile eseguire operazioni quali selezionare colonne specifiche di righe filtrate ordinate e sottoposte a paging. È possibile creare filtri logici abbastanza complessi.

Il funzionamento è garantito dal fatto che i metodi di query usati restituiscono oggetti **MobileServiceQuery&lt;T&gt;**, su cui è possibile richiamare altri metodi. Per terminare la serie di metodi ed eseguire effettivamente la query, chiamare il metodo **execute**.

Ecco un esempio di codice in cui *mToDoTable* è un riferimento alla tabella *ToDoItem*.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

Per un corretto concatenamento dei metodi è essenziale inserire per primi il metodo *where* e i predicati. In seguito è possibile chiamare metodi successivi nell'ordine più appropriato alle esigenze dell'applicazione.


##<a name="inserting"></a>Procedura: Inserire dati nel back-end

Il seguente codice illustra come inserire una nuova riga in una tabella.

In primo luogo viene creata un'istanza della classe *ToDoItem* e vengono impostate le relative proprietà.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

In seguito si esegue il seguente codice:

	ToDoItem entity = mToDoTable.insert(item).get();

L'entità restituita corrisponde ai dati inseriti nella tabella del back-end, inclusi l'ID e gli eventuali altri valori impostati sul back-end.

App per dispositivi mobili richiede la presenza di una colonna denominata **id** in ogni tabella, usata per indicizzare la tabella. Per impostazione predefinita, questa colonna usa dati di tipo stringa, necessari per supportare la sincronizzazione offline. Il valore predefinito della colonna ID è un GUID, ma è possibile fornire altri valori univoci, ad esempio indirizzi di posta elettronica o nomi utente. Se non si fornisce un valore ID di stringa per un record inserito, il back-end genera un nuovo valore GUID.

I valori ID di stringa offrono i vantaggi seguenti:

+ Gli ID possono essere generati senza creare un round trip al database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ I valori ID si integrano in modo più efficace con la logica di un'applicazione.

##<a name="updating"></a>Procedura: Aggiornare dati in un'app per dispositivi mobili

Il seguente codice illustra come aggiornare i dati in una tabella.

    mToDoTable.update(item).get();

In questo esempio *item* è un riferimento a una riga nella tabella *ToDoItem* alla quale sono state apportate alcune modifiche.

##<a name="deleting"></a>Procedura: Eliminare dati in un'app per dispositivi mobili

Il codice seguente illustra come eliminare dati da una tabella specificando l'oggetto dati.

	mToDoTable.delete(item);

È anche possibile eliminare un elemento specificando il campo **id** della riga da eliminare.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>Procedura: Cercare un elemento specifico

Questo codice illustra come cercare un elemento con un *id* specifico.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Procedura: Usare dati non tipizzati

Il modello di programmazione non tipizzato offre un controllo accurato sulla serializzazione JSON ed è consigliabile usarlo in alcuni scenari, ad esempio se la tabella del back-end contiene un numero elevato di colonne ed è necessario fare riferimento solo ad alcune di esse. L'uso del modello tipizzato richiede la definizione di tutte le colonne della tabella di app per dispositivi mobili nella classe dati. Tuttavia con il modello non tipizzato si possono definire solo le colonne che è necessario utilizzare.

La maggior parte delle chiamate API per l'accesso ai dati è simile alle chiamate della programmazione tipizzata. La principale differenza consiste nel fatto che nel modello non tipizzato i metodi vengono chiamati sull'oggetto **MobileServiceJsonTable**, anziché su **MobileServiceTable**.


### <a name="json_instance"></a>Procedura: Creare un'istanza di una tabella non tipizzata

Analogamente al modello tipizzato, si inizia recuperando un riferimento alla tabella, ma in questo caso si tratta di un oggetto **MobileServicesJsonTable**. Per ottenere il riferimento, chiamare il metodo **getTable** su un'istanza del client, ad esempio:

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Dopo avere creato un'istanza dell'oggetto **MobileServiceJsonTable**, sarà possibile chiamarvi quasi tutti i metodi utilizzabili con il modello di programmazione tipizzato. In alcuni casi, tuttavia, i metodi accettano un parametro non tipizzato, come è possibile notare negli esempi che seguono.

### <a name="json_insert"></a>Procedura: Eseguire insert in una tabella non tipizzata

Nel codice seguente viene illustrato come eseguire un'operazione di insert. Il primo passaggio consiste nel creare un oggetto [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), incluso nella libreria [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

Il passaggio successivo consiste nell'inserire l'oggetto.

    mJsonToDoTable.insert(jsonItem).get();


Se è necessario ottenere l'ID dell'oggetto inserito, usare questa chiamata al metodo:

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Procedura: Eliminare un'istanza da una tabella non tipizzata

Nel codice seguente viene illustrato come eliminare un'istanza, in questo caso la stessa istanza di un oggetto **JsonObject** creato nell'esempio *insert* precedente. Si noti che il codice è analogo a quello del caso tipizzato, ma il metodo ha una firma diversa poiché fa riferimento a un oggetto **JsonObject**.


         mToDoTable.delete(jsonItem);


È inoltre possibile eliminare un'istanza direttamente utilizzando il relativo ID:

		 mToDoTable.delete(ID);


### <a name="json_get"></a>Procedura: Restituire tutte le righe di una tabella non tipizzata

Nel codice seguente viene illustrato come recuperare un'intera tabella. Poiché si sta usando una tabella JSONO, è possibile recuperare in modo selettivo solo alcune colonne della tabella.

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

È possibile applicare filtri, ordinare ed eseguire il paging concatenando i metodi con gli stessi nomi di quelli utilizzati nel modello di programmazione tipizzato.



##<a name="custom-api"></a>Procedura: Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

Per chiamare l'endpoint dell'API personalizzata da un client Android, chiamare il metodo **invokeApi**. L'esempio seguente illustra come chiamare un endpoint API denominato *completeAll*, che restituisce una classe della raccolta denominata MarkAllResult.

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

Nel client viene chiamato il metodo **invokeApi**, che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori. Altre versioni di **invokeApi** consentono di inviare facoltativamente un oggetto nel corpo della richiesta, specificare il metodo HTTP e inviare parametri di query con la richiesta. Vengono fornite anche versioni non tipizzate di **invokeApi**.

##<a name="authentication"></a>Procedura: Aggiungere l'autenticazione all'app

Le esercitazioni descrivono già in dettaglio come aggiungere queste funzionalità.

Il servizio app supporta l'[autenticazione degli utenti di app](app-service-mobile-android-get-started-users.md) con diversi provider di identità esterni: Facebook, Google, account Microsoft, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione nel backend.

Sono supportati due flussi di autenticazione, un flusso *server* e un flusso *client*. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, perché si basa su SDK specifici del provider e del dispositivo e richiede la creazione di codice.

Per abilitare l'autenticazione nell'app, è necessario eseguire tre passaggi:

- Registrare l'app per l'autenticazione con un provider e configurare il back-end dell'app per dispositivi mobili.
- Limitare le autorizzazioni per la tabella solo agli utenti autenticati.
- Aggiungere codice di autenticazione all'app.

È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. Per modificare le richieste, è anche possibile usare il SID di un utente autenticato.

Le prime due attività vengono eseguite tramite il [portale di Azure](https://portal.azure.com/). Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili].

### <a name="caching"></a>Procedura: Aggiungere codice di autenticazione all'app

Il codice seguente avvia la procedura di accesso del flusso server con il provider Google:

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

È possibile ottenere l'ID dell'utente connesso da un oggetto **MobileServiceUser** usando il metodo **getUserId**. Per un esempio di come usare Futures per chiamare le API di accesso asincrone, vedere [Introduzione all'autenticazione].


### <a name="caching"></a>Procedura: Memorizzare nella cache i token di autenticazione

Per memorizzare nella cache i token di autenticazione, è necessario archiviare l'ID utente e il token di autenticazione in locale nel dispositivo. Al successivo avvio dell'app, la cache verrà verificata e, se sono presenti tali valori, sarà possibile ignorare la procedura di accesso e riattivare il client con questi dati. Questi dati sono tuttavia sensibili e devono essere crittografati per garantire la sicurezza in caso di furto del telefono.

È possibile vedere un esempio completo della memorizzazione nella cache dei token di autenticazione nella sezione [Memorizzare nella cache i token di autenticazione](app-service-mobile-android-get-started-users.md#cache-tokens).

Quando si prova a usare un token scaduto, viene visualizzata una risposta di tipo *401 - Non autorizzato*. L'utente deve quindi effettuare di nuovo l'accesso per ottenere nuovi token. Per evitare di scrivere codice per gestire questa situazione in ogni punto dell'app che chiama Servizi mobili, è possibile usare i filtri, che consentono di intercettare le chiamate inviate a e le risposte ricevute da Servizi mobili. Il codice di filtro verificherà quindi la risposta per un errore di tipo 401, attiverà, se necessario, il processo di accesso e quindi riprenderà la richiesta che ha generato l'errore. È anche possibile esaminare il token per controllare la scadenza.


## <a name="adal"></a>Procedura: Autenticare gli utenti con Active Directory Authentication Library

È possibile usare Active Directory Authentication Library (ADAL) per far accedere gli utenti all'applicazione tramite Azure Active Directory. Questa procedura è spesso preferibile all'uso dei metodi `loginAsync()`, perché garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso ad Azure Active Directory seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Assicurarsi di completare il passaggio facoltativo di registrazione di un'applicazione client nativa.

2. Installare ADAL modificando il file build.gradle per includere quanto segue:

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. Aggiungere all'applicazione il codice riportato di seguito apportando le sostituzioni seguenti:

* Sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di Azure classico].

* Sostituire **INSERT-RESOURCE-ID-HERE** con l'ID client per il back-end dell'app per dispositivi mobili. Questo è disponibile nella scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.

* Sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

* Sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint _/.auth/login/done_ del sito, usando lo schema HTTPS. Questo valore dovrebbe essere simile a \_https://contoso.azurewebsites.net/.auth/login/done_.

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## Procedura: Aggiungere notifiche push all'app

È disponibile una [panoramica](../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps) che descrive come Hub di notifica di Microsoft Azure supporti un'ampia gamma di notifiche push.

In [questa esercitazione](app-service-mobile-android-get-started-push.md) viene inviata una notifica push ogni volta che viene inserito un record.

## Procedura: Aggiungere la sincronizzazione offline all'app
L'esercitazione introduttiva contiene codice che implementa la sincronizzazione offline. Cercare codice preceduto da commenti come i seguenti:

	// Offline Sync

Rimuovendo il commento nelle righe di codice seguenti, è possibile implementare la sincronizzazione offline e aggiungere un codice simile al codice di altre app per dispositivi mobili.

##<a name="customizing"></a>Procedura: Personalizzare il client

Esistono diversi modi per personalizzare il comportamento predefinito del client.

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

Il client presuppone che i nomi di tabella e di colonna e i tipi di dati nel back-end corrispondano tutti esattamente agli oggetti di dati definiti nel client. Esistono però diversi motivi per i quali i nomi nel client e nel server potrebbero non corrispondere. In questo scenario è consigliabile applicare i tipi di personalizzazioni seguenti:

- I nomi di colonna usati nella tabella di Servizi mobili non corrispondono a quelli usati nel client.
- Usare una tabella di Servizi mobili con un nome diverso dalla classe alla quale è stato eseguito il mapping nel client.
- Attivare l'uso automatico dell'iniziale maiuscola per le proprietà.
- Aggiungere proprietà complesse a un oggetto.

### <a name="columns"></a>Procedura: Eseguire il mapping di nomi di client e di server diversi

Si supponga che nel codice del client Java vengano utilizzati nomi standard Java per le proprietà dell'oggetto *ToDoItem*, come riportato di seguito.

- mId
- mText
- mComplete
- mDuration


È necessario serializzare i nomi di client in nomi JSON che corrispondono ai nomi di colonna della tabella *ToDoItem* nel server. Questa operazione viene effettuata nel codice seguente, in cui viene utilizzata la libreria [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Procedura: Eseguire il mapping di nomi di tabella diversi tra il client e il back-end

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

È possibile utilizzare questo metodo generale ogni volta in cui si dispone di un oggetto complesso che non è automaticamente serializzabile in JSON e nella tabella di Servizi mobili.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Restituire tutti gli elementi]: #showAll
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[portale di Azure]: https://portal.azure.com
[Introduzione all'autenticazione]: app-service-mobile-android-get-started-users.md
[Introduzione all'autenticazione in Servizi mobili]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0907_2016-->