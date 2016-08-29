<properties
    pageTitle="Proprietà del ruolo di Azure"
    description="Informazioni su come usare Azure Toolkit per Eclipse per configurare le impostazioni di ruolo di Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# Proprietà del ruolo di Azure #

In Azure Toolkit for Eclipse è possibile specificare diverse impostazioni di configurazione per il ruolo di Azure.

## Configurazione delle proprietà del ruolo di Azure ##

La configurazione delle proprietà del ruolo di Azure viene eseguita tramite le finestre di dialogo delle proprietà per il ruolo di lavoro. Aprire il menu di scelta rapida per il ruolo nel riquadro Project Explorer di Eclipse e selezionare il sottomenu **Azure**. Se il ruolo non viene visualizzato in Eclipse Project Explorer, espandere il progetto di Azure in Project Explorer.

![][ic789599]

Questo argomento descrive le diverse proprietà che è possibile impostare nelle finestre di dialogo **Properties** (Proprietà). Si noti che molte proprietà vengono compilate automaticamente quando si crea un nuovo progetto di distribuzione di Azure.

Per i ruoli di Azure sono disponibili le pagine delle proprietà seguenti.

* [Proprietà della macchina virtuale](#virtual_machine_properties)
* [Proprietà di memorizzazione nella cache](#caching_properties)
* [Proprietà dei certificati](#certificates_properties)
* [Proprietà dei componenti](#components_properties)
* [Proprietà di debug](#debugging_properties)
* [Proprietà di endpoint](#endpoints_properties)
* [Proprietà di variabili di ambiente](#environment_variables_properties)
* [Proprietà di bilanciamento del carico/affinità di sessione (dette anche "sessioni permanenti")](#session_affinity_properties)
* [Proprietà di archiviazione locale](#local_storage_properties)
* [Proprietà di configurazione del server](#server_configuration_properties)
* [Proprietà di offload SSL](#ssl_offloading_properties)
	
<a name="virtual_machine_properties"></a>
### Proprietà della macchina virtuale ###

Aprire il menu di scelta rapida del ruolo nel riquadro Project Explorer di Eclipse, fare clic su **Azure** e quindi su **Properties**. In questa finestra di dialogo è possibile modificare le dimensioni della macchina virtuale e il numero di istanze, come illustrato nell'immagine seguente.

![][ic719499]

>[AZURE.NOTE] Solo Windows: quando si imposta il numero di istanze su un valore maggiore di 1 e si configura un server applicazioni, il toolkit consentirà l'esecuzione di una sola istanza del ruolo nell'emulatore, indipendentemente da questa impostazione. In questo modo si evitano conflitti di binding delle porte tra le diverse istanze del server (ad esempio, tutte le istanze che provano a eseguire il binding alla porta 8080) quando vengono eseguite nello stesso computer. L'impostazione del numero di istanze specificata viene mantenuta, ma diventa effettiva solo in caso di distribuzione nel cloud.

<a name="caching_properties"></a>
### Proprietà di memorizzazione nella cache ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Caching**. In questa finestra di dialogo è possibile abilitare cache denominate con risorse condivise e compatibili con Memcache, che consentono di velocizzare le applicazioni Web.

![][ic719483]

Nella pagina delle proprietà **Caching** è possibile specificare impostazioni globali per le opzioni seguenti:

* Se è abilitata la memorizzazione nella cache con risorse condivise.
* Dimensioni della cache espresse come percentuale della memoria.
* Nome dell'account di archiviazione per salvare lo stato della cache quando l'applicazione viene eseguita come servizio cloud o nessuno se non si vuole salvare lo stato della cache. Il nome dell'account di archiviazione non viene usato quando si esegue l'applicazione nell'emulatore di calcolo. Se si imposta il nome dell'account di archiviazione su **(auto)**, che corrisponde al valore predefinito, la configurazione della memorizzazione nella cache userà automaticamente lo stesso account di archiviazione selezionato nella finestra di dialogo **Publish to Azure**.

>[AZURE.NOTE] L'impostazione **(auto)** avrà l'effetto desiderato solo se si pubblica la distribuzione usando la procedura di pubblicazione guidata del toolkit di Eclipse. Se invece si pubblica manualmente il file con estensione cspkg tramite un meccanismo esterno, ad esempio il [portale di gestione di Azure][], la distribuzione non funzionerà correttamente.

La finestra di dialogo seguente mostra le proprietà di una cache.

![][ic719501]

* **Name:** nome della cache con risorse condivise.
* **Port number:** numero di porta da usare per la cache.
* **Expiration policy:** uno dei valori seguenti che specificano la scadenza di una chiave nella cache.
    * **Absolute:** la chiave scade al raggiungimento del periodo di tempo specificato in **Minutes to live**.
    * **NeverExpires:** alla chiave non è associata un'ora di scadenza.
    * **SlidingWindow:** la chiave scade se non è stata usata per il periodo di tempo specificato in **Minutes to live**. Ogni volta che si accede alla chiave, la scadenza viene reimportata.
* **Minutes to live:** numero massimo di minuti di durata della chiave Memcache, in base ai criteri di scadenza.
* **La disponibilità elevata con backup replicati in istanze del ruolo diverse:** se abilitata, questa opzione fornisce disponibilità elevata usando backup replicati in diverse istanze del ruolo. Si noti che per l'uso di questa funzionalità, devono essere attive almeno due istanze del ruolo per la distribuzione.

Per aggiungere una nuova cache, fare clic sul pulsante **Add** nella pagina delle proprietà **Caching**. Verrà visualizzata la finestra di dialogo **Configure Named Cache**. Specificare i valori per le proprietà descritte sopra.

Per modificare una cache denominata, selezionarla e fare clic sul pulsante**Edit** nella pagina delle proprietà **Caching**. Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della cache. Fare clic su **OK** per salvare i valori della cache.

Per eliminare una cache, selezionarla e fare clic sul pulsante **Remove** nella pagina delle proprietà **Caching** e quindi fare clic su **Yes** per confermare l'eliminazione.

Per altre informazioni sull'uso della funzionalità di memorizzazione nella cache, vedere [Come usare la memorizzazione nella cache con risorse condivise][].

<a name="certificates_properties"></a>
### Proprietà dei certificati ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Certificates**.

![][ic710964]

In questa finestra di dialogo è possibile aggiungere o rimuovere i certificati a cui fa riferimento il progetto Eclipse. Si noti che i certificati elencati in questa finestra di dialogo non vengono archiviati automaticamente in un archivio delle chiavi Java e quindi non sono disponibili automaticamente per l'uso in un'applicazione Java. Vengono solo registrati con Azure per poter essere precaricati nell'archivio certificati di Windows delle macchine virtuali che eseguono la distribuzione e usati successivamente con altro software Windows. Attualmente, la sola funzionalità del toolkit che usa i certificati a cui viene fatto riferimento in questo modo nella finestra di dialogo **Certificates** è [Offload SSL][], per via della dipendenza da Internet Information Services (IIS) e Application Request Routing (ARR), secondo cui il certificato appropriato deve essere reso disponibile in questo modo.

Quando si distribuisce il progetto in Azure tramite la pubblicazione guidata, verrà richiesto di puntare ai file PFX (Personal Information Exchange) corrispondenti a questi certificati, insieme alle relative password, per poterli caricare automaticamente nel servizio di Azure, ma solo se non sono già stati caricati in precedenza.

<a name="components_properties"></a>
### Proprietà dei componenti ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Components**. In questa finestra di dialogo è possibile aggiungere, modificare o rimuovere i componenti del ruolo e modificare l'ordine in cui vengono elaborati.

![][ic719502]

La funzionalità Components consente di aggiungere dipendenze al progetto di distribuzione di Azure, ad esempio progetti di applicazioni Java, file speciali e istruzioni della riga di comando eseguibili necessari per la distribuzione.

Per ogni componente, è possibile specificare:

* Il passaggio da eseguire quando si importa il componente nel progetto di distribuzione di Azure al momento della compilazione.
* Il passaggio da eseguire quando si distribuisce il componente nel cloud di Azure.

>[AZURE.NOTE] Quando si specificano i file dei componenti o le righe di comando, tenere presente che la distribuzione verrà pubblicata in una macchina virtuale di Windows, quindi i passaggi personalizzati devono essere validi per un sistema operativo Windows.

Di seguito sono illustrate le proprietà di Components:

* **Import:** metodo che indica la modalità di importazione del componente nel progetto al momento della compilazione. Può essere uno dei valori seguenti:
    * **copy:** Il componente viene copiato dal percorso locale specificato nella proprietà **From** alla directory **approot** del ruolo.
    * **EAR:** il componente è un file EAR (Enterprise ARchive) Java importato da un Enterprise Application Project nel percorso locale specificato nella proprietà **From**. Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
    * **JAR:** il componente è un file JAR (Java Archive) e viene importato da un progetto Java nel percorso locale specificato nella proprietà **From**. Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
    * **none:** non viene eseguita alcuna azione per importare il componente. Questo valore è applicabile quando si presuppone che il componente sia già presente nella directory **approot** del ruolo o quando il componente è semplicemente un'istruzione della riga di comando eseguibile, secondo quanto specificato nella proprietà **As** quando il metodo **Deploy** è **exec**.
    * **WAR:** il componente è un file WAR (Web application ARchive) Java e viene importato da un progetto Java nel percorso locale specificato nella proprietà **From**. Viene rilevato automaticamente dal toolkit in base alla natura del progetto in quella posizione.
    * **zip:** il componente è un file ZIP e viene importato comprimendo la directory o il file specificato nella proprietà **From**.
* **From:** percorso di origine nel computer locale alla cartella o al file che rappresenta l'elemento o gli elementi da importare nella distribuzione. In questa proprietà è possibile usare variabili di ambiente di Windows. Tutti i componenti importabili saranno importati nella directory **approot** del ruolo al momento della compilazione del progetto.
	
	Si noti che è possibile distribuire un componente da un download quando si esegue la distribuzione nel cloud (non nell'emulatore di calcolo). Vedere le informazioni correlate seguenti sull'aggiunta di un componente.
	
* **As:** nome file con cui il componente verrà importato nella directory **approot** del ruolo e quindi distribuito nel cloud di Azure. Per mantenere lo stesso nome presente nel computer locale, lasciare vuota questa proprietà. Per i componenti eseguibili, ovvero quelli il cui metodo **Deploy** è impostato su **exec**, può essere un'istruzione arbitraria della riga di comando di Windows.

	>[AZURE.IMPORTANT] Se per questo valore si usano spazi, saranno gestiti diversamente a seconda del metodo di distribuzione. Se il metodo di distribuzione è **exec**, gli spazi saranno interpretati come separatori di argomenti della riga di comando e non come parte del nome file. Per tutti gli altri metodi di distribuzione, gli spazi saranno interpretati come parte del nome file.
	
* **Deploy:** metodo che indica l'azione applicata al componente quando viene avviata la distribuzione. Può essere uno dei valori seguenti:
    * **copy:** Il componente viene copiato nel percorso di destinazione specificato nella proprietà **To**.
    * **exec:** Il componente è un'istruzione eseguibile della riga di comando di Windows eseguita nel contesto del percorso specificato nella proprietà **To**, all'avvio della distribuzione.
    * **none:** non viene applicata alcuna azione al componente all'avvio della distribuzione.
    * **zip:** il componente viene decompresso nel percorso di destinazione specificato nella proprietà **To**. Questo metodo è disponibile solo quando la proprietà **Import** è **zip**.
* **To:** percorso di destinazione nella macchina virtuale in cui verrà distribuito il componente. In questa proprietà è possibile usare le variabili di ambiente Windows e i percorsi di file sono relativi alla directory **approot**.
	
Per aggiungere un nuovo componente, fare clic sul pulsante **Add** nella pagina delle proprietà **Components**. Verrà visualizzata la finestra di dialogo **Azure Role Component**. Specificare i valori per le proprietà descritte sopra.

L'esempio seguente mostra come aggiungere un nuovo componente WAR.

![][ic719503]

Quando si esegue la distribuzione nel cloud (non nell'emulatore di calcolo), se si vuole distribuire il componente da un download, assicurarsi che **When in cloud, instead of including in the package, deploy from** sia selezionata. Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo dall'elenco a discesa **Storage account**. È possibile fare clic sul collegamento**Accounts** per modificare il contenuto dell'elenco. In questo modo, verrà compilato parzialmente il campo **URL** e quindi verrà compilata la parte rimanente dell'URL. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** dall'elenco a discesa **Storage account** e quindi immettere l'URL del componente nel campo **URL**. Specificare uno dei metodi seguenti:

* **copy:** il componente scaricato viene copiato nel percorso di destinazione specificato in **To Directory**.
* **Same:** lo stesso metodo usato per **Deploy from download** e per **Deploy from package**.
* **zip:** il componente scaricato viene decompresso nel percorso di destinazione specificato in **To Directory**.

Per modificare un componente, selezionarlo e fare clic sul pulsante **Edit** nella pagina delle proprietà**Components**. Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà del componente. Fare clic su **OK** per salvare i valori del componente.

Per eliminare un componente, selezionarlo e fare clic sul pulsante **Remove** nella pagina delle proprietà **Components** e quindi fare clic su **Yes** per confermare l'eliminazione.

I componenti vengono elaborati nell'ordine elencato. Usare i pulsanti **Move Up** e **Move Down** per modificare l'ordine.

>[AZURE.NOTE] La funzionalità di configurazione del server si basa anche sui componenti. Questi componenti non possono essere rimossi o modificati senza rimuovere la configurazione del server corrispondente. Quando si prova ad apportare modifiche a tali componenti, verrà visualizzato un avviso a questo proposito.

<a name="debugging_properties"></a>
### Proprietà di debug ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Debugging**. In questa finestra di dialogo è possibile abilitare o disabilitare il debug remoto e creare configurazioni di debug, come illustrato nell'immagine seguente.

![][ic719504]

Per informazioni correlate, vedere[ Debug delle applicazione Azure in Eclipse][].

<a name="endpoints_properties"></a>
### Proprietà di endpoint ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Endpoints**. In questa finestra di dialogo è possibile creare un endpoint e modificare o rimuovere un endpoint, come illustrato nell'immagine seguente.

![][ic719505]

Per aggiungere un endpoint, fare clic sul pulsante **Add** nella pagina delle proprietà **Endpoints**. Verrà aperta la finestra di dialogo **Add Endpoint**.

![][ic710897]

Immettere un nome per l'endpoint, selezionare il tipo, **Input**, **Internal** o **InstanceInput**, e specificare la porta privata e quella pubblica. Fare clic su **OK** per salvare i nuovi valori dell'endpoint.

A seconda del tipo di endpoint, è possibile usare intervalli di porte come indicato di seguito:

* Per un endpoint dell'istanza di input, la porta pubblica può essere un intervallo di porte, ad esempio **2000-2010**, e la porta privata è un valore fisso.
* Per un endpoint interno, la porta pubblica non viene usata e la porta privata può essere un intervallo o essere lasciata vuota o impostata su un asterisco per indicare che viene impostata automaticamente da Azure.
* Per gli endpoint di input, la porta pubblica può essere solo un valore fisso e la porta privata può essere un valore fisso o essere lasciata vuota o impostata su un asterisco per indicare che viene impostata automaticamente da Azure.

Se si vuole usare un singolo numero di porta anziché un intervallo, lasciare vuota la casella di testo relativa alla fine dell'intervallo.

Per le porte impostate su automatico, se è necessario determinare quale porta viene effettivamente usata in fase di esecuzione, l'applicazione può usare l'API di runtime del servizio Azure, documentata nel [riepilogo del pacchetto com.microsoft.windowsazure.serviceruntime][].

Per informazioni su come usare gli endpoint di input dell'istanza per facilitare il debug di una distribuzione a istanze multiple, vedere [Debug di un'istanza del ruolo specifica in una distribuzione con istanze multiple][].

Per modificare un endpoint, selezionarlo e fare clic sul pulsante **Edit** nella pagina delle proprietà **Endpoints**. Verrà aperta una finestra di dialogo in cui è possibile modificare il nome dell'endpoint, il tipo e le porte pubblica e privata. Fare clic su **OK** per salvare i valori dell'endpoint modificati.

Per eliminare un endpoint, selezionarlo e fare clic sul pulsante **Remove** nella pagina delle proprietà **Endpoints**, quindi fare clic su **Yes** per confermare l'eliminazione.

Per configurare correttamente alcune funzionalità, ad esempio, Caching, Remote Debugging, Session Affinity o SSL offloading, abilitate dall'utente in un ruolo, il toolkit è in grado di configurare automaticamente endpoint speciali che verranno elencati insieme a quelli definiti dall'utente. Il toolkit impedisce all'utente di modificare o eliminare gli endpoint generati automaticamente finché è abilitata la funzionalità associata.

<a name="environment_variables_properties"></a>
### Proprietà di variabili di ambiente ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Environment Variables**. In questa finestra di dialogo è possibile creare una variabile di ambiente e modificare o rimuovere una variabile di ambiente, come illustrato nell'immagine seguente.

![][ic719506]

Le variabili di ambiente sono disponibili per lo script di avvio all'avvio del ruolo.

>[AZURE.NOTE] Quando si specificano variabili di ambiente, tenere presente che la distribuzione verrà pubblicata in una macchina virtuale di Windows, quindi tali variabili di ambiente devono essere valide per un sistema operativo Windows.

Per un esempio di variabile di ambiente disponibile all'avvio del ruolo, creare una nuova variabile di ambiente facendo clic sul pulsante **Add**. Di seguito è illustrata la creazione di una variabile di ambiente denominata **MyRoleVersion** a cui viene assegnato il valore **1.0**.

![][ic659268]

Nel codice jsp code è possibile visualizzare il valore usando il metodo `System.getenv`:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Questo è l'output risultante quando viene eseguita l'applicazione:

![][ic552233]

Per modificare una variabile di ambiente, selezionarla e fare clic sul pulsante **Edit** nella pagina delle proprietà **Environment Variables**. Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della variabile di ambiente. Fare clic su **OK** per salvare i valori delle variabili di ambiente.

Per eliminare una variabile di ambiente, selezionarla e fare clic sul pulsante **Remove** nella pagina delle proprietà **Environment Variables** e quindi fare clic su **Yes** per confermare l'eliminazione.

Per configurare correttamente alcune funzionalità (ad esempio, Server Configuration, Remote Debugging o Local Storage) abilitate dall'utente in un ruolo, il toolkit è in grado di configurare automaticamente variabili di ambiente speciali che verranno elencate insieme a quelle definite dall'utente. Il toolkit impedisce all'utente di modificare o eliminare le variabili di ambiente generate automaticamente finché è abilitata la funzionalità associata.

<a name="session_affinity_properties"></a>
### Proprietà di bilanciamento del carico/affinità di sessione (dette anche "sessioni permanenti") ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Load Balancing**. In questa finestra di dialogo è possibile abilitare o disabilitare l'affinità di sessione, come illustrato nell'immagine seguente.

![][ic719492]

Per informazioni correlate, vedere [Affinità di sessione][]. Si noti anche il comportamento di questa funzionalità nel contesto di offload SSL, come descritto in [Offload SSL][].

<a name="local_storage_properties"></a>
### Proprietà di archiviazione locale ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Local Storage**. In questa finestra di dialogo è possibile creare, modificare o rimuovere lo spazio di archiviazione locale temporaneo per la macchina virtuale che esegue l'applicazione. È possibile impostare valori specifici per le dimensioni dello spazio di archiviazione locale e specificare se il contenuto viene mantenuto quando il ruolo viene riciclato, come illustrato nella figura seguente.

![][ic719508]

È anche possibile specificare facoltativamente una variabile di ambiente corrispondente all'archiviazione locale.

Per impostazione predefinita, tutto ciò che si distribuisce in Azure viene decompresso e inserito nella cartella **approot** dell'istanza del ruolo. Anche se le dimensioni della cartella sono sufficienti per la maggior parte delle distribuzioni semplici anche dopo la decompressione, lo spazio allocato per la directory **approot** è limitato e non ben definito (meno di 1 GB è una regola generale ragionevole). Per assicurare quindi che in Azure venga allocato spazio su disco sufficiente per le implementazioni più grandi che potrebbero non rientrare nella cartella **approot**, è consigliabile configurare una risorsa di archiviazione locale tramite la finestra di dialogo **Local Storage**. Per un modo semplice per eseguire questa operazione, vedere [Distribuzione di distribuzioni di grandi dimensioni][].

È possibile fare riferimento facilmente alla risorsa di archiviazione degli script di avvio, ad esempio **startup.cmd**, usando la variabile di ambiente associata automaticamente alla risorsa dal toolkit per Eclipse, come illustrato nella finestra di dialogo **Local Storage**. Tale variabile di ambiente conterrà il percorso completo della risorsa locale configurata al momento dell'esecuzione dello script di avvio.

Per modificare la risorsa di archiviazione locale, selezionarla e fare clic sul pulsante **Edit** nella pagina delle proprietà **Local Storage**. Verrà aperta una finestra di dialogo in cui è possibile modificare le proprietà della risorsa di archiviazione locale. Fare clic su **OK** per salvare i valori della risorsa di archiviazione locale.

Per eliminare una risorsa di archiviazione locale, selezionarla e fare clic sul pulsante **Remove** nella pagina delle proprietà **Local Storage**, quindi fare clic su **Yes** per confermare l'eliminazione.

<a name="server_configuration_properties"></a>
### Proprietà di configurazione del server ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **Server Configuration**. In questa finestra di dialogo è possibile aggiungere, rimuovere e modificare JDK e il server applicazioni Java usato nella distribuzione e aggiungere o rimuovere le applicazioni, ad esempio file WAR, JAR o EAR, usate nella distribuzione.

### Configurazione di JDK ###

Questa finestra di dialogo consente di specificare il pacchetto JDK da usare per la distribuzione. Se si usa Eclipse in Windows, è possibile specificare il pacchetto JDK da usare in locale durante l'esecuzione nell'emulatore di Azure e scegliere di distribuire tale installazione locale in Azure. Nei sistemi operativi non Windows non è applicabile l'impostazione JDK dell'emulatore e non è possibile distribuire il pacchetto JDK installato localmente perché non è compatibile con Windows. Tuttavia, indipendentemente dal sistema operativo in uso, è possibile scegliere tra i pacchetti JDK di terze parti per la distribuzione in Azure o selezionare il proprio pacchetto JDK compatibile con Windows da un percorso di download alternativo.

Di seguito è riportato un esempio di come è possibile specificare un pacchetto JDK in Windows:

![][ic780647]

Se si usa Eclipse in Windows, è possibile specificare un pacchetto JDK da usare con l'emulatore di calcolo. A questo scopo, assicurarsi che **Use the JDK from this file path for testing locally** sia selezionata nella sezione **Emulator deployment**. Specificare quindi il percorso locale del pacchetto JDK. È possibile cercare un JDK diverso se quello che si vuole usare non viene selezionato automaticamente. È anche possibile distribuire il proprio JDK nel servizio cloud di Azure. A questo scopo, selezionare l'opzione **Deploy my local JDK (auto-upload to cloud storage)** nella sezione **Cloud deployment**.

Nota: nei sistemi operativi non Windows le impostazioni **Emulator deployment** e l'opzione **Deploy my local JDK** non sono disponibili. L'esempio seguente illustra come specificare un pacchetto JDK in un Mac o in un altro sistema operativo non Windows supportato:

![][ic789643]

Indipendentemente dal sistema operativo in uso, sono disponibili le due opzioni **Cloud deployment** seguenti per l'origine e il tipo di pacchetto JDK:

* **Deploy a 3rd party JDK package available on Azure**
* **Deploy from a custom download**

Se si usa l'opzione **Deploy a 3rd party JDK package available from Azure**:

1. Selezionare la casella di controllo denominata **Deploy a 3rd party JDK package available from Azure**.
1. Nell'elenco a discesa selezionare il pacchetto JDK di terze parti disponibile in Azure.
1. La scheda **JDK**sarà simile alla seguente in Windows: ![][ic780648] In Mac OS o negli altri sistemi operativi non Windows supportati la scheda sarà simile alla seguente: ![][ic789643]
1. Fare clic su **OK** per salvare le modifiche.
1. Quando viene richiesto di accettare il contratto di licenza del provider del pacchetto JDK di terze parti, leggere le condizioni di licenza. Presupponendo che si accettino le condizioni, fare clic su **Yes** per chiudere la finestra di dialogo **Accept license agreement**. Si noti che è possibile modificare la logica sottostante relativa agli elementi da visualizzare nell'elenco a discesa dell'opzione**Deploy a 3rd party JDK package available from Azure**. Per personalizzare gli elementi, nella finestra di dialogo **JDK** fare clic sul collegamento **Personalizza**. Verrà chiusa la pagina delle proprietà **JDK** e in Eclipse verrà aperto il file **componentsets.xml** che può essere modificato in base alle esigenze. La documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml**.

Se si usa l'opzione **Deploy a JDK from a custom download** :

1. Creare un file ZIP della directory di installazione di JDK, assicurandosi che il nodo stesso della directory sia l'elemento figlio della struttura dello ZIP e non il suo contenuto. Prendere nota del nome della directory, che sarà necessario in seguito, e tenere presente che questa installazione di JDK verrà distribuita in una macchina virtuale di Windows.
1. Caricare il file ZIP nell'account di archiviazione di Azure come BLOB. È possibile eseguire questa operazione con uno strumento disponibile esternamente per caricare BLOB nell'archiviazione di Azure. È consigliabile usare un BLOB privato. Prendere nota dell'URL del BLOB del contenuto del file ZIP.
1. Selezionare la casella di controllo denominata **Deploy a JDK from a custom download**. Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo dall'elenco a discesa **Storage account**. È possibile fare clic sul collegamento**Accounts** per modificare il contenuto dell'elenco. In questo modo, verrà compilato parzialmente il campo **URL** e quindi verrà compilata la parte rimanente dell'URL. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** dall'elenco a discesa **Storage account** e quindi immettere l'URL del download di JDK nel campo **URL**. Se si usa l'archiviazione di Azure, è necessario che i nomi di BLOB nell'URL siano specificati in lettere minuscole.
1. Assicurarsi che la casella di testo **JAVA\_HOME** faccia riferimento al nome della directory corretta. Per impostazione predefinita, farà riferimento al nome della directory JDK con lo stesso valore scelto per l'uso locale. Se tuttavia la directory contenuta nel file ZIP ha un nome diverso, ad esempio, a causa dell'uso di una versione diversa, aggiornare di conseguenza il nome della directory nella casella di testo**JAVA\_HOME**, perché questa impostazione verrà usata nel cloud (non nell'emulatore di calcolo).
1. Fare clic su **OK** per salvare le modifiche.

È tutto. A questo punto, quando si compila per il cloud, si noterà che le dimensioni del pacchetto saranno più ridotte, il processo di compilazione richiederà in genere meno tempo e anche la pubblicazione nel cloud della distribuzione stessa richiederà meno tempo. Si noti che l'opzione **Deploy my local JDK (auto-upload to cloud storage)** o **Deploy a JDK from a custom download** è effettiva solo quando l'applicazione viene distribuita nel cloud. Queste opzioni non hanno alcun effetto sull'esperienza dell'emulatore di calcolo. La versione locale dei componenti verrà comunque usata durante la distribuzione nell'emulatore di calcolo.

### Configurazione del server ###

Di seguito è riportato un esempio di come è possibile specificare un server applicazioni.

![][ic796926]

Verificare che la casella di controllo **Deploy a server of this type** sia selezionate e quindi scegliere il tipo di server applicazioni che si vuole usare.

Per specificare un server da usare per la distribuzione nel cloud, è possibile usare le seguenti opzioni:

1. **Deploy a 3rd party server available on Azure**: questa opzione è applicabile in particolare in scenari di sviluppo e/o test in cui l'efficienza e la semplicità di distribuzione costituiscono le priorità e il server non richiede una configurazione personalizzata. L'opzione è applicabile anche se si vuole usare uno di questi server come punto di partenza, includendo passaggi appropriati di personalizzazione del server nella logica di avvio della distribuzione.
1. **Deploy from a custom download**: questa opzione è applicabile in particolare negli scenari di produzione, quando si ha un server appositamente preparato e configurato che si vuole usare nel cloud.
1. **Deploy my local server installation**: questa opzione è applicabile in particolare se l'installazione del server locale è già configurata in modo personalizzato per l'uso. Se si sceglie questa opzione, è necessario specificare anche il percorso del server locale nella casella di testo **Local server path** seguente.

Se si usa l'opzione **Deploy a 3rd party server available on Azure**:

1. Selezionare la casella di controllo denominata **Deploy a 3rd party server available on Azure**.
1. Nel menu a discesa selezionare il software server desiderato da usare con la distribuzione nel cloud. Se in precedenza è già stato specificato un tipo di server da usare, si potrà scegliere solo un server cloud appartenente alla stessa famiglia di quel tipo di server. Ma se non si è scelto un tipo di server, è possibile scegliere un server qualsiasi tra quelli attualmente disponibili in Azure. Il tipo di server verrà selezionato automaticamente.
1. Fare clic su **OK** per salvare le modifiche.

Se si usa l'opzione **Deploy from a custom download**:

1. Assicurarsi di avere già selezionato un tipo di server seguendo i passaggi precedenti. In questo modo verrà indicata al plug-in la modalità di distribuzione del server dal download personalizzato, che deve appartenere alla stessa famiglia del tipo di server selezionato.
1. Selezionare la casella di controllo denominata **Deploy from a custom download**. Se si vuole effettuare il download dall'account di archiviazione di Azure, selezionarlo dall'elenco a discesa **Storage account**. È possibile fare clic sul collegamento**Accounts** per modificare il contenuto dell'elenco. In questo modo, verrà compilato parzialmente il campo **URL** e quindi verrà compilata la parte rimanente dell'URL. del file ZIP di download del server. Quando si usa l'archiviazione di Azure, i nomi di BLOB nell'URL devono essere specificati in lettere minuscole. Se non si vuole usare l'archiviazione di Azure, selezionare **(none)** dall'elenco a discesa **Storage account** e quindi immettere l'URL del file ZIP di download del server nel campo **URL**. Il file ZIP contiene una cartella figlio che rappresenta la directory di installazione del server applicazioni. Se ad esempio si usa un file ZIP per Apache Tomcat 7.0.35, il file conterrà una cartella figlio che rappresenta la directory di installazione, ad esempio **apache-tomcat-7.0.35**.
1. Specificare il valore per la variabile di ambiente della home directory. Per impostazione predefinita, verrà usato il valore del server applicazioni locale, se disponibile, ma è possibile specificare un valore diverso se il server applicazioni cloud è diverso da quello locale. Tuttavia, è necessario assicurarsi che il server applicazioni cloud sia della stessa famiglia del tipo di server selezionato in precedenza. Se in futuro si aggiorna il file ZIP del server applicazioni cloud, è possibile modificare manualmente l'impostazione della home directory oppure fare in modo che corrisponda di nuovo all'impostazione locale, se è stato modificato anche il server applicazioni locale.
1. Fare clic su **OK** per salvare le modifiche.

La logica sottostante relativa agli elementi visualizzati nella scheda **Server** della pagina delle proprietà **Server Configuration** può essere personalizzata. Si tratta di una funzionalità avanzata che potrebbe essere utile se le proprie esigenze si estendono oltre i valori predefiniti o se si vuole aggiungere altri server. Per personalizzare la logica nella finestra di dialogo **Server** fare clic sul collegamento **Customize**. Verrà chiusa la pagina delle proprietà **Server Configuration** e il file **componentsets.xml** verrà aperto in Eclipse, dove potrà essere modificato secondo le esigenze per estendere il modello di configurazione del server. La documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml**.

Se si usa l'opzione il **Deploy my local server (auto-upload to cloud storage)**:

1. Selezionare la casella di controllo denominata **Deploy my local server (auto-upload to cloud storage)**.
1. Nell'elenco a discesa **Storage account** selezionare **(auto)**. Se si specifica **(auto)**, il toolkit per Eclipse userà per il server lo stesso account di archiviazione selezionato per la distribuzione nella finestra di dialogo **Publish to Azure**.
1. Fare clic su **OK** per salvare le modifiche.

Selezionare un percorso di installazione del server in questo computer nella casella di testo **Local server path**, se viene soddisfatta una delle seguenti condizioni:

* Si vuole testare la distribuzione nell'emulatore (solo per Windows).
* Si vuole distribuire il server installato localmente nel cloud.
* Si vuole usare un download di server personalizzato nel cloud. In questo caso assicurarsi anche che sopra sia selezionata l'opzione **Deploy my local server (auto-upload to cloud storage)**.

Se nessuna delle opzioni precedenti è applicabile, l'impostazione del server locale è facoltativa.

### Configurazione di applicazioni ###

Di seguito è riportato un esempio di come è possibile specificare un'applicazione.

![][ic719512]

Fare clic su **Add** per aggiungere un'altra applicazione o su **Remove** per rimuovere un'applicazione. Ai fini dell'efficienza, se si vuole usare un download per l'origine di un'applicazione in caso di distribuzione nel cloud, usare le [proprietà dei componenti](#components_properties) per specificare un URL, l'account di archiviazione e così via.

A partire dalla versione di aprile 2014, le applicazioni vengono caricate automaticamente nello stesso account di archiviazione (nel contenitore **eclipsedeploy**) selezionato per la distribuzione. La logica di avvio della distribuzione include un passaggio che prevede prima di tutto il download di tali applicazioni dall'account di archiviazione. Questo significa che si possono aggiornare le applicazioni nella distribuzione senza dover ricompilare e ridistribuire l'intero pacchetto. A questo scopo caricare manualmente le versioni più recenti dell'applicazione direttamente in tale account di archiviazione, ad esempio tramite il portale di Azure, e sostituire i file WAR originariamente caricati dal toolkit. Iniziare quindi semplicemente a riciclare tutte le istanze del ruolo usando di nuovo il portale di gestione di Azure oppure tramite le utilità della riga di comando. L'attivazione del riciclo del ruolo direttamente dal toolkit per Eclipse non è attualmente supportata.

### Note sulla configurazione del server ###

Le modifiche apportate nella pagina delle proprietà **Server configuration** vengono riflesse negli elementi `<component>` del file package.xml.

Quando si usa l'opzione **Automatically upload...** o **Deploy from download...** per JDK o per il server applicazioni durante la compilazione per il cloud, non l'emulatore di calcolo, e si è connessi alla rete, nell'output della console è possibile che vengano visualizzati messaggi di compilazione come il seguente, perché il compilatore Ant verifica la disponibilità del download:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...`

Se è stata selezionata l'opzione **Deploy from download...**, è possibile che venga visualizzato il seguente avviso, ma la compilazione continuerà:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).`

Questo avviso è la sola indicazione che la disponibilità del download non è stata verificata. Di conseguenza, se la distribuzione nel cloud non riesce per un motivo qualsiasi, verificare se è stato visualizzato questo avviso.

Per disabilitare la verifica del download, ad esempio se si ritiene che rallenti inutilmente la compilazione, impostare l'attributo `verifydownloads` su `false` nell'elemento `<windowsazurepackage>` del file package.xml:

`<windowsazurepackage verifydownloads="false" ...>`

Se è stata selezionata l'opzione **Automatically upload...**, nella finestra della console verranno visualizzati messaggi di compilazione che indicano lo stato di avanzamento del caricamento ogni 5 secondi, ogni volta che è necessaria un'operazione di caricamento.

<a name="ssl_offloading_properties"></a>
### Proprietà di offload SSL ###

Aprire il menu di scelta rapida relativo al ruolo nel riquadro Project Explorer di Eclipse, scegliere **Azure** e quindi **SSL Offloading**.

![][ic719481]

In questa finestra di dialogo è possibile abilitare l'offload SSL, che consente di abilitare facilmente il supporto del protocollo HTTPS ( Hypertext Transfer Protocol Secure) nella distribuzione Java in Azure, senza dover configurare SSL nel server applicazioni Java. Per altre informazioni, vedere l'articolo relativo all'[offload SSL][] e l'articolo su [come usare l'offload SSL][].

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Installare il Toolkit di Azure per Eclipse.][]

[Creazione di un'applicazione Hello World per Azure in Eclipse][]

[Proprietà del progetto Azure][]

[Elenco di account di archiviazione di Azure][]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][].

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Proprietà del progetto Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Elenco di account di archiviazione di Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[riepilogo del pacchetto com.microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creazione di un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debug di un'istanza del ruolo specifica in una distribuzione con istanze multiple]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[ Debug delle applicazione Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Distribuzione di distribuzioni di grandi dimensioni]: http://go.microsoft.com/fwlink/?LinkID=699536
[Come usare la memorizzazione nella cache con risorse condivise]: http://go.microsoft.com/fwlink/?LinkID=699542
[come usare l'offload SSL]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[Affinità di sessione]: http://go.microsoft.com/fwlink/?LinkID=699548
[Offload SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!---HONumber=AcomDC_0817_2016-->