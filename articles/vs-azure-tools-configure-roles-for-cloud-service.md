<properties
   pageTitle="Configurare i ruoli per un servizio cloud di Azure con Visual Studio | Microsoft Azure"
   description="Informazioni su come impostare e configurare i ruoli per i servizi cloud di Azure tramite Visual Studio."
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
   ms.date="09/08/2015"
   ms.author="kempb" />

# Configurare i ruoli per un servizio cloud di Azure con Visual Studio

Un servizio cloud di Azure può includere uno o più ruoli di lavoro o ruoli Web. Per ogni ruolo è necessario definire il modo in cui tale ruolo viene configurato e configurare anche la modalità di esecuzione del ruolo. Per altre informazioni sui ruoli nei servizi cloud, vedere il video [Introduzione ai servizi cloud di Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). Le informazioni per il servizio cloud vengono archiviate nei file seguenti:

- **ServiceDefinition.csdef**

    Il file di definizione del servizio definisce le impostazioni di runtime per il servizio cloud e include i ruoli richiesti, gli endpoint e le dimensioni della macchina virtuale. Nessun dato archiviato in questo file può essere modificato durante l'esecuzione del ruolo.

- **ServiceConfiguration.cscfg**

    Il file di configurazione del servizio configura il numero delle istanze di un ruolo eseguite e i valori delle impostazioni definiti per un ruolo. I dati archiviati nel file possono essere modificati durante l'esecuzione del ruolo.

Per potere archiviare valori diversi per queste impostazioni relative all'esecuzione del ruolo, è possibile creare più configurazioni del servizio. È possibile usare una configurazione del servizio diversa per ogni ambiente di distribuzione. Ad esempio, è possibile configurare la stringa di connessione dell'account di archiviazione in modo che usi l'emulatore di archiviazione di Azure locale in una configurazione del servizio locale e creare un'altra configurazione del servizio in modo che usi l'archiviazione di Azure nel cloud.

Quando si crea un nuovo servizio cloud in Visual Studio, vengono create per impostazione predefinita due configurazioni del servizio. Queste configurazioni vengono aggiunte al progetto Azure con i nomi seguenti:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Configurare un servizio cloud di Azure

È possibile configurare un servizio cloud di Azure da Esplora soluzioni in Visual Studio, come illustrato nella figura seguente.

![Configurare il servizio cloud](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### Per configurare un servizio cloud di Azure

1. Per configurare ogni ruolo nel progetto Azure da **Esplora soluzioni**, aprire il menu di scelta rapida per il ruolo nel progetto Azure e quindi scegliere **Proprietà**.

    Una pagina con il nome del ruolo viene visualizzata nell'editor di Visual Studio. La pagina visualizza i campi per la scheda **Configurazione**.

1. Nell'elenco **Configurazione del servizio** scegliere il nome della configurazione del servizio da modificare.

    Per apportare modifiche a tutte le configurazioni del servizio per questo ruolo, è possibile scegliere **Tutte le configurazioni**.

    >[AZURE.IMPORTANT]Se si sceglie una configurazione del servizio specifica, alcune proprietà verranno disabilitate perché possono essere impostate solo per tutte le configurazioni. Per modificare queste proprietà, è necessario scegliere Tutte le configurazioni.

    È ora possibile scegliere una scheda per aggiornare eventuali proprietà abilitate in tale visualizzazione.

## Cambiare il numero di istanze del ruolo

Per migliorare le prestazioni del servizio cloud, è possibile cambiare il numero di istanze di un ruolo in esecuzione, in base al numero di utenti o al carico previsto per un ruolo specifico. Una macchina virtuale separata viene creata per ogni istanza di un ruolo quando il servizio cloud è in esecuzione in Azure. Ciò influirà sulla fatturazione per la distribuzione di questo servizio cloud. Per altre informazioni sulla fatturazione, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

### Per cambiare il numero di istanze per un ruolo

1. Scegliere la scheda **Configurazione**.

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.

    >[AZURE.NOTE]È possibile impostare il numero di istanze per una configurazione del servizio specifica o per tutte le configurazioni del servizio.

1. Nella casella di testo **Conteggio istanze** immettere il numero di istanze da avviare per questo ruolo.

    >[AZURE.NOTE]Ogni istanza viene eseguita in una macchina virtuale distinta quando si pubblica il servizio cloud in Azure.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche nel file di configurazione del servizio.

## Gestire le stringhe di connessione per gli account di archiviazione

È possibile aggiungere, rimuovere o modificare le stringhe di connessione per le configurazioni del servizio. Potrebbero essere necessarie diverse stringhe di connessione per configurazioni del servizio diverse. Ad esempio, è possibile che si voglia creare una stringa di connessione locale per una configurazione del servizio locale con valore `UseDevelopmentStorage=true`. È anche possibile che si voglia definire una configurazione del servizio cloud che usa un account di archiviazione di Azure.

>[AZURE.CAUTION]Quando si immettono le informazioni chiave dell'account di archiviazione di Azure per una stringa di connessione dell'account di archiviazione, tali informazioni vengono archiviate localmente nel file di configurazione del servizio. Queste informazioni, tuttavia, non vengono attualmente archiviate come testo crittografato.

Se si usa un valore diverso per ogni configurazione del servizio, non sarà necessario usare stringhe di connessione diverse nel servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile usare lo stesso nome per la stringa di connessione nel codice e il valore sarà diverso in base alla configurazione del servizio selezionata quando si compila il servizio cloud o quando lo si pubblica.

### Per gestire le stringhe di connessione per gli account di archiviazione

1. Scegliere la scheda **Impostazioni**.

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.

    >[AZURE.NOTE]È possibile aggiornare le stringhe di connessione per una configurazione del servizio specifica, ma se è necessario aggiungere o eliminare una stringa di connessione si dovrà selezionare Tutte le configurazioni.

1. Per aggiungere una stringa di connessione, scegliere **Aggiungi impostazione**. Una nuova voce verrà aggiunta all'elenco.

1. Nella casella di testo **Nome** digitare il nome da usare per la stringa di connessione.

1. Nell'elenco a discesa **Tipo** scegliere **Stringa di connessione**.

1. Per cambiare il valore per la stringa di connessione, fare clic sul pulsante con i puntini di sospensione (...). Verrà visualizzata la finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**.

1. Per usare l'emulatore dell'account di archiviazione locale, scegliere il pulsante di opzione **Emulatore di archiviazione di Microsoft Azure** e quindi scegliere **OK**.

1. Per usare un account di archiviazione in Azure, scegliere il pulsante di opzione **Sottoscrizione** e selezionare l'account di archiviazione desiderato.

1. Per usare le credenziali personalizzate, scegliere il pulsante di opzione **Credenziali immesse manualmente**. Immettere il nome dell'account di archiviazione e la chiave primaria o secondaria. Per informazioni su come creare un account di archiviazione e come immettere dettagli per l'account di archiviazione nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**, vedere [Preparare la pubblicazione o la distribuzione di un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Per eliminare una stringa di connessione, selezionarla e quindi scegliere **Rimuovi impostazione**.

1. Scegliere l'icona **Salva** sulla barra degli strumenti per salvare le modifiche nel file di configurazione del servizio.

1. Per accedere alla stringa di connessione nel file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione. Il codice seguente illustra un esempio in cui viene creata una risorsa di archiviazione BLOB e vengono caricati dati mediante una stringa di connessione `MyConnectionString` dal file di configurazione del servizio quando un utente sceglie **Button1** nella pagina Default.aspx nel ruolo Web per un servizio cloud di Azure. Aggiungere le istruzioni using seguenti a Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Aprire il file Default.aspx.cs nella visualizzazione di progettazione, quindi aggiungere un pulsante dalla casella degli strumenti. Aggiungere il codice seguente al metodo `Button1_Click`. Questo codice usa `GetConfigurationSettingValue` per ottenere il valore dal file di configurazione del servizio per la stringa di connessione. Verrà quindi creato un BLOB nell'account di archiviazione a cui si fa riferimento nella stringa di connessione `MyConnectionString` e infine il programma aggiungerà il testo al BLOB.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Aggiungere impostazioni personalizzate da usare nel servizio cloud di Azure

Le impostazioni personalizzate nel file di configurazione del servizio consentono di aggiungere un nome e un valore per una stringa per una configurazione del servizio specifica. È possibile scegliere di usare questa impostazione per configurare una funzionalità nel servizio cloud leggendo il valore dell'impostazione e usando questo valore per controllare la logica nel codice. È possibile cambiare questi valori della configurazione del servizio senza dovere ricompilare il pacchetto del servizio o quando il servizio cloud è in esecuzione. Il codice può cercare notifiche in caso di modifiche di un'impostazione. Vedere [Evento RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

È possibile aggiungere, rimuovere o modificare impostazioni personalizzate per le configurazioni del servizio. Potrebbero essere necessari diversi valori per queste stringhe per configurazioni del servizio diverse.

Se si usa un valore diverso per ogni configurazione del servizio, non sarà necessario usare stringhe diverse nel servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile usare lo stesso nome per la stringa nel codice e il valore sarà diverso in base alla configurazione del servizio selezionata quando si compila il servizio cloud o quando lo si pubblica.

### Per aggiungere impostazioni personalizzate da usare nel servizio cloud di Azure

1. Scegliere la scheda **Impostazioni**.

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.

    >[AZURE.NOTE]È possibile aggiornare le stringhe per una configurazione del servizio specifica, ma se è necessario aggiungere o eliminare una stringa si dovrà selezionare **Tutte le configurazioni**.

1. Per aggiungere una stringa, scegliere **Aggiungi impostazione**. Una nuova voce verrà aggiunta all'elenco.

1. Nella casella di testo **Nome** immettere il nome da usare per la stringa.

1. Nell'elenco a discesa **Tipo** scegliere **Stringa**.

1. Per aggiungere o cambiare il valore per la stringa, digitare il nuovo valore nella casella di testo **Valore**.

1. Per eliminare una stringa, selezionarla e quindi scegliere **Rimuovi impostazione**.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche nel file di configurazione del servizio.

1. Per accedere alla stringa nel file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione.

    È necessario assicurarsi che le istruzioni using seguenti siano già state aggiunte al file Default.aspx.cs, in modo analogo alla procedura precedente.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Aggiungere il codice seguente al metodo `Button1_Click` per accedere a questa stringa con la stessa procedura usata per una stringa di connessione. Il codice potrà quindi eseguire codice specifico in base al valore della stringa di impostazioni per il file di configurazione del servizio usato.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## Gestire le risorse di archiviazione locali per ogni istanza del ruolo

È possibile aggiungere una risorsa di archiviazione del file system locale per ogni istanza del ruolo. È possibile archiviare in questa posizione i dati locali a cui non devono accedere altri ruoli. Eventuali dati che non devono essere salvati in una risorsa di archiviazione di tipo tabella, BLOB o database SQL possono essere archiviati qui. Ad esempio, è possibile usare questa risorsa di archiviazione locale per memorizzare nella cache i dati che potrebbe essere necessario usare di nuovo. Le altre istanze di un ruolo non potranno accedere a questi dati memorizzati. Per altre informazioni sulle risorse di archiviazione locale, vedere [Configurare le risorse di archiviazione locale](cloud-services-configure-local-storage-resources.md).

Le impostazioni delle risorse di archiviazione locali vengono applicate a tutte le configurazioni del servizio. È possibile aggiungere, rimuovere o modificare le risorse di archiviazione locali solo per tutte le configurazioni del servizio.

### Per gestire le risorse di archiviazione locali per ogni istanza del ruolo

1. Scegliere la scheda **Risorsa di archiviazione locale**.

1. Nell'elenco **Configurazione servizio** scegliere **Tutte le configurazioni**.

1. Per aggiungere una voce relativa a una risorsa di archiviazione locale, scegliere **Aggiungi risorsa di archiviazione locale**. Una nuova voce verrà aggiunta all'elenco.

1. Nella casella di testo **Nome** digitare il nome da usare per la risorsa di archiviazione locale.

1. Nella casella di testo **Dimensioni** digitare le dimensioni in MB necessarie per la risorsa di archiviazione locale.

1. Per rimuovere i dati da questa risorsa di archiviazione locale quando la macchina virtuale per il ruolo viene riciclata, selezionare la casella di controllo **Pulisci a riciclo ruolo**.

1. Per modificare una voce relativa a una risorsa di archiviazione locale, scegliere la riga da aggiornare. Modificare quindi i campi, come illustrato nel passaggio precedente.

1. Per eliminare una voce relativa a una risorsa di archiviazione locale, scegliere la voce desiderata nell'elenco e quindi scegliere il pulsante **Rimuovi risorsa di archiviazione locale**.

1. Per salvare le modifiche ai file di configurazione del servizio, scegliere l'icona **Salva** sulla barra degli strumenti.

1. Per accedere alla risorsa di archiviazione locale aggiunta al file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione della risorsa locale. Usare le righe di codice seguenti per accedere a questo valore e creare un file denominato **MyStorageTest.txt**, quindi scrivere una riga di dati di test in tale file. È possibile aggiungere questo codice nel metodo `Button_Click` usato nelle procedure precedenti:

1. È necessario assicurarsi che le istruzioni using seguenti vengano aggiunte al file Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Aggiungere il codice seguente al metodo `Button1_Click`. Il file verrà creato nella risorsa di archiviazione locale e i dati di test verranno scritti nel file.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Facoltativamente) Per visualizzare il file creato quando si esegue il servizio cloud in modalità locale, seguire questa procedura:

  1. Eseguire il ruolo Web e fare clic su **Button1** per assicurarsi che il codice in `Button1_Click` venga chiamato.

  1. Nell'area di notifica aprire il menu di scelta rapida per l'icona di Azure e scegliere **Mostra interfaccia emulatore di calcolo**. Verrà visualizzata la finestra di dialogo **Emulatore di calcolo di Azure**.

  1. Selezionare il ruolo Web.

  1. Sulla barra dei menu scegliere **Strumenti**, **Apri archivio locale**. Verrà visualizzata una finestra di Esplora risorse.

  1. Sulla barra dei menu immettere **MyStorageTest.txt** nella casella di testo **Cerca** e quindi premere **INVIO** per avviare la ricerca.

    Il file viene visualizzato nei risultati della ricerca.

  1. Per visualizzare i contenuti del file, aprire il menu di scelta rapida per il file e quindi scegliere **Apri**.

## Raccogliere i dati di diagnostica del servizio cloud

È possibile raccogliere i dati di diagnostica per il servizio cloud di Azure. Questi dati vengono aggiunti a un account di archiviazione. Potrebbero essere necessarie diverse stringhe di connessione per configurazioni del servizio diverse. Ad esempio, è possibile che si voglia creare un account di archiviazione locale per una configurazione del servizio locale con valore UseDevelopmentStorage=true. È anche possibile che si voglia definire una configurazione del servizio cloud che usa un account di archiviazione di Azure. Per altre informazioni su Diagnostica di Azure, vedere Raccogliere dati di registrazione usando Diagnostica di Azure.

>[AZURE.NOTE]La configurazione del servizio locale è già stata definita in modo da usare le risorse locali. Se si usa la configurazione del servizio cloud per la pubblicazione del servizio cloud di Azure, la stringa di connessione specificata durante la pubblicazione verrà usata anche per la stringa di connessione di diagnostica, a meno che non sia stata specificata una stringa di connessione diversa. Se si crea un pacchetto del servizio cloud usando Visual Studio, la stringa di connessione nella configurazione del servizio non verrà modificata.

### Per raccogliere i dati di diagnostica del servizio cloud

1. Scegliere la scheda **Configurazione**.

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare oppure scegliere **Tutte le configurazioni**.

    >[AZURE.NOTE]È possibile aggiornare l'account di archiviazione per una configurazione del servizio specifica, ma se si vuole abilitare o disabilitare la diagnostica è necessario scegliere Tutte le configurazioni.

1. Per abilitare la diagnostica, selezionare la casella di controllo **Abilita diagnostica**.

1. Per cambiare il valore per l'account di archiviazione, fare clic sul pulsante con i puntini di sospensione (...).

    Verrà visualizzata la finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**.

1. Per usare una stringa di connessione locale, scegliere l'opzione relativa all'emulatore di archiviazione di Azure e quindi scegliere **OK**.

1. Per usare un account di archiviazione associato alla sottoscrizione di Azure, scegliere l'opzione **Sottoscrizione**.

1. Per usare un account di archiviazione per la stringa di connessione locale, scegliere l'opzione **Credenziali immesse manualmente**.

    Per altre informazioni su come creare un account di archiviazione e come immettere dettagli per l'account di archiviazione nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**, vedere [Preparare la pubblicazione o la distribuzione di un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Scegliere l'account di archiviazione da usare in **Nome account**.

    Se si immettono manualmente le credenziali dell'account di archiviazione, copiare o digitare la chiave primaria in **Chiave dell'account**. La chiave può essere copiata dal portale di gestione di Azure. Per copiare la chiave, eseguire questi passaggi dalla visualizzazione **Account di archiviazione** nel portale di gestione di Azure:

  1. Selezionare l'account di archiviazione da usare per il servizio cloud.

  1. Scegliere il pulsante **Gestisci chiavi di accesso** nella parte inferiore della schermata. Verrà visualizzata la finestra di dialogo **Gestisci chiavi di accesso**.

  1. Per copiare la chiave di accesso, scegliere il pulsante **Copia negli Appunti**. È ora possibile incollare la chiave nel campo **Chiave dell'account**.

1. Per usare l'account di archiviazione fornito come stringa di connessione per la diagnostica (e la memorizzazione nella cache) durante la pubblicazione del servizio cloud in Azure, selezionare la casella di controllo **Aggiorna le stringhe di connessione dell'archivio di sviluppo per la diagnostica e la memorizzazione nella cache con le credenziali dell'account di archiviazione di Microsoft Azure quando si esegue la pubblicazione in Microsoft Azure**.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche nel file di configurazione del servizio.

## Modificare le dimensioni della macchina virtuale usata per ogni ruolo

È possibile configurare le dimensioni delle macchine virtuali per ogni ruolo. È possibile configurare le dimensioni solo per tutte le configurazioni del servizio. Se si selezionano le dimensioni minori per le macchine virtuali, verranno allocati meno core di CPU, meno memoria e meno risorse di archiviazione su disco locali. Anche la larghezza di banda allocata sarà ridotta. Per altre informazioni su queste dimensioni e sulle risorse allocate, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md).

Le risorse necessarie per ogni macchina virtuale in Azure influiscono sul costo di esecuzione del servizio cloud di Azure. Per altre informazioni sulla fatturazione di Azure, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

### Per modificare le dimensioni della macchina virtuale

1. Scegliere la scheda **Configurazione**.

1. Nell'elenco **Configurazione servizio** scegliere **Tutte le configurazioni**.

1. Per selezionare le dimensioni della macchina virtuale per questo ruolo, scegliere le dimensioni appropriate dall'elenco **Dimensioni macchina virtuale**.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche nel file di configurazione del servizio.

## Gestire gli endpoint e i certificati per i ruoli

È possibile configurare gli endpoint di rete specificando il protocollo, il numero di porta e, per HTTPS, le informazioni sul certificato SSL. Le versioni precedenti a giugno 2012 supportano HTTP, HTTPS e TCP. La versione di giugno 2012 supporta questi protocolli e UDP. Non è possibile usare UDP per gli endpoint di input nell'emulatore di calcolo. È possibile usare quel protocollo solo per endpoint interni.

Per migliorare la sicurezza del servizio cloud di Azure, è possibile creare endpoint che usano il protocollo HTTPS. Ad esempio, se è disponibile un servizio cloud usato dai clienti per gli ordini di acquisto, è consigliabile assicurarsi che le rispettive informazioni siano protette tramite SSL.

È anche possibile aggiungere endpoint che possono essere usati internamente o esternamente. Gli endpoint esterni sono definiti endpoint di input. Un endpoint di input offre un altro punto di accesso agli utenti del servizio cloud. Se è disponibile un servizio WCF, è possibile che si voglia esporre un endpoint interno che possa essere usato da un ruolo Web per l'accesso al servizio.

>[AZURE.IMPORTANT]È possibile aggiornare gli endpoint solo per tutte le configurazioni del servizio.

Se si aggiungono endpoint HTTPS, sarà necessario usare un certificato SSL. A tale scopo, è possibile associare certificati al ruolo per tutte le configurazioni del servizio e usarli per gli endpoint.

>[AZURE.IMPORTANT]Questi certificati non sono inclusi nel servizio. È necessario caricare i certificati separatamente in Azure tramite il portale di gestione della piattaforma Azure.

Qualsiasi certificato di gestione associato alle configurazioni del servizio viene applicato solo quando il servizio cloud è in esecuzione in Azure. Quando il servizio cloud è in esecuzione nell'ambiente di sviluppo locale, verrà usato un certificato standard gestito dall'emulatore di calcolo di Azure.

### Per aggiungere un certificato a un ruolo

1. Scegliere la scheda **Certificati**.

1. Nell'elenco **Configurazione servizio** scegliere **Tutte le configurazioni**.

    >[AZURE.NOTE]Per aggiungere o rimuovere certificati, è necessario selezionare Tutte le configurazioni. È possibile aggiornare il nome e l'identificazione personale per una configurazione del servizio specifica, se necessario.

1. Per aggiungere un certificato per questo ruolo, scegliere **Aggiungi certificato**. Una nuova voce verrà aggiunta all'elenco.

1. Nella casella di testo **Nome** immettere il nome per il certificato.

1. Nell'elenco **Percorso archivio** scegliere il percorso per il certificato da aggiungere.

1. Nell'elenco **Nome archivio** scegliere l'archivio da usare per la selezione del certificato.

1. Per aggiungere il certificato, scegliere il pulsante con i puntini di sospensione (...). Verrà visualizzata la finestra di dialogo **Sicurezza di Windows**.

1. Scegliere il certificato da usare nell'elenco e quindi fare clic su **OK**.

    >[AZURE.NOTE]Quando si aggiunge un certificato dall'archivio certificati, qualsiasi certificato intermedio viene aggiunto automaticamente alle impostazioni di configurazione. Anche questi certificati intermedi devono essere caricati in Azure al fine di configurare correttamente il servizio per SSL.

1. Per eliminare un certificato, selezionarlo e quindi scegliere **Rimuovi certificato**.

1. Scegliere l'icona **Salva** sulla barra degli strumenti per salvare le modifiche nei file di configurazione del servizio.

### Per gestire gli endpoint per un ruolo

1. Scegliere la scheda **Endpoint**.

1. Nell'elenco **Configurazione servizio** scegliere **Tutte le configurazioni**.

1. Per aggiungere un endpoint, scegliere **Aggiungi endpoint**. Una nuova voce verrà aggiunta all'elenco.

1. Nella casella di testo **Nome** digitare il nome da usare per l'endpoint.

1. Scegliere il tipo di endpoint necessario nell'elenco **Tipo**.

1. Scegliere il protocollo per l'endpoint necessario dall'elenco **Protocollo**.

1. Se si tratta di un endpoint di input, nella casella di testo **Porta pubblica** immettere la porta pubblica da usare.

1. Nella casella di testo **Porta privata** immettere la porta privata da usare.

1. Se l'endpoint richiede il protocollo HTTPS, nell'elenco **Nome certificato SSL** scegliere un certificato da usare.

    >[AZURE.NOTE]Questo elenco mostra i certificati aggiunti per questo ruolo nella scheda **Certificati**.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche nei file di configurazione del servizio.

## Passaggi successivi
Per altre informazioni sui progetti Azure in Visual Studio, vedere [Configurazione di un progetto Azure](vs-azure-tools-configuring-an-azure-project.md). Per altre informazioni sullo schema del servizio cloud, vedere [Guida di riferimento agli schemi](https://msdn.microsoft.com/library/azure/dd179398).

<!---HONumber=Oct15_HO3-->