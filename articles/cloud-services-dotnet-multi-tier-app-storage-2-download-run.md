<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra" />

# Configurazione e distribuzione dell'applicazione Azure Email Service - 2 di 5

Questa è la seconda di una serie di cinque esercitazioni in cui viene illustrato come compilare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie][prima esercitazione della serie].

In questa esercitazione si apprenderà come:

-   Configurare il computer per lo sviluppo in Azure installando Azure SDK.
-   Configurare e testare l'applicazione Azure Email Service nel computer locale.
-   Pubblicare l'applicazione in Azure.
-   Visualizzare e modificare tabelle, code e BLOB di Azure usando Esplora server in Visual Studio.
-   Configurare la traccia e visualizzare i dati traccia.
-   Ridimensionare l'applicazione aumentando il numero di istanze del ruolo di lavoro.

### Sezioni dell'esercitazione

-   [Configurare l'ambiente di sviluppo][Configurare l'ambiente di sviluppo]
-   [Scaricare ed eseguire la soluzione completata][Scaricare ed eseguire la soluzione completata]
-   [Visualizzare le risorse di archiviazione per sviluppatori in Visual Studio][Visualizzare le risorse di archiviazione per sviluppatori in Visual Studio]
-   [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
-   [Creare un servizio cloud][Creare un servizio cloud]
-   [Configurare l'applicazione per il servizio Archiviazione di Azure][Configurare l'applicazione per il servizio Archiviazione di Azure]
-   [Configurare l'applicazione per l'uso di SendGrid][Configurare l'applicazione per l'uso di SendGrid]
-   [Distribuire l'applicazione in Azure][Distribuire l'applicazione in Azure]
-   [Innalzare di livello l'applicazione dall'ambiente di gestione temporanea all'ambiente di produzione][Innalzare di livello l'applicazione dall'ambiente di gestione temporanea all'ambiente di produzione]
-   [Configurare e visualizzare i dati traccia][Configurare e visualizzare i dati traccia]
-   [Aggiungere un'altra istanza del ruolo di lavoro per gestire un carico maggiore][Aggiungere un'altra istanza del ruolo di lavoro per gestire un carico maggiore]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## <a name="downloadcnfg"></a><span class="short-header">Scaricare ed eseguire</span>Scaricare ed eseguire la soluzione completata

1.  Scaricare e decomprimere la [soluzione completata][soluzione completata].

2.  Avviare Visual Studio.

3.  Nel menu **File** scegliere **Open Project**, passare alla cartella in cui è stata scaricata la soluzione, quindi aprire il file di soluzione.

4.  Premere CTRL+MAIUSC+B per compilare la soluzione.

    Per impostazione predefinita, Visual Studio ripristina automaticamente il contenuto del pacchetto NuGet, che non era incluso nel file con estensione *zip*. In caso di mancato ripristino dei pacchetti, installarli manualmente passando alla finestra di dialogo **Gestisci pacchetti NuGet** per la soluzione, quindi facendo clic sul pulsante **Ripristina** in alto a destra.

5.  In **Esplora soluzioni** verificare che come progetto di avvio sia selezionato **AzureEmailService**.

6.  Premere CTRL+F5 per eseguire l'applicazione.

    La home page dell'applicazione verrà visualizzata nel browser.

7.  Sulla barra dei menu fare clic su **Mailing Lists**.

    Le schermate mostrano lo stile della pagina Web dei modelli di progetto di Visual Studio 2012, ma il contenuto è uguale a quello per Visual Studio 2013.

    ![Eseguire l'app.][Eseguire l'app.]

8.  Fare clic su **Create New**.

9.  Immettere alcuni dati di test, quindi fare clic su **Create**.

    ![Eseguire l'app.][1]

10. Creare ancora un paio di voci della lista di distribuzione.

    ![Pagina di indice della lista di distribuzione][Pagina di indice della lista di distribuzione]

11. Fare clic su **Subscribers**, quindi aggiungere alcuni sottoscrittori. Impostare **Verified** su `true`.

    ![Pagina di indice dei sottoscrittori][Pagina di indice dei sottoscrittori]

12. Per preparare l'aggiunta di messaggi, creare un file con estensione *txt* che contiene il corpo di un messaggio di posta elettronica da inviare. Creare quindi un file con estensione *htm* contenente lo stesso testo e codice HTML, ad esempio per formattare una delle parole del messaggio in grassetto o in corsivo. Tali file verranno utilizzati nel passaggio successivo.

13. Fare clic su **Messages**, quindi aggiungere alcuni messaggi. Selezionare i file creati nel passaggio precedente. Non modificare la data pianificata, il cui valore predefinito è impostato tra una settimana. L'applicazione non è in grado di inviare messaggio finché non viene configurato SendGrid.

    ![Pagina di creazione del messaggio][Pagina di creazione del messaggio]

    ![Pagina di indice dei messaggi][Pagina di indice dei messaggi]

    I dati immessi e visualizzati sono gestiti dall'emulatore di archiviazione di Azure. Per emulare il funzionamento del servizio Archiviazione di Azure nel cloud, viene usato un database LocalDB di SQL Server Express. L'applicazione usa l'emulatore di archiviazione perché corrisponde a quanto configurato per l'uso nel progetto al momento del download. Questa impostazione è archiviata nei file con estensione *cscfg* nel progetto **AzureEmailService**. Il file *ServiceConfiguration.Local.cscfg* determina quello che verrà utilizzato quando l'applicazione viene eseguita in locale in Visual Studio, mentre *ServiceConfiguration.Cloud.cscfg* determina quello che verrà utilizzato quando l'applicazione viene distribuita nel cloud. Più avanti verrà spiegato come configurare l'applicazione per l'uso di un account di archiviazione di Azure.

14. Chiudere il browser.

## <a name="StorageExpVS"></a>Visualizzare le risorse di archiviazione per sviluppatori in Visual Studio

Il browser **Archiviazione di Azure** in **Esplora server** offre un modo pratico per usare direttamente le risorse del servizio di archiviazione di Azure.

1.  Nel menu **Visualizza** di Visual Studio scegliere **Esplora server**.

2.  Espandere il nodo **Azure**, quindi il nodo **Archiviazione** e infine il nodo **(Sviluppo)** sotto il nodo **Archiviazione di Azure**.

    Se non è già stato effettuato l'accesso ad Azure in Visual Studio, verranno richieste le credenziali dell'account Azure. Immettere le credenziali dell'account associato alla sottoscrizione in cui si vuole eseguire il servizio cloud.

3.  Espandere **Tables** per visualizzare le tabelle create nei passaggi precedenti.

    ![Esplora server][Esplora server]

4.  Fare doppio clic sulla tabella **MailingList**.

    ![Esplora archivi in Visual Studio][Esplora archivi in Visual Studio]

    Si noti il modo in cui nella finestra vengono mostrati i diversi schemi nella tabella. Alle entità `MailingList` sono assegnate le proprietà `Description` e `FromEmailAddress` mentre alle entità `Subscriber` sono assegnate le proprietà `Verified` e `SubscriberGUID` (quest'ultima non visualizzata perché la larghezza dell'immagine non è sufficiente). La tabella include le colonne per tutte le proprietà. Se una data riga si riferisce a un'entità che non include una certa proprietà, la cella è vuota.

Per lavorare con le risorse di archiviazione di Azure, è disponibile anche lo strumento [Esplora archivi Azure][Esplora archivi Azure].

## <a name="createWASA"></a>Creare un account di archiviazione di Azure

Quando si esegue l'applicazione di esempio in Visual Studio, è possibile accedere a tabelle, code e BLOB nell'emulatore di archiviazione di Azure oppure in un account di archiviazione di Azure nel cloud. In questa sezione dell'esercitazione si creerà l'account di archiviazione di Azure che verrà configurato per l'uso in Visual Studio più avanti.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] dal browser.

2.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su **Storage** e quindi su **New**.

![Nuovo archivio][Nuovo archivio]

1.  Fare clic su **Quick Create**.

![Quick Create][Quick Create]

1.  Nella casella di immissione dell'URL immettere un prefisso URL.

    La combinazione di questo prefisso e del testo visualizzato sotto la casella corrisponderà all'URL univoco dell'account di archiviazione. Se il prefisso immesso è già stato utilizzato da un altro utente, verrà visualizzato il messaggio "The storage name is already in use" sopra la casella di testo e sarà necessario selezionarne uno diverso.

2.  Impostare come area quella in cui si desidera distribuire l'applicazione.

3.  Impostare la casella di riepilogo **Replication** su **Locally redundant**.

    Quando per un account di archiviazione è abilitata la replica geografica, il contenuto archiviato viene replicato in una posizione geografica secondaria per consentire il failover in tale posizione in caso di errore grave nella posizione primaria. La replica geografica può comportare costi aggiuntivi. Per gli account di test e di sviluppo si preferisce in genere non pagare per la replica geografica. Per altre informazioni, vedere l'articolo su [come gestire gli account di archiviazione][come gestire gli account di archiviazione].

4.  Fare clic su **Create Storage Account**.

    La figura seguente illustra la creazione di un account di archiviazione con l'URL `aestest3.core.windows.net`.

    ![Creazione dell'account di archiviazione con prefisso URL][Creazione dell'account di archiviazione con prefisso URL]

    Il completamento di questa operazione può richiedere alcuni minuti. Durante l'attesa è possibile ripetere la procedura e creare un account di archiviazione di produzione. È spesso comodo disporre di un account di archiviazione di prova da utilizzare per lo sviluppo in locale, di un altro account di archiviazione di prova per il testing in Azure e di un account di archiviazione di produzione.

5.  Fare clic sull'account di prova creato nel passaggio precedente, quindi fare clic sull'icona **Manage Access Keys**.

    ![Manage Keys][Manage Keys]

    ![Keys GUID][Keys GUID]

    Quando si seleziona l'account di archiviazione, in Visual Studio le stringhe di connessione verranno configurate automaticamente con una di queste chiavi. È anche possibile aggiornare le stringhe di connessione manualmente.

    La presenza di due chiavi consente di modificare periodicamente quella utilizzata senza causare interruzioni del servizio in un'applicazione attiva. Dopo aver rigenerato la chiave non utilizzata, sarà possibile modificare la stringa di connessione nell'applicazione in modo che utilizzi la chiave rigenerata. Se fosse disponibile una sola chiave, l'applicazione perderebbe la connettività all'account di archiviazione durante la rigenerazione della chiave. Le chiavi mostrate in figura non sono più valide perché sono state rigenerate dopo l'acquisizione dell'immagine.

## <a name="createcloudsvc"></a><span class="short-header">Creare un servizio cloud</span>Creare un servizio cloud

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic su **Servizi cloud** e quindi sull'icona **Nuovo**.

    ![Creazione rapida di un cloud][Creazione rapida di un cloud]

2.  Fare clic su **Quick Create**.

3.  Nella casella di immissione dell'URL immettere un prefisso URL.

    Analogamente a quello di archiviazione, anche questo URL deve essere univoco e verrà visualizzato un messaggio di errore se il prefisso scelto è già utilizzato da un altro utente.

4.  Impostare come area quella in cui si desidera distribuire l'applicazione.

    È consigliabile creare il servizio cloud nella stessa area in cui è stato creato l'account di archiviazione. Quando il servizio cloud e l'account di archiviazione si trovano in data center diversi (aree diverse), la latenza aumenterà e verrà addebitato il costo relativo alla larghezza di banda esterna al data center. La larghezza di banda nell'ambito di un data center è gratuita.

    I gruppi di affinità di Azure offrono un meccanismo per ridurre la distanza tra le risorse in un data center e di conseguenza la latenza. In questa esercitazione non vengono utilizzati gruppi di affinità. Per altre informazioni, vedere [Come creare un gruppo di affinità in Azure][Come creare un gruppo di affinità in Azure].

5.  Fare clic su **Create Cloud Service**.

    Nell'immagine seguente viene creato un servizio cloud il cui URL è aescloud.cloudapp.net.

    ![Creazione dell'account di archiviazione con prefisso URL][2]

## <a name="conf4azureStorage"></a><span class="short-header">Utilizzare l'account di archiviazione</span>Configurare l'applicazione per l'uso dell'account di archiviazione di Azure

Verrà ora spiegato come configurare l'applicazione per l'utilizzo di un account di archiviazione di Azure, anziché dell'archivio di sviluppo, quando viene eseguita in Visual Studio.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **MvcWebRole** nella sezione **Ruoli** del progetto **AzureEmailService**, quindi scegliere **Proprietà**.

    ![Pulsante destro del mouse su Proprietà][Pulsante destro del mouse su Proprietà]

2.  Nella finestra **MvcWebRole [Ruolo]** fare clic sulla scheda **Impostazioni**.

3.  Nella casella di riepilogo **Configurazione servizio** selezionare **Locale**.

4.  Se si seleziona la voce **StorageConnectionString**, verrà visualizzato un pulsante con puntini di sospensione (**...**) all'estremità destra della riga. Fare clic su tale pulsante per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

    ![Pulsante destro del mouse su Proprietà][3]

5.  Nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione** fare clic su **Sottoscrizione** e quindi selezionare i valori in **Sottoscrizione** e in **Nome account** per l'account di archiviazione.

    ![Pulsante destro del mouse su Proprietà][4]

6.  Eseguire la stessa procedura usata per la stringa di connessione `StorageConnectionString` per impostare la stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

7.  Impostare le stringhe di connessione per i ruoli WorkerRoleA e WorkerRoleB eseguendo la stessa procedura completata per le due stringhe di connessione del ruolo MvcWebRole.

8.  Aprire il file **ServiceConfiguration.Local.cscfg** disponibile nel progetto **AzureEmailService**.

    Nell'elemento `Role` per `MvcWebRole` è possibile notare un elemento `ConfigurationSettings` che include le impostazioni aggiornate tramite l'interfaccia utente di Visual Studio.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    Si noti che negli elementi `Role` per i due ruoli di lavoro le stringhe di connessione sono identiche.

    È possibile modificare questi file direttamente anziché nella finestra **[Ruolo]** di Visual Studio. Per altre informazioni sui file di configurazione, vedere [Configurazione di un progetto Azure.][Configurazione di un progetto Azure.]

### Testare l'applicazione configurata per l'uso dell'account di archiviazione

1.  Premere CTRL+F5 per eseguire l'applicazione. Immettere alcuni dati facendo clic sui collegamenti **Mailing Lists**, **Subscribers** e **Messages** come è già stato fatto in precedenza in questa esercitazione.

2.  In Visual Studio aprire **Esplora server**.

3.  Espandere il nodo **Archiviazione** sotto il nodo **Azure** e quindi espandere il nodo per l'account di archiviazione che dovrà essere usato dall'applicazione.

4.  Espandere **Tabelle** e fare doppio clic sulla tabella `MailingList` per visualizzare i dati immessi nelle pagine **Mailing List** e **Subscriber** dell'applicazione.

### Procedura facoltativa per disabilitare l'avvio automatico dell'emulatore di archiviazione di Azure

Per ridurre i tempi di avvio del progetto e utilizzare un minor numero di risorse locali, è possibile disabilitare l'avvio automatico dell'emulatore di archiviazione di Azure se questo non viene usato.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto cloud **AzureEmailService** e scegliere **Proprietà**.

    ![Selezione delle proprietà del progetto cloud][Selezione delle proprietà del progetto cloud]

2.  Selezionare la scheda **Sviluppo**.

3.  Impostare **Start Azure storage emulator** su **False**.

    ![Disabilitazione dell'avvio automatico dell'emulatore di archiviazione][Disabilitazione dell'avvio automatico dell'emulatore di archiviazione]

    **Nota**: impostare questa opzione su false solo l'emulatore di archiviazione non viene usato.

    In questa finestra è possibile modificare da **Local** a **Cloud** (da **ServiceConfiguration.Local.cscfg** a *ServiceConfiguration.Cloud.cscfg*) il file *Service Configuration* utilizzato quando si esegue l'applicazione in locale.

4.  Nella barra delle applicazioni di Windows fare clic con il pulsante destro del mouse sull'icona dell'emulatore di calcolo e scegliere **Shutdown Storage Emulator**.

    ![ASE][ASE]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>Configurazione dell'applicazione per l'utilizzo di SendGrid

Per inviare i messaggi di posta elettronica, nell'applicazione di esempio viene usato SendGrid. Per poter inviare messaggi tramite SendGrid, è necessario configurare un account di SendGrid e quindi aggiornare un file di configurazione con le relative credenziali.

<div class="note"><p><strong>Nota:</strong> se non si desidera o non &egrave; possibile utilizzare SendGrid, &egrave; possibile sostituirlo facilmente con il proprio servizio di posta elettronica. Il codice in cui viene usato SendGrid &egrave; isolato in due metodi del ruolo di lavoro B. In [Tutorial 5][tut5] vengono descritte le impostazioni da modificare per implementare una diversa modalit&agrave; di invio dei messaggi di posta elettronica. Se non si desidera eseguire questa operazione, ignorare questa procedura e continuare con l'esercitazione; tutte le altre funzionalit&agrave; dell'applicazione, ovvero pagine Web, pianificazione della posta elettronica e cos&igrave; via, verranno eseguite normalmente, ad eccezione dell'invio effettivo dei messaggi.</p></div>

### Creare un account SendGrid

1.  Attenersi alle istruzioni in [Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure][Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure] per iscriversi e ottenere un account gratuito.

### Aggiornare le credenziali di SendGrid nelle proprietà dei ruoli di lavoro

È possibile che in precedenza nell'esercitazione, durante l'impostazione delle credenziali dell'account di archiviazione per il ruolo Web e i due ruoli di lavoro, si sia notato che per il ruolo di lavoro B erano presenti tre impostazioni non incluse nel ruolo Web o nel ruolo di lavoro A. È possibile utilizzare ora la stessa interfaccia utente per configurare queste tre impostazioni, ovvero selezionare **Cloud** nell'elenco a discesa **Configurazione servizio**.

Nella procedura seguente è illustrato un modo alternativo per impostare le proprietà, tramite la modifica del file di configurazione.

1.  Modificare il file *ServiceConfiguration.Cloud.cscfg* nel progetto `AzureEmailService` e immettere i valori di nome utente e password di SendGrid ottenuti nel passaggio seguente nell'elemento `WorkerRoleB` che include queste impostazioni. Nel codice seguente è illustrato l'elemento WorkerRoleB.

    ![SendGridSettings][SendGridSettings]

2.  È presente anche un'impostazione AzureMailServiceURL. Impostare questo valore sull'URL selezionato durante la creazione del servizio cloud di Azure, ad esempio: "<http://aescloud.cloudapp.net>".

Quando si aggiorna il file di configurazione del cloud, si configurano le impostazioni che verranno utilizzate quando l'applicazione viene eseguita nel cloud. Per fare in modo che l'applicazione invii messaggi di posta elettronica durante l'esecuzione in locale, è necessario aggiornare anche il file *ServiceConfiguration.Local.cscfg*.

## <a name="deployAz"></a><span class="short-header">Distribuzione in Azure</span>Distribuire l'applicazione in Azure

Per distribuire l'applicazione, è possibile creare un pacchetto in Visual Studio e caricarlo utilizzando il portale di gestione di Azure oppure pubblicarlo direttamente da Visual Studio. In questa esercitazione verrà utilizzato il metodo basato sulla pubblicazione.

L'applicazione verrà prima pubblicata nell'ambiente di gestione temporanea e successivamente la distribuzione nella gestione temporanea verrà innalzata di livello alla fase di produzione.

### Implementare le restrizioni IP

Quando si effettua la distribuzione nell'ambiente di gestione temporanea, l'applicazione risulta accessibile pubblicamente a chiunque ne conosca l'URL. La prima operazione da eseguire è quindi implementare restrizioni IP per evitare che possa essere utilizzata da utenti non autorizzati. In un'applicazione di produzione si implementerà un meccanismo di autenticazione e autorizzazione, ad esempio il sistema di identità di ASP.NET, tuttavia queste funzioni sono state omesse dall'applicazione di esempio per facilitarne la configurazione, la distribuzione e il test.

1.  Aprire il file *Web.Release.config* disponibile nella cartella radice del progetto `MvcWebRole` e sostituire il valore 127.0.0.1 dell'attributo **ipAddress** con il proprio indirizzo IP. Per visualizzare il file **Web.Release.config** in **Esplora soluzioni** è necessario espandere il file *Web.config*.

    Per trovare l'indirizzo IP, cercare "Trova IP" con [Bing][Bing] o con un altro motore di ricerca.

    Dopo la pubblicazione dell'applicazione, vengono applicate le trasformazioni specificate nel file *Web.release.config* e gli elementi della restrizione IP vengono aggiornati nel file *web.config* distribuito nel cloud. È possibile visualizzare il file *web.config* trasformato nella cartella *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* dopo la creazione del pacchetto.

### Configurare l'applicazione per l'utilizzo dell'account di archiviazione durante l'esecuzione nel cloud

In precedenza nell'esercitazione, durante l'impostazione delle credenziali dell'account di archiviazione per il ruolo Web e i due ruoli di lavoro, sono state impostate le credenziali da utilizzare durante l'esecuzione dell'applicazione in locale. È ora necessario impostare le credenziali dell'account di archiviazione da utilizzare quando si esegue l'applicazione nel cloud.

Per questo test si utilizzeranno per il cloud le stesse credenziali utilizzate per l'esecuzione in locale. Per la distribuzione di un'applicazione di produzione è in genere necessario utilizzare per l'ambiente di produzione un account diverso da quello per il testing. Una procedura consigliata per l'ambiente di produzione consiste inoltre nell'utilizzare un account diverso per la stringa di connessione della diagnostica rispetto a quello della stringa di connessione di archiviazione, tuttavia per questo test verrà utilizzato lo stesso account.

È possibile utilizzare la stessa interfaccia utente per configurare le stringhe di connessione. Non dimenticare di selezionare **Cloud** nell'elenco a discesa **Configurazione servizio**. In alternativa, è possibile modificare il file di configurazione, come illustrato nella procedura seguente.

1.  Aprire il file *ServiceConfiguration.Local.cscfg* nel progetto **AzureEmailService** e copiare gli elementi `Setting` per `StorageConnectionString` e `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Aprire il file *ServiceConfiguration.Cloud.cscfg* nel progetto **AzureEmailService** e incollare gli elementi copiati nell'elemento `Configuration Settings` per `MvcWebRole`, `WorkerRoleA` e `WorkerRoleB`, sostituendo gli elementi `Setting` già presenti.

3.  Verificare che le stringhe di connessione definite dal ruolo Web e dai due ruoli di lavoro siano identiche.

### Pubblicare l'applicazione

1.  Se non è già aperto, avviare Visual Studio e aprire la soluzione **AzureEmailService**.

2.  Fare clic con il pulsante destro del mouse sul progetto cloud **AzureEmailService** e scegliere **Pubblica**.

    ![Pacchetto][Pacchetto]

    Verrà visualizzata la finestra di dialogo **Publish Azure Application**.

    ![Pacchetto cloud][Pacchetto cloud]

3.  Se in precedenza è stato utilizzato il metodo automatico per importare le credenziali dell'account di archiviazione, la sottoscrizione di Azure è inclusa nell'elenco a discesa ed è possibile selezionarla facendo clic su **Next**. In caso contrario, fare clic su **Sign in to download credentials** e attenersi alle istruzioni in [Configurazione dell'applicazione per il servizio Archiviazione di Azure][Configurare l'applicazione per il servizio Archiviazione di Azure] per scaricare e importare le impostazioni di pubblicazione.

4.  Nella scheda **Impostazioni comuni** verificare le impostazioni nell'elenco a discesa **Servizio cloud**.

5.  Nell'elenco a discesa **Environment** cambiare **Production** in **Staging**.

    ![Dashboard][Dashboard]

6.  Non modificare le impostazioni predefinite **Release** per **Build configuration** e **Cloud** per **Service configuration**.

    Le impostazioni predefinite della scheda **Advanced** sono corrette per questa esercitazione. La scheda **Advanced** include alcune impostazioni utili per lo sviluppo e il testing. Per altre informazioni su questa scheda, vedere [Procedura guidata Pubblica l'applicazione Azure][Procedura guidata Pubblica l'applicazione Azure].

7.  Fare clic su **Avanti**.

8.  Nel passaggio **Summary** della procedura guidata fare clic sull'icona di **salvataggio** (immagine del dischetto visualizzata a destra dell'elenco a discesa Target profile) per salvare le impostazioni di pubblicazione.

    Alla successiva pubblicazione dell'applicazione verranno utilizzate le impostazioni salvate e non sarà necessario eseguire di nuovo la procedura guidata.

9.  Verificare le impostazioni, quindi fare clic su **Publish**.

    ![pub][pub]

	La finestra **Log attività di Azure** verrà aperta in Visual Studio.

1.  Fare clic sull'icona della freccia verso destra per espandere i dettagli della distribuzione.

    ![pub][5]


    ![pub][6]

    Il completamento della distribuzione richiede almeno 5 minuti.

2.  Quando lo stato della distribuzione è completo, fare clic su **Website URL** per avviare l'applicazione.

    ![Dashboard][7]

3.  Immettere alcuni dati nelle pagine Web **Mailing List**, **Subscriber** e **Message** per testare l'applicazione.

    **Nota**: al termine del testing eliminare l'applicazione per evitare di pagare per risorse non utilizzate. Se si utilizza un [account di valutazione gratuita di Azure][account di valutazione gratuita di Azure], l'utilizzo dei tre ruoli distribuiti comporterà l'esaurimento del limite mensile in due settimane. Per eliminare una distribuzione utilizzando il portale di gestione di Azure, selezionare il servizio cloud e fare clic su **ELIMINA** nella parte inferiore della pagina, quindi selezionare la distribuzione di produzione o di gestione temporanea.

    ![pub][8]

4.  Nel log attività di Azure in Visual Studio selezionare **Apri in Esplora server**.

    In **Calcolo di Azure** in **Esplora server** è possibile monitorare la distribuzione. Se è stata selezionata l'opzione **Enable Remote Desktop for all roles** nella procedura guidata **Publish Azure Application** è possibile fare clic con il pulsante destro del mouse su un'istanza del ruolo e scegliere **Connect using Remote Desktop**.

    ![pub][9]

## <a name="swap"></a>Innalzare di livello l'applicazione dall'ambiente di gestione temporanea all'ambiente di produzione

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic sull'icona **Servizi cloud** nel riquadro sinistro, quindi selezionare il servizio cloud e fare clic sulla scheda **Dashboard**.

2.  Fare clic su **Swap**.

3.  Fare clic su **Yes** per completare lo scambio VIP, ovvero degli IP virtuali. Il completamento di questa operazione può richiedere alcuni minuti.

    ![Dashboard][10]

4.  Scorrere verso il basso nella scheda **Dashboard** per la distribuzione **Produzione** fino alla sezione **riepilogo rapido** in basso a destra nella pagina. Si noti che l'impostazione di **Site URL** è cambiata e al posto di un prefisso GUID è visualizzato il nome del servizio cloud.

    ![Dashboard][11]

5.  Fare clic sul collegamento in **Site URL** oppure copiarlo e incollarlo in un browser per testare l'applicazione in ambiente di produzione.

    Se non sono state modificate le impostazioni dell'account di archiviazione, durante l'esecuzione dell'applicazione nel cloud verranno visualizzati i dati immessi durante il testing della versione preconfigurata dell'applicazione.

## <a name="trace"></a>Configurazione e visualizzazione dei dati traccia

La traccia costituisce uno strumento insostituibile per il debug di un'applicazione cloud. In questa sezione dell'esercitazione verrà illustrato come visualizzare i dati traccia.

1.  Verificare che la stringa di connessione di diagnostica sia configurata per l'utilizzo dell'account di archiviazione di Azure e non dell'archivio di sviluppo.

    Se sono state seguite le istruzioni riportate in precedenza nell'esercitazione, tali valori saranno identici. Per verificarlo, è possibile utilizzare l'interfaccia utente di Visual Studio (scheda **Impostazioni** in **Proprietà** per i ruoli) oppure esaminare i file *ServiceConfiguration.\*.cscfg*.

	**Nota:** la procedura consigliata consiste nell'utilizzare per i dati traccia un account di archiviazione diverso rispetto a quello utilizzato per i dati di produzione, tuttavia per semplicità in questa esercitazione è stato configurato lo stesso account per la traccia.

1.  In Visual Studio aprire *WorkerRoleA.cs* nel progetto **WorkerRoleA**, cercare `ConfigureDiagnostics` ed esaminare il metodo `ConfigureDiagnostics`.

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    In questo codice, l'elemento `DiagnosticMonitor` è configurato in modo da consentire l'archiviazione di un massimo di 500 MB di dati traccia (dopo tale limite i dati meno recenti verranno sovrascritti) e da archiviare tutti i messaggi di traccia (LogLevel.Verbose). L'elemento `ScheduledTransferPeriod` indica che i dati traccia vengono trasferiti nell'archivio a intervalli di un minuto. È necessario impostare `ScheduledTransferPeriod` in modo da salvare i dati traccia.

    Il metodo `ConfigureDiagnostics` in ogni ruolo di lavoro e Web consente di configurare il listener di traccia in modo da registrare i dati quando si chiama l'API di traccia. Per altre informazioni, vedere l'articolo relativo all'[utilizzo della traccia nelle applicazioni cloud di Windows Azure][utilizzo della traccia nelle applicazioni cloud di Windows Azure]

2.  In **Esplora server** fare doppio clic su **WADLogsTable** (espandere **Azure** / **Archiviazione** / **nome account di archiviazione** / **Tabelle**) per l'account di archiviazione aggiunto in precedenza. È possibile immettere un [filtro Servizi dati WCF][filtro Servizi dati WCF] per limitare il numero di entità visualizzate. Nell'immagine seguente vengono visualizzati solo i messaggi di avviso e di errore.

    ![Dashboard][12]

## <a name="addRole"></a>Aggiungere un'altra istanza del ruolo di lavoro per gestire un carico maggiore

Per la scalabilità delle risorse di calcolo nei ruoli di Azure sono disponibili due approcci, ovvero mediante la specifica delle [dimensioni delle macchine virtuali][dimensioni delle macchine virtuali] e/o mediante la specifica del numero di istanze di macchine virtuali in esecuzione.

La dimensione della macchia virtuale viene specificata nell'attributo `vmsize` dell'elemento `WebRole` o `WorkerRole` del file *ServiceDefinition.csdef*. L'impostazione predefinita è `Small`, che consente di disporre di un solo core e di 1,75 GB di RAM. Per applicazioni multithread che utilizzano una grande quantità di memoria, spazio su disco e larghezza di banda, è possibile aumentare la dimensione della macchina virtuale per migliorare le prestazioni. Ad esempio con una macchina virtuale `ExtraLarge` è possibile disporre di 8 core CPU e 14 GB di RAM. L'aumento di memoria, core CPU, spazio su disco e larghezza di banda su un unico computer è noto come *scalabilità verticale*. Candidati ideali per la scalabilità verticale sono le applicazioni Web ASP.NET che utilizzano [metodi asincroni][metodi asincroni]. Per una descrizione delle risorse disponibili con le diverse dimensioni di macchine virtuali, vedere [Dimensioni delle macchine virtuali][dimensioni delle macchine virtuali].

Il ruolo di lavoro B in questa applicazione costituisce il componente limite in caso di carico elevato dal momento che esegue l'invio dei messaggi di posta elettronica. Il ruolo di lavoro A si limita a creare i messaggi della coda, attività che non richiede un uso intensivo delle risorse. Dal momento che il ruolo di lavoro B non è multithread e non dispone di un footprint di memoria esteso, non costituisce un candidato ottimale per la scalabilità verticale. Per il ruolo di lavoro B è possibile applicare una scalabilità lineare, ovvero in grado di raddoppiare le prestazioni quando si raddoppiano le istanze, aumentando il numero delle istanze. L'aumento del numero di istanze di calcolo è noto come *scalabilità orizzontale*. Dal momento che la scalabilità orizzontale implica un costo per ciascuna istanza, è consigliabile utilizzarla solo quando richiesto dall'applicazione.

È possibile applicare la scalabilità orizzontale a un ruolo Web o di lavoro aggiornando l'impostazione nell'interfaccia utente di Visual Studio oppure modificando direttamente i file *ServiceConfiguration.\*.cscfg*. Il numero delle istanze viene specificato nella scheda **Configurazione** della finestra **Proprietà** del ruolo e nell'elemento `Instances` dei file con estensione *cscfg*. Quando si aggiorna l'impostazione, è necessario distribuire il file di configurazione aggiornato per rendere effettiva la modifica. In alternativa, per gli incrementi di carico transitori, è possibile cambiare manualmente il numero di istanze del ruolo o configurare Azure in modo che modifichi manualmente il numero delle istanze in base ai criteri specificati. Per altre informazioni sulla scalabilità automatica, vedere l'[ultima esercitazione di questa serie][ultima esercitazione di questa serie].

In questa sezione dell'esercitazione si applicherà la scalabilità orizzontale al ruolo di lavoro B utilizzando il portale di gestione, ma verrà prima illustrato come farlo in Visual Studio.

Per effettuare questa operazione in Visual Studio, è necessario fare clic con il pulsante destro del mouse sul ruolo in **Ruoli** nel progetto cloud e scegliere **Proprietà**.

![Pulsante destro del mouse su Proprietà][Pulsante destro del mouse su Proprietà]

Scegliere quindi la scheda **Configurazione** sulla sinistra e selezionare **Cloud** nell'elenco a discesa **Configurazione**.

![Conteggio istanze][Conteggio istanze]

Si noti che in questa scheda è anche possibile configurare le dimensioni delle macchine virtuali.

Nella procedura seguente viene spiegato come applicare la scalabilità orizzontale utilizzando il portale di gestione di Azure.

1.  Nel portale di gestione di Azure selezionare il servizio cloud e quindi fare clic su **Scale**.

2.  Aumentare il numero di istanze per il ruolo di lavoro B, quindi fare clic su **Save**.

    ![Aumento del numero di istanze][Aumento del numero di istanze]

    Il provisioning delle nuove macchine virtuali può richiedere alcuni minuti.

3.  Selezionare la scheda **Istanze** per visualizzare le singole istanze del ruolo nell'applicazione.

    ![Visualizzazione delle istanze][Visualizzazione delle istanze]

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare, distribuire e scalare l'applicazione completata. Nelle esercitazioni seguenti verrà illustrato come creare l'applicazione da zero. Nell'[esercitazione successiva][esercitazione successiva] verrà creato il ruolo Web.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB di Archiviazione di Azure, vedere l'[ultima esercitazione di questa serie][13].

<div><a href="/it-it/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Esercitazione 3</a></div>

  [prima esercitazione della serie]: /it-it/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Configurare l'ambiente di sviluppo]: #setupdevenv
  [Scaricare ed eseguire la soluzione completata]: #downloadcnfg
  [Creare un account di archiviazione di Azure]: #createWASA
  [Creare un servizio cloud]: #createcloudsvc
  [Configurare l'applicazione per il servizio Archiviazione di Azure]: #conf4azureStorage
  [Configurare l'applicazione per l'uso di SendGrid]: #sendGrid
  [Distribuire l'applicazione in Azure]: #deployAz
  [Innalzare di livello l'applicazione dall'ambiente di gestione temporanea all'ambiente di produzione]: #swap
  [Configurare e visualizzare i dati traccia]: #trace
  [Aggiungere un'altra istanza del ruolo di lavoro per gestire un carico maggiore]: #addRole
  [soluzione completata]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Eseguire l'app.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Pagina di indice della lista di distribuzione]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Pagina di indice dei sottoscrittori]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [Pagina di creazione del messaggio]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [Pagina di indice dei messaggi]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Esplora server]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [Esplora archivi in Visual Studio]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Esplora archivi Azure]: http://azurestorageexplorer.codeplex.com/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Nuovo archivio]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Quick Create]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [come gestire gli account di archiviazione]: /it-it/manage/services/storage/how-to-manage-a-storage-account/
  [Creazione dell'account di archiviazione con prefisso URL]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Manage Keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [Keys GUID]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Creazione rapida di un cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Come creare un gruppo di affinità in Azure]: http://msdn.microsoft.com/it-it/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Pulsante destro del mouse su Proprietà]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Configurazione di un progetto Azure.]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405486.aspx
  [Selezione delle proprietà del progetto cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Disabilitazione dell'avvio automatico dell'emulatore di archiviazione]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure]: http://www.windowsazure.com/it-it/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
  [Pacchetto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Pacchetto cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Dashboard]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Procedura guidata Pubblica l'applicazione Azure]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [account di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/ "free-trial account"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [utilizzo della traccia nelle applicazioni cloud di Windows Azure]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Windows Azure"
  [filtro Servizi dati WCF]: http://msdn.microsoft.com/it-it/library/windowsazure/ff683669.aspx "WCF filter"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [dimensioni delle macchine virtuali]: http://msdn.microsoft.com/it-it/library/windowsazure/ee814754.aspx "VM sizes"
  [metodi asincroni]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"
  [ultima esercitazione di questa serie]: /it-it/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Conteggio istanze]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [Aumento del numero di istanze]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [Visualizzazione delle istanze]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [esercitazione successiva]: /it-it/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [13]: /it-it/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
