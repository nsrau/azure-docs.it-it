<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-hyper-v-recovery-vault" pageTitle="Configure Hyper-V Recovery Manager to protect virtual machines in VMM clouds" metaKeywords="hyper-v recovery, VMM, clouds, disaster recovery" description="Azure Hyper-V Recovery Manager can help you protect applications and services by coordinating the replication and recovery of virtual machines located in System Center 2012 VMM private clouds." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configure Azure Hyper-V Recovery Manager" editor="jimbe" manager="cfreeman" authors="" />

Configurazione di Gestione ripristino Hyper-V di Azure
======================================================

Gestione ripristino Hyper-V di Azure consente di coordinare e gestire la protezione di macchine virtuali di Hyper-V disponibili in cloud privati su server Virtual Machine Manager (VMM) in System Center 2012 Service Pack 1 (SP1) o System Center 2012 R2. Grazie a Gestione ripristino Hyper-V è possibile orchestrare il failover di tali macchine virtuali da un server host Hyper-V locale a un altro. Gli insiemi di credenziali di Gestione ripristino Hyper-V in Azure vengono utilizzati per l'archiviazione della configurazione di protezione.

Informazioni sull'esercitazione
-------------------------------

In questa esercitazione è disponibile una procedura dettagliata rapida per la distribuzione di Gestione ripristino Hyper-V. Per informazioni più dettagliate, fare riferimento alle risorse seguenti:

-   [Guida alla pianificazione per Gestione del ripristino di Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321294) - Include informazioni dettagliate sulla procedura di pianificazione da completare prima dell'avvio di una distribuzione completa.
-   [Guida alla distribuzione per Gestione del ripristino di Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321295) - Include istruzioni dettagliate per una distribuzione completa.
-   In caso di problemi nel corso dell'esercitazione, vedere [Gestione ripristino Hyper-V: Scenari di errore comuni e soluzioni](http://go.microsoft.com/fwlink/?LinkId=389879) oppure inviare domande al [forum relativo ai Servizi di ripristino di Azure](http://go.microsoft.com/fwlink/?LinkId=313628)

Prima di iniziare
-----------------

Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.

### Prerequisiti di Gestione ripristino Hyper-V

-   **Account Azure** - È necessario disporre di un account di Azure con abilitata l'opzione Servizi di ripristino di Azure. Se non si dispone di un account o se la caratteristica non è registrata, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://aka.ms/try-azure) e [Dettagli prezzi per Gestione ripristino Hyper-V](http://go.microsoft.com/fwlink/?LinkId=378268).
-   **Certificato (.cer)** - Per registrare server VMM in un insieme di credenziali di Gestione ripristino Hyper-V, sarà necessario caricare un certificato di gestione (.cer) contenente la chiave pubblica per l'insieme di credenziali. Tenere presente quanto segue:
    -   Ai fini dell'esercitazione, è possibile utilizzare un certificato autofirmato, creato utilizzando lo strumento Makecert.exe. Per una distribuzione completa è possibile utilizzare qualsiasi certificato SSL valido conforme ai prerequisiti dei certificati illustrati in [Prerequisiti e supporto](%20http://go.microsoft.com/fwlink/?LinkId=386485) nella Guida alla pianificazione.
    -   A ogni insieme di credenziali è associato solo un singolo certificato con estensione cer alla volta. Se necessario, è possibile caricare un nuovo certificato per sovrascrivere un certificato esistente.
-   **Certificato (file con estensione pfx)** - È possibile esportare il certificato con estensione cer come file con estensione pfx (contenente la chiave privata). Tale file deve essere importato in ogni server VMM contenente macchine virtuali da proteggere. Nel corso della distribuzione, quando si installa l'agente del provider del servizio di gestione ripristino Hyper-V in ogni server VMM, è necessario selezionare il file PFX per completare la registrazione del server VMM nell'insieme di credenziali.

### Prerequisiti di VMM

-   Almeno un server VMM in esecuzione su System Center 2012 SP1 o System Center 2012 R2.
-   Cloud privati VMM. Se è in esecuzione un server VMM, sarà necessario configurare due cloud. Per due o più server VMM, sarà necessario almeno un cloud sul server VMM di origine e un cloud nel server VMM di destinazione da utilizzare per il ripristino. Il cloud primario da proteggere deve includere quanto segue:
    -   Uno o più gruppi host VMM
    -   Uno o più server host Hyper-V in ogni gruppo host.
-   Se si desidera che le macchine virtuali siano connesse a una rete VM dopo il failover, è necessario configurare il mapping di rete in Gestione ripristino Hyper-V. Prima di iniziare la distribuzione, verificare quanto segue:
    -   La macchina virtuale nel cloud sul server VMM di origine è connessa a una rete VM. È necessario che tale rete VM sia collegata a una rete logica associata al cloud.
    -   Il cloud interessato nel server VMM di destinazione dispone di una rete VM corrispondente. È necessario che tale rete VM sia collegata a una rete logica corrispondente associata al cloud di destinazione.

Passaggi dell'esercitazione
---------------------------

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:

-   [Recupero e configurazione di certificati](#createcert) - Ottenere un certificato con estensione cer, esportarlo come file PFX e importare il file PFX nei server VMM.
-   [Passaggio 1: Creare un insieme di credenziali](#vault) - Creare un insieme di credenziali di Gestione ripristino Hyper-V in Azure.
-   [Passaggio 2: Caricare il certificato](#upload) - Caricare il certificato di gestione nell'insieme di credenziali.
-   [Passaggio 3: Scaricare e installare il provider](#download) - Scaricare e installare il provider del servizio di gestione ripristino Hyper-V nei server VMM da proteggere. Registrare quindi i server VMM nell'insieme di credenziali.
-   [Passaggio 4: Configurare le impostazioni di protezione del cloud](#clouds) - Configurare le impostazioni di protezione per i cloud VMM.
-   [Passaggio 5: Configurare il mapping di rete](#networks) - Eseguire il mapping di reti VM da server VMM di origine a server VMM di destinazione.
-   [Passaggio 6: Abilitare la protezione per le macchine virtuali](#virtualmachines) - Abilitare la protezione per le macchine virtuali Hyper-V presenti nei cloud VMM.
-   [Passaggio 7: Configurare ed eseguire piani di ripristino](#recovery%20plans) - Creare e personalizzare piani di ripristino che raggruppino le macchine virtuali a fini di failover. Eseguire quindi il piano di ripristino.
-   [Passaggio 8: Eseguire il monitoraggio](#jobs) - Eseguire il monitoraggio di impostazioni, stato e avanzamento utilizzando le schede *\*Jobs** e **Dashboard**.

Recupero e configurazione di certificati
----------------------------------------

Per ottenere e configurare certificati, eseguire la procedura seguente:

1.  [Ottenere un certificato per la procedura dettagliata](#obtaincert) - Ottenere un certificato utilizzando lo strumento MakeCert, oppure utilizzare un certificato esistente conforme ai [requisiti per i certificati](http://go.microsoft.com/fwlink/?LinkId=321294).
2.  [Esportare il certificato nel formato PFX](#exportcert) - Sul server in cui si trova il certificato o in cui è stato creato, esportare il file con estensione cer come file con estensione pfx (con la chiave privata).
3.  [Importare il certificato con estensione pfx nei server VMM](#importcert) - Al termine dell'esportazione del file PFX, importare tale file nell'archivio certificati personale in ogni server VMM da registrare nell'insieme di credenziali.

### Ottenere un certificato auto-firmato (con estensione cer)

Utilizzare lo strumento MakeCert per creare un certificato x.509 con estensione cer conforme a tutti i requisiti per i certificati, come indicato di seguito:

1.  Nel computer in cui si desidera eseguire MakeCert, scaricare la versione più recente di [Windows SDK](http://go.microsoft.com/fwlink/?LinkId=378269). Il comando makecert.exe fa parte del Windows Software Development Kit principale, quindi non è necessario scaricare e installare l'intero SDK.
2.  Nella pagina Specify Location selezionare **Install the Windows Software Development Kit for Windows 8.1 to this computer**.
3.  Nella pagina Join the Customer Experience Improvement Program (CEIP) selezionare l'impostazione desiderata.
4.  Nella pagina relativa al Contratto di licenza fare clic su **Accept** per accettare le condizioni specificate.
5.  Nella pagina Select the features you want to install deselezionare tutte le opzioni tranne **Windows Software Development Kit**.
6.  Al termine dell'installazione, verificare che makecert.exe sia disponibile nella cartella C:\\Programmi (x86)\\Windows Kits\\*WindowsVersion*\\bin\\x64.
7.  Aprire un prompt dei comandi (cmd.exe) con privilegi di amministratore, quindi passare alla cartella makecert.exe.
8.  Eseguire il comando seguente per creare un certificato autofirmato. Sostituire NomeCertificato con il nome da utilizzare per il certificato, quindi specificare la data di scadenza effettiva del certificato dopo -e:
	`makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer`

Il certificato verrà creato e archiviato nella stessa cartella. È consigliabile spostarlo in un percorso più accessibile, in modo da consentirne l'esportazione nel passaggio successivo.

### Esportare il certificato nel formato PFX

Per esportare il file con estensione cer nel formato PFX, eseguire la procedura seguente.

1.  Nella schermata Start digitare **mmc.exe** per avviare Microsoft Management Console (MMC).
2.  Scegliere **Aggiungi/Rimuovi snap-in** dal menu **File**. Verrà visualizzata la finestra di dialogo **Aggiungi o rimuovi snap-in**.
3.  In **Snap-in disponibili** fare clic su **Certificati**, quindi su **Aggiungi**.
4.  Selezionare **Account del computer**, quindi fare clic su **Avanti**.
5.  Selezionare **Computer locale**, quindi fare clic su **Fine**.
6.  Nell'albero della console di MMC espandere **Certificati**, quindi espandere **Personali**.
7.  Nel riquadro dei dettagli fare clic sul certificato che si desidera gestire.
8.  Scegliere **Tutte le attività** dal menu **Azione**, quindi fare clic su **Esporta**. Verrà visualizzata l'Esportazione guidata certificati. Fare clic su **Avanti**.
9.  Nella pagina **Esportazione della chiave privata con il certificato** fare clic su **Sì** per esportare la chiave privata. Fare clic su **Avanti**. Questo passaggio è obbligatorio solo se si desidera esportare la chiave privata in altri server dopo l'installazione.
10. Nella pagina Formato file di esportazione selezionare **Scambio di informazioni personali â€“ PKCS \#12 (\*.PFX)**. Fare clic su **Avanti**.
11. Nella pagina **Password** digitare e confermare la password utilizzata per crittografare la chiave privata. Fare clic su **Avanti**.
12. Per esportare il certificato in formato PFX, seguire le istruzioni visualizzate nella procedura guidata.

### Importare il certificato con estensione pfx nei server VMM

Dopo l'esportazione, copiare il certificato in ogni VMM da registrare nell'insieme di credenziali, quindi importarlo eseguendo la procedura seguente. Se MakeCert.exe è stato eseguito su un sever VMM, non sarà necessario importare il certificato in tale server.

1.  Copiare i file del certificato di chiave privata (con estensione pfx) in una posizione nel server locale.
2.  Nello snap-in Certificati di MMC selezionare **Account del computer**, quindi fare clic su Avanti.
3.  Selezionare Computer locale, quindi fare clic su **Fine**. Nella finestra di dialogo **Aggiungi o rimuovi snap-in** fare clic su **OK**.
4.  In MMC espandere **Certificati**, fare clic con il pulsante destro del mouse su **Personali**, scegliere Tutte le attività, quindi fare clic su **Importa** per avviare l'Importazione guidata certificati.
5.  Nella pagina iniziale dell'Importazione guidata certificati fare clic su **Avanti**.
6.  Nella pagina File da importare fare clic su **Sfoglia**, quindi individuare la cartella contenente il file di certificato con estensione pfx che include il certificato da importare. Selezionare il file appropriato, quindi fare clic su **Apri**.
7.  Nella casella **Password** della pagina Password digitare la password per il file di chiave privata specificato nella procedura precedente, quindi fare clic su **Avanti**.
8.  Nella pagina Archivio certificati selezionare **Colloca tutti i certificati nel seguente archivio**, quindi fare clic su **Sfoglia**, selezionare l'archivio **Personali**, fare clic su **OK** e infine su Avanti.
9.  Nella pagina Completamento dell'Importazione guidata certificati fare clic su **Fine**.

Al termine di questa procedura sarà possibile scegliere il certificato con estensione cer da caricare nell'insieme di credenziali, quindi di selezionare il certificato con estensione pfx quando si esegue la registrazione di un server VMM durante l'installazione del provider.

<h2 data-morhtml="true"\>Passaggio 1: Creare un insieme di credenziali</h2\>

1.  Accedere al [portale di gestione](https://manage.windowsazure.com).

2.  Fare clic su **Data Services**, quindi su **Recovery Services**.

    ![Programma di anteprima](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3.  Fare clic su **Recovery Services**, quindi su **Create New**, scegliere **Hyper-V Recovery Manager Vault** e infine fare clic su **Quick Create**.

    ![Nuovo insieme di credenziali](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

4.  In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In **Region** selezionare l'area geografica per l'insieme di credenziali. Le aree geografiche disponibili includono l'Asia Pacifico sudorientale, l'Europa settentrionale e gli Stati Uniti orientali.****

6.  In **Subscription** immettere i dettagli della sottoscrizione.

7.  Fare clic su **Create vault**.

Verificare lo stato dell'insieme di credenziali nelle notifiche nella parte inferiore del portale. La creazione corretta dell'insieme di credenziali verrà confermata da un messaggio. L'insieme verrà elencato come **Online** nella pagina Recovery Services.

<h2 data-morhtml="true"\>Passaggio 2: Caricare un certificato ( con estensione cer)</h2\>

1.  Nella pagina **Recovery Services** aprire l'insieme di credenziali necessario.
2.  Fare clic sull'icona Quick Start per aprire la pagina corrispondente.

    ![Icona Quick Start](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

3.  Fare clic su **Manage Certificate**.

    ![Quick Start](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

4.  Nella finestra di dialogo **Manage Certificate** fare clic su **Browse For File** per individuare il file con estensione cer da caricare nell'insieme di certificati.

    ![Manage Certificate](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)

<h2 data-morhtml="true"\>Passaggio 3: Scaricare e installare il provider</h2\> Installare il provider del servizio di gestione ripristino Hyper-V in ogni server VMM da registrare nell'insieme di credenziali. La versione più recente del file di installazione del provider è archiviata nell'Area download di Azure. Quando si esegue il file in un server VMM, il provider viene installato e il server VMM viene registrato nell'insieme di credenziali.

1.  Nella pagina **Quick Start** fare clic su **Download Provider** per ottenere il file EXE di installazione del provider. Eseguire tale file nel server VMM per avviare la configurazione del provider.

    ![Download Agent](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2.  Per completare l'installazione del provider, attenersi alla procedura visualizzata.

    ![Installazione completata](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3.  Al termine dell'installazione del provider, eseguire i passaggi della procedura guidata per registrare il server VMM nell'insieme di credenziali.
4.  Nella pagina Internet Connection specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Il provider può utilizzare le impostazioni predefinite per la connessione a Internet disponibili sul server oppure è possibile fare clic su **Use a proxy server for Internet requests** per utilizzare impostazioni personalizzate.

    ![Impostazioni Internet](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)

    Se si desidera utilizzare impostazioni personalizzate per questa procedura dettagliata, leggere le informazioni disponibili nel [Passaggio 2: Installare il provider e registrare i server VMM](http://go.microsoft.com/fwlink/?LinkId=378266) della Guida alla distribuzione.

5.  Nella pagina Certificate Registration selezionare il file con estensione pfx corrispondente al file con estensione cer caricato nell'insieme di credenziali.

    ![Registro certificati](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)

    ![Insieme di credenziali certificato](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

6.  Nella pagina VMM Server specificare un nome descrittivo per il server VMM. Tale nome consente di identificare il server nella console di Gestione ripristino Hyper-V.
7.  Selezionare **Synchronize cloud data with the vault** per sincronizzare i dati in tutti i cloud privati disponibili nel server VMM con l'insieme di credenziali di Gestione ripristino Hyper-V. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, è possibile pubblicare singolarmente ogni cloud per sincronizzarlo prima di configurare le impostazioni di protezione del cloud.
8.  Fare clic su **Register** per completare il processo.

    ![Impostazioni Internet](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

In questa fase i metadati del server VMM vengono recuperati da Gestione ripristino Hyper-V, in modo da orchestrare il failover e il ripristino. Dopo la registrazione corretta di un server, il relativo nome descrittivo verrà visualizzato nella scheda **Resources** della pagina Servers nell'insieme di credenziali.

Passaggio 4: Configurare le impostazioni di protezione del cloud
----------------------------------------------------------------

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Se l'opzione **Synchronize cloud data with the vault** non è stata abilitata durante l'installazione del provider nel server VMM, sarà necessario pubblicare il cloud in Gestione ripristino Hyper-V dalla console VMM. Dopo il rilevamento dei cloud da parte di Gestione ripristino Hyper-V, sarà possibile configurare le impostazioni di protezione.

### Pubblicare un cloud

1.  Nella console VMM aprire l'area di lavoro **VMs and Services**.
2.  Nel riquadro **VMs and Services** aprire il cloud da pubblicare.
3.  Per pubblicare il cloud, nella pagina **General** delle proprietà del cloud selezionare **Send configuration data about this cloud to the Azure Hyper-V Recovery Manager**. Dopo la pubblicazione, il cloud verrà visualizzato nell'insieme di credenziali.

    ![Cloud](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)

    ![Cloud pubblicato](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

### Configurare i cloud

Per configurare i cloud in modo da assicurare la protezione, eseguire le operazioni seguenti:

1.  Nella pagina Quick Start fare clic su **Configure Protection Settings**.
2.  Nella scheda **Protected Items** selezionare il cloud da configurare, quindi passare alla scheda **Configuration**.

    ![Configurazione del cloud](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)

3.  In **Target Location** specificare il server VMM che gestisce il cloud da utilizzare per il ripristino.
4.  In **Target Cloud** specificare il cloud di destinazione da utilizzare per il failover di macchine virtuali nel cloud di origine.
5.  In **Copy Frequency** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione. Risulta rilevante solo se l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene utilizzata un'impostazione predefinita pari a cinque minuti.
6.  In **Additional Recovery Points** lasciare l'impostazione predefinita. Questo valore consente di specificare se si desidera creare punti di ripristino aggiuntivi. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
7.  In **Application-Consistent Snapshot Frequency** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot utilizzano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si desidera impostare questo valore per la procedura dettagliata dell'esercitazione, verificare che sia impostato su un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
8.  In **Data Transfer Compression** specificare se si desidera comprimere i dati replicati trasferiti.
9.  In **Authentication** specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. A meno che non sia stato configurato un ambiente Kerberos funzionante, è consigliabile selezionare HTTPS per questa procedura dettagliata. Se si seleziona HTTPS, i server host si autenticheranno a vicenda utilizzando un certificato server e il traffico sarà crittografato. Gestione ripristino Hyper-V configura automaticamente i certificati da utilizzare per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
10. In **Port** lasciare l'impostazione predefinita. Questo valore consente di impostare il numero di porta su cui i computer host Hyper-V di origine e di destinazione rimangono in attesa di traffico di replica.
11. In **Initial Replication Settings** specificare il modo in cui verrà gestita la replica iniziale di dati dal percorso di origine al percorso di destinazione, prima dell'inizio della replica normale.
    -   **Over the network** - La copia di dati tramite rete può richiedere molto tempo e molte risorse. È consigliabile utilizzare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si utilizza la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
    -   **Offline** - Tramite questo metodo viene specificato che la replica iniziale sarà eseguita utilizzando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per utilizzare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM](http://go.microsoft.com/fwlink/?LinkId=323469) nella Guida alla distribuzione.

#### Impostazioni successive alla configurazione della protezione

Al termine della configurazione di un cloud, tutti i cluster e i server host configurati nei cloud di origine e di destinazione verranno configurati per la replica. In particolare, vengono configurati gli elementi seguenti:

-   Regole del firewall utilizzate da Replica Hyper-V, in modo da aprire le porte per il traffico di replica.
-   I certificati necessari per la replica sono stati installati.
-   Le impostazioni di Replica Hyper-V sono state configurate.

È possibile modificare le impostazioni del cloud nella scheda **Configure**. Si noti che:

-   È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
-   Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.
-   Al termine del salvataggio della configurazione del cloud, verrà creato un processo, che potrà essere monitorato nella scheda **Jobs**. Dopo il salvataggio della configurazione, per modificare il percorso o il cloud di destinazione sarà necessario rimuovere la configurazione del cloud, quindi riconfigurare il cloud.

Passaggio 5: Configurare il mapping di rete
-------------------------------------------

Nella scheda **Networks** è possibile configurare il mapping tra reti VM nei server VMM di origine e di destinazione. Il mapping di rete consente di assicurare che dopo il failover le macchine virtuali di replica vengano connesse a reti appropriate e che tali macchine vengano posizionate su host in grado di accedere alle reti VM. Per verificare i requisiti di rete per VMM, vedere [Prerequisiti e supporto](%20http://go.microsoft.com/fwlink/?LinkId=386485) nella Guida alla pianificazione. È consigliabile eseguire il mapping delle reti come indicato di seguito:

-   Eseguire il mapping di reti utilizzate da ogni cloud configurato per la protezione.
-   Eseguire il mapping di rete prima di abilitare le macchine virtuali per la protezione, poiché il mapping viene utilizzato durante il posizionamento delle macchine virtuali di replica. Se lo si configura dopo l'abilitazione della protezione, il mapping funzionerà correttamente, ma potrebbe essere necessario eseguire la migrazione di alcune macchine virtuali di replica.

Per eseguire il mapping delle reti, eseguire le operazioni seguenti:

1.  Nella pagina Quick Start fare clic su **Configure Network Mapping**.
2.  In **Source Location** selezionare il server VMM di origine.
3.  In **Target Location** selezionare il server VMM di destinazione. Se si distribuisce Gestione ripristino Hyper-V con un singolo server VMM, il server di origine e il server di destinazione saranno uguali. Viene visualizzato l'elenco di reti VM di origine e delle reti VM di destinazione corrispondenti. Per le reti non mappate viene visualizzato un valore vuoto.
4.  Selezionare una voce non mappata nell'elenco di origine e di destinazione, quindi fare clic su **Map**. Il servizio rileva le reti VM nel server di destinazione e le visualizza.

    ![Manage certificate](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

5.  Nella pagina Select a Target Network selezionare la rete VM di destinazione da utilizzare nel server VMM di destinazione. ![Rete di destinazione](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png)

6.  Fare clic sulle icone informazioni accanto ai nomi delle reti di origine e di destinazione per visualizzare le subnet e il tipo per ogni rete.

    Quando si seleziona una rete di destinazione, vengono visualizzati i cloud protetti che utilizzano la rete di origine e viene mostrata inoltre la disponibilità delle reti VM di destinazione associate ai cloud. È consigliabile selezionare una rete di destinazione disponibile per tutti i cloud utilizzati per la protezione.

7.  Fare clic sul segno di spunta per completare il processo di mapping. Un processo inizierà a tenere traccia dell'avanzamento del mapping, come mostrato nella scheda **Jobs**.

    In questo modo verranno connesse alla rete VM di destinazione eventuali macchine virtuali di replica esistenti corrispondenti alle macchine virtuali connesse alla rete VM di origine. Verranno inoltre connesse alla rete VM di destinazione eventuali nuove macchine virtuali di replica create dopo l'abilitazione della replica nelle macchine virtuali connesse alla rete VM di origine.

### Modificare i mapping di rete

La scheda **Networks** consente di modificare o rimuovere i mapping di rete. Tenere presente quanto segue:

-   Se si annulla un mapping selezionato, il mapping sarà rimosso e le macchine virtuali di replica verranno disconnesse dalla rete a cui erano connesse al momento del mapping.
-   Quando si modifica un mapping selezionato, le modifiche vengono aggiornate e le macchine virtuali di replica vengono connesse alle nuove impostazioni di rete specificate.

Passaggio 6: Abilitare la protezione per le macchine virtuali
-------------------------------------------------------------

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud. Per attivare la protezione, nella console VMM fare clic con il pulsante destro del mouse su ogni macchina virtuale da proteggere, quindi scegliere **Enable Recovery**.

Dopo l'abilitazione della protezione, le macchine virtuali saranno visualizzate nell'elenco di VM del cloud. La scheda **Jobs** consente di visualizzare l'avanzamento dell'azione di abilitazione della protezione.

![Macchine virtuali](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

Quando si abilita la protezione delle macchine virtuali, vengono creati tre processi. Viene eseguito il processo Enable Protection. Dopo il completamento della replica iniziale, vengono eseguiti due processi Finalize Protection. La macchina virtuale sarà pronta per il failover solo dopo il completamento corretto di questi tre processi.

Passaggio 7: Configurare ed eseguire piani di ripristino
--------------------------------------------------------

Un piano di ripristino consente di riunire le macchine virtuali in gruppi, in modo che sia possibile elaborarle come unità singola, quindi di specificare l'ordine di failover dei gruppi. Oltre a definire gruppi di macchine virtuali, è possibile personalizzare i piani di ripristino in modo che eseguano script automatici o attendano la conferma di azioni manuali. Per creare un piano di ripristino, eseguire le operazioni seguenti:

1.  Nella pagina Quick Start fare clic su **Create Recovery Plan**.
2.  Nella pagina Specify the Recovery Page Name and Target specificare un nome e i server VMM di origine e di destinazione. Se si distribuisce Gestione ripristino Hyper-V su un singolo server VMM, il server di origine e il server di destinazione saranno uguali. Il server VMM di origine deve includere macchine virtuali in cui è stata abilitata la protezione.

    ![Create Recovery Plan](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)

3.  Nella pagina Select Virtual Machines selezionare le macchine virtuali da aggiungere al piano di ripristino. Le macchine virtuali verranno aggiunte al gruppo predefinito del piano di ripristino (Group 1).
4.  Fare clic sul segno di spunta per creare il piano di ripristino. La scheda **Recovery Plans** consente di eliminare i piani di ripristino creati.

    ![Macchine virtuali del piano di ripristino](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)

Dopo la creazione di un piano di ripristino sarà possibile eseguire le azioni seguenti:

-   Personalizzare il piano. È possibile aggiungere nuovi gruppi a un piano di ripristino o aggiungere macchine virtuali a un gruppo. È possibile definire azioni personalizzate sotto forma di script e azioni manuali da completare prima e dopo gruppi specifici. I nomi dei gruppi sono incrementali. Dopo il piano di ripristino predefinito Group 1, si crea Group 2, quindi Group 3 e così via. Il failover delle macchine virtuali viene eseguito in base all'ordine dei gruppi. Per ulteriori informazioni sulla personalizzazione dei piani di ripristino, vedere [Passaggio 6: creazione e personalizzazione dei piani di ripristino](http://go.microsoft.com/fwlink/?LinkId=378271) nella Guida alla distribuzione.
-   Eseguire un failover di test.

### Eseguire i piani di ripristino

I piani di ripristino possono essere eseguiti come parte di un test proattivo o un failover pianificato oppure durante un failover non pianificato. In questa procedura dettagliata viene illustrato come eseguire un failover di test. Per informazioni sugli altri tipi di failover, vedere la [Guida alle operazioni e al monitoraggio](hhttp://go.microsoft.com/fwlink/?LinkId=378272).

### Eseguire il piano

Eseguire un failover di test per verificare il piano di ripristino in un ambiente di testing. Un failover di test risulta utile per assicurarsi che il piano di ripristino e la strategia per il failover delle macchine virtuali funzionino come previsto. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

##### Prima di iniziare

Quando si attiva un failover di test, viene richiesto di specificare il modo in cui le macchine virtuali di test devono essere connesse alle reti dopo il failover.

-   Se si desidera utilizzare una rete esistente, è consigliabile creare una rete logica separata, non utilizzata in produzione con queste finalità.
-   Se si seleziona l'opzione per la creazione automatica di una rete VM di test, le reti temporanee e le macchine virtuali di test verranno eliminate automaticamente al termine del failover di test.
-   Se si utilizza una rete logica basata su VLAN (Virtual LAN), è necessario verificare che i siti di rete aggiunti alla rete logica siano isolati.
-   Se si utilizza una rete logica basata su Virtualizzazione rete Windows, Gestione ripristino Hyper-V creerà automaticamente reti VM isolate.

##### Eseguire un failover di test

Per eseguire un failover di test per un piano di recupero, attenersi alla procedura seguente:

1.  Nella scheda **Recovery Plans** selezionare il piano di ripristino necessario.
2.  Per avviare il failover, fare clic sul pulsante **Test Failover**.
3.  Nella pagina Confirm Test Failover specificare il modo in cui le macchine virtuali devono essere connesse alle reti al termine del failover di test, come indicato di seguito:

-   **None** - Selezionare questa impostazione per specificare che le reti VM non devono essere utilizzate nel failover di test. Utilizzare questa opzione se si desidera eseguire il test di singole macchine virtuali invece della configurazione di rete. Questa opzione offre inoltre una breve panoramica del funzionamento della funzionalità di failover di test. Le macchine virtuali di test non saranno connesse a reti dopo un failover.
-   **Use existing** - Utilizzare questa opzione se è già stata creata e isolata una rete VM da utilizzare per il failover di test. Al termine del failover, tutte le macchine virtuali di test utilizzate nel failover di test saranno connesse alla rete specificata in **VM Network**.
-   **Create automatically** - Selezionare questa opzione per specificare che Gestione ripristino Hyper-V deve creare automaticamente una rete VM basata sull'impostazione specificata in Logical Network e i siti di rete correlati. Utilizzare questa opzione se il piano di ripristino utilizza più reti VM. Nel caso delle reti di Virtualizzazione rete Windows, è possibile utilizzare questa opzione per creare automaticamente reti VM con le stesse impostazioni (subnet e pool di indirizzi IP) specificate per quelle incluse nella rete della macchina virtuale di replica. Queste reti VM vengono eliminate automaticamente al termine del failover di test.

Dopo il completamento del failover di test, eseguire le operazioni seguenti:

1.  Verificare che le macchine virtuali vengano avviate correttamente.
2.  Dopo la verifica dell'avvio corretto delle macchine virtuali, completare il failover di test per eliminare gli elementi inclusi nell'ambiente isolato. Se è stata selezionata l'opzione per la creazione automatica delle reti VM, verranno eliminate tutte le macchine virtuali di test e le reti di test.
3.  Fare clic su **Notes** per registrare e salvare eventuali osservazioni associate al failover di test.
4.  Quando viene avviato un failover di test per un piano di ripristino, il processo di failover viene visualizzato nella pagina dei dettagli del piano di ripristino. È possibile visualizzare i singoli passaggi del failover di test e il rispettivo stato, quindi visualizzare o creare note associate al failover di test. È inoltre possibile visualizzare i dettagli dei processi di failover esaminando il processo di failover nella scheda**Jobs**. I dettagli visualizzati includono ogni processo associato al failover, il rispettivo stato, l'ora di avvio e la durata. È possibile esportare un processo nell'elenco di failover in un foglio di calcolo di Excel.

Tenere presente quanto segue:

-   Nella pagina Confirm Test Failover sono disponibili i dettagli relativi al server VMM in cui verranno create e visualizzate le macchine virtuali di test.
-   La scheda **Jobs** consente di verificare lo stato dei processi di failover di test.

Passaggio 8: Eseguire il monitoraggio
-------------------------------------

### Utilizzare la scheda Jobs e la scheda Dashboard

#### Jobs

Nella scheda **Jobs** sono visualizzate le attività principali eseguite dall'insieme di credenziali di Gestione ripristino Hyper-V, incluse la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test), la conferma di un failover non pianificato e la configurazione di una replica inversa.

Nella scheda **Jobs** è possibile eseguire le attività seguenti:

-   **Run a job query** -  È possibile eseguire una query per recuperare processi corrispondenti a criteri specificati. Per filtrare i processi, è possibile utilizzare i parametri seguenti:

-   Individuare processi eseguiti su un server VMM specifico.
-   Individuare processi eseguiti su un cloud, una macchina virtuale, una rete, un piano di ripristino in un cloud o su tutte tali opzioni.
-   Identificare i processi eseguiti durante un intervallo specifico di data e ora.

Una query può restituire al massimo 200 processi. È pertanto consigliabile limitare i parametri della query, in modo che restituisca un valore inferiore alla soglia massima.

-   **Get job details** - È possibile fare clic su un processo nell'elenco **Jobs** per ottenere ulteriori dettagli, inclusi il nome del processo, il relativo stato, l'ora di inizio e la durata. La scheda Job details consente di esportare i dettagli relativi al processo in un foglio di calcolo di Excel oppure di tentare di riavviare un processo non riuscito, ignorato o annullato.
-   **Export Jobs** - È possibile esportare i risultati di una query relativa a un processo in un foglio di calcolo di Excel.
-   **Restart** - È possibile riavviare processi non riusciti, per tentare di eseguirli di nuovo.
-   **Error Details** - In caso di processi non riusciti, è possibile fare clic su **Error Details** per ottenere un elenco di errori relativi al processo. Le possibili cause e alcuni suggerimenti vengono visualizzati per ogni errore. È possibile copiare la descrizione dell'errore negli Appunti per la risoluzione.

> Nella scheda **Jobs** sono disponibili le informazioni seguenti:

-   **Name** - Nome del processo.
-   **Item** - Nome del cloud, del piano di ripristino, del server VMM o della macchina virtuale su cui è stato eseguito il processo.
-   **Status** - Stato del processo, ovvero se il processo è stato completato, annullato, non è riuscito o altro ancora.
-   **Type** - Tipo di processo.
-   **Started At** - Ora di inizio del processo.
-   **Task Time** - Durata del processo.

![Scheda Jobs](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)

#### Eseguire una query di processo

È possibile eseguire query per recuperare processi corrispondenti ai criteri specificati, inclusi:

-   Processi eseguiti su un server VMM specifico.
-   Processi eseguiti su un cloud, una macchina virtuale, una rete, un piano di ripristino specifici o su tutti tali elementi.
-   Processi eseguiti durante un intervallo di tempo specifico.

Per eseguire una query relativa ai processi, eseguire le operazioni seguenti:

1.  Aprire la scheda **Jobs**.
2.  In **Server** specificare il server VMM su cui è stato eseguito il processo.
3.  In **Type** specificare se il processo è stato eseguito su un cloud, una macchina virtuale, una rete o un piano di ripristino in un cloud.
4.  In **Status** specificare lo stato del processo.
5.  In **Duration** specificare l'intervallo di tempo durante il quale si è verificato il processo.

#### Dashboard

Nella scheda **Dashboard** è disponibile una rapida panoramica dell'utilizzo di Gestione ripristino Hyper-V nell'organizzazione. Offre un gateway centralizzato per la visualizzazione di macchine virtuali protette da insiemi di credenziali di Gestione ripristino Hyper-V. La scheda Dashboard offre le funzionalità seguenti:

Nella scheda **Dashboard** è possibile eseguire le attività seguenti:

-   **Download Provider** - Scaricare il provider del servizio di gestione ripristino Hyper-V per l'installazione in un server VMM.
-   **Manage Certificate** - Modificare le impostazioni per il certificato associato all'insieme di credenziali.
-   **Delete** - Eliminare un insieme di credenziali.
-   **Resynchronize virtual machines** - Se Gestione ripristino Hyper-V rileva che alcune macchine virtuali primarie e di ripristino non sono sincronizzate come previsto, è possibile visualizzare un elenco delle macchine virtuali rilevanti e selezionare quelle che si desidera provare a risincronizzare dal Dashboard.

Nella scheda **Dashboard** sono disponibili le informazioni seguenti:

-   **Usage Overview** - Mostra il numero di macchine virtuali la cui protezione è gestita da Gestione ripristino Hyper-V. 
-   **Quick Glance** - Mostra informazioni essenziali relative alla configurazione per i servizi di ripristino e per l'insieme di credenziali di Gestione ripristino Hyper-V. Indica se l'insieme di credenziali è online, quale certificato è assegnato a tale insieme, la data di scadenza del certificato e i dettagli di sottoscrizione del servizio.
-   **Recent Jobs** - Mostra i processi che hanno avuto esito positivo o negativo nelle ultime 24 ore oppure i processi in corso o in attesa di azioni.
-   **Issues** - Nel dashboard vengono mostrate informazioni sui problemi relativi alle connessioni dei server VMM e sui problemi relativi alle impostazioni di configurazione del cloud o alla sincronizzazione della replica delle macchine virtuali. È possibile ottenere ulteriori dettagli su un problema, visualizzare i processi associati al problema o tentare di risincronizzare le macchine virtuali.

![Dashboard](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

Passaggi successivi
-------------------

-   Per pianificare e distribuire Gestione ripristino Hyper-V in un ambiente di produzione completo, vedere [Guida alla pianificazione per Gestione del ripristino di Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321294) e [Guida alla distribuzione per Gestione del ripristino di Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321295).
-   In caso di domande, vedere il [forum relativo ai Servizi di ripristino di Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

