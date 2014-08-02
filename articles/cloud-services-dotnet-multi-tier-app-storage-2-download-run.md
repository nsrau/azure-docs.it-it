<properties  linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Step 2: Download and Run" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 2: Download and run" metaKeywords="Azure tutorial, deploying email service app, publishing email service" description="The second tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configuring and Deploying the Azure Email Service application - 2 of 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Configurazione e distribuzione dell'applicazione Azure Email Service - 2 di 5

Questa è la seconda di una serie di cinque esercitazioni in cui viene illustrato come compilare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In questa esercitazione viene illustrato come configurare il computer per lo sviluppo in Azure e come distribuire l'applicazione Azure Email Service in un servizio cloud Azure utilizzando uno dei seguenti prodotti:

* Visual Studio 2012
* Visual Studio 2012 Express per il Web
* Visual Studio 2010
* Visual Web Developer Express 2010

> [WACOM.NOTE] Dopo la stesura di questa esercitazione è stato rilasciato Visual Studio 2013 e sono stati aggiornati sia il portale di gestione che l'SDK di Azure. Note simili a questa sono state aggiunte nei punti in cui la procedura è leggermente diversa quando si utilizza Visual Studio 2013 e la versione più recente dell'SDK. Le note sono state scritte nel mese di marzo 2014 e le procedure modificate sono state testate con la versione 2.3 dell'SDK.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. Sarà quindi possibile iniziare a sviluppare per Azure del tutto gratuitamente.

In questa esercitazione si apprenderà come:

* Configurare il computer per lo sviluppo in Azure installando Azure SDK.
* Configurare e testare l'applicazione Azure Email Service nel computer locale.
* Pubblicare l'applicazione in Azure.
* Visualizzare e modificare tabelle, code e BLOB di Azure utilizzando Visual Studio o Esplora archivi Azure.
* Configurare la traccia e visualizzare i dati traccia.
* Ridimensionare l'applicazione aumentando il numero di istanze del ruolo di lavoro.

<div class="dev-callout"><strong>Nota</strong>
<p>Per completare l'esercitazione, è necessario un account Azure. È possibile creare un <a href="/en-us/pricing/free-trial/" target="_blank">account di valutazione gratuita</a> or <a href="/en-us/pricing/member-offers/msdn-benefits/" target="_blank">attivare i benefici della sottoscrizione MSDN</a>.</p>
</div>
<br />


### Sezioni dell'esercitazione

* [Configurazione dell'ambiente di sviluppo](#setupdevenv)
* [Configurazione di un account Azure gratuito](#setupwindowsazure)
* [Creazione di un account di archiviazione di Azure](#createWASA)
* [Installazione di Esplora archivi Azure](#installASE)
* [Creazione di un servizio cloud](#createcloudsvc)
* [Download ed esecuzione della soluzione completata](#downloadcnfg)
* [Visualizzazione delle risorse di archiviazione per sviluppatori in Visual Studio](#StorageExpVS)
* [Configurazione dell'applicazione per il servizio Archiviazione di Azure](#conf4azureStorage)
* [Distribuzione dell'applicazione in Azure](#deployAz)
* [Innalzamento di livello dell'applicazione dalla gestione temporanea alla produzione](#swap)
* [Configurazione dell'applicazione per l'utilizzo di SendGrid](#sendGrid)
* [Configurazione e visualizzazione dei dati traccia](#trace)
* [Aggiunta di un'altra istanza del ruolo di lavoro per gestire un carico maggiore](#addRole)

> [WACOM.NOTE] Nella sezione seguente relativa all'installazione dell'SDK, il collegamento corretto per gli utenti di Visual Studio 2013 è [http://go.microsoft.com/fwlink/?LinkID=324322][1].

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

<h2><a name="createWASA"></a><span  class="short-header">Creazione di un account di archiviazione</span>Creazione di un account di archiviazione di Azure</h2>


Quando si esegue l'applicazione di esempio in Visual Studio, è possibile accedere a tabelle, code e BLOB nell'archivio di sviluppo di Azure oppure in un account di archiviazione di Azure nel cloud. Per emulare il funzionamento del servizio Archiviazione di Azure nel cloud, nell'archivio di sviluppo viene utilizzato un database LocalDB di SQL Server Express. In questa esercitazione si inizierà a utilizzare l'archivio di sviluppo e si imparerà a configurare l'applicazione per l'utilizzo di un account di archiviazione cloud quando viene eseguita in Visual Studio. In questa sezione dell'esercitazione si creerà l'account di archiviazione di Azure che verrà configurato per l'uso in Visual Studio più avanti.

1.  Accedere al [portale di gestione di Azure][2] dal browser.

2.  Nel [portale di gestione di Azure][2] fare clic su **Storage** e quindi su **New**.
    
    ![Nuovo archivio](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png)

3.  Fare clic su **Quick Create**.
    
    ![Quick Create](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png)

4.  Nella casella di immissione dell'URL immettere un prefisso URL.
    
    La combinazione di questo prefisso e del testo visualizzato sotto la casella corrisponderà all'URL univoco dell'account di archiviazione. Se il prefisso immesso è già stato utilizzato da un altro utente, verrà visualizzato il messaggio "The storage name is already in use" sopra la casella di testo e sarà necessario selezionarne uno diverso.

5.  Impostare come area quella in cui si desidera distribuire l'applicazione.

6.  Impostare la casella di riepilogo **Replication** su **Locally redundant**.
    
    Quando per un account di archiviazione è abilitata la replica geografica, il contenuto archiviato viene replicato in una posizione geografica secondaria per consentire il failover in tale posizione in caso di errore grave nella posizione primaria. La replica geografica può comportare costi aggiuntivi. Per gli account di test e di sviluppo si preferisce in genere non pagare per la replica geografica. Per ulteriori informazioni, vedere l'articolo su [come gestire gli account di archiviazione](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

7.  Fare clic su **Create Storage Account**.
    
    Nell'immagine seguente viene creato un account di archiviazione il cui URL è `aestest3.core.windows.net`.
    
    ![Creazione dell'account di archiviazione con prefisso URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png)
    
    Il completamento di questa operazione può richiedere alcuni minuti. Durante l'attesa è possibile ripetere la procedura e creare un account di archiviazione di produzione. È spesso comodo disporre di un account di archiviazione di prova da utilizzare per lo sviluppo in locale, di un altro account di archiviazione di prova per il testing in Azure e di un account di archiviazione di produzione.

8.  Fare clic sull'account di prova creato nel passaggio precedente, quindi fare clic sull'icona **Manage Access Keys**.
    
    ![Gestione delle chiavi](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)
    
    ![GUID delle chiavi](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)
    

    Nelle varie fasi di questa esercitazione sarà necessaria la **chiave di accesso primaria** o la **chiave di accesso secondaria**. È possibile utilizzare una di tali chiavi in una stringa di connessione di archiviazione.
    
    La presenza di due chiavi consente di modificare periodicamente quella utilizzata senza causare interruzioni del servizio in un'applicazione attiva. Dopo aver rigenerato la chiave non utilizzata, sarà possibile modificare la stringa di connessione nell'applicazione in modo che utilizzi la chiave rigenerata. Se fosse disponibile una sola chiave, l'applicazione perderebbe la connettività all'account di archiviazione durante la rigenerazione della chiave. Le chiavi mostrate in figura non sono più valide perché sono state rigenerate dopo l'acquisizione dell'immagine.

9.  Copiarne una negli appunti per utilizzarla nella sezione successiva.

<h2><a name="installASE"></a><span class="short-header">Installazione di Esplora archivi Azure</span>Installazione di Esplora archivi Azure</h2>


**Esplora archivi Azure** è uno strumento che è possibile utilizzare per aggiornare ed effettuare ricerche in tabelle, code e BLOB di Azure. Verrà utilizzato in tutte queste esercitazioni per verificare il corretto aggiornamento dei dati e per creare dati di test.

1.  Installare [Esplora archivi Azure][3].
    
    Le schermate si riferiscono a Esplora archivi Azure 4, ma se si preferisce è possibile installare la versione 5.

2.  Avviare **Esplora archivi Azure** e fare clic su **Add Account**.
    
    ![Aggiunta di un account in Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png)

3.  Immettere il nome dell'account di archiviazione di prova e incollare la chiave copiata in precedenza.

4.  Fare clic su **Add Storage Account**.
    
    ![Aggiunta di un account in Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png)

Sono inoltre disponibili altri strumenti compatibili con il servizio Archiviazione di Azure. Per ulteriori informazioni, vedere il post relativo a [Esplora archivi Azure (2014)][4].

<h2><a name="createcloudsvc"></a><span class="short-header">Creazione di un servizio cloud</span>Creazione di un servizio cloud</h2>


1.  Accedere al [portale di gestione di Azure][2] dal browser.

2.  Fare clic su **Cloud Services** e quindi sull'icona **New**.
    
    ![Creazione rapida di un cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png)

3.  Fare clic su **Quick Create**.

4.  Nella casella di immissione dell'URL immettere un prefisso URL.
    
    Analogamente a quello di archiviazione, anche questo URL deve essere univoco e verrà visualizzato un messaggio di errore se il prefisso scelto è già utilizzato da un altro utente.

5.  Impostare come area quella in cui si desidera distribuire l'applicazione.
    
    È consigliabile creare il servizio cloud nella stessa area in cui è stato creato l'account di archiviazione. Quando il servizio cloud e l'account di archiviazione si trovano in data center diversi (aree diverse), la latenza aumenterà e verrà addebitato il costo relativo alla larghezza di banda esterna al data center. La larghezza di banda nell'ambito di un data center è gratuita.
    
    I gruppi di affinità di Azure offrono un meccanismo per ridurre la distanza tra le risorse in un data center e di conseguenza la latenza. In questa esercitazione non vengono utilizzati gruppi di affinità. Per ulteriori informazioni, vedere [Come creare un gruppo di affinità in Azure][5].

6.  Fare clic su **Create Cloud Service**.
    
    Nell'immagine seguente viene creato un servizio cloud il cui URL è aescloud.cloudapp.net.
    
    ![Creazione dell'account di archiviazione con prefisso URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png)
    
    È possibile procedere con il passaggio successivo senza attendere il completamento di questa operazione.

<h2><a name="downloadcnfg"></a><span  class="short-header">Download ed esecuzione</span>Download ed esecuzione della soluzione completata</h2>


1.  Scaricare e decomprimere la [soluzione completata][6].

2.  Avviare Visual Studio con autorizzazioni elevate.
    
    Con l'emulatore di calcolo che consente a Visual Studio di eseguire un progetto di Azure in locale sono necessarie autorizzazioni elevate.
    
    > [WACOM.NOTE] Con SDK 2.3 e versioni successive l'emulatore di calcolo predefinito di Azure non necessita di autorizzazioni elevate, tuttavia questo progetto è configurato per l'uso dell'emulatore di calcolo originale, pertanto tali autorizzazioni sono necessarie.

3.  Ripristinare i pacchetti NuGet.
    
    Per ridurre al minimo le dimensioni del download, la soluzione completa viene fornita senza gli assembly o gli altri contenuti relativi ai pacchetti NuGet installati. Quando si apre e si compila la soluzione, il contenuto del pacchetto viene recuperato automaticamente. A tale scopo, è necessario abilitare l'opzione per il ripristino dei pacchetti NuGet in Visual Studio. Se il ripristino dei pacchetti NuGet non è ancora stato abilitato, eseguire la procedura seguente.
    
    > [WACOM.NOTE] In Visual Studio 2013 l'impostazione predefinita prevede l'abilitazione del ripristino automatico dei pacchetti, tuttavia, poiché il progetto da aprire è di Visual Studio 2012, il ripristino non verrà eseguito automaticamente. Attendere l'apertura della soluzione, quindi aprire la finestra di dialogo **Manage NuGet Packages** come indicato di seguito. In alto a destra verrà visualizzato un pulsante **Restore**: fare clic su questo pulsante per ripristinare i pacchetti.
    
         1. Nel menu **Strumenti** fare clic su **Gestione pacchetti libreria**, quindi scegliere **Manage NuGet Packages for Solution**. 

         2. Nell'angolo in basso a sinistra della finestra di dialogo **Manage NuGet Packages** fare clic su **Settings**.

         3. Nel riquadro sinistro della finestra di dialogo **Options** selezionare **General** in **Package Manager**.

         4. Selezionare **Allow NuGet to download missing packages during build**.
    
    ![Abilitazione del ripristino dei pacchetti NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

4.  Nel menu **File** scegliere **Open Project**, passare alla cartella in cui è stata scaricata la soluzione, quindi aprire il file di soluzione.

5.  In **Esplora soluzioni** verificare che come progetto di avvio sia selezionato **AzureEmailService**.

6.  Premere CTRL+F5 per eseguire l'applicazione.
    
    > [WACOM.NOTE] Per consentire la compilazione della soluzione, è necessario aggiungere i riferimenti agli assembly dell'SDK correnti. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto MvcWebRole, quindi scegliere **Aggiungi** -- **Riferimento**. In **Assembly** fare clic su **Estensioni**. Selezionare *Microsoft.WindowsAzure.Diagnostics* e *Microsoft.WindowsAzure.ServiceRuntime*, quindi WorkerRoleB.
    
    La home page dell'applicazione verrà visualizzata nel browser.
    
    ![Eseguire l'app.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png)

7.  Fare clic su **Create New**.

8.  Immettere alcuni dati di test, quindi fare clic su **Create**.
    
    ![Eseguire l'app.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png)

9.  Creare ancora un paio di voci della lista di distribuzione.
    
    ![Pagina di indice della lista di distribuzione](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png)

10. Fare clic su **Subscribers**, quindi aggiungere alcuni sottoscrittori. Impostare **Verified** su `true`.
    
    ![Pagina di indice dei sottoscrittori](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png)

11. Per preparare l'aggiunta di messaggi, creare un file con estensione *txt* che contiene il corpo di un messaggio di posta elettronica da inviare. Creare quindi un file con estensione *htm* contenente lo stesso testo e codice HTML, ad esempio per formattare una delle parole del messaggio in grassetto o in corsivo. Tali file verranno utilizzati nel passaggio successivo.

12. Fare clic su **Messages**, quindi aggiungere alcuni messaggi. Selezionare i file creati nel passaggio precedente. Non modificare la data pianificata, il cui valore predefinito è impostato tra una settimana. L'applicazione non è in grado di inviare messaggio finché non viene configurato SendGrid.
    
    ![Pagina di creazione del messaggio](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png)
    <br/><br/>
     ![Pagina dell'indice dei messaggi](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png)

I dati immessi e visualizzati vengono inseriti nell'archivio di sviluppo di Azure. Per emulare il funzionamento del servizio Archiviazione di Azure nel cloud, nell'archivio di sviluppo viene utilizzato un database LocalDB di SQL Server Express. L'applicazione utilizza l'archivio di sviluppo perché corrisponde a quanto configurato per l'uso nel progetto durante il download. Questa impostazione è archiviata nei file con estensione *cscfg* nel progetto **AzureEmailService**. Il file *ServiceConfiguration.Local.cscfg* determina quello che verrà utilizzato quando l'applicazione viene eseguita in locale in Visual Studio, mentre *ServiceConfiguration.Cloud.cscfg* determina quello che verrà utilizzato quando l'applicazione viene distribuita nel cloud. Più avanti verrà spiegato come configurare l'applicazione per l'utilizzo dell'account di archiviazione di Azure creato in precedenza.

<h2><a name="StorageExpVS"></a><span class="short-header">Risorse di archiviazione per sviluppatori</span>Visualizzazione delle risorse di archiviazione per sviluppatori in Visual Studio</h2>


Il browser **Archiviazione di Azure** in **Esplora server** offre una comoda visualizzazione di sola lettura delle risorse di Archiviazione di Azure.

1.  Nel menu **Visualizza** di Visual Studio scegliere **Esplora server**.

2.  Espandere il nodo **(Development)** sotto **Azure Storage**.
    
    > [WACOM.NOTE] Con la versione corrente dell'SDK espandere **Windows Azure** / **Storage** / **(Development)**.

3.  Espandere **Tables** per visualizzare le tabelle create nei passaggi precedenti.
    
    ![Esplora server](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png)

4.  Fare doppio clic sulla tabella **MailingList**.
    
    ![Esplora archivi in Visual Studio](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png)
    
    Si noti che nella finestra sono illustrati i diversi schemi della tabella. Alle entità `MailingList` sono assegnate le proprietà `Description` e `FromEmailAddress`, mentre alle le entità `Subscriber` sono assegnate le proprietà `Verified` e `SubscriberGUID` (quest'ultima non visualizzata perché l'immagine non è abbastanza larga). La tabella include le colonne per tutte le proprietà. Se una data riga si riferisce a un'entità che non include una certa proprietà, la cella è vuota.

Non è possibile utilizzare il browser di archiviazione in Visual Studio per aggiornare o eliminare risorse di archiviazione di Azure. È possibile utilizzare [Esplora archivi Azure][3] per aggiornare o eliminare risorse di archiviazione di sviluppo. Per configurare Esplora archivi Azure per l'uso dell'archivio di sviluppo, fare clic sulla casella di controllo **Developer Storage** nella finestra di dialogo **Add Storage Account**.

> [WACOM.NOTE] Con la versione più recente dell'SDK è possibile aggiornare l'archivio di sviluppo in **Esplora server**.

<h2><a name="conf4azureStorage"></a><span  class="short-header">Utilizzo dell'account di archiviazione</span>Configurazione dell'applicazione per l'utilizzo dell'account di archiviazione di Azure</h2>


Verrà ora spiegato come configurare l'applicazione per l'utilizzo di un account di archiviazione di Azure, anziché dell'archivio di sviluppo, quando viene eseguita in Visual Studio. Sono disponibili due procedure, una più recente, introdotta nella versione 1.8 dell'SDK, per eseguire l'operazione in Visual Studio, e uno meno recente che implica la copia delle impostazioni dal portale di gestione di Azure. Di seguito verrà illustrata la procedura più recente per configurare le impostazioni dell'account di archiviazione.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **MvcWebRole** nella sezione **Ruoli** del progetto **AzureEmailService**, quindi scegliere **Proprietà**.
    
    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)
    
    

2.  Fare clic sulla scheda **Impostazioni**. Nella casella di riepilogo **Configurazione servizio** selezionare **Locale**.

3.  Se si seleziona la voce **StorageConnectionString**, verrà visualizzato un pulsante con puntini di sospensione (**...**) all'estremità destra della riga. Fare clic su tale pulsante per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.
    
    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)
    
    

4.  Nella finestra di dialogo **Create Storage Connection String** fare clic su **Your subscription** e quindi su **Download Publish Settings**.
    
    > [WACOM.NOTE] Nella versione più recente dell'SDK questa finestra di dialogo consente di accedere ad Azure e di selezionare semplicemente l'account di archiviazione da utilizzare.
    
    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png)
    
    
    
    In Visual Studio verrà avviata una nuova istanza del browser predefinito, con l'URL della pagina di download delle impostazioni di pubblicazione nel portale di gestione di Azure. Se non si è connessi al portale, verrà richiesto di effettuare l'accesso. Dopo l'accesso, nel browser verrà richiesto di salvare le impostazioni di pubblicazione. Prendere nota del percorso in cui si salvano le impostazioni.
    
    ![Impostazioni di pubblicazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png)

5.  Nella finestra di dialogo **Create Storage Connection String** fare clic su **Import** e quindi passare al file di impostazioni di pubblicazione salvato nel passaggio precedente.

6.  Selezionare la sottoscrizione e l'account di archiviazione che si desidera utilizzare, quindi fare clic su **OK**.
    
    ![Selezione dell'account di archiviazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png)

7.  Impostare la stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` eseguendo la stessa procedura completata per la stringa di connessione `StorageConnectionString`.
    
    Non è necessario scaricare di nuovo il file di impostazioni di pubblicazione. Quando si fa clic sui puntini di sospensione accanto alla stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, si noterà che le informazioni sulla sottoscrizione sono memorizzate nella finestra di dialogo **Create Storage Connection String**. Dopo aver fatto clic sul pulsante di opzione **Your subscription**, è sufficiente selezionare gli stessi valori di **Subscription** e **Account Name** selezionati in precedenza e quindi fare clic su **OK**.

8.  Impostare le stringhe di connessione per i ruoli WorkerRoleA e WorkerRoleB eseguendo la stessa procedura completata per le due stringhe di connessione del ruolo MvcWebRole.

### Metodo manuale per configurare le credenziali dell'account di archiviazione

Di seguito verrà illustrata la procedura per configurare manualmente le impostazioni dell'account di archiviazione. Se si è utilizzato il metodo automatico illustrato nella procedura precedente, è possibile ignorare questa procedura oppure continuare a leggere per conoscere i dettagli relativi ai passaggi.

1.  Accedere al [portale di gestione di Azure][2] dal browser.

2.  Fare clic sulla scheda **Storage** e quindi sull'account di prova creato nel passaggio precedente, quindi scegliere l'icona **Manage Keys**.
    
    ![Gestione delle chiavi](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)<br/>
    
    
    ![GUID delle chiavi](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)<br/>
    
    

3.  Copiare la chiave di accesso primaria o secondaria.

4.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **MvcWebRole** nella sezione **Ruoli** del progetto **AzureEmailService**, quindi scegliere **Proprietà**.
    
    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)
    
    

5.  Fare clic sulla scheda **Impostazioni**. Nella casella di riepilogo **Configurazione servizio** selezionare **Locale**.

6.  Se si seleziona la voce **StorageConnectionString**, verrà visualizzato un pulsante con puntini di sospensione (**...**) all'estremità destra della riga. Fare clic su tale pulsante per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.
    
    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)
    
    

7.  Nella finestra di dialogo **Create Storage Connection String** selezionare il pulsante di opzione **Manually entered credentials**. Immettere il nome dell'account di archiviazione e la chiave di accesso primaria o secondaria copiata dal portale.

8.  Fare clic su **OK**.

È possibile utilizzare la stessa procedura per configurare le impostazioni per i ruoli di lavoro. In alternativa, è possibile propagare le impostazioni dei ruoli Web ai ruoli di lavoro modificando il file di configurazione. Nella procedura seguente viene spiegato come modificare il file di configurazione. Queste istruzioni fanno ancora parte del metodo manuale per impostare le credenziali dell'account di archiviazione, pertanto non è necessario eseguire queste operazioni se le impostazioni sono già state propagate ai ruoli di lavoro con il metodo automatico.

1.  Aprire il file **ServiceConfiguration.Local.cscfg** disponibile nel progetto **AzureEmailService**.
    
    Nell'elemento `Role` di `MvcWebRole` è possibile notare un elemento `ConfigurationSettings` che include le impostazioni aggiornate tramite l'interfaccia utente di Visual Studio UI.
    
           <Role  name="MvcWebRole">
             <Instances  count="1" />
             <ConfigurationSettings>
               <Setting  name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
               <Setting  name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
             </ConfigurationSettings>
           </Role>
    
    Si noti che negli elementi `Role` dei due ruoli di lavoro le stringhe di connessione sono identiche.

2.  Eliminare gli elementi `Setting` per queste due stringhe di connessione dagli elementi `WorkerRoleA` e `WorkerRoleB`, quindi copiare e incollare al loro posto gli elementi `Setting` dell'elemento `MvcWebRole`.

Per ulteriori informazioni sui file di configurazione, vedere [Configurazione di un progetto Azure.][7]

### Testare l'applicazione configurata per l'uso dell'account di archiviazione

1.  Premere CTRL+F5 per eseguire l'applicazione. Immettere alcuni dati facendo clic sui collegamenti **Mailing Lists**, **Subscribers** e **Messages** come è già stato fatto in precedenza in questa esercitazione.

È ora possibile utilizzare **Esplora archivi Azure** o **Esplora server** per visualizzare i dati immessi dall'applicazione nelle tabelle di Azure.

### Utilizzare Esplora archivi Azure per visualizzare i dati immessi nell'account di archiviazione

1.  Aprire **Esplora archivi Azure**.

2.  Selezionare l'account di archiviazione per il quale sono state immesse le credenziali in precedenza.

3.  In **Storage Type** selezionare **Tables**.

4.  Selezionare la tabella `MailingList`, quindi fare clic su **Query** per visualizzare i dati immessi nelle pagine **Mailing List** e **Subscriber** dell'applicazione.

   ![Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png)<br/>

### Utilizzare Esplora server per visualizzare i dati immessi nell'account di archiviazione

1.  In **Esplora server** (o **Esplora database**) fare clic con il pulsante destro del mouse su **Azure Storage** e scegliere **Aggiungi nuovo account di archiviazione**.

2.  Attenersi alla stessa procedura utilizzata in precedenza per impostare le credenziali dell'account di archiviazione.

3.  Espandere il nuovo nodo in **Azure Storage** per visualizzare i dati archiviati nell'account di archiviazione di Azure.
    
    ![Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png)<br/>

### Procedura facoltativa per disabilitare l'avvio automatico dell'emulatore di archiviazione di Azure

Per ridurre i tempi di avvio del progetto e utilizzare un minor numero di risorse locali, è possibile disabilitare l'avvio automatico dell'emulatore di archiviazione di Azure se questo non viene utilizzato.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto cloud **AzureEmailService** e scegliere **Proprietà**.
    
    ![Selezione delle proprietà del progetto cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png)
    
    

2.  Selezionare la scheda **Sviluppo**.

3.  Impostare **Start Azure storage emulator** su **False**.
    
    ![Disabilitazione dell'avvio automatico dell'emulatore di archiviazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png)
    
    
    
    **Nota**: impostare questa opzione su false solo l'emulatore di archiviazione non viene utilizzato.
    
    In questa finestra è possibile modificare da **Local** a **Cloud** (da **ServiceConfiguration.Local.cscfg** a *ServiceConfiguration.Cloud.cscfg*) il file *Service Configuration* utilizzato quando si esegue l'applicazione in locale.

4.  Nella barra delle applicazioni di Windows fare clic con il pulsante destro del mouse sull'icona dell'emulatore di calcolo e scegliere **Shutdown Storage Emulator**.
    
    ![Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png)
    
    

<h2><a name="sendGrid"></a><span  class="short-header">SendGrid</span>Configurazione dell'applicazione per l'utilizzo di SendGrid</h2>


Per inviare i messaggi di posta elettronica, nell'applicazione di esempio viene utilizzato SendGrid. Per poter inviare messaggi tramite SendGrid, è necessario configurare un account di SendGrid e quindi aggiornare un file di configurazione con le relative credenziali.


<div class="note"><p><strong>Nota:</strong> se non si desidera o non è possibile utilizzare SendGrid, è possibile sostituirlo facilmente con il proprio servizio di posta elettronica. Il codice in cui viene utilizzato SendGrid è isolato in due metodi del ruolo di lavoro B. In [Tutorial 5][tut5] vengono descritte le impostazioni da modificare per implementare una diversa modalità di invio dei messaggi di posta elettronica. Se non si desidera eseguire questa operazione, ignorare questa procedura e continuare con l'esercitazione; tutte le altre funzionalità dell'applicazione, ovvero pagine Web, pianificazione della posta elettronica e così via, verranno eseguite normalmente, ad eccezione dell'invio effettivo dei messaggi.</p></div>


### Creare un account SendGrid

1.  Attenersi alle istruzioni in [Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure][8] per iscriversi e ottenere un account gratuito.

### Aggiornare le credenziali di SendGrid nelle proprietà dei ruoli di lavoro

È possibile che in precedenza nell'esercitazione, durante l'impostazione delle credenziali dell'account di archiviazione per il ruolo Web e i due ruoli di lavoro, si sia notato che per il ruolo di lavoro B erano presenti tre impostazioni non incluse nel ruolo Web o nel ruolo di lavoro A. È possibile utilizzare ora la stessa interfaccia utente per configurare queste tre impostazioni, ovvero selezionare **Cloud** nell'elenco a discesa **Configurazione servizio**.

Nella procedura seguente è illustrato un modo alternativo per impostare le proprietà, tramite la modifica del file di configurazione.

1.  Modificare il file *ServiceConfiguration.Cloud.cscfg* nel progetto `AzureEmailService` e immettere i valori di nome utente e password di SendGrid ottenuti nel passaggio seguente nell'elemento `WorkerRoleB` che include queste impostazioni. Nel codice seguente è illustrato l'elemento WorkerRoleB.
    
    ![SendGridSettings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png)
    
    

2.  È presente anche un'impostazione AzureMailServiceURL. Impostare questo valore sull'URL selezionato durante la creazione del servizio cloud di Azure, ad esempio: "http://aescloud.cloudapp.net".

Quando si aggiorna il file di configurazione del cloud, si configurano le impostazioni che verranno utilizzate quando l'applicazione viene eseguita nel cloud. Per fare in modo che l'applicazione invii messaggi di posta elettronica durante l'esecuzione in locale, è necessario aggiornare anche il file *ServiceConfiguration.Local.cscfg*.

<h2><a name="deployAz"></a><span  class="short-header">Distribuzione in Azure</span>Distribuzione dell'applicazione in Azure</h2>


Per distribuire l'applicazione, è possibile creare un pacchetto in Visual Studio e caricarlo utilizzando il portale di gestione di Azure oppure pubblicarlo direttamente da Visual Studio. In questa esercitazione verrà utilizzato il metodo basato sulla pubblicazione.

L'applicazione verrà prima pubblicata nell'ambiente di gestione temporanea e successivamente la distribuzione nella gestione temporanea verrà innalzata di livello alla fase di produzione.

### Implementare le restrizioni IP

Quando si effettua la distribuzione nell'ambiente di gestione temporanea, l'applicazione risulta accessibile pubblicamente a chiunque ne conosca l'URL. La prima operazione da eseguire è quindi implementare restrizioni IP per evitare che possa essere utilizzata da utenti non autorizzati. In un'applicazione di produzione si implementerà un meccanismo di autenticazione e autorizzazione, ad esempio il sistema di appartenenze ASP.NET, tuttavia queste funzioni sono state omesse dall'applicazione di esempio per facilitarne la configurazione, la distribuzione e il testing.

1.  Aprire il file *Web.Release.config* disponibile nella cartella radice del progetto `MvcWebRole` e sostituire il valore 127.0.0.1 dell'attributo **ipAddress** con il proprio indirizzo IP. Per visualizzare il file **Web.Release.config** in **Esplora soluzioni** è necessario espandere il file *Web.config*.
    
    Per trovare l'indirizzo IP, cercare "Trova IP" con [Bing][9] o con un altro motore di ricerca.
    
    Dopo la pubblicazione dell'applicazione, vengono applicate le trasformazioni specificate nel file *Web.release.config* e gli elementi della restrizione IP vengono aggiornati nel file *web.config* distribuito nel cloud. È possibile visualizzare il file *web.config* trasformato nella cartella *AzureEmailService\MvcWebRole\obj\Release\TransformWebConfig\transformed* dopo la creazione del pacchetto.

### Configurare l'applicazione per l'utilizzo dell'account di archiviazione durante l'esecuzione nel cloud

In precedenza nell'esercitazione, durante l'impostazione delle credenziali dell'account di archiviazione per il ruolo Web e i due ruoli di lavoro, sono state impostate le credenziali da utilizzare durante l'esecuzione dell'applicazione in locale. È ora necessario impostare le credenziali dell'account di archiviazione da utilizzare quando si esegue l'applicazione nel cloud.

Per questo test si utilizzeranno per il cloud le stesse credenziali utilizzate per l'esecuzione in locale. Per la distribuzione di un'applicazione di produzione è in genere necessario utilizzare per l'ambiente di produzione un account diverso da quello per il testing. Una procedura consigliata per l'ambiente di produzione consiste inoltre nell'utilizzare un account diverso per la stringa di connessione della diagnostica rispetto a quello della stringa di connessione di archiviazione, tuttavia per questo test verrà utilizzato lo stesso account.

È possibile utilizzare la stessa interfaccia utente per configurare le stringhe di connessione. Non dimenticare di selezionare **Cloud** nell'elenco a discesa **Configurazione servizio**. In alternativa, è possibile modificare il file di configurazione, come illustrato nella procedura seguente.

1.  Aprire il file *ServiceConfiguration.Local.cscfg* nel progetto **AzureEmailService** e copiare gli elementi `Setting` per `StorageConnectionString` e `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Aprire il file *ServiceConfiguration.Cloud.cscfg* nel progetto **AzureEmailService** e incollare gli elementi copiati nell'elemento `Configuration Settings` per `MvcWebRole`, `WorkerRoleA` e `WorkerRoleB`, sostituendo gli elementi `Setting` già presenti.

3.  Verificare che le stringhe di connessione definite dal ruolo Web e dai due ruoli di lavoro siano identiche.

### Pubblicare l'applicazione

1.  Se non è già aperto, avviare Visual Studio come amministratore e aprire la soluzione **AzureEmailService**.

2.  Fare clic con il pulsante destro del mouse sul progetto cloud **AzureEmailService** e scegliere **Pubblica**.
    
    ![Pacchetto](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png)
    
    Verrà visualizzata la finestra di dialogo **Publish Azure Application**.
    
    ![Pacchetto cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png)

3.  Se in precedenza è stato utilizzato il metodo automatico per importare le credenziali dell'account di archiviazione, la sottoscrizione di Azure è inclusa nell'elenco a discesa ed è possibile selezionarla facendo clic su **Next**. In caso contrario, fare clic su **Sign in to download credentials** e attenersi alle istruzioni in [Configurazione dell'applicazione per il servizio Archiviazione di Azure](#conf4azureStorage) per scaricare e importare le impostazioni di pubblicazione.

4.  Nella scheda **Common Settings** verificare le impostazioni nell'elenco a discesa **Cloud Service**.

5.  Nell'elenco a discesa **Environment** cambiare **Production** in **Staging**.
    
    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png)

6.  Non modificare le impostazioni predefinite **Release** per **Build configuration** e **Cloud** per **Service configuration**.
    
    Le impostazioni predefinite della scheda **Advanced** sono corrette per questa esercitazione. La scheda **Advanced** include alcune impostazioni utili per lo sviluppo e il testing. Per ulteriori informazioni su questa scheda, vedere [Procedura guidata Pubblica l'applicazione Azure][10].

7.  Fare clic su **Avanti**.

8.  Nel passaggio **Summary** della procedura guidata fare clic sull'icona di **salvataggio** (immagine del dischetto visualizzata a destra dell'elenco a discesa Target profile) per salvare le impostazioni di pubblicazione.
    
    Alla successiva pubblicazione dell'applicazione verranno utilizzate le impostazioni salvate e non sarà necessario eseguire di nuovo la procedura guidata.

9.  Verificare le impostazioni, quindi fare clic su **Publish**.
    
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png)
    
    La finestra **Log attività di Azure** verrà aperta in Visual Studio.

10. Fare clic sull'icona della freccia verso destra per espandere i dettagli della distribuzione.
    
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png)
    <br/><br/>
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png)
    
    Il completamento della distribuzione richiede almeno 5 minuti.

11. Quando lo stato della distribuzione è completo, fare clic su **Website URL** per avviare l'applicazione.
    
    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png)

12. Immettere alcuni dati nelle pagine Web **Mailing List**, **Subscriber** e **Message** per testare l'applicazione.
    
    **Nota**: al termine del testing eliminare l'applicazione per evitare di pagare per risorse non utilizzate. Se si utilizza un [account di valutazione gratuita di Azure][11], l'utilizzo dei tre ruoli distribuiti comporterà l'esaurimento del limite mensile in due settimane. Per eliminare una distribuzione utilizzando il portale di gestione di Azure, selezionare il servizio cloud e fare clic su **ELIMINA** nella parte inferiore della pagina, quindi selezionare la distribuzione di produzione o di gestione temporanea.
    
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png)

13. Nel log attività di Azure in Visual Studio selezionare **Apri in Esplora server**.
    
    In **Calcolo di Azure** in **Esplora server** è possibile monitorare la distribuzione. Se è stata selezionata l'opzione **Enable Remote Desktop for all roles** nella procedura guidata **Publish Azure Application** è possibile fare clic con il pulsante destro del mouse su un'istanza del ruolo e scegliere **Connect using Remote Desktop**.
    
    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png)

<h2><a name="swap"></a><span  class="short-header">Produzione</span>Innalzamento di livello dell'applicazione dalla gestione temporanea alla produzione</h2>


1.  Nel [portale di gestione di Azure][2] fare clic sull'icona **Cloud Services** nel riquadro sinistro, quindi selezionare il servizio cloud.

2.  Fare clic su **Swap**.

3.  Fare clic su **Yes** per completare lo scambio VIP, ovvero degli IP virtuali. Il completamento di questa operazione può richiedere alcuni minuti.
    
    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png)

4.  Al termine dello scambio, fare clic sull'icona **Cloud Services** nel riquadro sinistro e selezionare il servizio cloud.

5.  Scorrere verso il basso nella scheda **Dashboard** per la distribuzione **Production** fino alla sezione **quick glance** in basso a destra nella pagina. Si noti che l'impostazione di **Site URL** è cambiata e al posto di un prefisso GUID è visualizzato il nome del servizio cloud.
    
    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png)

6.  Fare clic sul collegamento in **Site URL** oppure copiarlo e incollarlo in un browser per testare l'applicazione in ambiente di produzione.
    
    Se non sono state modificate le impostazioni dell'account di archiviazione, durante l'esecuzione dell'applicazione nel cloud verranno visualizzati i dati immessi durante il testing della versione preconfigurata dell'applicazione.

<h2><a name="trace"></a><span  class="short-header">Traccia</span>Configurazione e visualizzazione dei dati traccia</h2>


La traccia costituisce uno strumento insostituibile per il debug di un'applicazione cloud. In questa sezione dell'esercitazione verrà illustrato come visualizzare i dati traccia.

1.  Verificare che la stringa di connessione di diagnostica sia configurata per l'utilizzo dell'account di archiviazione di Azure e non dell'archivio di sviluppo.
    
    Se sono state seguite le istruzioni riportate in precedenza nell'esercitazione, tali valori saranno identici. Per verificarlo, è possibile utilizzare l'interfaccia utente di Visual Studio (scheda **Impostazioni** in **Proprietà** per i ruoli) oppure esaminare i file *ServiceConfiguration.*.cscfg*.
    
    **Nota:** la procedura consigliata consiste nell'utilizzare per i dati traccia un account di archiviazione diverso rispetto a quello utilizzato per i dati di produzione, tuttavia per semplicità in questa esercitazione è stato configurato lo stesso account per la traccia.

2.  In Visual Studio aprire *WorkerRoleA.cs* nel progetto **WorkerRoleA**, cercare `ConfigureDiagnostics` ed esaminare il metodo `ConfigureDiagnostics`.
    
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
    
    In questo codice `DiagnosticMonitor` è configurato in modo da consentire l'archiviazione di un massimo di 500 MB di dati traccia (dopo tale limite i dati meno recenti verranno sovrascritti) e da archiviare tutti i messaggi di traccia (LogLevel.Verbose). `ScheduledTransferPeriod` indica che i dati traccia vengono trasferiti nell'archivio a intervalli di un minuto. È necessario impostare `ScheduledTransferPeriod` in modo da salvare i dati traccia.
    
    Il metodo `ConfigureDiagnostics` in ogni ruolo di lavoro e Web consente di configurare il listener di traccia in modo da registrare i dati quando si chiama l'API di traccia. Per ulteriori informazioni, vedere l'articolo all'[utilizzo della traccia in applicazioni cloud di Azure][12]

3.  In **Server Explorer** fare doppio clic su **WADLogsTable** (espandere **Storage** / **nomeaccountdiarchiviazione** / **Tables**) per l'account di archiviazione aggiunto in precedenza. È possibile immettere un [filtro Servizi dati WCF][13] per limitare il numero di entità visualizzate. Nell'immagine seguente vengono visualizzati solo i messaggi di avviso e di errore.
    
    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png)

<h2><a name="addRole"></a><span class="short-header">Aggiunta di un'istanza del ruolo</span>Aggiunta di un'altra istanza del ruolo di lavoro per gestire un carico maggiore</h2>


Per la scalabilità delle risorse di calcolo nei ruoli di Azure sono disponibili due approcci, ovvero mediante la specifica delle [dimensioni delle macchine virtuali][14] e/o mediante la specifica del numero di istanze di macchine virtuali in esecuzione.

La dimensione della macchia virtuale viene specificata nell'attributo `vmsize` dell'elemento `WebRole` o `WorkerRole` del file *ServiceDefinition.csdef*. L'impostazione predefinita è `Small`, che consente di disporre di un solo core e di 1,75 GB di RAM. Per applicazioni multithread che utilizzano una grande quantità di memoria, spazio su disco e larghezza di banda, è possibile aumentare la dimensione della macchina virtuale per migliorare le prestazioni. Ad esempio con una macchina virtuale `ExtraLarge` è possibile disporre di 8 core CPU e 14 GB di RAM. L'aumento di memoria, core CPU, spazio su disco e larghezza di banda su un unico computer è noto come *scalabilità verticale*. Candidati ideali per la scalabilità verticale sono le applicazioni Web ASP.NET che utilizzano [metodi asincroni][15]. Per una descrizione delle risorse disponibili con le diverse dimensioni di macchine virtuali, vedere [Dimensioni delle macchine virtuali][14].

Il ruolo di lavoro B in questa applicazione costituisce il componente limite in caso di carico elevato dal momento che esegue l'invio dei messaggi di posta elettronica. Il ruolo di lavoro A si limita a creare i messaggi della coda, attività che non richiede un uso intensivo delle risorse. Dal momento che il ruolo di lavoro B non è multithread e non dispone di un footprint di memoria esteso, non costituisce un candidato ottimale per la scalabilità verticale. Per il ruolo di lavoro B è possibile applicare una scalabilità lineare, ovvero in grado di raddoppiare le prestazioni quando si raddoppiano le istanze, aumentando il numero delle istanze. L'aumento del numero di istanze di calcolo è noto come *scalabilità orizzontale*. Dal momento che la scalabilità orizzontale implica un costo per ciascuna istanza, è consigliabile utilizzarla solo quando richiesto dall'applicazione.

È possibile applicare la scalabilità orizzontale a un ruolo Web o di lavoro aggiornando l'impostazione nell'interfaccia utente di Visual Studio oppure modificando direttamente i file *ServiceConfiguration.\*.cscfg*. Il numero delle istanze viene specificato nella scheda **Configurazione** della finestra **Proprietà** del ruolo e nell'elemento `Instances` dei file con estensione *cscfg*. Quando si aggiorna l'impostazione, è necessario distribuire il file di configurazione aggiornato per rendere effettiva la modifica. In alternativa, per aumenti transitori del carico è possibile cambiare il numero di istanze del ruolo nel portale di gestione di Azure. È inoltre possibile configurare il numero di istanze utilizzando l'API di gestione di Azure. È infine possibile utilizzare il [blocco applicazione per la scalabilità automatica](/en-us/develop/net/how-to-guides/autoscaling/) per scalare orizzontalmente in modo automatico in risposta a un incremento del carico. Per ulteriori informazioni sulla scalabilità automatica, vedere i collegamenti alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

In questa sezione dell'esercitazione si applicherà la scalabilità orizzontale al ruolo di lavoro B utilizzando il portale di gestione, ma verrà prima illustrato come farlo in Visual Studio.

Per effettuare questa operazione in Visual Studio, è necessario fare clic con il pulsante destro del mouse sul ruolo in **Ruoli** nel progetto cloud e scegliere **Proprietà**.

![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

Scegliere quindi la scheda **Configurazione** sulla sinistra e selezionare **Cloud** nell'elenco a discesa **Configurazione**.

![Conteggio istanze](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png)

Si noti che in questa scheda è anche possibile configurare le dimensioni delle macchine virtuali.

Nella procedura seguente viene spiegato come applicare la scalabilità orizzontale utilizzando il portale di gestione di Azure.

1.  Nel portale di gestione di Azure selezionare il servizio cloud e quindi fare clic su **Scale**.

2.  Aumentare il numero di istanze per il ruolo di lavoro B, quindi fare clic su **Save**.
    
    ![Aumento del numero di istanze](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png)
    
    Il provisioning delle nuove macchine virtuali può richiedere alcuni minuti.

3.  Selezionare la scheda **Instances** per visualizzare le singole istanze del ruolo nell'applicazione.
    
    ![Visualizzazione delle istanze](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png)

<h2><a name="nextsteps"></a><span  class="short-header">Passaggi successivi</span>Passaggi successivi</h2>


In questa esercitazione si è appreso come configurare, distribuire e scalare l'applicazione completata. Nelle esercitazioni seguenti verrà illustrato come creare l'applicazione da zero. Nell'[esercitazione successiva](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/) verrà creato il ruolo Web.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB si Archiviazione di Azure, vedere la fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).


<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Esercitazione 3</a></div>


[1]: http://go.microsoft.com/fwlink/?LinkID=324322
[2]: http://manage.windowsazure.com
[3]: http://azurestorageexplorer.codeplex.com/
[4]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[5]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
[6]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
[8]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
[9]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
[10]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
[11]: http://www.windowsazure.com/en-us/pricing/free-trial/ "free-trial account"
[12]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Windows Azure"
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF filter"
[14]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "VM sizes"
[15]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"