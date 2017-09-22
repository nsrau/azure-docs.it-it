---
title: Come usare Azure Mobile Apps SDK per Android | Microsoft Docs
description: Come usare Azure Mobile Apps SDK per Android
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 443893ed038e293b27f7781818ffcee9dcc5fc38
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Come usare Azure Mobile Apps SDK per Android

Questa guida illustra come usare Android SDK del client per le App per dispositivi mobili di Azure per implementare scenari comuni, ad esempio:

* L'esecuzione di query sui dati, come inserimento, aggiornamento ed eliminazione.
* L'autenticazione.
* La gestione degli errori.
* La personalizzazione del client.

Questa guida descrive Android SDK lato client.  Per altre informazioni sugli SDK lato server per le app per dispositivi mobili, vedere [Usare l'SDK del server back-end .NET][10] o [Come usare Node.js SDK back-end][11].

## <a name="reference-documentation"></a>Documentazione di riferimento

È possibile trovare il [riferimento API Javadocs][12] per la libreria client Android in GitHub.

## <a name="supported-platforms"></a>Piattaforme supportate

Azure Mobile Apps SDK per Android supporta API di livello da 19 a 24 (da KitKat a Nougat) per i fattori di forma di telefoni e tablet.  L'autenticazione in particolare utilizza un approccio comune ai framework Web per raccogliere le credenziali.  L'autenticazione basata sul flusso server non funziona con dispositivi con fattore di forma piccolo, ad esempio gli orologi.

## <a name="setup-and-prerequisites"></a>Installazione e prerequisiti

Completare l' [esercitazione introduttiva sulle App per dispositivi mobili di Azure](app-service-mobile-android-get-started.md) .  Questa attività garantisce che vengano soddisfatti tutti i prerequisiti per lo sviluppo di App per dispositivi mobili di Azure.  L'esercitazione introduttiva è utile per configurare il proprio account e creare il primo back-end per dispositivi mobili.

Se si decide di non completare l'esercitazione introduttiva, completare le attività seguenti:

* [creare un back-end dell'app per dispositivi mobili][13] da usare con l'app Android.
* In Android Studio [aggiornare i file di compilazione Gradle](#gradle-build).
* [Abilitare l'autorizzazione per Internet](#enable-internet).

### <a name="gradle-build"></a>Aggiornare il file di compilazione Gradle

Modificare entrambi i file **build.gradle** :

1. Aggiungere il codice seguente al livello *Project* del file **build.gradle** all'interno del tag *buildscript*:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Aggiungere il codice seguente al livello *Module app* del file **build.gradle** all'interno del tag *dependencies*:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.3.0'
    ```

    La versione più recente è la 3.3.0. Le versioni supportate sono elencate [su bintray][14].

### <a name="enable-internet"></a>Abilitare l'autorizzazione per Internet

Per accedere ad Azure, è necessario abilitare l'autorizzazione INTERNET per l'app. Se non è già abilitata, aggiungere la riga di codice seguente al file **AndroidManifest.xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Creare una connessione client

App per dispositivi mobili di Azure fornisce quattro funzioni all'applicazione per dispositivi mobili:

* Accesso ai dati e sincronizzazione offline con un servizio di App per dispositivi mobili di Azure.
* Chiamata ad API personalizzate scritte con Azure Mobile Apps Server SDK.
* Autenticazione con autenticazione e autorizzazione nel servizio app di Azure.
* Registrazione di notifiche push con Hub di notifica.

Per ogni funzione prima di tutto è necessario creare un oggetto `MobileServiceClient`.  È consigliabile creare un solo oggetto `MobileServiceClient` nel client per dispositivi mobili, ovvero il modello deve essere singleton.  Per creare un oggetto `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` è una stringa o un oggetto URL che punta al back-end per dispositivi mobili.  Se si usa il servizio app di Azure per ospitare il back-end per dispositivi mobili, verificare di usare la versione `https://` sicura dell'URL.

Per il client è anche necessario l'accesso all'attività o al contesto (il parametro `this` nell'esempio).  La costruzione MobileServiceClient deve avvenire nel metodo `onCreate()` dell'attività a cui si fa riferimento nel file `AndroidManifest.xml`.

Come procedura consigliata, è opportuno astrarre le comunicazioni del server nella relativa classe (modello singleton).  In questo caso, è consigliabile passare l'attività nel costruttore per configurare il servizio in modo appropriato.  Ad esempio:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

È ora possibile chiamare `AzureServiceAdapter.Initialize(this);` nel metodo `onCreate()` dell'attività principale.  Tutti gli altri metodi che hanno necessità di accedere al client usano `AzureServiceAdapter.getInstance();` per ottenere un riferimento all'adattatore del servizio.

## <a name="data-operations"></a>Operazioni dati

Azure Mobile Apps SDK fondamentalmente fornisce accesso ai dati archiviati in SQL Azure nel back-end dell'app per dispositivi mobili.  È possibile accedere a questi dati usando classi fortemente tipizzate (preferibile) o query non tipizzate (non consigliato).  La maggior parte di questa sezione illustra l'uso di classi fortemente tipizzate.

### <a name="define-client-data-classes"></a>Definire le classi di dati client

Per accedere ai dati dalle tabelle di SQL Azure, definire le classi di dati client che corrispondono alle tabelle nel back-end dell'app per dispositivi mobili. Negli esempi di questo argomento si presuppone l'uso di una tabella denominata **MyDataTable** con le colonne seguenti:

* id
* text
* complete

L'oggetto lato client tipizzato corrispondente si trova in un file denominato **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Aggiungere i metodi getter e setter per ogni campo aggiunto.  Se la propria tabella di SQL Azure include più colonne, aggiungere i campi corrispondenti a questa classe.  Se ad esempio l'oggetto di trasferimento dati (DTO) include una colonna Priority di tipo Integer, è consigliabile aggiungere questo campo insieme ai relativi metodi getter e setter:

```java
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
```

Per informazioni su come creare altre tabelle nel back-end delle app per dispositivi mobili, vedere [Procedura: Definire un controller tabelle][15] (back-end .NET) o [Procedura: Definire le tabelle con uno schema dinamico][16] (back-end Node.js).

Una tabella del back-end di App per dispositivi mobili di Azure definisce cinque campi speciali, quattro dei quali disponibili per i client:

* `String id`: ID univoco globale per il record.  Come procedura consigliata, impostare l'ID come rappresentazione di stringa di un oggetto [UUID][17].
* `DateTimeOffset updatedAt`: data/ora dell'ultimo aggiornamento.  Il campo updatedAt viene impostato dal server e non deve mai essere impostato dal codice client.
* `DateTimeOffset createdAt`: data/ora di creazione dell'oggetto.  Il campo createdAt viene impostato dal server e non deve mai essere impostato dal codice client.
* `byte[] version`: rappresentata in genere come stringa, anche la versione viene impostata dal server.
* `boolean deleted`: indica che il record è stato eliminato, ma non ancora definitivamente.  Non usare `deleted` come proprietà nella classe.

Il campo `id` è obbligatorio.  I campi `updatedAt` e `version` vengono usati per la sincronizzazione offline, rispettivamente per la sincronizzazione incrementale e per la risoluzione dei conflitti.  Il campo `createdAt` è un campo di riferimento e non viene usato dal client.  I nomi sono nomi delle proprietà usati per la trasmissione in rete e non sono modificabili.  È tuttavia possibile creare un mapping tra l'oggetto e tali nomi usando la libreria [gson][3].  ad esempio:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getUpdatedAt() { return mCreatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getText() { return mVersion; }
    public final void setText(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Creare un riferimento alla tabella

Per accedere a una tabella, creare prima di tutto un oggetto [MobileServiceTable][8] chiamando il metodo **getTable** su [MobileServiceClient][9].  Questo metodo presenta due overload:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Nel codice seguente **mClient** è un riferimento all'oggetto MobileServiceClient.  Il primo overload viene usato quando il nome della classe e il nome della tabella sono identici ed è quello usato nella guida introduttiva:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Il secondo overload viene usato quando il nome della tabella è diverso da quello della classe: il primo parametro è il nome della tabella.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Eseguire una query su una tabella del back-end

Ottenere prima di tutto un riferimento alla tabella.  Eseguire quindi una query sul riferimento alla tabella.  Una query è qualsiasi combinazione di:

* Una [clausola di filtro](#filtering) `.where()`.
* Una [clausola di ordinamento](#sorting) `.orderBy()`.
* Una [clausola di selezione campo](#selection) `.select()`.
* `.skip()` e `.top()` per i [risultati di paging](#paging).

Le clausole devono essere presentate nell'ordine precedente.

### <a name="filter"></a> Applicazione di filtri ai risultati

Il formato generale di una query è:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

L'esempio precedente restituisce tutti i risultati, fino alle dimensioni di pagina massime impostate dal server.  Il metodo `.execute()` esegue la query sul back-end.  La query viene convertita in una query [OData v3][19] prima della trasmissione al back-end di App per dispositivi mobili.  Alla ricezione, il back-end di App per dispositivi mobili converte la query in un'istruzione SQL prima di eseguirla nell'istanza di SQL Azure.  Poiché l'attività di rete richiede tempo, il metodo `.execute()` restituisce [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrare i dati restituiti

L'esecuzione della query seguente restituisce tutti gli elementi della tabella **ToDoItem** in cui **complete** è uguale a **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** è il riferimento alla tabella di Servizi mobili creata in precedenza.

Definire un filtro con la chiamata al metodo **where** sul riferimento alla tabella. Il metodo **where** è seguito da un metodo **field**, seguito a sua volta da un metodo che specifica il predicato logico. I possibili metodi di predicato includono **eq** (uguale a), **ne** (non uguale a), **gt** (maggiore di), **ge** (maggiore o uguale a), **lt** (minore di), **le** (minore o uguale a). Questi metodi consentono di confrontare campi numerici e campi stringa con valori specifici,

ad esempio filtrare in base alle date. I metodi seguenti consentono di confrontare l'intero campo data o parti della data: **year**, **month**, **day**, **hour**, **minute** e **second**. L'esempio seguente aggiunge un filtro per gli elementi la cui *data di scadenza* (due) è uguale a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

I metodi seguenti supportano filtri complessi sui campi di tipo stringa: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** e **length**. L'esempio seguente filtra le righe della tabella in cui la colonna *text* inizia con "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

I metodi operatore seguenti sono supportati sui campi numerici: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** e **round**. L'esempio seguente filtra le righe della tabella in cui il valore di **duration** è un numero pari.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

È possibile combinare i predicati con questi metodi logici: **and**, **or** e **not**. L'esempio seguente combina due degli esempi precedenti.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Raggruppare e annidare operatori logici:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Per una descrizione più dettagliata ed esempi di filtro, vedere [Exploring the richness of the Android client query model][20] (Analisi delle funzionalità complesse disponibili nel modello di query client per Android).

### <a name="sorting"></a>Ordinare i dati restituiti

Il codice di esempio seguente restituisce tutti gli elementi di una tabella di oggetti **ToDoItems** elencati in ordine crescente in base al campo *text* . *mToDoTable* è il riferimento alla tabella del back-end creata in precedenza.

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Il primo parametro del metodo **orderBy** è una stringa uguale al nome del campo in base al quale eseguire l'ordinamento. Il secondo parametro usa l'enumerazione **QueryOrder** per specificare l'ordinamento crescente o decrescente.  Se per il filtro si usa il metodo ***where***, è necessario chiamare il metodo ***where*** prima del metodo ***orderBy***.

### <a name="selection"></a>Selezionare colonne specifiche

Il codice seguente illustra come restituire tutti gli elementi di una tabella di oggetti **ToDoItems**, visualizzando però solo i campi **complete** e **text**. **mToDoTable** è il riferimento alla tabella del back-end creata in precedenza.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

I parametri della funzione select sono i nomi in formato stringa delle colonne della tabella che si vuole restituire.  Il metodo **select** deve seguire metodi come **where** e **orderBy**. Può essere seguito da metodi di paging come **skip** e **top**.

### <a name="paging"></a>Restituire i dati in pagine

I dati vengono **SEMPRE** restituiti in pagine.  Il numero massimo di record restituiti viene impostato dal server.  Se il client richiede più record, il server restituisce il numero massimo di record.  Per impostazione predefinita, le dimensioni di pagina massime nel server sono pari a 50 record.

Il primo esempio illustra come selezionare i primi cinque elementi di una tabella. La query restituisce gli elementi di una tabella di oggetti **ToDoItems**. **mToDoTable** è il riferimento alla tabella del back-end creata in precedenza.

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Ecco una query che ignora i primi cinque elementi e quindi restituisce i cinque successivi:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Per ottenere tutti i record in una tabella, implementare il codice per scorrere tutte le pagine:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Una richiesta per tutti i record con questo metodo crea un minimo di due richieste al back-end di App per dispositivi mobili.

> [!TIP]
> La scelta delle dimensioni di pagina appropriate è un equilibrio tra utilizzo della memoria mentre la richiesta è in corso, utilizzo della larghezza di banda e ritardo nella ricezione completa dei dati.  Il valore predefinito (50 record) è adatto a tutti i dispositivi.  Se si opera esclusivamente su dispositivi con maggiore memoria, aumentare fino a 500.  È stato osservato che l'aumento delle dimensioni di pagina oltre i 500 record comporta ritardi inaccettabili e problemi di memoria di grandi dimensioni.

### <a name="chaining"></a>Procedura: Concatenare metodi di query

I metodi usati per eseguire query su tabelle di back-end possono essere concatenati. Il concatenamento dei metodi di query consente di selezionare colonne specifiche di righe filtrate ordinate e sottoposte a paging. È possibile creare filtri logici complessi.  Ogni metodo di query restituisce un oggetto Query. Per terminare la serie di metodi ed eseguire effettivamente la query, chiamare il metodo **execute** . ad esempio:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

I metodi con query concatenate devono essere ordinati nel modo seguente:

1. Metodi di filtraggio (**where**).
2. Metodi di ordinamento (**orderBy**).
3. Metodi di selezione (**select**).
4. Metodi di paging (**skip** e **top**).

## <a name="binding"></a>Associare dati all'interfaccia utente

L'associazione dati riguarda tre componenti:

* Origine dati
* Layout della schermata
* Adattatore che collega i due componenti.

Nel codice di esempio i dati vengono restituiti dalla tabella di app per dispositivi mobili di SQL Azure **ToDoItem** in una matrice. Si tratta di una delle attività più comuni per le applicazioni dati.  Le query su database restituiscono spesso una raccolta di righe che il client ottiene in un elenco o una matrice. In questo esempio la matrice è l'origine dati.  Nel codice viene specificato un layout di schermata che definisce la visualizzazione dei dati che appaiono sul dispositivo.  I due elementi vengono associati tra loro tramite un adattatore, che in questo codice è un'estensione della classe **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="layout"></a>Definire il layout

Il layout è definito da diversi frammenti di codice XML. Dato un layout esistente, il codice seguente rappresenta l'oggetto **ListView** che si vuole popolare con i dati del server.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Nel codice precedente l'attributo *listitem* consente di specificare l'ID del layout per una singola riga dell'elenco. Questo codice specifica una casella di controllo e il testo ad essa associato e viene creata una sola istanza per ogni elemento nell'elenco. Questo layout non visualizza il campo **id** , ma se il layout è più complesso nella visualizzazione vengono specificati campi aggiuntivi. Il codice è incluso nel file **row_list_to_do.xml**.

```java
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
```

#### <a name="adapter"></a>Definire l'adattatore
Poiché l'origine dati della visualizzazione è una matrice di oggetti **ToDoItem**, viene creata una sottoclasse per l'adattatore da una classe **ArrayAdapter&lt;ToDoItem&gt;**. Questa sottoclasse consente di ottenere una visualizzazione per ogni oggetto **ToDoItem** che usa il layout **row_list_to_do**.  Nel codice viene definita la classe seguente che costituisce un'estensione della classe **ArrayAdapter&lt;E&gt;**:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Eseguire l'override del metodo **getView** dell'adattatore. ad esempio:

```
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
```

Creare un'istanza della classe nell'attività nel modo seguente:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Il secondo parametro del costruttore ToDoItemAdapter è un riferimento al layout. È ora possibile creare un'istanza di **ListView** e assegnare l'adattatore a **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Usare l'adattatore per l'associazione all'interfaccia utente

È ora possibile utilizzare l'associazione dati. Il codice seguente illustra come recuperare gli elementi nella tabella e inserisce gli elementi restituiti per l'adattatore locale.

```java
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
```

Chiamare l'adattatore ogni volta che si modifica la tabella **ToDoItem** . Dal momento che le modifiche vengono fatte record per record, si gestisce una singola riga anziché una raccolta. Quando si inserisce un elemento, chiamare il metodo **add** sull'adattatore, mentre quando lo si elimina, chiamare il metodo **remove**.

Un esempio completo è disponibile nel [progetto di avvio rapido per Android][21].

## <a name="inserting"></a>Inserire dati nel back-end

Viene creata un'istanza della classe *ToDoItem* e vengono impostate le relative proprietà.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Usare quindi **insert()** per inserire un oggetto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

L'entità restituita corrisponde ai dati inseriti nella tabella del back-end, inclusi l'ID e gli eventuali altri valori, ad esempio i campi `createdAt`, `updatedAt` e `version`, impostati sul back-end.

Le tabelle di App per dispositivi mobili richiedono una colonna chiave primaria denominata **id**. Questa colonna deve essere una stringa. Il valore predefinito della colonna ID è un GUID.  È possibile specificare altri valori univoci, come gli indirizzi di posta elettronica o i nomi utente. Se non si fornisce un valore ID di stringa per un record inserito, il back-end genera un nuovo GUID.

I valori ID di stringa offrono i vantaggi seguenti:

* Gli ID possono essere generati senza creare un round trip al database.
* L'unione di record da tabelle o database diversi risulta semplificata.
* I valori ID si integrano in modo più efficace con la logica di un'applicazione.

I valori ID di stringa sono **OBBLIGATORI** per il supporto di sincronizzazione offline.  Non è possibile modificare un ID dopo che è stato archiviato nel database back-end.

## <a name="updating"></a>Aggiornare dati in un'app per dispositivi mobili

Per aggiornare i dati in una tabella, passare il nuovo oggetto al metodo **update()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

In questo esempio *item* è un riferimento a una riga nella tabella *ToDoItem* alla quale sono state apportate alcune modifiche.  Viene aggiornata la riga con lo stesso **id** .

## <a name="deleting"></a>Eliminare dati in un'app per dispositivi mobili

Il codice seguente illustra come eliminare dati da una tabella specificando l'oggetto dati.

```java
mToDoTable
    .delete(item);
```

È anche possibile eliminare un elemento specificando il campo **id** della riga da eliminare.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Cercare un elemento specifico per ID

Cercare un elemento con un campo **id** specifico con il metodo **lookUp()**:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Procedura: Usare dati non tipizzati

Il modello di programmazione non tipizzato offre un controllo accurato della serializzazione JSON.  È consigliabile usarlo in alcuni scenari comuni. Un esempio di utilizzo è una tabella di back-end che contiene un numero elevato di colonne ed è necessario fare riferimento solo ad alcune di esse.  Il modello tipizzato richiede la definizione di tutte le colonne definite nel back-end di App per dispositivi mobili nella classe dati.  La maggior parte delle chiamate API per l'accesso ai dati è simile alle chiamate della programmazione tipizzata. La principale differenza consiste nel fatto che nel modello non tipizzato i metodi vengono chiamati sull'oggetto **MobileServiceJsonTable**, anziché su **MobileServiceTable**.

### <a name="json_instance"></a>Creare un'istanza di una tabella non tipizzata

Analogamente al modello tipizzato, si inizia recuperando un riferimento alla tabella, ma in questo caso si tratta di un oggetto **MobileServicesJsonTable** . Ottenere il riferimento chiamando il metodo **getTable** su un'istanza del client:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Dopo avere creato un'istanza di **MobileServiceJsonTable**, l'istanza contiene praticamente la stessa API disponibile con il modello di programmazione tipizzato. In alcuni casi, i metodi accettano un parametro non tipizzato anziché un parametro tipizzato.

### <a name="json_insert"></a>Eseguire un inserimento in una tabella non tipizzata
Nel codice seguente viene illustrato come eseguire un'operazione di insert. Il primo passaggio consiste nel creare un oggetto [JsonObject][1], incluso nella libreria [gson][3].

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Usare quindi il metodo **insert()** per inserire l'oggetto non tipizzato nella tabella.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se è necessario ottenere l'ID dell'oggetto inserito, usare il metodo **getAsJsonPrimitive()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Eliminare un'istanza da una tabella non tipizzata
Nel codice seguente viene illustrato come eliminare un'istanza, in questo caso la stessa istanza di un oggetto **JsonObject** creato nell'esempio *insert* precedente. Il codice è analogo a quello del caso tipizzato, ma il metodo ha una firma diversa poiché fa riferimento a un oggetto **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

È inoltre possibile eliminare un'istanza direttamente utilizzando il relativo ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Restituire tutte le righe di una tabella non tipizzata
Il seguente codice illustra come recuperare un'intera tabella. Poiché si sta usando una tabella JSONO, è possibile recuperare in modo selettivo solo alcune colonne della tabella.

```java
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
```

Lo stesso insieme di metodi di filtro e paging disponibili per il modello tipizzato è disponibile per il modello non tipizzato.

## <a name="offline-sync"></a>Implementare la sincronizzazione offline

Azure Mobile Apps Client SDK implementa anche la sincronizzazione offline dei dati usando un database SQLite per archiviare una copia locale dei dati del server.  Le operazioni eseguite su una tabella offline non richiedono la connettività per dispositivi mobili.  La sincronizzazione offline agevola la resilienza e le prestazioni a scapito di una logica più complessa per la risoluzione dei conflitti.  Azure Mobile Apps Client SDK implementa le funzionalità seguenti:

* Sincronizzazione incrementale: vengono scaricati solo i record aggiornati e nuovi, risparmiando larghezza di banda e utilizzo della memoria.
* Concorrenza ottimistica: si presuppone che le operazioni abbiano esito positivo.  La risoluzione dei conflitti viene posticipata finché gli aggiornamenti non vengono eseguiti nel server.
* Risoluzione dei conflitti: l'SDK rileva quando è stata apportata una modifica in conflitto nel server e fornisce hook per avvisare l'utente.
* Eliminazione temporanea: i record eliminati vengono contrassegnati come tali, consentendo agli altri dispositivi di aggiornare la cache offline.

### <a name="initialize-offline-sync"></a>Inizializzare la sincronizzazione offline

Ogni tabella offline deve essere definito nella cache offline prima dell'uso.  La definizione della tabella in genere viene eseguita immediatamente dopo la creazione del client:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Ottenere un riferimento alla tabella della cache offline

Per una tabella online, si usa `.getTable()`.  Per una tabella offline, usare `.getSyncTable()`:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Tutti i metodi disponibili per le tabelle online (inclusi filtri, ordinamento, paging, inserimento di dati, aggiornamento di dati ed eliminazione di dati) funzionano correttamente sia nelle tabelle online che in quelle offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizzare la cache locale offline

La sincronizzazione è sotto il controllo dell'app.  Ecco un esempio di metodo di sincronizzazione:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Se viene fornito un nome di query al metodo `.pull(query, queryname)`, viene usata la sincronizzazione incrementale per restituire solo i record creati o modificati dopo il corretto completamento dell'ultimo pull.

### <a name="handle-conflicts-during-offline-synchronization"></a>Gestire i conflitti durante la sincronizzazione offline

Se si verifica un conflitto durante un'operazione `.push()`, viene generata un'eccezione `MobileServiceConflictException`.   L'elemento rilasciato dal server viene incorporato nell'eccezione e può essere recuperato da `.getItem()` nell'eccezione.  Modificare il push chiamando gli elementi seguenti sull'oggetto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Dopo che tutti i conflitti sono stati contrassegnati nel modo desiderato, chiamare di nuovo `.push()` per risolvere tutti i conflitti.

## <a name="custom-api"></a>Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

Per chiamare l'endpoint dell'API personalizzata da un client Android, chiamare il metodo **invokeApi** . L'esempio seguente illustra come chiamare un endpoint API denominato **completeAll**, che restituisce una classe della raccolta denominata **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
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
```

Nel client viene chiamato il metodo **invokeApi** , che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori. Altre versioni di **invokeApi** consentono di inviare facoltativamente un oggetto nel corpo della richiesta, specificare il metodo HTTP e inviare parametri di query con la richiesta. Vengono fornite anche versioni non tipizzate di **invokeApi** .

## <a name="authentication"></a>Aggiungere l'autenticazione all'app

Le esercitazioni descrivono già in dettaglio come aggiungere queste funzionalità.

Il servizio app supporta l'[autenticazione degli utenti di app](app-service-mobile-android-get-started-users.md) con diversi provider di identità esterni: Facebook, Google, account Microsoft, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione nel backend.

Sono supportati due flussi di autenticazione, ovvero un flusso **server** e un flusso **client**. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia Web del provider di identità.  Non sono necessari SDK aggiuntivi per implementare l'autenticazione del flusso server. L'autenticazione del flusso server non fornisce un'integrazione completa con il dispositivo mobile ed è consigliata solo per dimostrare scenari concettuali.

Il flusso client assicura maggiore integrazione con funzionalità specifiche del dispositivo, ad esempio Single-Sign-On, poiché si basa su SDK forniti dal provider di identità.  Ad esempio, è possibile integrare l'SDK di Facebook nell'applicazione per dispositivi mobili.  Il client per dispositivi mobili passa all'app Facebook e conferma l'accesso prima di passare di nuovo all'app per dispositivi mobili.

Per abilitare l'autenticazione nell'app, è necessario eseguire quattro passaggi:

* Registrare l'app per l'autenticazione con un provider di identità.
* Configurare il back-end del servizio app.
* Limitare le autorizzazioni per la tabella solo agli utenti autenticati nel back-end del servizio app.
* Aggiungere codice di autenticazione all'app.

È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. Per modificare le richieste, è anche possibile usare il SID di un utente autenticato.  Per altre informazioni, vedere [Aggiungere l'autenticazione all'app Android] e la documentazione sulle procedure dell'SDK del server.

### <a name="caching"></a>Autenticazione: flusso server

Il codice seguente avvia la procedura di accesso del flusso server con il provider Google.  È necessaria una configurazione aggiuntiva a causa dei requisiti di sicurezza per il provider Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Aggiungere anche il metodo seguente alla classe Activity principale:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

L'elemento `GOOGLE_LOGIN_REQUEST_CODE` definito nell'attività principale viene usato per il metodo `login()` e nel metodo `onActivityResult()`.  È possibile scegliere qualsiasi numero univoco, purché lo stesso numero venga usato nei metodi `login()` e `onActivityResult()`.  Se si astrae il codice client in un adattatore di un servizio (come illustrato prima), è consigliabile chiamare i metodi appropriati sull'adattatore del servizio.

È anche necessario configurare il progetto per customtabs.  Specificare prima di tutto un URL di reindirizzamento.  Aggiungere il frammento di codice seguente a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Aggiungere **redirectUriScheme** al file `build.gradle` per l'applicazione:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Aggiungere infine `com.android.support:customtabs:23.0.1` all'elenco di dipendenze nel file `build.gradle`:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.2.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

È possibile ottenere l'ID dell'utente connesso da un oggetto **MobileServiceUser** usando il metodo **getUserId**. Per un esempio di come usare Futures per chiamare le API di accesso asincrone, vedere [Aggiungere l'autenticazione all'app Android].

> [!WARNING]
> Lo schema URL indicato rispetta la distinzione tra maiuscole e minuscole.  Assicurarsi che tutte le occorrenze di `{url_scheme_of_you_app}` rispettino questa distinzione.

### <a name="caching"></a>Memorizzare nella cache i token di autenticazione

Per memorizzare nella cache i token di autenticazione, è necessario archiviare l'ID utente e il token di autenticazione in locale nel dispositivo. Al successivo avvio dell'app, la cache viene verificata e, se sono presenti questi valori, è possibile ignorare la procedura di accesso e riattivare il client con questi dati. Questi dati sono tuttavia sensibili e devono essere crittografati per garantire la sicurezza in caso di furto del telefono.  È possibile vedere un esempio completo della memorizzazione nella cache dei token di autenticazione nella sezione [Memorizzare nella cache i token di autenticazione][7].

Quando si prova a usare un token scaduto, viene visualizzata una risposta di tipo *401 - Non autorizzato* . È possibile gestire gli errori di autenticazione tramite i filtri.  I filtri intercettano le richieste al back-end del servizio app. Il codice di filtro verifica quindi la risposta per un errore di tipo 401, attiva il processo di accesso e quindi riprende la richiesta che ha generato l'errore.

### <a name="refresh"></a>Usare i token di aggiornamento

La durata definita del token restituito dall'autenticazione e dall'autorizzazione nel servizio app di Azure è di un'ora.  Dopo questo periodo, è necessario ripetere l'autenticazione dell'utente.  Se si usa un token di lunga durata ricevuto tramite l'autenticazione basata sul flusso client, è possibile ripetere l'autenticazione con l'autenticazione e l'autorizzazione del servizio app di Azure usando lo stesso token.  Viene generato un altro token del servizio app di Azure con una nuova durata.

È anche possibile registrare il provider per l'uso di token di aggiornamento.  Un token di aggiornamento non è sempre disponibile.  È necessaria una configurazione aggiuntiva:

* Per **Azure Active Directory**, configurare un segreto client per l'app Azure Active Directory.  Specificare il segreto client nel servizio app di Azure durante la configurazione dell'autenticazione di Azure Active Directory.  Quando si chiama `.login()`, passare `response_type=code id_token` come parametro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Per **Google**, passare `access_type=offline` come parametro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Per un **account Microsoft**, selezionare l'ambito `wl.offline_access`.

Per aggiornare un token, chiamare `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Come procedura consigliata, creare un filtro che rileva una risposta 401 dal server e prova ad aggiornare il token dell'utente.

## <a name="log-in-with-client-flow-authentication"></a>Accedere con l'autenticazione basata sul flusso client

Il processo generale per l'accesso con l'autenticazione basata sul flusso client è il seguente:

* Configurare l'autenticazione e l'autorizzazione del servizio app di Azure come si configura l'autenticazione basata sul flusso server.
* Integrare l'SDK del provider di autenticazione per generare un token di accesso.
* Chiamare il metodo `.login()` come illustrato di seguito:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
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
    ```

Sostituire il metodo `onSuccess()` con il codice che si vuole usare all'esecuzione di un accesso.  La stringa `{provider}` è un provider valido: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount** o **twitter**.  Se è stata implementata l'autenticazione personalizzata, è anche possibile usare il tag del provider di autenticazione personalizzata.

### <a name="adal"></a>Autenticare gli utenti con Active Directory Authentication Library (ADAL)

È possibile usare Active Directory Authentication Library (ADAL) per far accedere gli utenti all'applicazione tramite Azure Active Directory. L'uso dell'accesso del flusso client è spesso preferibile all'uso dei metodi `loginAsync()` , perché garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso ad Azure Active Directory seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory][22]. Assicurarsi di completare il passaggio facoltativo di registrazione di un'applicazione client nativa.
2. Installare ADAL modificando il file build.gradle per includere le definizioni seguenti:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Aggiungere all'applicazione il codice seguente apportando le sostituzioni seguenti:

* Sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato eseguito il provisioning dell'applicazione. Il formato deve essere https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Sostituire **INSERT-RESOURCE-ID-HERE** con l'ID client per il back-end dell'app per dispositivi mobili. L'ID client è disponibile nella scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.
* Sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.
* Sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint */.auth/login/done* del sito, usando lo schema HTTPS. Questo valore deve essere simile a *https://contoso.azurewebsites.net/.auth/login/done*.

```java
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
```

## <a name="filters"></a>Modificare la comunicazione client-server

La connessione client è in genere una connessione HTTP di base che usa la libreria HTTP sottostante fornita con Android SDK.  I motivi per cui potrebbe essere necessario modificarla sono diversi:

* Si vuole usare una libreria HTTP alternativa per modificare i timeout.
* Si vuole fornire un indicatore di stato.
* Si vuole aggiungere un'intestazione personalizzata per supportare la funzionalità di gestione API.
* Si vuole intercettare una risposta non riuscita per poter implementare la riautenticazione.
* Si vogliono registrare le richieste del back-end in un servizio di analisi.

### <a name="using-an-alternate-http-library"></a>Uso di una libreria HTTP alternativa

Chiamare il metodo `.setAndroidHttpClientFactory()` immediatamente dopo avere creato il riferimento al client.  Ad esempio, per impostare il timeout di connessione su 60 secondi, invece dei 10 secondi predefiniti:

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementare un filtro per l'avanzamento

È possibile implementare un intercettazione di ogni richiesta implementando `ServiceFilter`.  Il codice seguente ad esempio aggiorna un indicatore stato già creato:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

È possibile collegare questo filtro al client come segue:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizzare le intestazioni delle richieste

Usare l'elemento `ServiceFilter` seguente e collegare il filtro come per `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurare la serializzazione automatica

È possibile specificare una strategia di conversione che si applica a tutte le colonne tramite l'API [gson][3]. La libreria client Android usa [gson][3] in modo invisibile per serializzare oggetti Java in dati JSON, prima che i dati vengano inviati al Servizio app di Azure.  Il codice seguente usa **setFieldNamingStrategy()** per impostare la strategia. Questo esempio elimina il carattere iniziale (una "m") e quindi applica il minuscolo al carattere successivo per ogni nome di campo, ad esempio, trasforma "mId" in "id".  Implementare una strategia di conversione per ridurre la necessità di annotazioni `SerializedName()` nella maggior parte dei campi.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Questo codice deve essere eseguito prima di creare un riferimento al client per dispositivi mobili usando **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Introduzione all'autenticazione]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

