<properties urlDisplayName="Get Started" pageTitle="Introduzione a Servizi mobili e Sencha Touch" metaKeywords="" description="Seguire questa esercitazione per iniziare a sviluppare con Servizi mobili e con il framework per app mobile Sencha HTML5. " metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="" documentationCenter="Mobile" title="Get started with Mobile Services and Sencha Touch" authors="sencha" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-sencha" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sencha" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili e Sencha Touch

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questa esercitazione illustra come sfruttare Servizi mobili di Azure nell'applicazione Sencha Touch. Sencha Touch verrà usato per creare una semplice app To Do List che usa un servizio mobile definito tramite il portale di gestione. Questa esercitazione è destinata a sviluppatori di applicazioni Web di livello da intermedio ad avanzato già esperti di JavaScript e conoscono il framework di Sencha Touch. </p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione. Nel video Arthur Kay spiega come creare un'applicazione Sencha Touch usando un back-end di Servizi mobili di Azure.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/partner-sencha-mobile-services-get-started/mobile-sencha-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">14:37</span></div>
</div>



Di seguito è riportata una schermata dell'app completata:

![][0]



##Requisiti

- Scaricare e installare <a href="http://wwww.sencha.com/products/touch/download" target="_blank">Sencha Touch</a>.

- Scaricare e installare lo strumento <a href="http://www.sencha.com/products/sencha-cmd/download" target="_blank">Sencha Cmd</a>.

- Java Runtime Environment (JRE) o Java Development Kit (se si intende creare app per Android)

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

##Creazione di una tabella TodoItems

Dopo avere creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova tabella di database da usare nel servizio mobile.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Nella scheda Avvio rapido fare clic su **HTML** in **Scegli una piattaforma** ed espandere **Crea nuova app HTML**.

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    Di seguito sono visualizzati i tre semplici passaggi per creare e ospitare un'app HTML connessa al servizio mobile.

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. Fare clic su **Crea tabella TodoItem** per creare una tabella in cui archiviare i dati dell'app.

	<div class="dev-callout"> 
	<b>Nota</b> 
	<p>NON scaricare l'app HTML dal portale di gestione. Verrà invece creata manualmente un'applicazione Sencha Touch nella sezione seguente.</p></div> 


1. Prendere nota della **chiave dell'app** e dell'**URL dell'app** nel portale di gestione perché verranno usati in altre sezioni di questa esercitazione.

    ![app key](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. Nella scheda **Configura** verificare che `localhost` sia presente nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.

    ![Setup CORS for localhost](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##Generazione dell'applicazione Touch

La generazione di un'applicazione Sencha Touch modello è un'attività semplice se si usa Sencha Cmd e costituisce una valida soluzione per rendere un'applicazione rapidamente operativa.

Dalla directory in cui è stato installato il framework Touch eseguire il comando seguente:

	$ sencha generate app Basic /path/to/application

Verrà generata un'applicazione Touch modello il cui nome è 'Basic'. Per avviare l'applicazione, passare nel browser alla directory /path/to/application per visualizzare l'applicazione Touch standard di esempio.

##Installazione di Sencha Touch Extensions for Azure

L'estensione per Azure viene installata sia manualmente che come pacchetto di Sencha. È possibile scegliere il metodo preferito.

###Installazione manuale

Nella maggior parte delle applicazioni Touch se si desidera aggiungere una libreria esterna di classi, è sufficiente scaricare il pacchetto, decomprimerlo nella directory dell'applicazione e configurare il caricatore Touch con il percorso della libreria. 

Per aggiungere manualmente le estensioni di Azure all'applicazione, usare la procedura seguente:

1. Scaricare [qui](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure) il pacchetto di estensioni per Azure. Per accedere a quest'area, è possibile usare l'ID dei forum Sencha.

2. Copiare il pacchetto di estensioni per Azure dalla directory di download a quella in cui si desidera salvarla e decomprimerla: 

        $ cd /path/to/application
	    $ mv /download-location/azure.zip .
    	$ unzip azure.zip  

    Verrà creata una directory **azure** contenente i file di origine, gli esempi e la documentazione dell'intero pacchetto. I file di origine sono disponibili nella directory **azure/src**.


###Installazione come pacchetto di Sencha

<div class="dev-callout"> 
	<b>Nota</b> 
	<p>È possibile usare questo metodo solo quando l'applicazione è stata generata con il comando <code>sencha generate app</code>.</p></div> 

Tutte le applicazioni generate da Sencha Cmd contengono una cartella "packages" nella radice. Il percorso della cartella "packages" è configurabile; tuttavia, indipendentemente dal percorso, tale cartella viene usata per archiviare tutti i pacchetti usati dall'applicazione o delle applicazioni se è stato creata un'area di lavoro di Sencha.

Poiché Ext.Azure è un "pacchetto" di Sencha Cmd, il codice sorgente può essere facilmente installato e incluso nell'applicazione tramite Sencha Cmd. Per altre informazioni, vedere la pagina relativa ai [pacchetti di Sencha Cmd](http://docs.sencha.com/cmd/3.1.2/#!/guide/command_packages).

Per scaricare e installare il pacchetto di estensione per Azure dall'archivio dei pacchetti di Sencha, sarà necessario aggiungere il nome del pacchetto al file **app.json** e compilare l'applicazione:

1. Aggiungere il pacchetto per Azure alla sezione requires del file app.json:

	    {
            "name": "Basic",
	        "requires": [
    	        "touch-azure"
        	]
    	}
    
2. Ricompilare l'applicazione usando **sencha cmd** per recuperare e installare il pacchetto:

	    $ sencha app build

Sia **sencha app build** che **sencha app refresh** eseguono la procedura necessaria per integrare il pacchetto nell'applicazione. In genere dopo la modifica dei requisiti del pacchetto è necessario eseguire **sencha app refresh** in modo da aggiornare i metadati necessari per supportare "dev mode".

Indipendentemente dal comando eseguito, Sencha Cmd scaricherà ed espanderà il pacchetto nella cartella "packages". Dopo tale operazione nell'area di lavoro sarà presente una cartella "packages/touch-azure".

##Inclusione e configurazione di Azure

**Nome file**: app.js 

A questo punto, dopo aver scaricato e installato l'estensione per Azure nella directory dell'applicazione, l'operazione successiva consiste nell'indicare all'applicazione dove reperire i file di origine e nel richiedere tali file:

1. Configurare il caricatore Sencha con il percorso del codice sorgente:
 
        Ext.Loader.setConfig({
       	    enabled : true,
           	paths   : {
               	'Ext'       : 'touch/src',
               	'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Richiedere i file di classe di Azure:

		Ext.application({

			requires: [ 'Ext.azure.Azure' ],

			// ...

		});


3. Configurazione di Azure

	Per inizializzare il pacchetto Azure, chiamare il metodo **Ext.Azure.init** nella sezione relativa all'avvio dell'applicazione. A questo metodo viene passato un oggetto Configuration contenente le credenziali del servizio mobile oltre ad altre credenziali e funzionalità che si vuole usare.

	Anche se è possibile passare l'oggetto Configuration direttamente al metodo init, è consigliabile creare una proprietà di configurazione dell'applicazione Sencha denominata **azure** e inserirvi tutte le informazioni appropriate. È quindi possibile passare il valore di questa proprietà al metodo Ext.Azure.init.

	Quando si crea un servizio mobile in Azure (vedere l'[introduzione ad Azure](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)), al servizio vengono assegnati un URL e una chiave applicazione. È necessario fornire queste informazioni al pacchetto Azure per consentirne la connessione al servizio.

	In questo esempio viene illustrata una semplicissima procedura di configurazione e inizializzazione di Azure in cui vengono forniti solo l'URL e la chiave applicazione:

	    Ext.application({
    	    name: 'Basic',

        	requires: [ 'Ext.azure.Azure' ],

	        azure: {
    	        appKey: 'myazureservice-access-key',
        	    appUrl: 'myazure-service.azure-mobile.net'
	        },

    	    launch: function() {

        	    // Call Azure initialization

            	Ext.Azure.init(this.config.azure);

 	       }
    	});

	Per altre informazioni sulle opzioni di configurazione di Azure, consultare la documentazione dell'API Ext.Azure.


Congratulazioni. L'applicazione in uso dovrebbe poter accedere al servizio mobile.

##Creazione dell'app ToDo

Dopo aver configurato l'applicazione in modo da includere l'estensione Azure e aver specificato le credenziali del servizio mobile, è possibile passare alla creazione di un'applicazione Touch che usa il servizio mobile per visualizzare e modificare i dati dell'elenco attività archiviati nel servizio.

###Configurare il proxy di dati Azure

**Nome file:** app/model/TodoItem.js

L'applicazione Touch comunicherà con il servizio mobile tramite un proxy di dati. Il proxy si occupa di tutte le attività, dall'invio delle richieste alla ricezione dei dati dal servizio mobile. Se usato in combinazione con un modello e un archivio dati Touch, le attività di elaborazione dei dati remoti e di inserimento di tali dati vengono gestite direttamente da Touch.

I modelli Sencha Touch forniscono la definizione dei record di dati che verranno usati nell'applicazione. In questo modo è possibile definire non solo i campi di dati, ma anche fornire la configurazione relativa al proxy che gestirà le comunicazioni tra l'applicazione e il servizio mobile di Azure.

Nel codice seguente vengono definiti i campi (e i relativi tipi) per il modello, nonché fornire una configurazione proxy. Quando si configura il proxy, è necessario assegnarvi un tipo (in questo caso 'azure'), il nome tabella del servizio mobile (ToDoItem) e altri parametri facoltativi. In questo esempio verrà attivato il paging del proxy per consentire lo spostamento tra le voci dell'elenco.

Il proxy di Azure imposterà automaticamente tutte le intestazioni HTTP con le operazioni CRUD appropriate previste dall'API di Azure API, incluse le eventuali credenziali di autenticazione esistenti.

	Ext.define('Basic.model.TodoItem', {
    	extend : 'Ext.data.Model',

	    requires : [
    	    'Ext.azure.Proxy'
    	],

	    config : {
    	    idProperty : 'id',
        	useCache   : false,

	        fields     : [
    	        {
        	        name : 'id',
            	    type : 'int'
            	},
            	{
                	name : 'text',
                	type : 'string'
            	},
            	{
	                name : 'complete',
    	            type : 'boolean'
        	    }
	        ],

	        proxy : {
    	        type               : 'azure',
        	    tableName          : 'TodoItem',
            	enablePagingParams : true
        	}
    	}
	});


###Archiviare gli elementi ToDo 

**Nome file**: app/store/TodoItems.js

Gli archivi Sencha Touch consentono di archiviare raccolte di record di dati (modelli) utilizzabili come origini di componenti Touch per visualizzare i record in modi diversi. Tali archivi possono includere, ad esempio, griglie, grafici ed elenchi.

In questo caso verrà definito un archivio usato come contenitore di tutti gli elementi dell'elenco attività recuperati dal servizio mobile di Azure. Si noti che la configurazione dell'archivio contiene il nome del tipo di modello (Basic.model.TodoItem), definito in precedenza, e che consente di definire la struttura dei record contenuti nell'archivio.

Sono inoltre disponibili altre opzioni di configurazione dell'archivio, ad esempio per specificare la dimensione della pagina (8 record) e per indicare che l'ordinamento dei record dell'archivio verrà effettuato in remoto dal servizio mobile di Azure, pertanto non verrà effettuato nessun ordinamento in locale nell'archivio stesso.

	Ext.define('Basic.store.TodoItems', {
    	extend : 'Ext.data.Store',

	    requires : [
    	    'Basic.model.TodoItem'
	    ],

	    config : {
    	    model        : 'Basic.model.TodoItem',
        	pageSize     : 8,
	        remoteSort   : true,
    	    remoteFilter : true
    	}
	});


###Visualizzare e modificare gli elementi ToDo

**Nome file**: app/view/DataItem.js

NA questo punto, dopo aver definito la struttura di ogni elemento Todo e aver creato un archivio in cui inserire tutti i record, è opportuno definire in che modo si vuole che tali informazioni vengano visualizzate all'utente dell'app. A tale scopo vengono in genere usate le **visualizzazioni**, ovvero un qualsiasi numero di componenti Touch, sia singoli che combinati con altri. 

La visualizzazione seguente è costituita da un elemento ListItem che definisce la visualizzazione dei singoli record unitamente ad alcuni pulsanti per gestire le azioni relative all'eliminazione dei record.

	Ext.define('Basic.view.DataItem', {
    	extend : 'Ext.dataview.component.ListItem',
    	xtype  : 'basic-dataitem',

	    requires : [
    	    'Ext.Button',
        	'Ext.layout.HBox',
        	'Ext.field.Checkbox'
    	],

	    config : {
    	    checkbox : {
        	    docked     : 'left',
            	xtype      : 'checkboxfield',
            	width      : 50,
            	labelWidth : 0
        	},

	        text : {
    	        flex : 1
        	},

	        button : {
    	        docked   : 'right',
        	    xtype    : 'button',
            	ui       : 'plain',
	            iconMask : true,
    	        iconCls  : 'delete',
        	    style    : 'color: red;'
        	},

	        dataMap : {
    	        getText : {
        	        setHtml : 'text'
            	},

	            getCheckbox : {
    	            setChecked : 'complete'
        	    }
        	},

	        layout : {
    	        type : 'hbox',
        	    align: 'stretch'
        	}
    	},

	    applyCheckbox : function(config) {
    	    return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
    	},

	    updateCheckbox : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	},

	    applyButton : function(config) {
    	    return Ext.factory(config, Ext.Button, this.getButton());
    	},

	    updateButton : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	}

	});


###Creare la visualizzazione primaria

**Nome file**: app/view/Main.js

Dopo aver definito il layout di un singolo elemento dell'elenco attività, si desidera ora creare un'interfaccia utente completa per tale elenco, grazie alla quale è possibile definire l'elenco effettivo degli elementi, un titolo per l'applicazione e un pulsante per aggiungere una nuova attività. 

	Ext.define('Basic.view.Main', {
    	extend : 'Ext.dataview.List',
    	xtype  : 'main',

	    requires : [
    	    'Ext.TitleBar',
        	'Ext.dataview.List',
        	'Ext.data.Store',
        	'Ext.plugin.PullRefresh',
        	'Ext.plugin.ListPaging',
        	'Basic.view.DataItem'
    	],

	    config : {
    	    store : 'TodoItems',

        	useSimpleItems : false,
        	defaultType    : 'basic-dataitem',

	        plugins : [
    	        {
        	        xclass          : 'Ext.plugin.PullRefresh',
            	    pullRefreshText : 'Pull down to refresh!'
            	},
            	{
                	xclass     : 'Ext.plugin.ListPaging',
                	autoPaging : true
            	}
        	],

	        scrollable : {
    	        direction     : 'vertical',
        	    directionLock : true
        	},

	        items : [
    	        {
        	        docked : 'top',
            	    xtype  : 'titlebar',
                	title  : 'Azure Mobile - Basic Data Example'
            	},
            	{
                	xtype  : 'toolbar',
                	docked : 'bottom',
                	items  : [
                    	{
                        	xtype       : 'textfield',
                        	placeHolder : 'Enter new task',
                        	flex        : 1
                    	},
                    	{
                        	xtype  : 'button',
                        	action : 'add',
                        	text   : 'Add'
                    	}
                	]
            	}
        	]
    	}
	});

###Garantire la compatibilità di tutti gli elementi

**Nome file**: app/controller/Main.js

Il passaggio finale dell'applicazione consiste nel rispondere alle selezioni dei pulsanti per operazioni quali eliminazione o salvataggio e nel fornire la logica su cui si basano queste richieste. In Sencha Touch vengono usati controller che rimangono in ascolto di questi eventi e rispondono di conseguenza.

	Ext.define('Basic.controller.Main', {
    	extend : 'Ext.app.Controller',

	    config : {
    	    refs : {
        	    todoField : 'main toolbar textfield',
            	main      : 'main'
        	},

	        control : {
    	        'button[action=add]'    : {
        	        tap : 'onAddItem'
            	},
            	'button[action=reload]' : {
                	tap : 'onReload'
            	},

	            main : {
    	            activate      : 'loadInitialData',
        	        itemdoubletap : 'onItemEdit'
            	},

	            'basic-dataitem checkboxfield' : {
    	            change : 'onItemCompleteTap'
        	    },

            	'basic-dataitem button' : {
                	tap : 'onItemDeleteTap'
            	}
        	}
    	},

	    loadInitialData : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onItemDeleteTap : function (button, e, eOpts) {
    	    var store    = Ext.getStore('TodoItems'),
        	    dataItem = button.up('dataitem'),
            	rec      = dataItem.getRecord();

	        rec.erase({
    	        success: function (rec, operation) {
        	        store.remove(rec);
            	},
            	failure: function (rec, operation) {
                	Ext.Msg.alert(
                    	'Error',
                    	Ext.util.Format.format('There was an error deleting this task.<br/><br/>	Status Code: {0}<br/>Status Text: {1}', 
                    	operation.error.status, 
                    	operation.error.statusText)
                	);
            	}
        	});
    	},

	    onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
    	    var dataItem = checkbox.up('dataitem'),
        	    rec      = dataItem.getRecord(),
            	recVal   = rec.get('complete');

	        // this check is needed to prevent an issue where multiple creates get triggered from one create
        	if (newVal !== recVal) {
            	rec.set('complete', newVal);
            	rec.save({
                	success: function (rec, operation) {
                    	rec.commit();
                	},
                	failure: function (rec, operation) {
                    	// since there was a failure doing the update on the server then silently reject the change
	                    rec.reject(true);
    	                Ext.Msg.alert(
        	                'Error',
            	            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
            	            operation.error.status, 
            	            operation.error.statusText)
	                    );
    	            }
        	    });
        	}
    	},

	    onItemEdit : function (list, index, target, record, e, eOpts) {
    	    var rec = list.getSelection()[0];

        	Ext.Msg.prompt('Edit', 'Rename task',
            	function (buttonId, value) {
                	if (buttonId === 'ok') {
                    	rec.set('text', value);
                    	rec.save({
                        	success: function (rec, operation) {
                            	rec.commit();
                        	},
                        	failure: function (rec, operation) {
                            	// since there was a failure doing the update on the server then reject the change
                            	rec.reject();
                            	Ext.Msg.alert(
                                	'Error',
                                	Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                                	operation.error.status, 
                                	operation.error.statusText)
                            	);
                        	}
                    	});
                	}
            	},
            	null,
            	false,
            	record.get('text')
        	);
    	},

	    onReload : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onAddItem : function () {
    	    var me = this,
        	    rec,
            	store = Ext.getStore('TodoItems'),
            	field = me.getTodoField(),
            	value = field.getValue();

	        if (value === '') {
    	        Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
        	}
        	else {
            	rec = Ext.create('Basic.model.TodoItem', {
                	complete : false,
                	text     : value
            	});
            	//store.insert(0, rec); //insert at the top
            	//store.sync();
            	rec.save({
                	success: function (rec, operation) {
                    	store.insert(0, rec); //insert at the top
                    	field.setValue('');
                	},
                	failure: function (rec, operation) {
                    	Ext.Msg.alert(
                        	'Error',
                        	Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                        	operation.error.status, 
                        	operation.error.statusText)
                    	);
                	}
            	});
        	}
    	}
	});

###Combinare tutti gli elementi

**Nome file**: app.js

Il passaggio finale implica la modifica del file principale dell'applicazione, nonché la specifica delle informazioni sui modelli, gli archivi, le visualizzazioni e i controller definiti. I file di origine di tali risorse vengono caricati automaticamente nell'applicazione. Viene infine chiamato il metodo launch, che consente di creare e mostrare la visualizzazione principale dell'applicazione 'Basic.main.View'.


	Ext.Loader.setConfig({
    	enabled : true,
    	paths   : {
        	'Ext'       : 'touch/src',
        	'Ext.azure' : 'packages/azure/src'
    	}
	});

	Ext.application({
    	name : 'Basic',

	    requires : [
    	    'Ext.MessageBox',
        	'Ext.azure.Azure'
    	],

	    views : [
    	    'Main'
    	],

	    controllers : [
    	    'Main'
    	],

	    stores : [
    	    'TodoItems'
    	],

	    azure : {
    	    appUrl : 'YOUR_APP_URL.azure-mobile.net',
        	appKey : 'YOUR_APP_KEY'
    	},

	    icon : {
    	    '57'  : 'resources/icons/Icon.png',
        	'72'  : 'resources/icons/Icon~ipad.png',
        	'114' : 'resources/icons/Icon@2x.png',
        	'144' : 'resources/icons/Icon~ipad@2x.png'
    	},

	    isIconPrecomposed : true,

	    startupImage : {
    	    '320x460'   : 'resources/startup/320x460.jpg',
        	'640x920'   : 'resources/startup/640x920.png',
        	'768x1004'  : 'resources/startup/768x1004.png',
        	'748x1024'  : 'resources/startup/748x1024.png',
        	'1536x2008' : 'resources/startup/1536x2008.png',
        	'1496x2048' : 'resources/startup/1496x2048.png'
    	},

	    launch : function () {
    	    // Destroy the #appLoadingIndicator element
        	Ext.fly('appLoadingIndicator').destroy();

	        // Initialize Azure
    	    Ext.Azure.init(this.config.azure);

	        // Initialize the main view
    	    Ext.Viewport.add(Ext.create('Basic.view.Main'));
	    },

	    onUpdated : function () {
    	    Ext.Msg.confirm(
        	    "Application Update",
            	"This application has just successfully been updated to the latest version. Reload now?",
	            function (buttonId) {
    	            if (buttonId === 'yes') {
        	            window.location.reload();
            	    }
            	}
        	);
   		}
	});

###Ospitare ed eseguire l'app Sencha Touch

La fase finale di questa esercitazione prevede l'hosting e l'esecuzione della nuova app nel computer locale.

  1. Nel terminale passare al percorso dell'applicazione non compressa.

  2. In Sencha Cmd eseguire i comandi seguenti:

    * *sencha app refresh*: In tal modo verranno individuate tutte le dipendenze dell'app
e verranno scaricati gli eventuali pacchetti necessari (ad esempio [Sencha Touch Extensions for Azure](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)).

    * *sencha web start*: verrà avviato un server Web locale per testare l'applicazione..

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. Aprire in un Web browser l'URL indicato nel terminale per avviare l'app, ad esempio http://localhost:1841.

  4. Nell'app digitare un testo significativo, ad esempio _Completare l'esercitazione_ e quindi fare clic su **Add**.

    ![new todo item](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem.

  5. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella TodoItems.

    ![Todo Items table](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

    ![browse todo table](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##Passaggi successivi
Dopo aver completato la Guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili con Sencha:

[Scaricare](https://github.com/arthurakay/sencha-touch-azure-example) un'app di esempio completata con ulteriori definizioni di stile e funzionalità per maggiori informazioni su tutte le operazioni eseguibili con Sencha Touch.

Sono inoltre disponibili altre informazioni su Sencha Touch Extensions for Azure:

  * [Procedura dettagliata](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters) dell'app di esempio
  * [Forum di Sencha](http://www.sencha.com/forum)
  * [Documentazione di Sencha](http://docs.sencha.com/)
  * Utilizzo di Sencha con Servizi mobili di Azure: [(Video)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##Risorse aggiuntive

  * [Download di Sencha Touch](http://pages.sencha.com/touch-for-azure.html)
  * [Sencha Touch Extensions for Azure](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)
 

##Riepilogo

L'esempio illustrato in questa pagina è incluso nel pacchetto Sencha Touch Extensions for Azure ed è disponibile nella directory examples come esempio Basic Data. Sono inoltre disponibili altri esempi in cui vengono illustrate altre funzionalità di queste estensioni, oltre a commenti e spiegazioni dettagliate.

Per altre informazioni introduttive su Sencha Touch, vedere l'insieme completo delle [guide](http://docs.sencha.com/touch/#!/guide)

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png
