<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure

<div class="dev-callout"> 

<p>Azure Site Recovery pu&ograve; proteggere le macchine virtuali in esecuzione su host Hyper-V situati in cloud System Center Virtual Machine Manager (VMM). &Egrave; possibile configurare:</p>

<ul>
<li><b>Protezione da sito locale ad Azure</b>: le macchine virtuali locali presenti nei server host Hyper-V in cloud VMM vengono replicate in Azure. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale all'archiviazione di Azure.</li>

<li><b>Protezione da sito locale a sito locale</b>: le macchine virtuali presenti nei server host Hyper-V in cloud VMM vengono replicate da un sito locale a un altro. Le impostazioni di protezione vengono configurate e abilitate negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale a un altro. Azure Site Recovery si occupa semplicemente di orchestrare il processo.
Altre informazioni su questo scenario sono disponibili in <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale</a>.</li>

</ul>
 
<h2><a id="about"></a>Informazioni sull'esercitazione</h2>


<P>In questa esercitazione viene descritto come distribuire Azure Site Recovery per una rapida prova. Dove possibile, vengono usati il percorso pi&ugrave; rapido e le impostazioni predefinite. Sono incluse le procedure per:
<ul>
<li>Configurare un insieme di credenziali di Azure Site Recovery: ottenere un certificato caricato nell'insieme di credenziali e configurato sul server VMM di origine e generare una chiave dell'insieme di credenziali. </li>
<li>Configurare il server VMM di origine e i server host Hyper-V: installare il provider di Azure Site Recovery nel server VMM di origine e installare l'agente di Servizi di ripristino di Azure nei server host Hyper-V.</li>
<li>Configurare i cloud VMM: configurare le impostazioni di protezione per i cloud sul server VMM di origine.</li>
<li>Abilitare le macchine virtuali: abilitare la protezione per le macchine virtuali</li>
<li>Eseguire un failover: creare un piano di ripristino ed eseguire un failover di test.</li>
</ul>


Per informazioni su una distribuzione completa, vedere:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Pianificare la distribuzione di Azure Site Recovery</a>, che descrive i passaggi di pianificazione da completare prima di avviare una distribuzione completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Distribuire Azure Site Recovery: Protezione da sito locale ad Azure</a>, in cui sono fornite istruzioni dettagliate per una distribuzione completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">Amministrare e monitorare Azure Site Recovery</a>, in cui viene descritto come eseguire failover e gestire e monitorare la distribuzione.</LI>
</UL>
<P>In caso di problemi durante questa esercitazione, vedere l'articolo wiki su <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Scenari di errore comuni e soluzioni</a> oppure inviare domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui Servizi di ripristino di Azure</a>.</P>

</div>

## <span id="before"></span></a>Prima di iniziare

<div class="dev-callout"> 
<P>Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.</P>

<h3><a id="HVRMPrereq"></a>Prerequisiti di Azure</h3>

<UL>
<LI><b>Account Azure</b>: &egrave; necessario un account Azure. Se non si dispone di un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Ottenere <a href="http://go.microsoft.com/fwlink/?LinkId=378268">informazioni dettagliate sui prezzi di Azure Site Recovery Manager</a>.</LI>
<LI><b>Certificato</b>: &egrave; necessario caricare un certificato di gestione (con estensione cer) con una chiave pubblica nell'insieme di credenziali. Questo certificato verr&agrave; esportato come file con estensione pfx (con la chiave privata) e importato in ogni server VMM da registrare nell'insieme di credenziali. Per questa esercitazione si user&agrave; un certificato autofirmato. Per una distribuzione completa &egrave; possibile usare qualsiasi certificato SSL valido conforme ai <a href="http://go.microsoft.com/fwlink/?LinkId=321294">prerequisiti dei certificati</a> illustrati nella guida alla pianificazione</a>. </LI>


</LI>

<LI><b>Account di archiviazione di Azure</b>: per archiviare dati replicati in Azure &egrave; necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione. Per altre informazioni sulla configurazione dell'archiviazione di Azure, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introduzione ad Archiviazione di Microsoft Azure</a>.</LI>
</UL>

<h3><a id="VMMPrereq"></a>Prerequisiti di VMM</h3>

<UL>
<LI><b>Server VMM</b>: un server VMM in esecuzione su System Center 2012 R2.</LI>
<LI><b>Cloud VMM</b>: almeno un cloud sul server VMM server. Il cloud deve includere:
    <UL>
    <LI>Uno o pi&ugrave; gruppi host VMM</LI>
    <LI>Uno o pi&ugrave; cluster o server host Hyper-V in ogni gruppo host.</LI>
    <li>Una o pi&ugrave; macchine virtuali situate nel server Hyper-V di origine nel cloud. Le macchine virtuali devono essere di generazione 1.</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>Prerequisiti delle macchine virtuali</h3>

<UL>
<LI><b>Generazione</b>: Azure supporta solo macchine virtuali di generazione 1.</LI>
<LI>Per un elenco completo dei requisiti di supporto delle macchine virtuali per il failover in Azure, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Prerequisiti e supporto</a> nella Guida alla pianificazione. </LI>  
</UL>

<h2><a id="tutorial"></a>Passaggi dell'esercitazione</h2> 

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:
<UL>
<LI><a href="#createcert">Passaggio 1: Ottenere e configurare i certificati</a>: ottenere un certificato con estensione cer, esportarlo come file PFX e importare il file PFX nei server VMM.</LI>
<LI><a href="#vault">Passaggio 2: Creare un insieme di credenziali</a>: creare un insieme di credenziali di Azure Site Recovery.</LI>
<LI><a href="#upload">Passaggio 3: Configurare l'insieme di credenziali</a>: caricare un certificato di gestione nell'insieme di credenziali e generare una chiave per l'insieme di credenziali. La chiave viene usata per fare in modo che il provider situato sul server VMM esegua solo comandi inviati da Azure Site Recovery.</LI>
<LI><a href="#download">Passaggio 4: Installare l'applicazione provider</a>: eseguire l'applicazione del provider di Microsoft Azure Site Recovery sul server VMM. In questo modo viene installato il provider e viene registrato il server VMM nell'insieme di credenziali.</LI>
<LI><a href="#agent">Passaggio 5: Installare l'applicazione agente</a>: installare l'agente di Servizi di ripristino di Microsoft Azure su ogni host Hyper-V.</LI>
<LI><a href="#clouds">Passaggio 6: Configurare la protezione cloud</a>: configurare le impostazioni di protezione per i cloud VMM.</LI>
<LI><a href="#NetworkMapping">Passaggio 7: Configurare il mapping di rete</a>: facoltativamente, &egrave; possibile configurare il mapping di rete in modo da mappare le reti VM di origine alle reti Azure di destinazione.</LI>
<LI><a href="#virtualmachines">Passaggio 8: Abilitare la protezione per le macchine virtuali</a>: abilitare la protezione per le macchine virtuali presenti nei cloud VMM protetti.</LI>
<LI><a href="#recovery plans">Passaggio 9: Configurare ed eseguire piani di ripristino</a>: creare un piano di ripristino ed eseguire un failover di test per il piano.</LI>
</UL>



<a name="createcert"></a> <h2>Passaggio 1: Recupero e configurazione di certificati</h2>

Per ottenere e configurare certificati, eseguire la procedura seguente:
<OL>
<LI><a href="#obtaincert">Ottenere un certificato autofirmato per la procedura dettagliata</a>: ottenere un certificato tramite lo strumento MakeCert.</LI>
<LI><a href="#exportcert">Esportare il certificato in formato PFX</a>: sul server in cui &egrave; stato creato il certificato esportare il file CER come file PFX (con la chiave privata). </LI>
<LI><a href="#importcert">Importare il certificato PFX nel server VMM</a>: al termine dell'esportazione, importare il file con estensione pfx nella cartella Personale nell'archivio Computer locale sul server VMM da registrare nell'insieme di credenziali.</LI>
</OL>


<h3><a id="obtaincert"></a>Ottenere un certificato auto-firmato (con estensione cer)</h3>
<P>Creare un certificato x.509 con estensione cer conforme a tutti i requisiti per i certificati:</P>
<ol>
<LI>
Nel computer in cui si vuole eseguire MakeCert, scaricare la versione pi&ugrave; recente di <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>. Non &egrave; necessario installare l'intero SDK.</LI>
<ol>
<LI>Nella pagina Specify Location selezionare <b>Install the Windows Software Development Kit for Windows 8.1 to this computer</b>.</LI>
<LI>Nella pagina Select the features you want to install deselezionare tutte le opzioni tranne <b>Windows Software Development Kit</b>.</LI>
<LI>Al termine dell'installazione, verificare che makecert.exe sia disponibile nella cartella C:\Programmi (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64.</LI>
<LI>Aprire un prompt dei comandi (cmd.exe) con privilegi di amministratore, quindi passare alla cartella makecert.exe.</LI> 
<LI>Eseguire il comando seguente per creare il certificato autofirmato. Sostituire NomeCertificato con il nome da usare per il certificato, quindi specificare la data di scadenza effettiva del certificato dopo -e:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>Lo stato di operazione &egrave; riuscita indica che il certificato &egrave; stato creato. &Egrave; archiviato nella stessa cartella in cui si trova makecert.exe. &Egrave; possibile spostarlo in un percorso pi&ugrave; accessibile per l'esportazione.</P>

<h3><a id="exportcert"></a>Esportare il certificato nel formato PFX</h3>
<P>Per esportare il file con estensione cer nel formato PFX, eseguire la procedura seguente.</P>
<ol>
<li>Lo stato di operazione &egrave; riuscita indica che il certificato &egrave; stato creato. &Egrave; archiviato nella stessa cartella in cui si trova makecert.exe. &Egrave; possibile spostarlo in un percorso pi&ugrave; accessibile per l'esportazione.</li>
<li>Nel riquadro dei dettagli fare clic sul certificato che si desidera gestire.</li>
<li>Scegliere <b>Tutte le attivit&agrave;</b> dal menu <b>Azioni</b>, quindi fare clic su <b>Esporta</b>. Verr&agrave; visualizzata l'Esportazione guidata certificati. Fare clic su <b>Avanti</b>.</li>
<li>Nella pagina <b>Esportazione della chiave privata con il certificato</b> fare clic su <b>S&igrave;</b> per esportare la chiave privata. Fare clic su <b>Avanti</b>. Questo passaggio &egrave; obbligatorio solo se si desidera esportare la chiave privata in altri server dopo l'installazione.</li>
<li>Nella pagina Formato file di esportazione selezionare <b>Scambio di informazioni personali &ndash; PKCS #12 (.PFX)</b>. Fare clic su <b>Avanti</b>.</li>
<li>Nella pagina <b>Password</b> digitare e confermare la password usata per crittografare la chiave privata. Fare clic su <b>Avanti</b>.</li>
<li>Per esportare il certificato in formato PFX, seguire le istruzioni visualizzate nella procedura guidata.</li>
</ol>

<h3><a id="importcert"></a>Importare il certificato con estensione pfx nei server VMM</h3>
<p>Dopo l'esportazione copiare il file PFX nel server VMM, quindi reimportarlo. Si noti che se MakeCert.exe &egrave; stato eseguito sul server VMM, non &egrave; necessario importare il certificato in quel server.</p>
 
<ol>
<li>Copiare i file del certificato di chiave privata (con estensione pfx) in una posizione nel server locale.</li>
<li>Nello snap-in Certificati di MMC selezionare <b>Account del computer</b> e fare clic su <b>Avanti</b>.</li>
<li>Selezionare <b>Computer locale</b> e fare clic su <b>Fine</b>.</li>
<li>In MMC espandere <b>Certificati</b>, fare clic con il pulsante destro del mouse su <b>Personale</b>, scegliere <b>Tutte le attivit&agrave;</b>, quindi fare clic su <b>Importa</b> per avviare l'Importazione guidata certificati.</li>
<li>Nella pagina Fila da importare fare clic su <b>Sfoglia</b> e individuare la cartella in cui si trova il file del certificato con estensione pfx contenente il certificato da importare. Selezionare il file appropriato, quindi fare clic su <b>Apri</b>.</li>
<li>Nella casella <b>Password</b> della pagina Password digitare la password per il file PFX della chiave privata, quindi fare clic su <b>Avanti</b>.</li>
<li>Nella pagina Archivio certificati selezionare <b>Colloca tutti i certificati nel seguente archivio</b>, quindi fare clic su <b>Sfoglia</b>, selezionare l'archivio <b>Personale</b>, fare clic su <b>OK</b> e quindi su <b>Avanti</b>. Completare la procedura guidata.</li>
</ol>

Al termine di questa procedura sar&agrave; possibile scegliere il certificato con estensione cer da caricare durante la configurazione dell'insieme di credenziali, quindi selezionare il certificato con estensione pfx quando si esegue la registrazione di un server VMM durante l'installazione del provider.
</div>

<a name="vault"></a>

## Passaggio 2: Creare un insieme di credenziali

1.  Accedere al [portale di gestione][portale di gestione].

2.  Espandere **Servizi dati**, espandere **Servizi di ripristino** e fare clic su **Insieme di credenziali di Azure Site Recovery**.

3.  Fare clic su **Crea nuovo** e quindi su **Creazione rapida**.

4.  In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In **Region** selezionare l'area geografica per l'insieme di credenziali. Le aree geografiche disponibili includono Asia orientale, Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale e Asia sudorientale.
6.  Fare clic su **Create vault**.

    ![Nuovo insieme di credenziali][Nuovo insieme di credenziali]

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.

<a name="upload"></a>

## Passaggio 3: Configurare l'insieme di credenziali


1.  Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può essere anche aperta in qualsiasi momento tramite l'icona.

    ![Icona Quick Start][Icona Quick Start]

2.  Nell'elenco a discesa **Configurare il ripristino** selezionare **Tra un sito locale e Microsoft Azure**.
3.  Per caricare il certificato (con estensione cer) nell'insieme di credenziali, fare clic su **Gestisci certificato**.

    ![Quick Start][Quick Start]

4.  Nella finestra di dialogo **Gestisci certificato** fare clic su **Cerca file** e selezionare il file con estensione cer.

    ![Manage Certificate][Manage Certificate]

5.  Per generare una chiave per l'insieme di credenziali, fare clic su **Ottieni la chiave dell'insieme di credenziali**. La chiave viene generata automaticamente. Se si rigenera una chiave, la chiave precedente sarà sovrascritta. Si noti che questa chiave sarà necessaria successivamente quando si installerà il provider di Azure Site Recovery nel server VMM.

    ![Manage Certificate][1]

<a name="download"></a>

## Passaggio 4: Installare il provider di Azure Site Recovery

1.  Nella pagina **Avvio rapido** fare clic su **Scarica provider** per ottenere la versione più recente del file di installazione del provider.

    ![Scaricare il file del provider][Scaricare il file del provider]

2.  Eseguire il file nel server VMM di origine.

    ![Download Agent][Download Agent]

3.  Al termine dell'installazione del provider, proseguire l'installazione per registrare il server nell'insieme di credenziali.
    ![Setup Complete][Setup Complete]
4.  Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Fare clic su **Avanti** per usare le impostazioni di connessione a Internet predefinite configurate sul server.
    ![Internet Settings][Internet Settings]
5.  Nella pagina Registrazione dell'insieme di credenziali eseguire le operazioni seguenti:

    -   Selezionare la chiave privata (con estensione pfx) importata nel server VMM.
    -   Selezionare l'insieme di credenziali in cui si vuole registrare il server.
    -   Specificare la chiave dell'insieme di credenziali generata precedentemente. Tagliare e incollare il valore della chiave dalla pagina Avvio rapido.

    </p>
    ![Registro certificati][Registro certificati]

6.  Nella pagina Crittografia dati specificare se si vuole abilitare l'opzione per crittografare i dati durante la replica per un cloud specifico. Se si seleziona questa opzione, viene generato automaticamente un certificato SSL. Quando si esegue un failover, è necessario selezionare questo certificato. Dopo avere abilitato questa impostazione, è possibile abilitare o disabilitare la crittografia dei dati per un cloud nel portale di Azure Site Recovery. Per questa esercitazione lasciare l'impostazione predefinita e fare clic su **Avanti**.

    ![Insieme di credenziali certificato][Insieme di credenziali certificato]

7.  Nella pagina Server VMM eseguire le operazioni seguenti:

    -   Specificare un nome descrittivo per il server VMM. Questo nome viene usato per identificare il server nella console di Azure Site Recovery.
    -   Selezionare **Sincronizza metadati cloud con insieme credenziali** per sincronizzare le informazioni sui cloud VMM con l'insieme di credenziali di Azure Site Recovery. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, è possibile pubblicare singolarmente ogni cloud per sincronizzarlo prima di configurare le impostazioni di protezione del cloud.

8.  Fare clic su **Register** per completare il processo.

    ![PublishCloud][PublishCloud]

Dopo la registrazione corretta di un server, il relativo nome descrittivo verrà visualizzato nella scheda **Risorse** della pagina Server nell'insieme di credenziali.

## <span id="storage"></span></a>Passaggio 5: Installare l'agente di Servizi di ripristino di Azure

Installare l'agente di Servizi di ripristino di Azure su ogni server host Hyper-V presente nei cloud VMM da proteggere.

1.  Nella pagina Avvio rapido fare clic su **Scarica agente Servizi Azure Site Recovery e installa negli host** per ottenere la versione più recente dei file di installazione dell'agente.

    ![Installare l'agente di Servizi di ripristino][Installare l'agente di Servizi di ripristino]

2.  Eseguire il file di installazione su ogni server host Hyper-V presente nei cloud VMM da proteggere.
3.  Nella pagina Controllo dei prerequisiti fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

![Prerequisiti per l'agente di Servizi di ripristino di Azure][Prerequisiti per l'agente di Servizi di ripristino di Azure]

1.  Nella pagina Impostazioni di installazione specificare dove si vuole installare l'agente e selezionare il percorso della cache in cui verranno installati i metadati di backup. Fare clic su **Installa**.

Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Fare clic su **Avanti** per usare le impostazioni di connessione a Internet predefinite configurate sul server![Recovery Services Agent location][Recovery Services Agent location]

## <span id="clouds"></span></a>Passaggio 6: Configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. L'opzione **Sincronizza dati cloud con insieme credenziali** è stata abilitata in fase di installazione del provider per visualizzare tutti i cloud sul server VMM nella scheda **Elementi protetti** nell'insieme di credenziali.

![Cloud pubblicato][Cloud pubblicato]

Configurare le impostazioni di protezione come segue:

1.  Nella pagina Avvio rapido fare clic su **Configurare la protezione per i cloud VMM**.

![Abilitare la protezione cloud][Abilitare la protezione cloud]

1.  Nella scheda **Elementi protetti** fare clic sul cloud da configurare e passare alla scheda **Configurazione**.
2.  In **Destinazione** selezionare **Microsoft Azure**.
3.  In **Account di archiviazione** selezionare l'archiviazione di Azure che si vuole usare per archiviare le macchine virtuali di Azure.
4.  Impostare **Crittografa dati archiviati** su **Disattivato**. Questa impostazione specifica che i dati devono essere replicati crittografati tra il sito locale e Azure.
5.  In **Frequenza di copia** lasciare l'impostazione predefinita. Questo valore specifica la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione.
6.  In **Mantieni punti di ripristino per** lasciare l'impostazione predefinita. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
7.  In **Frequenza degli snapshop coerenti con l'applicazione** lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si imposta un valore, questo deve essere inferiore al numero di punti di ripristino aggiuntivi configurati.

    ![Configurazione del cloud][Configurazione del cloud]

8.  In **Ora inizio replica** specificare quando deve essere avviata la replica iniziale dei dati in Azure. Verrà usato il fuso orario del server host Hyper-V. È consigliabile pianificare la replica iniziale durante le fasce orarie di minore attività.

    ![Impostazioni della replica cloud][Impostazioni della replica cloud]

Dopo avere salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda **Processi**. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica. Dopo il salvataggio, le impostazioni cloud possono essere modificate nella scheda **Configura**. Per modificare il percorso o la risorsa di archiviazione di destinazione, è necessario rimuovere la configurazione del cloud e quindi riconfigurare il cloud. Si noti che se si cambia l'account di archiviazione, la modifica viene applicata alle macchine virtuali abilitate per la protezione solo dopo la modifica dell'account di archiviazione. Non viene eseguita la migrazione delle macchine virtuali esistenti al nuovo account di archiviazione.

## <span id="networkmapping"></span></a>Passaggio 7: Configurare il mapping di rete

Facoltativamente, è possibile abilitare il mapping di rete in modo da mappare le reti VM di origine alle reti virtuali di Azure di destinazione. Se non si creano mapping di rete, solo le macchine virtuali di cui viene eseguito il failover nello stesso piano di ripristino possono connettersi tra loro dopo il failover in Azure. Quando si crea un mapping di rete, tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete possono connettersi le une alle altre, indipendentemente dal piano di ripristino di cui fanno parte. Inoltre, se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali. Se si vuole configurare il mapping di rete nell'ambito di questa esercitazione, vedere [Configurare il mapping di rete][Configurare il mapping di rete] nella guida alla distribuzione.

## <span id="virtualmachines"></span></a>Passaggio 8: Abilitare la protezione per le macchine virtuali

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.

Prima di abilitare la protezione per una macchina virtuale, verificare e aggiornare le relative impostazioni, se necessario. Ad esempio, il sistema operativo guest sulla macchina virtuale deve essere Windows Server 2008 o versioni successive oppure Linux. La macchina virtuale deve essere di generazione 1. Per un elenco completo dei requisiti di Azure Site Recovery, vedere [Prerequisiti e supporto][Prerequisiti e supporto] nella guida alla pianificazione.

Nella console VMM verificare e aggiornare le impostazioni. Le impostazioni del sistema operativo per la macchina virtuale vengono modificate nella pagina Generale delle proprietà della macchina virtuale. Le impostazioni disco del sistema operativo vengono aggiornate nella pagina Configurazione hardware.

![Modificare le proprietà della macchina virtuale][Modificare le proprietà della macchina virtuale]

![Modificare le proprietà della macchina virtuale][2]

1.  Per abilitare la protezione, nella scheda **Macchine virtuali** nel cloud in cui si trova la macchina virtuale fare clic su **Abilita protezione** e selezionare **Aggiungi macchine virtuali**.
2.  Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere.

![Abilitare la protezione delle macchine virtuali][Abilitare la protezione delle macchine virtuali]

Dopo avere abilitato la protezione, vengono creati due processi. Viene eseguito il processo Enable Protection. Quindi, al temine della replica iniziale, viene eseguito il processo di finalizzazione della protezione. La macchina virtuale sarà pronta per il failover solo dopo il completamento corretto di questi processi. Lo stato di avanzamento può essere monitorato nella scheda **Processi**.

![Processo di protezione delle macchine virtuali][Processo di protezione delle macchine virtuali]

## <span id="recoveryplans"></span></a>Passaggio 9: Configurare ed eseguire un piano di ripristino

Un piano di ripristino raggruppa le macchine virtuali in modo che possano essere sottoposte a failover come unità singola. Per creare un piano di ripristino, eseguire le operazioni seguenti:

1.  Nella scheda **Piani di ripristino** fare clic su **Crea**.
2.  Nella pagina Specificare il nome del piano di ripristino e la destinazione selezionare il server VMM di origine e Azure come destinazione.

    ![Create Recovery Plan][Create Recovery Plan]

3.  Nella pagina Seleziona macchine virtuali selezionare le macchine virtuali da aggiungere al piano di ripristino. Vengono mostrate solo le macchine virtuali in cui è abilitata la protezione. Le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino, ossia il Gruppo 1.
4.  Fare clic sul segno di spunta per creare il piano di ripristino.

    ![Macchine virtuali del piano di ripristino][Macchine virtuali del piano di ripristino]

### <span id="run"></span></a>Testare a failover

I piani di ripristino possono essere eseguiti come parte di un test proattivo o un failover pianificato oppure durante un failover non pianificato. Questa procedura dettagliata descrive come eseguire un failover di test da VMM ad Azure per verificare che la strategia di failover funzioni come previsto. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata. Tenere presente quanto segue:

-   Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test.
-   Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto. Se si vuole procedere in questo senso, assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale mediante un indirizzo pubblico.

</p>
#### 

<p>
<span id="runtest"></span></a>Eseguire il failover

</h5>
Per eseguire un failover di test per un piano di recupero, eseguire la procedura seguente:

</p>
1.  Prima di eseguire il piano di ripristino, è possibile convalidare le impostazioni delle macchine virtuali nel piano, facendo clic sulla macchina virtuale nella pagina delle proprietà del cloud. Verificare le impostazioni nella pagina Proprietà dell'origine e della destinazione per il failover. Verificare in particolare che la dimensione suggerita per la macchina virtuale di destinazione in Azure sia corretta e che le impostazioni di rete siano accurate. Per un elenco completo di prerequisiti per le macchine virtuali, vedere [Prerequisiti e supporto][3].

![Proprietà della macchina virtuale][Proprietà della macchina virtuale]

1.  Nella scheda **Piani di ripristino** selezionare il piano di ripristino necessario.
2.  Per avviare il failover, fare clic sul pulsante **Failover di test**.
3.  Nella pagina Conferma failover di test selezionare la rete di Azure a cui verranno connesse le macchine virtuali dopo il failover. Facoltativamente è possibile selezionare **Nessuna rete**. Con questa impostazione le macchine virtuali non vengono connesse a una rete dopo il failover.

![Failover di test][Failover di test]

La scheda **Jobs** consente di verificare lo stato dei processi di failover di test. Dopo il completamento del failover di test, eseguire le operazioni seguenti:

1.  Verificare che le macchine virtuali vengano avviate correttamente in Azure.
2.  Fare clic su **Note** per registrare e salvare eventuali commenti associati al failover di test.
3.  Oltre ai dettagli nella scheda **Processi**, quando si esegue un failover di test per un piano di ripristino, il processo viene visualizzato nella pagina dei dettagli del piano di ripristino. È possibile visualizzare i passaggi e lo stato del failover, nonché visualizzare e creare note relative al failover di test.

#### 

<p>
<span id="runtest"></span></a><h5> Monitorare l'attività </h5><br>

La scheda <b> Processi </b> e il <b> Dashboard </b> consentono di visualizzare e monitorare i processi principali eseguiti dall'insieme di credenziali di Azure Site Recovery, tra cui la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.

</p>
Nella scheda **Processi** è possibile visualizzare i processi, eseguire il drill-down di dettagli ed errori dei processi, eseguire query sui processi per recuperare i processi che soddisfano specifici criteri, esportare processi in Excel e riavviare i processi non riusciti.

Nel **Dashboard** è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali la cui protezione è gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.

Per altre informazioni sull'interazione con i processi e il dashboard, vedere la [guida alle operazioni e al monitoraggio][guida alle operazioni e al monitoraggio].

## <span id="next"></span></a>Passaggi successivi

-   Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la [guida alla pianificazione per Azure Site Recovery][Pianificare la distribuzione di Azure Site Recovery] e la [guida alla distribuzione per Azure Site Recovery][guida alla distribuzione per Azure Site Recovery].
-   In caso di domande, vedere il [forum relativo ai Servizi di ripristino di Azure][forum sui Servizi di ripristino di Azure].

  [Pianificare la distribuzione di Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [forum sui Servizi di ripristino di Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Prerequisiti e supporto]: http://go.microsoft.com/fwlink/?LinkId=402602
  [portale di gestione]: https://manage.windowsazure.com
  [Nuovo insieme di credenziali]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icona Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [Manage Certificate]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Scaricare il file del provider]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [Download Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup Complete]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro certificati]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [Insieme di credenziali certificato]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Installare l'agente di Servizi di ripristino]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Prerequisiti per l'agente di Servizi di ripristino di Azure]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Recovery Services Agent location]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [Cloud pubblicato]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Abilitare la protezione cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [Configurazione del cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Impostazioni della replica cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [Configurare il mapping di rete]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modificare le proprietà della macchina virtuale]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Abilitare la protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Processo di protezione delle macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Create Recovery Plan]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [Macchine virtuali del piano di ripristino]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Proprietà della macchina virtuale]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Failover di test]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [guida alle operazioni e al monitoraggio]: http://go.microsoft.com/fwlink/?LinkId=398534
  [guida alla distribuzione per Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
