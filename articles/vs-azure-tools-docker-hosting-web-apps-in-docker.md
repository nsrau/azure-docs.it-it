<properties
   pageTitle="Hosting di applicazioni Web in Docker | Microsoft Azure"
   description="Informazioni su come utilizzare Visual Studio per ospitare un'applicazione Web in un contenitore Docker."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/17/2015"
   ms.author="kempb" />

# Hosting di applicazioni Web nel Docker

[Docker](https://www.docker.com/whatisdocker/) è un motore contenitore leggero, simile in qualche modo a una macchina virtuale, che è possibile utilizzare per ospitare le applicazioni e servizi. Visual Studio supporta Docker in Ubuntu, CoreOS e Windows.

In questo esempio viene illustrato come utilizzare Visual Studio 2015 Tools per l’estensione Docker per pubblicare un'applicazione ASP.NET 5 in una macchina virtuale di Ubuntu Linux (qui indicata come host Docker) in Azure con l'estensione Docker installata, insieme a un'applicazione Web ASP.NET 5. La stessa esperienza può essere utilizzata per la pubblicazione in un contenitore di Windows.

È possibile pubblicare l'applicazione in un nuovo host Docker ospitato in Azure o su un server locale, Hyper-V o un host Boot2Docker utilizzando l’impostazione **Host personalizzati**. Dopo la pubblicazione dell'applicazione su un host Docker, è possibile utilizzare gli strumenti da riga di comando Docker per interagire con il contenitore su cui è stata pubblicata l'applicazione.

## Creare e pubblicare un nuovo contenitore Docker

In queste procedure, verrà creato un nuovo progetto di applicazione Web ASP.NET 5, un host del contenitore, quindi si compilerà ed eseguirà il progetto di applicazione Web nel contenitore Docker. Per iniziare, scaricare e installare [Visual Studio 2015 Tools for Docker](aka.ms/DockerToolsForVS).

### Aggiungere un progetto di applicazione Web ASP.NET 5

1. Creare un nuovo progetto di applicazione Web ASP.NET. Dal menu principale scegliere **File**, **Nuovo progetto**. In **C#**, **Web**, scegliere **Applicazione Web ASP.NET**.

1. Nell'elenco dei **Modelli di anteprima ASP.NET 5**, scegliere **Sito Web**.

1. Poiché l'applicazione Web sarà ospitata/eseguita nel Docker, deselezionare la casella di controllo **Host nel cloud** se è selezionata e quindi scegliere il pulsante **OK**.

  ![][0]

  Questo è il punto in cui in genere si aggiunge il codice all'applicazione Web affinché venga eseguita un'operazione utile, ma in questo esempio, si lasciano solo le impostazioni predefinite. Si noti che è anche possibile scegliere di utilizzare applicazioni Web ASP.NET 5.

### Pubblicare il progetto

1. Nel menu di scelta rapida del progetto ASP.NET, scegliere **Pubblica**.

1. Nella sezione **Selezionare una destinazione di pubblicazione** della finestra di dialogo **Pubblica sito Web** scegliere il pulsante **Contenitori Docker **.

    Se non viene visualizzata un'opzione Contenitori Docker, verificare di avere installato Visual Studio 2015 Tools per Docker e di aver selezionato un modello di sito Web di ASP.NET 5 nella procedura precedente.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Seleziona macchina virtuale Docker**. Questo consente di specificare l'host Docker in cui si desidera pubblicare il progetto. È possibile creare un nuovo host Docker o scegliere una VM esistente ospitata in Azure o altrove. Più avanti in questo esempio, si creerà un nuovo host Docker di Azure.

1. Se si è già connessi a un account Azure, andare al passaggio 5. Se non si è connessi a un account, scegliere il pulsante **Aggiungi un account**.

    ![][2]

1. Nella finestra di dialogo **Accedi a Visual Studio** immettere l'account di posta elettronica per la sottoscrizione Azure e quindi scegliere il pulsante **Continua**.

1. Scegliere il pulsante **Nuova** per creare una nuova VM Docker di Azure e quindi scegliere il pulsante **OK**.

    ![][3]

    Si noti che è inoltre possibile scegliere di utilizzare un host Docker esistente. A tale scopo, selezionarlo nell’elenco a discesa **Macchine virtuali di Docker Azure esistenti** anziché scegliere il pulsante **Nuova**. Questo elenco non visualizza solo gli host del contenitore, elenca tutte le VM nel tenant di Azure.

    In alternativa, è possibile scegliere di pubblicare un host Docker personalizzato. Vedere [Fornire un host Docker personalizzato](#BKMK_CustomHost) più avanti in questo argomento per ulteriori informazioni.

1. Immettere le seguenti informazioni nella finestra di dialogo **Crea una macchina virtuale in Microsoft Azure**. Al termine, scegliere il pulsante **OK**. Questo consente di creare una macchina virtuale Linux con un’estensione Docker configurata.

    ![][4]

    Si noti che è anche possibile creare un HOST contenitore di Windows utilizzando Windows Server 2016 Technical Preview 3 (TP3).

    ![][8]

    |Nome proprietà|Impostazione|
    |---|---|
    |Location|Modificare questa impostazione per l'area più vicina alle impostazioni locali.|
    |Nome DNS|Immettere un nome univoco per la macchina virtuale. Se il nome viene accettato da Azure, viene visualizzato un cerchio verde con un segno di spunta bianco a destra. Se il nome non viene accettato, viene visualizzato un cerchio rosso con una x bianca. In tal caso, immettere un nome univoco.|
    |Immagine|Scegliere un'immagine del sistema operativo da utilizzare nell'host Docker, se presente. Per questo esempio, scegliere un'immagine Ubuntu Server. Si noti che un'immagine di Windows Server ora è disponibile nell'elenco delle immagini disponibili.|
    |Nome utente|Specificare un Nome utente univoco per la macchina virtuale.|
    |Password|Immettere una password per l'utente e quindi confermarla.|
    |Directory dei certificati |Consente di specificare la cartella in cui sono archiviati i certificati Docker. Anche se è possibile creare una nuova cartella o puntare a una cartella esistente, è consigliabile utilizzare la cartella dei certificati predefinita (C:\\Users\\[*username*]\\.docker). In caso contrario, le opzioni di autenticazione non possono essere recuperate automaticamente se si riutilizza lo stesso host su un altro progetto o sistema.|

1. Fare clic sul pulsante con i puntini di sospensione (...) accanto alla voce **Directory certificati** e quindi creare una nuova cartella per i certificati Docker oppure passare a una cartella dei certificati Docker esistente.

    Se non è possibile trovare i certificati Docker necessari per la VM, accanto alla voce viene visualizzata un'icona con punto esclamativo che consente di sapere che i certificati necessari non sono stati trovati e che proseguendo i certificati esistenti verranno eliminati e quindi ricreati.

1. Scegliere il pulsante **OK** per iniziare a creare la VM. Verrà visualizzato un messaggio che segnala la creazione della macchina virtuale in Azure.

    Visual Studio crea un file di modello di gestione delle risorse di Azure (ARM), file dei parametri e uno script di PowerShell, per poter eseguire nuovamente i comandi in futuro.

    ![][7]

    Visual Studio visualizza l'avanzamento dell'operazione nella finestra **Output**. Visual Studio richiama uno script di PowerShell per distribuire la VM. Lo script utilizza i cmdlet PowerShell di Azure per distribuire il gruppo di risorse di Azure. In seguito, un altro script di PowerShell esegue i comandi Docker per la pubblicazione, così come accadrebbe per la creazione manuale dell'host Docker.

    Il provisioning dell'host Docker può richiedere alcuni minuti, quindi controllare lo stato nella finestra Output per visualizzare il completamento del processo.

1. Dopo il completamento del provisioning dell'host Docker in Azure, è possibile verificare l'account nel portale di Azure. Dovrebbe essere possibile visualizzare la nuova macchina virtuale nella categoria **Macchina virtuale** nel portale di Azure.

1. Quando l'host Docker è pronto, tornare indietro e pubblicare il progetto dell’applicazione Web. Nel menu di scelta rapida per il nodo del progetto dell'applicazione Web in **Esplora soluzioni**, scegliere **Pubblica**. Visual Studio crea un file di pubblicazione basato sulla VM creata.

1. Nella scheda **Connessione** della finestra di dialogo **Pubblica sito Web** scegliere la casella **Convalida connessione** per assicurarsi che l'host Docker sia pronto. Se la connessione è buona, scegliere il pulsante **Pubblica** per pubblicare l'applicazione Web.

    La prima volta che si pubblica un'applicazione su un host Docker sono necessari alcuni minuti per scaricare le immagini di base a cui si fa riferimento nel file Docker (ad esempio **FROM** *imagename*).

    Si noti che il file Docker è specifico del sistema operativo. Se si sceglie eseguire nuovamente la pubblicazione in un sistema operativo diverso, sarà necessario rinominare il file Docker in modo che Visual Studio possa creare un nuovo valore predefinito in base al sistema operativo di destinazione. Ad esempio, se si esegue la pubblicazione prima in un contenitore Linux e successivamente si decide di eseguirla in un contenitore Windows, è consigliabile rinominare il file Docker con un nome univoco, ad esempio DockerLinux. Quindi, quando si esegue nuovamente la pubblicazione in Windows, Visual Studio creerà nuovamente il file Docker predefinito per Windows. Successivamente, quando si esegue nuovamente la pubblicazione in uno dei sistemi, è sufficiente selezionare il file Docker appropriato per il sistema operativo.

## Fornire un host Docker personalizzato

Nella procedura precedente è stato illustrato come creare una macchina virtuale Docker ospitata in Azure. Tuttavia, se si dispone già di un host Docker esistente in un'altra posizione, è possibile scegliere di eseguire la pubblicazione su di esso anziché in Azure.

### Come fornire un host Docker personalizzato

1. Nella finestra di dialogo **Selezionare macchina virtuale Docker** selezionare la casella di controllo **Host Docker personalizzato**.

    ![][5]

1. Scegliere il pulsante **OK**.

1. Nella finestra di dialogo **Pubblica sito Web** aggiungere i valori alle impostazioni della sezione **CustomDockerHost**, ad esempio: URL del server, nome dell'immagine, percorso Dockerfile e numeri delle porte di host e contenitore.

    Nella sezione **Opzioni avanzate Docker** è possibile visualizzare o modificare le opzioni di autenticazione e di esecuzione, nonché la riga di comando Docker.

    ![][6]

1. Dopo aver immesso tutti i valori necessari, scegliere il pulsante **Convalida connessione** per assicurarsi che la connessione all'host Docker funzioni correttamente.

1. Se la connessione funziona correttamente, è possibile scegliere il pulsante **Avanti** per visualizzare un elenco dei componenti che verranno pubblicati oppure è possibile selezionare il pulsante **Pubblica** per pubblicare immediatamente il progetto.

## Testare l'host Docker

Ora che il progetto è stato pubblicato in un host Docker in Azure, è possibile testarlo selezionando le relative impostazioni. Poiché gli strumenti da riga di comando Docker vengono installati con l'estensione di Visual Studio, è possibile inviare comandi a Docker direttamente da un prompt dei comandi di Windows.

La procedura riportata di seguito è utilizzata per la comunicazione con un host Docker che è stato distribuito in Azure.

### Come testare l'host Docker

1. Aprire un prompt dei comandi di Windows.

1. Assegnare l'host Docker e verificare le variabili di ambiente. Per eseguire questa operazione, immettere i seguenti comandi nel prompt dei comandi. Sostituire il nome dell'host Docker per *NomeVMAzure*.

    ```
    Set DOCKER_HOST=tcp://<NameofAzureVM>.cloudapp.net:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    La chiamata di questi comandi evita la necessità di aggiungere `–H (Host) tcp://<NameofAzureVM>.cloudapp.net:2376` e `--TLSVERIFY` a ogni comando eseguito.

1. Ora è possibile eseguire comandi simili per verificare se l'host Docker è presente e funzionante.

    |Riga di comando|Descrizione|
    |---|---|
    |info docker|Ottenere informazioni sulla versione Docker.|
    |docker ps|Ottenere un elenco dei contenitori in esecuzione.|
    |docker ps –a|Ottenere un elenco di contenitori, compresi quelli che sono stati arrestati.|
    |docker logs <Docker container name>|Ottenere un log per il contenitore specificato.|
    |docker images|Ottenere un elenco di immagini.|

    Per un elenco completo dei comandi Docker, è sufficiente immettere il comando `docker` nel prompt dei comandi. Per ulteriori informazioni, vedere [Riga di comando Docker](https://docs.docker.com/reference/commandline/cli/).

## Passaggi successivi

Ora che è stato creato un host Docker, è possibile inviare comandi Docker ad esso. Per ulteriori informazioni su Docker, vedere la [Documentazione Docker](https://docs.docker.com/) e l’[Esercitazione in linea Docker](https://www.docker.com/tryit/).

Per problemi relativi all'utilizzo di Docker in Visual Studio, vedere [Risoluzione degli errori di Docker](vs-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=August15_HO8-->