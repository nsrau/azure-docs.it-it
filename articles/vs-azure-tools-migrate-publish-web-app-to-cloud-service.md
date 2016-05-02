<properties
   pageTitle="Come eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio | Microsoft Azure"
   description="Procedura per eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="04/19/2016"
   ms.author="tarcher" />

# Procedura: Eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio

Per sfruttare i servizi di hosting e scalabilità di Azure, è possibile eseguire la migrazione e la pubblicazione dell'applicazione Web su un servizio cloud di Azure. È possibile eseguire un'applicazione Web in Azure con modifiche minime all'applicazione esistente.

>[AZURE.NOTE] Oggetto di questo argomento è la distribuzione in servizi cloud, non in siti Web. Per informazioni sulla distribuzione in siti Web, vedere [Distribuire un'app Web nel servizio app di Azure](./app-service-web/web-sites-deploy.md).

Per un elenco di modelli specifici supportati sia per Visual C# che per Visual Basic, vedere la sezione **Modelli di progetto supportati** più avanti in questo argomento.

È innanzitutto necessario abilitare l'applicazione Web per Azure da Visual Studio. La seguente figura mostra i passaggi chiave per pubblicare l'applicazione Web esistente mediante l'aggiunta di un progetto Azure da utilizzare per la distribuzione. Questo processo aggiunge alla soluzione un progetto Azure con il ruolo Web richiesto. In base al tipo di progetto Web di cui si dispone, le proprietà del progetto per gli assembly vengono aggiornate anche se il pacchetto del servizio richiede ulteriori assembly per la distribuzione.

![Pubblicare un'applicazione Web in Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] Il comando **Converti**, **Converti in progetto Servizio cloud Microsoft Azure** è visualizzato solo per il progetto Web nella soluzione. Ad esempio, il comando non è disponibile per un progetto Silverlight nella soluzione. Quando si crea un pacchetto del servizio o si pubblica l'applicazione in Azure, possono verificarsi avvisi o errori. Questi avvisi ed errori possono aiutare a risolvere i problemi prima della distribuzione in Azure. Ad esempio, si potrebbe ricevere un avviso relativo a un assembly mancante. Per altre informazioni su come gestire eventuali avvisi come errori, vedere [Configurare un progetto di servizio cloud di Azure con Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se si compila l'applicazione, la si esegue in locale con l'emulatore di calcolo o la si pubblica in Azure, si potrebbe visualizzare il seguente errore nella finestra **Elenco errori**: **Percorso e/o nome di file specificato troppo lungo**. Questo errore si verifica perché la lunghezza del nome completo del progetto di Azure è troppo lunga. La lunghezza del nome del progetto, incluso il percorso completo, non può essere superiore a 146 caratteri. Ad esempio, questo è il nome di progetto completo, incluso il percorso di file per un progetto Azure creato per un'applicazione Silverlight: `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Potrebbe essere necessario spostare la soluzione in una directory diversa con un percorso più breve, per ridurre la lunghezza del nome completo del progetto.

Per eseguire la migrazione e la pubblicazione di un'applicazione Web in Azure da Visual Studio, seguire questi passaggi.

## Abilitare un'applicazione Web per la distribuzione in Azure

### Per abilitare un'applicazione Web per la distribuzione in Azure

1. Per abilitare l'applicazione Web per la distribuzione in Azure, aprire il menu di scelta rapida per un progetto Web nella soluzione e scegliere il pulsante per aggiungere il progetto di distribuzione di Azure.

    Si verificano le seguenti azioni:

    - Un progetto Azure chiamato `<name of the web project>.Azure` viene aggiunto alla soluzione per l'applicazione.

    - Un ruolo Web per il progetto Web viene aggiunto al progetto Azure.

    - La proprietà **Copia localmente** viene impostata su true per qualsiasi assembly necessario per MVC 2, MVC 3, MVC 4 e applicazioni aziendali di Silverlight. Questo aggiunge questi assembly al pacchetto del servizio utilizzato per la distribuzione.

  >[AZURE.IMPORTANT]Se si dispone di altri assembly o file necessari per l'applicazione Web, è necessario impostare manualmente le proprietà di questi file. Per informazioni su come impostare queste proprietà, vedere la sezione **Includere file nel pacchetto del servizio** più avanti in questo articolo.  

  >[AZURE.NOTE] Se esiste già un ruolo Web per un progetto Web specifico in un progetto Azure nella soluzione, i comandi **Converti**, **Converti in Progetto Servizio cloud Microsoft Azure** non vengono visualizzati nel menu di scelta rapida per questo progetto Web.

  Se si dispone di più progetti Web nell'applicazione web e si desidera creare ruoli Web per ciascun progetto Web, è necessario eseguire i passaggi in questa procedura per ciascun progetto Web. Questo crea progetti Azure distinti per ogni ruolo Web. Ciascun progetto Web può essere pubblicato separatamente. In alternativa, è possibile aggiungere manualmente un altro ruolo Web a un progetto Azure esistente nell'applicazione Web. A tale scopo, aprire il menu di scelta rapida per la cartella **Ruoli** nel progetto Azure, scegliere **Aggiungi**, quindi **Progetto ruolo Web nella soluzione**, scegliere il progetto da aggiungere come ruolo Web e quindi scegliere il pulsante **OK**.

## Utilizzare un Database SQL Azure per l'applicazione

Se si dispone di una stringa di connessione per l'applicazione Web che utilizza un database SQL Server locale, è necessario modificare questa stringa di connessione per utilizzare un'istanza del database SQL ospitata da Azure.

>[AZURE.IMPORTANT] La sottoscrizione deve consentire di utilizzare il database SQL. Se si accede alla sottoscrizione dal portale di gestione di Azure, è possibile determinare quali servizi sono forniti dall'abbonamento. Le istruzioni seguenti si applicano al portale di gestione rilasciato. Se si utilizza il portale di gestione di anteprima, passare alla procedura successiva.|

### Per utilizzare un'istanza di database SQL nel ruolo Web della stringa di connessione

1. Per creare un'istanza del database SQL nel portale di gestione di Azure, seguire i passaggi nel seguente articolo: [Creare un server di database SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Quando si impostano le regole del firewall per l'istanza del database SQL, è necessario selezionare la casella di controllo **Consenti ad altri servizi di Azure di accedere a questo server**.

1. Per creare un'istanza del database SQL da usare per la stringa di connessione, seguire i passaggi nella sezione successiva nell'articolo seguente: [Creare un database SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Per copiare la stringa di connessione ADO.NET da utilizzare per la stringa di connessione, eseguire i passaggi seguenti nel portale di gestione per Azure.

  1. Scegliere il pulsante **Database**, quindi aprire il nodo per la sottoscrizione utilizzata per creare l'istanza del database SQL.

  1. Per visualizzare le istanze disponibili del database SQL, scegliere il nodo **Database SQL**.

  1. Per visualizzare le proprietà del database, scegliere il database. Verrà visualizzata la vista **Proprietà**.

      >[AZURE.NOTE] Se la vista **Proprietà** non viene visualizzata, potrebbe essere necessario aprirla usando il separatore.

  1. Per visualizzare le stringhe di connessione, scegliere il pulsante con i puntini di sospensione (...) accanto a Visualizza.

    Verrà visualizzata la finestra di dialogo **Stringhe di connessione**.

  1. Per copiare la stringa di connessione ADO.NET, evidenziare il testo e premere i tasti Ctrl + C.

  1. Per chiudere la finestra di dialogo, scegliere il pulsante **Chiudi**.

1. Per sostituire la stringa di connessione nel file web.config per utilizzare questa istanza del database SQL, aprire il file web.config, evidenziare la voce della stringa di connessione esistente e quindi premere i tasti Ctrl + V. La stringa di connessione ADO.NET per l'istanza del database SQL sostituisce la stringa di connessione esistente.

1. È inoltre necessario aggiungere il parametro `MultipleActiveResultSets=True` alla stringa di connessione. La stringa di connessione deve avere il formato seguente:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Facoltativo) Un metodo alternativo per la modifica della stringa di connessione direttamente nel file web.config è aggiungere una sezione in uno dei file di trasformazione web.config, a seconda della configurazione di compilazione utilizzata per creare il pacchetto del servizio. Aprire il file Web.Debug.Config o il file Web.Release.Config. In questo file, aggiungere la seguente sezione:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Salvare il file modificato e ripubblicare l'applicazione.

### Per usare un'istanza del database SQL tramite il portale di gestione di Azure

1. Nel [portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkID=213885) scegliere il nodo Database SQL.

  - Se viene visualizzata l'istanza del database SQL che si desidera utilizzare, scegliere di aprirla.

  - Se non sono state create istanze, scegliere il collegamento appropriato e quindi creare un'istanza.

1. Dopo avere creato o aperto l'istanza di un database, scegliere il collegamento **Stringhe di connessione**.

1. Nella parte inferiore della pagina, scegliere il collegamento per configurare le impostazioni del firewall e accettare i valori predefiniti o configurare i valori desiderati.

1. Copiare la stringa di connessione ADO.NET, incollarla nel file web.config sulla stringa di connessione precedente per il database locale e assicurarsi di aggiungere `MultipleActiveResultSets=True`.

## Pubblicare un'applicazione Web in Azure

### Per pubblicare un'applicazione Web in Azure

1. Per testare l'applicazione nell'ambiente di sviluppo locale con l'emulatore di calcolo di Azure, aprire il menu di scelta rapida per il progetto Azure per il ruolo Web e scegliere **Imposta come progetto di avvio**. Scegliere **Debug**, **Avvia debug** (tastiera: **F5**).

    Verrà visualizzata la finestra di dialogo **Avvia l'ambiente di debug di Azure** e l'applicazione verrà avviata nel browser. Per informazioni dettagliate su come avviare ogni tipo di applicazione Web nell'emulatore di calcolo, vedere la tabella in questa sezione.

1. Per configurare i servizi per l'applicazione da pubblicare in Azure, è necessario disporre di un account Microsoft e di una sottoscrizione di Azure. Usare i passaggi nell'argomento seguente per configurare i servizi: [Preparare la pubblicazione o la distribuzione di un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Per pubblicare l'applicazione Web in Azure, aprire il menu di scelta rapida per il progetto Web e scegliere **Pubblica in Azure**.

    Verrà aperta la finestra di dialogo **Pubblica applicazione Azure** e Visual Studio avvierà il processo di distribuzione. Per altre informazioni su come pubblicare l'applicazione, vedere la sezione **Pubblicare un'applicazione Azure da Visual Studio** in [Pubblicazione di un servizio cloud con gli strumenti di Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] È inoltre possibile pubblicare l'applicazione Web dal progetto Azure. Per eseguire questa operazione, aprire il menu di scelta rapida per il progetto Azure e scegliere **Pubblica**.

1. Per visualizzare lo stato di avanzamento della distribuzione, è possibile visualizzare la finestra **Log attività di Azure**. Questo log viene visualizzato automaticamente all’avvio del processo di distribuzione. È possibile espandere la voce della riga nel registro delle attività per visualizzare informazioni dettagliate, come illustrato nella figura seguente:

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Facoltativo) Per annullare il processo di distribuzione, aprire il menu di scelta rapida per la voce nel registro attività e scegliere **Annulla e rimuovi**. Questo arresta il processo di distribuzione ed elimina l'ambiente di distribuzione da Azure.

    >[AZURE.NOTE] Per rimuovere questo ambiente di distribuzione dopo che è stato distribuito, è necessario utilizzare il Portale di gestione di Azure.

1. (Facoltativo) Una volta avviate le istanze del ruolo, Visual Studio mostrerà automaticamente l'ambiente di distribuzione nel nodo **Calcolo di Azure** in **Cloud Explorer** o **Esplora server**. Da qui è possibile visualizzare lo stato delle singole istanze del ruolo.

    La figura seguente mostra le istanze del ruolo in **Esplora server** mentre si trovano ancora nello stato di inizializzazione:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Per accedere all'applicazione dopo la distribuzione, scegliere la freccia accanto alla distribuzione quando lo stato **Completato** viene visualizzato nel **Log attività di Azure**. Consente di visualizzare l'URL dell'applicazione Web in Azure. Vedere la tabella seguente per informazioni dettagliate su come avviare un tipo specifico di applicazione Web da Azure.

    Nella tabella seguente sono elencati i dettagli su come avviare applicazioni Web specifiche da Azure o su come eseguire o effettuare il debug di un'applicazione Web localmente utilizzando l'emulatore di calcolo di Azure:

    |Tipo di applicazione Web|Eseguire/Effettuare il debug localmente utilizzando l'emulatore di calcolo|Esecuzione in Azure|
    |---|---|---|
    |Applicazione Web ASP.NET|Nella barra del menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **Distribuzione** per il **Log attività di Azure** per caricare la pagina iniziale nel browser.|
    |Applicazione Web ASP.NET MVC 2|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **Distribuzione** per il **Log attività di Azure** per caricare la pagina iniziale nel browser.|
    |Applicazione Web ASP.NET MVC 3|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **Distribuzione** per il **Log attività di Azure** per caricare la pagina iniziale nel browser.|
    |Applicazione Web ASP.NET MVC 4|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **Distribuzione** per il **Log attività di Azure** per caricare la pagina iniziale nel browser.|
    |Applicazione Web ASP.NET vuota|È necessario aggiungere una pagina .aspx all'applicazione che si imposta come pagina iniziale per il progetto Web. Nella barra dei menu scegliere quindi **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|Se si dispone di una pagina ASPX predefinita nell'applicazione, scegliere il collegamento ipertestuale URL visualizzato nella scheda **Distribuzione** per il **Log attività di Azure** e la pagina verrà caricata nel browser. Se si dispone di una pagina ASPX differente, è necessario passare a questa pagina specifica usando il seguente formato per l'URL: `<url for deployment>/<name of page>.aspx`|
    |Applicazione Silverlight|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario passare alla pagina specifica per l'applicazione usando il seguente formato per l'URL: `<url for deployment>/<name of page>.aspx`|
    |Applicazione aziendale di Silverlight|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario passare alla pagina specifica per l'applicazione usando il seguente formato per l'URL: `<url for deployment>/<name of page>.aspx`|
    |Applicazione di navigazione Silverlight|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario passare alla pagina specifica per l'applicazione usando il seguente formato per l'URL:`<url for deployment>/<name of page>.aspx`|
    |Applicazione di servizio WCF|È necessario impostare il file con estensione .svc come pagina iniziale per il progetto di servizio WCF. Nella barra dei menu scegliere quindi **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario passare al file con estensione svc per l'applicazione usando il seguente formato per l'URL: `<url for deployment>/<name of service file>.svc`|
    |Applicazione di servizio del flusso di lavoro WCF|È necessario impostare il file con estensione .svc come pagina iniziale per il progetto di servizio WCF. Nella barra dei menu scegliere quindi **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario passare al file con estensione svc per l'applicazione usando il seguente formato per l'URL: `<url for deployment>/<name of service file>.svc`|
    |Entità dinamiche ASP.NET|Nella barra del menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario aggiornare la stringa di connessione (vedere la sezione successiva). È inoltre necessario passare alla pagina specifica per l'applicazione utilizzando il seguente formato per l'url: `<url for deployment>/<name of page>.aspx`|
    |Linq ASP.NET Dynamic Data a SQL|Nella barra dei menu scegliere **Debug**, **Avvia debug** (tastiera: scegliere il tasto **F5**).|È necessario seguire i passaggi descritti in questa procedura: utilizzare un database SQL Azure per l'applicazione (vedere la sezione precedente in questo argomento). È inoltre necessario passare alla pagina specifica per l'applicazione utilizzando il seguente formato per l'url: `<url for deployment>/<name of page>.aspx`|

## Aggiornamento di una stringa di connessione per entità dinamiche ASP.NET

### Per aggiornare una stringa di connessione per entità dinamiche ASP.NET

1. Per creare un database SQL Azure che può essere usato per un'applicazione Web di entità dinamiche ASP.NET, seguire i passaggi nella procedura **Utilizzare un database SQL Azure per l'applicazione** precedentemente in questo argomento.

1. Aggiungere le tabelle e i campi necessari per il database dal portale di gestione di Azure.

1. La stringa di connessione per questo tipo di applicazione presenta il seguente formato nel file web.config:

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    Aggiornare il valore *connectionString* con la stringa di connessione ADO.NET per il database SQL Azure, come indicato di seguito:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. Per salvare il file web.config con le modifiche apportate alla stringa di connessione, nella barra dei menu scegliere **File**, **Salva web.config**.

## Modelli di progetto supportati

Per pubblicare un'applicazione Web in Azure, l'applicazione deve utilizzare uno dei modelli di progetto per C# o Visual Basic elencati nella tabella riportata di seguito.

|Gruppo modelli di progetto|Modello di progetto|
|---|---|
|Web|Applicazione Web ASP.NET|
|Web|Applicazione Web ASP.NET MVC 2|
|Web|Applicazione Web ASP.NET MVC 3|
|Web|Applicazione Web ASP.NET MVC4|
|Web|Applicazione Web ASP.NET vuota|
|Web|Applicazione Web vuota ASP.NET MVC 2|
|Web|Applicazione Web entità ASP.NET Dynamic Data|
|Web|Applicazione Web Linq ASP.NET Dynamic Data a SQL|
|Silverlight|Applicazione Silverlight|
|Silverlight|Applicazione aziendale di Silverlight|
|Silverlight|Applicazione di navigazione Silverlight|
|WCF|Applicazione di servizio WCF|
|WCF|Applicazione di servizio del flusso di lavoro WCF|
|Flusso di lavoro|Applicazione di servizio del flusso di lavoro WCF|

## Passaggi successivi
Per altre informazioni sulla pubblicazione, vedere [Preparare la pubblicazione o la distribuzione di un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Vedere anche [Configurazione delle credenziali per l'autenticazione denominate](vs-azure-tools-setting-up-named-authentication-credentials.md)

<!---HONumber=AcomDC_0420_2016-->