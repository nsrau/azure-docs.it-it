<properties
   pageTitle="Preparare la pubblicazione o la distribuzione di un'applicazione di Azure da Visual Studio"
   description="Informazioni sulle procedure per la configurazione di servizi cloud e dell'account di archiviazione e per la configurazione dell'applicazione Azure."
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
   ms.date="09/28/2015"
   ms.author="kempb" />

# Preparare la pubblicazione o la distribuzione di un'applicazione di Azure da Visual Studio

## Panoramica

Per potere pubblicare un progetto di servizio cloud, è prima di tutto necessario configurare i servizi seguenti:

- Un **servizio cloud** per l'esecuzione dei ruoli nell'ambiente Azure.

- Un **account di archiviazione** che fornisce l'accesso ai servizi BLOB, code e tabelle.

Usare le procedure seguenti per impostare questi servizi e configurare l'applicazione.


## Creare un servizio cloud

Per pubblicare un servizio cloud in Azure, è prima di tutto necessario creare un servizio cloud, che esegue i ruoli nell'ambiente Azure. È possibile creare un servizio cloud nel portale di gestione di Azure, come descritto nella sezione **Per creare un servizio cloud utilizzando il portale di gestione di Azure**, più avanti in questo argomento. È anche possibile creare un servizio cloud in Visual Studio usando la Pubblicazione guidata.

### Per creare un servizio cloud usando Visual Studio

1. Aprire il menu di scelta rapida per il progetto Azure, quindi scegliere **Pubblica**.

    ![VST\_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Se non è ancora stato effettuato l'accesso, accedere con il nome utente e la password dell'account Microsoft o dell'account aziendale associato alla sottoscrizione di Azure.

1. Scegliere il pulsante **Avanti** per passare alle pagina **Impostazioni**.

    ![Impostazioni comuni della Pubblicazione guidata](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Nell'elenco **Servizi cloud** scegliere **Crea nuovo**. Verrà visualizzata la finestra di dialogo **Crea servizi di Azure**.

1. Immettere il nome del servizio cloud. Il nome fa parte dell'URL per il servizio, quindi deve essere globalmente univoco. Nel nome non viene fatta distinzione tra maiuscole e minuscole.

### Per creare un servizio cloud usando il portale di gestione di Azure

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=253103) nel sito Web Microsoft.

1. (Facoltativo) Per visualizzare un elenco di servizi cloud già creati, selezionare il collegamento Servizi cloud sul lato sinistro della pagina.

1. Fare clic sull'icona **+** nell'angolo inferiore sinistro, quindi scegliere **Servizio cloud** dal menu che viene visualizzato. Verrà visualizzata un'altra schermata con due opzioni disponibili, **Creazione rapida** e **Creazione personalizzata**. L'opzione **Creazione rapida** consente di creare un servizio cloud semplicemente specificandone l'URL e l'area in cui sarà ospitato fisicamente. L'opzione **Creazione personalizzata** consente di pubblicare immediatamente un servizio cloud specificando un pacchetto (file con estensione .cspkg), un file di configurazione (file con estensione .cscfg) e un certificato. La Creazione personalizzata non è necessaria se si intende pubblicare il servizio cloud usando il comando **Pubblica** in un progetto Azure. Il comando **Pubblica** è disponibile nel menu di scelta rapida per un progetto Azure.

1. Scegliere l'opzione **Creazione rapida** per pubblicare il servizio cloud in un secondo momento usando Visual Studio.

1. Specificare un nome per il servizio cloud. Accanto al nome verrà visualizzato l'URL completo.

1. Nell'elenco scegliere l'area in cui si trova la maggior parte degli utenti.

1. Nella parte inferiore della finestra fare clic sul collegamento **Crea servizio cloud**.

## Creare un account di archiviazione

Un account di archiviazione fornisce l'accesso ai servizi BLOB, code e tabelle. È possibile creare un account di archiviazione usando Visual Studio o il [portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=253103).

### Per creare un account di archiviazione usando Visual Studio

1. In **Esplora soluzioni** aprire il menu di scelta rapida per il nodo **Archiviazione**, quindi scegliere **Crea account di archiviazione**.

    ![Creare un nuovo account di archiviazione di Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Selezionare o immettere le informazioni seguenti per il nuovo account di archiviazione nella finestra di dialogo **Crea account di archiviazione**.
    - Sottoscrizione di Azure a cui aggiungere l'account di archiviazione.
    - Nome da usare per il nuovo account di archiviazione.
    - Area o set di affinità (ad esempio Stati Uniti occidentali o Asia orientale).
    - Tipo di replica da usare per l'account di archiviazione, ad esempio Con ridondanza geografica.

1. Al termine dell'operazione scegliere **Crea**. Il nuovo account di archiviazione viene visualizzato nell'elenco **Archiviazione** in **Esplora soluzioni**.

### Per creare un account di archiviazione usando il portale di gestione di Azure

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=253103) nel sito Web Microsoft.

1. (Facoltativo) Per visualizzare gli account di archiviazione, fare clic sul collegamento **Archiviazione** nel riquadro sul lato sinistro della pagina.

1. Nell'angolo inferiore sinistro della pagina fare clic sull'icona **+**.

1. Nel menu che viene visualizzato scegliere **Archiviazione**, quindi **Creazione rapida**.

1. Assegnare un nome all'account di archiviazione che rappresenterà un URL univoco.

1. Assegnare un nome al servizio cloud. Accanto al nome verrà visualizzato l'URL completo.

1. Nell'elenco delle aree scegliere l'area in cui si trova la maggior parte degli utenti.

1. Specificare se si vuole abilitare la replica geografica. Se la si abilita, i dati saranno salvati in più ubicazioni fisiche per ridurre i rischi di perdita dei dati. Questa funzionalità rende più costoso il processo di archiviazione. Tuttavia è possibile ridurre il costo abilitando la georilevazione quando si crea l'account di archiviazione anziché aggiungere la funzionalità in un secondo momento. Per altre informazioni, vedere [Replica geografica](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Nella parte inferiore della finestra fare clic sul collegamento **Crea account di archiviazione**.

Dopo la creazione dell'account di archiviazione, saranno visualizzati gli URL che è possibile usare per accedere alle risorse di ogni servizio di archiviazione di Azure, nonché la chiavi di accesso primaria e secondaria per l'account. Questi tasti possono essere usati per autenticare le richieste eseguite in relazione ai servizi di archiviazione.

>[AZURE.NOTE]La chiave di accesso secondaria fornisce lo stesso accesso all'account di archiviazione offerto da quella primaria e viene generata come backup nel caso in cui la chiave di accesso primaria dovesse essere compromessa. È anche consigliabile rigenerare regolarmente le chiavi di accesso. È possibile modificare l'impostazione di una stringa di connessione per usare la chiave secondaria mentre si rigenera quella primaria, quindi procedere nuovamente alla modifica per usare la chiave primaria rigenerata mentre si rigenera quella secondaria.

## Configurare l'app per usare servizi forniti dall'account di archiviazione

È necessario configurare un ruolo che acceda ai servizi di archiviazione per usare i servizi di archiviazione di Azure creati. A questo scopo, è possibile usare più configurazioni del servizio per il progetto Azure. Per impostazione predefinita, nel progetto Azure vengono create due configurazioni. L'uso di più configurazioni del servizio consente di usare la stessa stringa di connessione nel codice, ma la stringa di connessione in ogni configurazione del servizio avrà un valore diverso. Ad esempio, è possibile usare una configurazione del servizio per eseguire ed effettuare il debug dell'applicazione in locale usando l'emulatore di archiviazione di Azure e una configurazione del servizio diversa per pubblicare l'applicazione in Azure. Per altre informazioni sulle configurazioni del servizio, vedere [Configurazione di un progetto Azure usando configurazioni del servizio multiple](vs-azure-tools-multiple-services-project-configurations.md).

### Per configurare l'applicazione per usare i servizi forniti dall'account di archiviazione

1. In Visual Studio aprire la soluzione Azure. In Esplora soluzioni aprire il menu di scelta rapida per ogni ruolo nel progetto Azure che accede ai servizi di archiviazione e scegliere **Proprietà**. Una pagina con il nome del ruolo viene visualizzata nell'editor di Visual Studio. La pagina visualizza i campi per la scheda **Configurazione**.

1. Nelle pagine delle proprietà per il ruolo scegliere **Impostazioni**.

1. Nell'elenco **Configurazione del servizio** scegliere il nome della configurazione del servizio da modificare. Per apportare modifiche a tutte le configurazioni del servizio per questo ruolo, è possibile scegliere **Tutte le configurazioni**. Per ulteriori informazioni sull'aggiornamento delle configurazioni del servizio, vedere la sezione **Gestire le stringhe di connessione degli account di archiviazione** nell'argomento [Configurare i ruoli di un servizio Cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Per modificare un'impostazione della stringa di connessione, scegliere il pulsante **…** accanto all'impostazione. Verrà visualizzata la finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**.

1. In **Connetti tramite** scegliere l'opzione **Sottoscrizione**.

1. Nell'elenco **Sottoscrizione** scegliere la sottoscrizione da usare. Se l'elenco delle sottoscrizioni non include quella desiderata, selezionare il collegamento **Download impostazioni di pubblicazione**.

1. Scegliere il nome dell'account di archiviazione dall'elenco **Nome account**. Gli strumenti di Azure ottengono le credenziali dell'account di archiviazione automaticamente tramite il file con estensione publishsettings. Per specificare le credenziali dell'account di archiviazione manualmente, scegliere l'opzione **Credenziali immesse manualmente** e quindi continuare la procedura. È possibile ottenere il nome dell'account di archiviazione e la chiave primaria dal [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se non si vogliono specificare le impostazioni dell'account di archiviazione manualmente, fare clic sul pulsante **OK** per chiudere la finestra di dialogo.

1. Fare clic sul collegamento **Immettere le credenziali dell'account di archiviazione**.

1. Nella casella **Nome account** immettere il nome dell'account di archiviazione.

    >[AZURE.NOTE]Accedere al portale di gestione, quindi fare clic su **Archiviazione**. Il portale mostra un elenco di account di archiviazione. Se si sceglie un account, viene aperta una pagina corrispondente. È possibile copiare il nome dell'account di archiviazione da questa pagina. Se si usa una versione precedente del portale di gestione, il nome dell'account di archiviazione compare nella visualizzazione**Account di archiviazione** del portale di gestione. Per copiare questo nome, evidenziarlo nella finestra **Proprietà** della visualizzazione e premere CTRL+C. Per incollare il nome in Visual Studio, fare clic nella casella di testo **Nome account** e premere CTRL+V.

1. Nella casella di testo **Chiave account** immettere la chiave primaria oppure copiarla e incollarla dal [portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Per copiare questa chiave dal portale di gestione:

    1. Nella parte inferiore della pagina per l'account di archiviazione appropriato selezionare il pulsante **Gestisci chiavi**.

    1. Nella pagina **Gestisci chiavi di accesso** selezionare il testo della chiave di accesso primaria, quindi premere CTRL+C.

    1. Negli strumenti di Azure incollare la chiave nella casella **Chiave account**.

    1. È necessario selezionare una delle opzioni seguenti per determinare le modalità di accesso del servizio all'account di archiviazione:
        - **Usa HTTP**. Si tratta dell'opzione standard. Ad esempio: `http://<account name>.blob.core.windows.net`.
        - Scegliere **Usa HTTPS** per una connessione sicura. Ad esempio: `https://<accountname>.blob.core.windows.net`.
        - Scegliere **Specifica endpoint personalizzati** per ciascuno dei tre servizi. È quindi possibile digitare questi endpoint nel campo per il servizio specifico.

        >[AZURE.NOTE]Se si creano endpoint personalizzati, sarà possibile creare una stringa di connessione più complessa. Quando si usa questo formato stringa, è possibile specificare endpoint del servizio di archiviazione che includono un nome di dominio personalizzato registrato per l'account di archiviazione con il servizio BLOB. È anche possibile concedere l'accesso solo alle risorse BLOB in un singolo contenitore, tramite una firma di accesso condiviso. Per altre informazioni sulla creazione di endpoint personalizzati, vedere [Configurare le stringhe di connessione di archiviazione di Azure](storage-configure-connection-string.md).

1. Per salvare queste modifiche della stringa di connessione, scegliere **OK**, quindi scegliere il pulsante **Salva**. Dopo avere salvato queste modifiche, è possibile ottenere il valore di questa stringa di connessione nel codice tramite [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando si pubblica l'applicazione in Azure, scegliere la configurazione del servizio che contiene l'account di archiviazione di Azure per la stringa di connessione. Dopo la pubblicazione, verificare che l'applicazione funzioni come previsto rispetto ai servizi di archiviazione Azure.

## Passaggi successivi

Per ulteriori informazioni sulla pubblicazione di app in Azure da Visual Studio, vedere [Pubblicazione di un servizio Cloud con gli strumenti di Azure](vs-azure-tools-publishing-a-cloud-service.md).

<!---HONumber=Oct15_HO3-->