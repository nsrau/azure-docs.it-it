<properties
   pageTitle="Hosting di applicazioni Web in Docker | Microsoft Azure"
   description="Informazioni su come usare Visual Studio per ospitare un'applicazione Web in un contenitore Docker."
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
   ms.date="02/10/2016"
   ms.author="tarcher" />

# Hosting di applicazioni Web nel Docker

[Docker](https://www.docker.com/whatisdocker/) è un motore contenitore leggero, simile in qualche modo a una macchina virtuale, che è possibile usare per ospitare le applicazioni e servizi. Visual Studio supporta Docker in Ubuntu, CoreOS e Windows.

Questo esempio illustra come usare l'estensione **Visual Studio 2015 Tools per Docker** per pubblicare un'app ASP.NET 5 in una macchina virtuale Ubuntu Linux (qui indicata come host Docker) in Azure. La stessa esperienza può essere usata per la pubblicazione in un contenitore di Windows.

Dopo la pubblicazione dell'app in un host Docker, è possibile usare gli strumenti da riga di comando Docker per interagire con il contenitore in cui è stata pubblicata l'app.

## Creare e pubblicare un nuovo contenitore Docker

Nella sezione seguente si creerà un nuovo progetto di applicazione Web ASP.NET 5 e un host contenitore, quindi si compilerà e si eseguirà il progetto di app Web in un contenitore Docker. Per iniziare, scaricare e installare gli [Strumenti Visual Studio 2015 per Docker](https://aka.ms/DockerToolsForVS).

### Aggiungere un progetto di applicazione Web ASP.NET 5

1. Nel menu di Visual Studio selezionare **File > Nuovo > Progetto**. 

1. Nella sezione **Modelli** della finestra di dialogo **Nuovo progetto** selezionare **Visual C#** > **Web**.

1. Selezionare **Applicazione Web ASP.NET** e assegnare un nome al nuovo progetto. Nelle schermate in questo articolo viene usato il nome predefinito **WebApplication1**.

1. Toccare **OK**.

1. Poiché l'app Web sarà ospitata/eseguita in Docker, deselezionare la casella di controllo **Ospita nel cloud** se è selezionata e quindi toccare **OK**.

  ![][0]

  A questo punto in genere è necessario aggiungere un codice all'app Web per fare in modo che esegua operazioni utili. Per questo esempio si cercherà di semplificare le operazioni per concentrarsi su Docker. Si noti che è anche possibile scegliere di usare un'app Web ASP.NET 5 esistente.

### Pubblicare il progetto
Dopo aver creato il progetto o aver aperto un progetto esistente, la procedura seguente fornirà le istruzioni per la pubblicazione del progetto in un contenitore Docker in Azure.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica** dal menu di scelta rapida.

1. Nella sezione **Seleziona una destinazione di pubblicazione** della finestra di dialogo **Pubblica sito Web** toccare **Contenitori Docker**.

    Se non viene visualizzata l'opzione Contenitori Docker, verificare di avere installato Visual Studio 2015 Tools per Docker e di aver selezionato un modello di sito Web di ASP.NET 5 nella sezione precedente.

    ![][1]

    La finestra di dialogo **Seleziona macchina virtuale Docker** consente di specificare l'host Docker in cui si vuole pubblicare il progetto. È possibile creare un nuovo host Docker o scegliere una VM esistente ospitata in Azure o altrove. Più avanti in questo esempio, si creerà un nuovo host Docker di Azure.

1. Se si è già connessi a un account Azure, andare al passaggio 5. Se non si è connessi a un account, toccare **Aggiungi un account**.

    ![][2]

1. Nella finestra di dialogo **Accedi a Visual Studio** immettere l'account di posta elettronica per la sottoscrizione di Azure e quindi toccare **Continua**.

1. Toccare **Nuova** per creare una nuova VM Docker di Azure e quindi toccare **OK**.

    ![][3]

    Si noti che è inoltre possibile scegliere di usare un host Docker esistente. A tale scopo, selezionarlo nell'elenco a discesa **Macchine virtuali Docker di Azure esistenti** anziché toccare **Nuova**. Si noti che l'elenco non è limitato solo agli host contenitori, ma include tutte le VM nel tenant di Azure.

    In alternativa, è possibile scegliere di pubblicare un host Docker personalizzato. Per altre informazioni, vedere la sezione [Fornire un host Docker personalizzato](#provide-a-custom-docker-host) più avanti in questo argomento.

1. Immettere le informazioni seguenti nella finestra di dialogo **Crea una macchina virtuale in Microsoft Azure**. Al termine, toccare **OK**. Questo consente di creare una macchina virtuale Linux con un’estensione Docker configurata.

    ![][4]

    Si noti che è anche possibile creare un HOST contenitore di Windows usando Windows Server 2016 Technical Preview 3 (TP3).

    ![][8]

	|Nome proprietà|Impostazione|
	|---|---|
	|Location|Modificare questa impostazione per l'area più vicina alle impostazioni locali.|
	|Nome DNS|Immettere un nome univoco per la macchina virtuale. Se il nome viene accettato da Azure, viene visualizzato un cerchio verde con un segno di spunta bianco a destra. Se il nome non viene accettato, viene visualizzato un cerchio rosso con una x bianca. In tal caso, immettere un nome univoco.|
	|Immagine|Scegliere un'immagine del sistema operativo da usare nell'host Docker, se presente. Per questo esempio, scegliere un'immagine Ubuntu Server. Si noti che un'immagine di Windows Server ora è disponibile nell'elenco delle immagini disponibili.|
	|Nome utente|Specificare un Nome utente univoco per la macchina virtuale.|
	|Password|Immettere una password per l'utente e quindi confermarla.|
	|Directory dei certificati |Consente di specificare la directory in cui sono archiviati i certificati Docker. Anche se è possibile creare una nuova directory o fare riferimento a una directory esistente, è consigliabile usare la directory dei certificati predefinita (C:\\Utenti\\[*nomeutente*]\\.docker). In caso contrario, le opzioni di autenticazione non possono essere recuperate automaticamente se si riutilizza lo stesso host su un altro progetto o sistema.|

1. Toccare i puntini di sospensione (...) accanto alla voce **Directory dei certificati** e quindi creare una nuova directory per i certificati Docker oppure passare a una directory dei certificati Docker esistente.

    Se non è possibile trovare i certificati Docker necessari per la VM, accanto alla voce viene visualizzata un'icona con punto esclamativo che consente di sapere che i certificati necessari non sono stati trovati e che proseguendo i certificati esistenti verranno eliminati e quindi ricreati.

1. Toccare **OK** per iniziare a creare la VM. Verrà visualizzato un messaggio che segnala la creazione della macchina virtuale in Azure.

    Visual Studio crea un file di modello di gestione delle risorse di Azure (ARM), file dei parametri e uno script di PowerShell, per poter eseguire nuovamente i comandi in futuro.

    ![][7]

    Visual Studio visualizza l'avanzamento dell'operazione nella finestra **Output**. Visual Studio richiama uno script di PowerShell per distribuire la VM. Lo script usa i cmdlet di Azure PowerShell per distribuire un gruppo di risorse di Azure. In seguito, un altro script di PowerShell esegue i comandi Docker per la pubblicazione, così come accadrebbe per la creazione manuale dell'host Docker.

    Il provisioning dell'host Docker può richiedere alcuni minuti, quindi controllare lo stato nella finestra Output di Visual Studio per visualizzare il completamento del processo.

1. Dopo il completamento del provisioning dell'host Docker in Azure, è possibile verificare l'account nel portale di Azure. Dovrebbe essere possibile visualizzare la nuova macchina virtuale nella categoria **Macchina virtuale** nel portale di Azure.

1. Quando l'host Docker è pronto, tornare indietro e pubblicare il progetto dell’applicazione Web. Nel menu di scelta rapida per il nodo del progetto dell'applicazione Web in **Esplora soluzioni** toccare **Pubblica**. Visual Studio crea un file di pubblicazione basato sulla VM creata.

1. Nella scheda **Connessione** della finestra di dialogo **Pubblica sito Web** toccare **Convalida connessione** per assicurarsi che l'host Docker sia pronto. Se la connessione è buona, toccare **Pubblica** per pubblicare l'app Web.

    La prima volta che si pubblica un'applicazione su un host Docker sono necessari alcuni minuti per scaricare le immagini di base a cui si fa riferimento nel file Docker (ad esempio **FROM** *nomeimmagine*).

    Si noti che il file Docker è specifico del sistema operativo. Se si sceglie eseguire nuovamente la pubblicazione in un sistema operativo diverso, sarà necessario rinominare il file Docker in modo che Visual Studio possa creare un nuovo valore predefinito in base al sistema operativo di destinazione. Se ad esempio si esegue la pubblicazione prima in un contenitore Linux e successivamente si decide di eseguirla in un contenitore Windows, è consigliabile rinominare il file Docker con un nome significativo e univoco, ad esempio DockerLinux. Quindi, quando si esegue nuovamente la pubblicazione in Windows, Visual Studio creerà nuovamente il file Docker predefinito per Windows. Successivamente, quando si esegue nuovamente la pubblicazione in uno dei sistemi, è sufficiente selezionare il file Docker appropriato per il sistema operativo.

## Fornire un host Docker personalizzato

Nella sezione precedente è stato illustrato come creare una macchina virtuale Docker ospitata in Azure. Se tuttavia si ha già un host Docker esistente in un'altra posizione, è possibile scegliere di eseguire la pubblicazione su quell'host anziché in Azure.

### Come fornire un host Docker personalizzato

1. Nella finestra di dialogo **Selezionare macchina virtuale Docker** selezionare la casella di controllo **Host Docker personalizzato**.

    ![][5]

1. Toccare **OK**.

1. Nella finestra di dialogo **Pubblica sito Web** aggiungere i valori alle impostazioni della sezione **CustomDockerHost**, ad esempio: URL del server, nome dell'immagine, percorso del file Docker e numeri di porta per host e contenitore.

    Nella sezione **Opzioni avanzate Docker** è possibile visualizzare o modificare le opzioni di autenticazione e di esecuzione, nonché la riga di comando Docker.

    ![][6]

1. Dopo aver immesso tutti i valori necessari, toccare **Convalida connessione** per assicurarsi che la connessione all'host Docker funzioni correttamente.

1. Se la connessione funziona correttamente, toccare **Avanti** per visualizzare un elenco dei componenti che verranno pubblicati oppure toccare **Pubblica** per pubblicare immediatamente il progetto.

## Testare l'host Docker

Dopo che il progetto è stato pubblicato in un host Docker in Azure, è possibile testarlo selezionando le relative impostazioni. Poiché gli strumenti da riga di comando Docker vengono installati con l'estensione di Visual Studio, è possibile inviare comandi a Docker direttamente da un prompt dei comandi di Windows.

La procedura riportata di seguito è usata per la comunicazione con un host Docker che è stato distribuito in Azure.

### Come testare l'host Docker

1. Aprire un prompt dei comandi di Windows.

1. Assegnare l'host Docker e verificare le variabili di ambiente. Per eseguire questa operazione, immettere i seguenti comandi nel prompt dei comandi. Sostituire *NameofAzureVM* con il nome dell'host Docker e *Region* con l'area in cui è stato creato.

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    Se si impostano queste variabili di ambiente, non sarà necessario specificare manualmente queste informazioni per ogni comando Docker emesso.

1. È ora possibile eseguire comandi simili al seguente per verificare se l'host Docker è presente e funzionante.

	|Riga di comando|Descrizione|
	|---|---|
	|`docker info`|Ottenere informazioni sulla versione Docker.|
	|`docker ps`|Ottenere un elenco dei contenitori in esecuzione.|
	|`docker ps –a`|Ottenere un elenco di contenitori, compresi quelli che sono stati arrestati.|
	|`docker logs <Docker container name>`|Ottenere un log per il contenitore specificato.|
	|`docker images`|Ottenere un elenco di immagini.|

    Per un elenco completo dei comandi Docker, immettere il comando `docker` nel prompt dei comandi e premere il tasto **&lt;INVIO>**. Per altre informazioni, vedere la documentazione relativa alla [riga di comando Docker](https://docs.docker.com/reference/commandline/cli/).

## Passaggi successivi

Ora che è stato creato un host Docker, è possibile inviare comandi Docker ad esso. Per altre informazioni su Docker, vedere la [documentazione su Docker](https://docs.docker.com/) e l'[esercitazione in linea relativa a Docker](https://www.docker.com/tryit/).

Per informazioni su come usare l'estensione di VM Docker per Linux su Azure, vedere la pagina relativa all'[estensione della macchina virtuale Docker per Linux su Azure](/virtual-machines/virtual-machines-docker-vm-extension.md).

Per problemi relativi all'uso di Docker in Visual Studio, vedere [Risoluzione dei problemi client Docker in Windows con Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0211_2016-->